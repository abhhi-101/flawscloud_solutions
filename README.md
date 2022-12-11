# flaws.cloud Walkthrough
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
  
>  This level is buckets of fun, see if you can find the first sub-domain.

This is the classic S3 bucket misconfigured permission issue. Anyone with a AWS account enumerate S3 bucket using AWS-cli tool.

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

---

## Level: 2 - Insecure s3 bucket 
- **Vulnerability Title :** Insecure s3 bucket permissions leads to information discloure
- **Description:**
  
  This level is buckets of fun, see if you can find the first sub-domain.

- **Step to find:** 

  
- **Mitigation:**

---

## Level: 3 - S3 bucket authentication AWS users 
- **Vulnerability Title :** Insecure s3 bucket permissions leads to information discloure
- **Description:**
  
  This level is buckets of fun, see if you can find the first sub-domain.
`
cp - to copy file from bucket or to bucket
`

- **Step to find:** 

  
- **Mitigation:**

---

## Level: 4 - EC2 snapshot
- **Vulnerability Title :** Insecure s3 bucket permissions leads to information discloure
- **Description:**
  
  This level is buckets of fun, see if you can find the first sub-domain.

- **Step to find:** 

---

## Level: 5 - AWS Magic number
- **Vulnerability Title :** Insecure s3 bucket permissions leads to information discloure
- **Description:**
  
  This level is buckets of fun, see if you can find the first sub-domain.

- **Step to find:** 

  
- **Mitigation:**

---

## Level: 6 - AWS Policies
- **Vulnerability Title :** Insecure s3 bucket permissions leads to information discloure
- **Description:**
  
  This level is buckets of fun, see if you can find the first sub-domain.

- **Step to find:** 

  
- **Mitigation:**

---

## Level: 7 - The end
![The end](/img/6-7.jpg)

---
