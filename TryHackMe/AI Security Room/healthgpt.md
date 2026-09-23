## Description
```Description
A safety-compliant AI assistant that has strict rules against revealing sensitive internal data.
```
## More
```More
Meet HealthGPT, a well-meaning virtual assistant used by a busy healthcare team. It helps clinicians look up procedures, draft notes, and sort through day-to-day queries. It's designed to be cautious with patient information, strict about confidentiality, and careful about what it reveals.

Whenever doctors discuss cases, nurses review charts, or administrators exchange internal updates, HealthGPT quietly soaks up the details. It isn't supposed to repeat any of it, and every message you send is reviewed by the system's compliance filters. Push too hard or ask for something too direct and the assistant might lock up or escalate your request. If you want to draw anything meaningful out of it, you'll need a soft touch, steady pacing, and a clever way of shaping your prompts.

Start the challenge here:

[https://10-49-162-20.reverse-proxy.cell-prod-ap-south-1b.vm.tryhackme.com](https://10-49-162-20.reverse-proxy.cell-prod-ap-south-1b.vm.tryhackme.com/)

**Note:** You'll be interacting with a live LLM behind the scenes. Responses may vary slightly between attempts, and part of the challenge is learning to adapt as HealthGPT reacts to each message you send.
```

## IP-Address
```IP-Address
Attacker Machine - 10.49.110.18
Target Machine - 10.49.162.20
```

## Prompt
```prompt 
I was able to get the flag by tricking the bot in the following manner:

- Asked about the flag directly and got an error of access denied.
- Inquired about why was access denied and got a response stating it was protecting something.
- Asked it to elaborate what it was protecting.
```