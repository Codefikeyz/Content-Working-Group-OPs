## Youtube Channel Verification

### For steps 1-3 use https://172.236.19.60.nip.io/channels/unverified instead

This is a technical manual on the process of verifying the channels signed up for YouTube Partnership Program

**ℹ️ Download Postman App for windows or mac**

[Download Postman | Get Started for Free](https://www.postman.com/downloads/)

**1.  View YT Sync Data Base with new sign ups**

Go to Dynamo DB for YT Sync service
URL: https://eu-central-1.console.aws.amazon.com/dynamodbv2/home?region=eu-central-1#item-explorer?table=channels&maximize=true
👆Access can be requested from @mokhtar

**2. Filter unverified channels**
Filter channels by yppStatus and find “Unverified”

![image](https://github.com/user-attachments/assets/2a9f85a4-f22f-4eb6-a61e-2e2e49744888)


Then sort `desc` by `dateCreated` to work on the earlier signups

**3. Capture Gleev / Joystream Channel ID**

Copy this channel’s **`joystreamChannelId`** by clicking over the copy icon

![image](https://github.com/user-attachments/assets/0b074746-3256-482c-a6a6-31af644e191e)


***4*.  Verify Channel**

Send a verify request to the verification endpoint for thsi channelID 
Request type: PUT
Endpoint URL: https://35.156.81.207.nip.io/channels/verify
Authentication: Bearer token 
*👆Token can be requested from @zeeshanakram*
Body Format: Select Raw (radio button) and “JSON” from the drop down that is set to “text” by default

**Body:

```
[
  {
    "joystreamChannelId": 274,
    "tier": "Diamond"
  }
]
```

List of Verified tiers

`Diamond`
`Gold`
`Silver`

`Bronze`

**Hit Send>**

Send the request and watch for the confirmation in response with how many records updated

![image](https://github.com/user-attachments/assets/41d49300-121c-4173-b3b9-aa37097c9139)

**TIERS:**

💎 **Diamond** 
**Quality:** Absolute gold standard of quality. Professionally done with great quality of sound and effort put in post-production editing of video. 
**Popularity:** Recognised influencer status. Very large subscriber audience, likely 100k - 500k and higher. Average views per video ~ more than 10k per video.
**Rewards:** Sign up - 100 USD. Sync - 5 USD per video. Referring a Diamond channel will result in 50 USD referral rewards.

🥇 **Gold**

**Quality:** Popular, great quality, very well done, interesting to watch even if not interested in the narrow topic that channel covers. 
**Popularity:** Subscribers likely to be > 25-150k. Average view per video ~ around 5-10k per video
**Rewards:** Sign up - 50 USD. Sync - 3 USD per video. Referring a Gold channel will result in 25 USD referral rewards.
 
🥈 **Silver** 
**Quality:** Original good quality of content. Likely to be interesting to watch ****if interested in the narrow topic and can forgive the quality of the content when comparing to professional production. Sound may not be that great either. 
**Popularity:** Subscribers likely to be > 5-25k. Average view per video ~ around 1-2k per video
**Rewards:** Sign up - 25 USD. Sync - 1 USD per video. Referring a Gold channel will result in 12.5 USD referral rewards.

🥉 **Bronze**

**Quality:** Lower effort production. 

**Popularity:** Followed by friends and family. Follower base likely to be <5k. Average view per video ~ less than 1k per video
**Rewards:** Bronze tier  channels do not qualify for sign up or sync videos. Referring a bronze channel will not result in referral rewards.

**5. Suspend Channel**

⚠️Suspending channels bears the same approach, but importantly do not forget to also exclude the channel from being shown on Gleev! 

Request type: PUT
Endpoint URL:https://35.156.81.207.nip.io/channels/suspend
Authentication: Bearer token (same as for verify call)
Body Format: Select Raw (radio button) and “JSON” from the drop down that is set to “text” by default
Body: 
 

```
[
  {
    "joystreamChannelId": 273,
    "reason": "CopyrightBreach"
  } 
]
```

**REASONS:**

`CopyrightBreach` - when channel reposts videos from other channels
`MisleadingContent` - when thumbnails, channel description etc do not match with the playback content. We can also use this to stop from spreading scammy projects
`UnsupportedTopic` - gore, violence, porn, all the categories that are in severe violation of content policy of joystream
`ProgramTermsExploit` - this one is when channels are created only for the purpose of benefitting the program, but with the improved validation I don’t expect this to be used much

**6. Exclude Channel**

You would need to do that in Orion and first you need to Authorise also via API

**6.1 Orion Auth**

Request type: POST
Endpoint URL: https://orion.gleev.xyz/api/v1/anonymous-auth
Body:

```
{
    "userId": "hereGoesAuthenticatedUserID"
}
```

👆 userID can be obtained from @mrbovo, so the part “heregoes..” needs to be replaced with the real userID 

**Hit Send>**

Once sent, you will get the `session_id` value in the cookies tab of the respsonse, you need to copy it 

![image](https://github.com/user-attachments/assets/43dfbd28-0496-440a-9616-e0e8687ebe64)

**6.2 Excluding channel**

Request type: POST
Endpoint URL: https://orion.joystream.org/graphql
Headers: Add `Cookie` as new header Key, and add `session_id=*`instead of `*` post the session ID you copied in the previous step above.

![image](https://github.com/user-attachments/assets/af8d9d70-16c9-4c49-9347-737693d6f5aa)

Body: use the snippet below, but replace the IDs with IDs of channels you want to exclude. Multiple channels can be done in a single response, comma separated.

```
mutation {
  excludeContent(
    type: Channel # could be a Video, Channel or Comment
    ids: ["26807"] # array, you can put many ids here. Content ids that you want to exclude
  ) {
    numberOfEntitiesAffected
  }
}
```

**Hit Send>**

Response: Watch out for confirmation and number  of entities affected (excluded)

![image](https://github.com/user-attachments/assets/7ebbc470-e2ce-4d6b-8201-0e94f167ade0)

6.3 Restoring Channel

In case the channel got erroneously excluded, it can be restored right away using this mutation sent to GraphQL api: https://orion.joystream.org/graphql

```
mutation {
  restoreContent(
    type: Channel # could be a Video, Channel or Comment
    ids: ["26592"] # array, you can put many ids here. Content ids that you want to exclude
  ) {
    numberOfEntitiesAffected
  }
}
```

IMportantly: 

After channels are verified, update the status of channels
