```markdown
# AWS CLI Learning Notes

## 1. Configuring AWS CLI with Access Keys

To configure the AWS CLI with access keys, run the following command:

```bash
aws configure --profile access
```

This will prompt you to enter your AWS Access Key, Secret Key, Region, and Output format.

---

## 2. Finding AWS CLI Profiles on Your Local Machine

To view the AWS profiles stored on your machine, you can open the following files:

- **Configuration File:**

```bash
vim ~/.aws/config
```

- **Credentials File:**

```bash
vim ~/.aws/credentials
```

---

## 3. Issuing CLI Commands

When you issue a command like `aws sts get-caller-identity`, AWS checks your credentials to identify your user and account information. 

### Example Command:

```bash
aws sts get-caller-identity
```

This returns details about the user who is authenticated:

```json
{
  "UserId": "AIDA....",
  "Account": "2722....",
  "Arn": "arn:aws:iam::2722...:user/cli"
}
```

It’s similar to running the `whoami` command on your local machine to see your current user identity.

If you are using a specific profile, you can add the `--profile` flag:

```bash
aws sts get-caller-identity --profile access
```

Example output:

```json
{
  "UserId": "AIDA....",
  "Account": "467...",
  "Arn": "arn:aws:iam::467...:user/get"
}
```

---

## 4. Switching to a Role in AWS CLI

To assume a role in AWS, use the following command to list the roles:

```bash
aws iam list-roles --profile access --query "Roles[?RoleName=='AWSCLIRole']"
```

Once you identify the role, add it to your AWS config file (`~/.aws/config`):

```ini
[profile AWSCLIRole]
role_arn = arn:aws:iam::115:role/AWSCLIRole
source_profile = access
```

Now, you can use the `AWSCLIRole` profile to get caller identity:

```bash
aws sts get-caller-identity --profile AWSCLIRole
```

Example output:

```json
{
  "UserId": "ARO......",
  "Account": "350.....",
  "Arn": "arn:aws:sts::350:assumed-role/AWSCLIRole/bot"
}
```

You can also verify the identity of the `access` profile:

```bash
aws sts get-caller-identity --profile access
```

Example output:

```json
{
  "UserId": "AIDA",
  "Account": "350",
  "Arn": "arn:aws:iam::350:user/get"
}
```

---

## 5. Accessing an S3 Bucket

To list all S3 buckets accessible under a particular profile (e.g., `AWSCLIRole`), use:

```bash
aws s3api list-buckets --profile AWSCLIRole
```

Example output:

```json
{
  "Buckets": [
    {
      "Name": "cy-data-bucket-",
      "CreationDate": "2021-02-04T14:52:41+00:00"
    }
  ],
  "Owner": {
    "DisplayName": "chri",
    "ID": "315c....."
  },
  "Prefix": null
}
```

---

## 6. Useful AWS CLI Commands

- **`aws iam get-user`:** Shows details about your IAM user or role.

```bash
aws iam get-user
```

- **`aws ec2 describe-instances`:** Provides information about all the EC2 instances in your AWS account.

```bash
aws ec2 describe-instances
```

- **`aws iam create-access-key --user-name YOUR_USERNAME`:** Creates a new access key for a specific IAM user.

```bash
aws iam create-access-key --user-name YOUR_USERNAME
```

---

## 7. GitGuardian for Third-Party Secret Detection

GitGuardian is a tool that helps identify exposed secrets (like AWS keys) in public GitHub repositories. It scans repositories, detects secrets, and sends alerts when such sensitive information is found.

---

## Conclusion

This document provides a brief overview of how to configure, use, and troubleshoot common AWS CLI commands. AWS CLI is a powerful tool for managing your AWS resources, and these basics are essential for interacting with your AWS environment efficiently.
```

