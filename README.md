# Flaws cloud - walkthrough ☁️
:shipit: This is my version of [Flaws Cloud](https://flaws.cloud) challanges solution *(Level1-Level7)* 

![Home page intro](./img/0.jpg)
## Prerequisites
 > 1. Any Linux distro
 > 2. AWS account -
 >   If you don't have a aws account, [Create New](https://portal.aws.amazon.com/billing/signup#/start/email)
 > 3. Any latest browser - Firefox, Chrome
 > 4. aws-cli tool - You can install using 
  > `sudo apt install awscli` or [Visit guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) for detailed installation
  
## Level: 1 - Enumerate AWS
- **Vulnerability Title :** Insecure s3 bucket permissions leads to information discloure at flaws.cloud
- **Description:**
- 
![level 1](./img/1.jpg)

>  This level is buckets of fun, see if you can find the first sub-domain.

This is the classic S3 bucket misconfigured permission issue. Anyone with a AWS account enumerate S3 bucket using AWS-cli tool.
On AWS you can set up S3 buckets with all sorts of permissions and functionality including using them to host static files. A number of people accidentally open them up with permissions that are too loose. Just like how you shouldn't allow directory listings of web servers, you shouldn't allow bucket listings. 

```bash
AWS-CLI commands used:
ls - to list bucket content
```

> aws s3 ls s3://flaws.cloud

![aws cli](./img/1-0.jpg)

We get **secret-dd02c7c.html** file, when we visit the file

![secret file](./img/1-2.jpg)

OR

We can also list the bucket content by visting [http://flaws.cloud.s3.amazonaws.com](http://flaws.cloud.s3.amazonaws.com)

![list browser](./img/1-1.jpg)

- **Mitigation:**

This issue can be mitigated by properly configuring s3 bucket permissions, regarding to keep it public or private.

If public, who can perform enumeration actions on the bucket like `ls, cp, mv` etc

By default, S3 buckets are private and secure when they are created. To allow it to be accessed as a web page, I had turn on "Static Website Hosting" and changed the bucket policy to allow everyone "s3:GetObject" privileges, which is fine if you plan to publicly host the bucket as a web page. But then to introduce the flaw, I changed the permissions to add "Everyone" to have "List" permissions.

![found level 2](./img/1-2.jpg)
---

## Level: 2 - Insecure s3 bucket 
- **Vulnerability Title :** Insecure s3 bucket permissions leads to information discloure
- **Description:**
 
![level 2](./img/2.0.jpg)
  > The next level is fairly similar, with a slight twist. You're going to need your own AWS account for this. You just need the free tier. 

After we get our AWS-CLI installed, we can configure awscli to use our account. Refer to configure [AWS-cli](https://www.youtube.com/watch?v=BzzCIsjrE7U)

Lets see if we can access the bucket with aws-cli 
> aws s3 ls s3://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud/

> aws s3 cp s3://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud/secret-e4443f.html . && cat secret-e4443f.html

![aws cli](./img/2.jpg)

we get next level url i.e., [http://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/](http://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/)

![level 3](./img/2-1.jpg)

- **Mitigation:**

Similar to opening permissions to "Everyone", people accidentally open permissions to "Any Authenticated AWS User". They might mistakenly think this will only be users of their account, when in fact it means anyone that has an AWS account. 
 *Only open permissions to specific AWS users.*

---

## Level: 3 - S3 bucket authentication AWS users 
- **Vulnerability Title :** Leaked AWS credentials are not rolled-out 
- **Description:**

![level 3](./img/3.0.jpg)
  
  The next level is fairly similar, with a slight twist. Time to find your first AWS key! I bet you'll find something that will let you list what other buckets are.
```bash
AWS command used:
ls - to list bucket content
cp - to copy file from bucket or to bucket
--recursive flag - to copy all files from directory
```
We also need `git` command line tool to read git commit made, which leaked AWS credentials
```bash
Git command used:
log - to log all the commits made
revert - to revert to any other commit
```

> aws s3 ls s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud 

We find a .git file, which indicates that the bucket is a github reposiroty, so to enumerate we will copy entire bucket in our local machine
> aws s3 cp s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud ./level3 --recursive


![git repo](./img/3-1.jpg)

After we have copied all the files in our local machine directory, we can use `git` commands to enumerate the repo

> cd ./level2

> git log

![git repo](./img/3-3.jpg)

As we can see, we got a commit saying "accidentally added something I shouldn't have"

We will revert back to the git to read what was added
> git revert b64c8dcfa8a39af06521cf4cb7cdce5f0ca9e526

![git repo](./img/3-4.jpg)

and read the content of the file **access_keys.txt**
> cat access_keys.txt 

![git repo](./img/3-5.jpg)
  
To get the Level 4 URL we need to add the aws configuration credentials to `aws config` file
> aws config --profile level3

Enter the Access_key and Secter_Key & Enumerate the bucket

We can enumerate all s3 buckets that user has permissions to vist using, 
> aws s3 ls

![aws ls](./img/4-0.jpg)

- **Mitigation:**
People often leak AWS keys and then try to cover up their mistakes without revoking the keys. You should always revoke any AWS keys (or any secrets) that could have been leaked or were misplaced. Roll your secrets early and often. 

Always roll your secrets if you suspect they were compromised or made public or stored or shared incorrectly. Roll early, roll often. Rolling secrets means that you revoke the keys (ie. delete them from the AWS account) and generate new ones. 

---

## Level: 4 - EC2 snapshot
- **Vulnerability Title :** Public EC2 snapshot with server credentials
- **Description:**

![level 4](./img/4.jpg)
  For the next level, you need to get access to the web page running on an EC2 at 4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud

It'll be useful to know that a snapshot was made of that EC2 shortly after nginx was setup on it. 

We cannot access next level without solving this challenge

![access denied 5](./img/4-1.jpg)

At the [http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/](http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/) we need login credentials

![access denied 5](./img/4-2.jpg)

So we will start with what we have!
To access EC2 instance snapshot, we need some information like user-identity number, snapshot-id:
> aws sts get-caller-identity --profile level3 

![access denied 5](./img/4-3.jpg)
> aws ec2 describe-snapshots --owner-ids 975426262029 --query 'Snapshots[]' --region us-west-2 --profile level3

![access denied 5](./img/4-4.jpg)

Now with all the required information, we will create a volume in our AWS account, and later attach the same volume with our instance
> aws ec2 creat-volume --availability-zone us-west-2a --region us-west-2 -snapshot-id snap-0b49342abd1bdcb89 

![access denied 5](./img/4-5.jpg)

![access denied 5](./img/4-6.jpg)
Now to use the volume, we need to create a EC2 instance and attach above created volume to our instance

![access denied 5](./img/4-7.jpg)

We have created the instance with vulnerable voulme attached, so start and instance and connect 

![access denied 5](./img/4-8.jpg)

To get the information from the volume, we need to mount it on our instance,
> sudo mount /dev/xvdb1 /mnt

![access denied 5](./img/4-9.jpg)

After we have mounted the volume, we need to find the credentials
> cd /mnt/home/ubuntu

> cat setupNginx.sh

![access denied 5](./img/4-10.jpg)

`htpasswd -b /etc/nginx/.htpasswd flaws nCP8xigdjpjyiXgJ7nJu7rw5Ro68iE8M`

Login using the credentials found and get to the next level

![access denied 5](./img/4-11.jpg)

- **Mitigation:**

AWS allows you to make snapshots of EC2's and databases (RDS). The main purpose for that is to make backups, but people sometimes use snapshots to get access back to their own EC2's when they forget the passwords. This also allows attackers to get access to things. Snapshots are normally restricted to your own account, so a possible attack would be an attacker getting access to an AWS key that allows them to start/stop and do other things with EC2's and then uses that to snapshot an EC2 and spin up an EC2 with that volume in your environment to get access to it. Like all backups, you need to be cautious about protecting them.

---

## Level: 5 - AWS Magic number
- **Vulnerability Title :** SSRF vulnerability at /proxy/ endpoint leads to internal files disclosure
- **Description:**

![level 5](./img/5-1.jpg)
  This EC2 has a simple HTTP only proxy on it. Here are some examples of it's usage:

    http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/flaws.cloud/
    http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/summitroute.com/blog/feed.xml
    http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/neverssl.com/ 

See if you can use this proxy to figure out how to list the contents of the level6 bucket at level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud that has a hidden directory in it. 

When we visit http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/flaws.cloud/, we will be provided with the content of `flaws.cloud`

Similarly, when we visit http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/neverssl.com/ , we get content of `neverssl.com`

This flow indicated that the backend logic of the application is taking user input after `/proxy/` endpoint and sending the GET request to the provided URL

![level 5](./img/5-3.jpg)

So lets see the request in intercepting proxy (Burp Suite)

![level 5](./img/5-4.jpg)

the response we get

![level 5](./img/5-5.jpg)

So lets try if we can send the request to internal IP, in AWS the internal IP address is 169.254.169.254 

![level 5](./img/5-6.jpg)

We get the content of `iam` user

Add the `AccessKeyId and SecretKeyId and Token` in `~/.aws/credentials --profile level5` file

![level 5](./img/5-7.jpg)

So, now we will try to access the files in s3 bucket

> aws --profile level5 s3 ls level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud

![level 5](./img/5-8.jpg)
> Visit http://level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud/ddcc78ff
  
- **Mitigation:**
The IP address 169.254.169.254 is a magic IP in the cloud world. AWS, Azure, Google, DigitalOcean and others use this to allow cloud resources to find out metadata about themselves. Some, such as Google, have additional constraints on the requests, such as requiring it to use `Metadata-Flavor: Google` as an HTTP header and refusing requests with an `X-Forwarded-For` header. AWS has recently created a new IMDSv2 that requires special headers, a challenge and response, and other protections, but many AWS accounts may not have enforced it. If you can make any sort of HTTP request from an EC2 to that IP, you'll likely get back information the owner would prefer you not see. 

Ensure your applications do not allow access to 169.254.169.254 or any local and private IP ranges. Additionally, ensure that IAM roles are restricted as much as possible.
---

## Level: 6 - AWS Policies
- **Vulnerability Title :** Insecure s3 bucket permissions leads to information discloure
- **Description:**

![level 6](./img/6-0.jpg)

  For this final challenge, you're getting a user access key that has the SecurityAudit policy attached to it. See what else it can do and what else you might find in this AWS account.

Access key ID: AKIAJFQ6E7BY57Q3OBGA
Secret: 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

Need a hint? Go to Hint 1

We are given AWS credentials and we have to find a URL to complete the challenge

We will start with adding the credentials in the `~/.aws/credentials/`

> aws --profile level6 iam list-attached-user-policies --user-name Level6

![level 6](./img/6-1.jpg)
> aws --profile level6 iam get-policy --policy-arn arn:aws:iam::975426262029:policy/list_apigateways 

![level 6](./img/6-2.jpg)
> aws --profile level6 iam get-policy-version --policy-arn arn:aws:iam::975426262029:policy/list_apigateways --version-id v4

![level 6](./img/6-3.jpg)
> aws --region us-west-2 --profile level6 lambda list-functions

![level 6](./img/6-4.jpg)
> aws --region us-west-2 --profile level6 lambda get-policy --function-name Level6

![level 6](./img/6-5.jpg)

We get the URL [s33ppypa75.execute-api.us-west-2.amazonaws.com/Prod/level6](https://s33ppypa75.execute-api.us-west-2.amazonaws.com/Prod/level6)

After visiting the above url, we are provieded with next challenge URL at is 

![level 6](./img/6-6.jpg)

Our challege END url is [http://theend-797237e8ada164bf9f12cebf93b282cf.flaws.cloud/d730aa2b/](http://theend-797237e8ada164bf9f12cebf93b282cf.flaws.cloud/d730aa2b/)
  
- **Mitigation:**

It is common to give people and entities read-only permissions such as the SecurityAudit policy. The ability to read your own and other's IAM policies can really help an attacker figure out what exists in your environment and look for weaknesses and mistakes.

Don't hand out any permissions liberally, even permissions that only let you read meta-data or know what your permissions are. 
---

## Level: 7 - The end
![The end](/img/6-7.jpg)

---
