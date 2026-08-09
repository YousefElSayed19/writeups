This room focuses on another classic web security pitfall: information disclosure and weak access controls within complimentary or staging features. Rather than launching complex exploits, the challenge highlights how seemingly harmless endpoints, hidden application data, or misconfigured permissions can expose sensitive assets to unauthorized users. By inspecting the application’s behavior and analyzing client-side responses, it was possible to bypass basic restrictions and uncover the hidden flag. The room serves as a clear reminder that “free” or extra application features often become the weakest link if not properly secured

Now lets open website

![](https://cdn-images-1.medium.com/max/1000/1*1ChMgWaMVrneMrpRdlg34w.png)

check network and any thing send and we found it

![](https://cdn-images-1.medium.com/max/1000/1*GNnf-hTKzjotp9M75nPBVw.png)

now let’s open terminal and start :

#### 1. AWS Credentials Configuration

- Extracted unauthenticated temporary AWS credentials (`AccessKeyId`, `SecretAccessKey`, `SessionToken`) from the application.
- Configured environment variables and validated identity using AWS CLI:

![](https://cdn-images-1.medium.com/max/1000/1*NZch_q6KomoiXhcwq7egMw.png)

#### 2. Source Code & JavaScript Analysis

- Inspected the web application source code (`index.html`) and retrieved `app.js`.
- Discovered hardcoded AWS Cognito configuration details and the target DynamoDB table name:
- **Identity Pool ID:** `us-east-1:836c0949-292d-485b-b532-52d5ca7bb688`
- **Table Name:** `complimentary-GuestWellnessProfiles`

![](https://cdn-images-1.medium.com/max/1000/1*5SX1E5gwWXbSZpNSKTBDfQ.png)

#### 3. Exploiting Excessive Permissions & Flag Extraction

- Exploited over-privileged Cognito guest permissions to read the entire table contents instead of querying single assigned entries:
- Bash

> `_aws dynamodb scan --table-name complimentary-GuestWellnessProfiles_`

- Uncovered guest profiles containing sensitive information (emails, plain-text passwords) and the hidden flag inside the VIP guest notes.

![](https://cdn-images-1.medium.com/max/1000/1*ufPudI-VfG_lYgh6_uh6QQ.png)

#### Flag : THM{fr33_app_fr33_d4t4!}