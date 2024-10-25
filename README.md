
## warden can submit reports after the contest is over

### Summary
Hi, I found an issue on  /api/functions/submit-finding that allows a warden to submit an issue even when the contest has ended.
### Description
C4 contests are usually time constrained and wardens are only supposed to submit issues during the stipulated time frame.
I found a way to break this allowing submissions even after the deadline is over. To avoid spamming the contest repo, I limited my testing to only submissions but I believe editing and withdrawing submissions are also affected in the same way

### Steps to reproduce
1. Wait for a contest to come to an end
2. Head over to https://code4rena.com/contests/2023-10-contestName and note there is no submission button
3. Manually change your computers clock to a few hours back and refresh the contest page, this time you find the submission button available
4. Write your report as if the contest is ongoing then set an intercepting proxy such as burpsuite to intercept our submission

5. With burp set to intercept, on the submission form click on **create issue**
6. This request is intercepted and we have a POST request to ` /api/functions/submit-finding` with a json body 
```json
 {"user":"c3phas","contest":301,"sponsor":"Party Protocol","repo":"2023-10-party-findings","emailAddresses":["warden@gmail.com"],"attributedTo":"c3phas","risk":"QA (Quality Assurance)","title":"QA Report","body":"## Test","labels":["bug","QA (Quality Assurance)"]}
```

7. Send the post request to burpsuite repeater tab
8. Note: If we attempt to send the request as is, the submission is still rejected with an error message `"error":"This contest has ended."`
9. On our JSON body, we modify the contest ID from 301 to 302 and click on send request. This time we get a 200 Ok response with the message `"message":"Issue posted successfully and confirmation email sent."`

## Proof of concept
To validate my finding, I submitted a QA finding under my name **c3phas** to the Party contest after the contest was over. You can see the report ID 588 was the last report submitted 
https://github.com/code-423n4/2023-10-party-findings/blob/main/data/c3phas-588.json

## Impact
The time constrain for contest can be easily bypassed which breaks the c4 audits model since they are supposed to be time bound. 
