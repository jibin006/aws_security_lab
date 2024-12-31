# Learning S3 Versioning with AWS CLI

## Overview
In this hands-on project, I learned how to manage S3 bucket versioning using the AWS CLI. It was a great experience that taught me how to protect files from accidental deletions and keep track of file changes.

## What I Built
- Set up S3 bucket versioning using AWS CLI
- Managed multiple versions of files
- Learned how to recover previous versions
- Figured out how to suspend versioning when needed

## Prerequisites
What you'll need:
- AWS CLI installed on your computer
- AWS account with access keys
- Basic command line knowledge

## Step-by-Step Guide

### 1. Setting Up AWS Profile
First, I created a new AWS profile to keep things organized:

```bash
aws configure --profile s3
# Added my access key and secret key
# Used us-east-1 as the region
```

Quick Tip: Always verify your setup worked by running:
```bash
aws s3 ls --profile s3
```
<img width="434" alt="image" src="https://github.com/user-attachments/assets/3b45ecc1-4041-474f-9ceb-409faba0cd44" />


### 2. Enabling Versioning
This was my first challenge - figuring out the right command structure:

```bash
aws s3api put-bucket-versioning \
    --bucket YOUR-BUCKET-NAME \
    --versioning-configuration Status=Enabled \
    --profile s3
```
<img width="594" alt="image" src="https://github.com/user-attachments/assets/cdff7a88-d358-46bf-8d43-faaa47a774a6" />

Note: It can take up to 15 minutes for object versioning to propagate through AWS.

To check if it worked:
```bash
aws s3api get-bucket-versioning --bucket YOUR-BUCKET-NAME --profile s3
```
<img width="602" alt="image" src="https://github.com/user-attachments/assets/576419eb-1804-4675-937d-d65ff10b7f06" />

### 3. Working with File Versions
Here's what I learned about managing different versions:

1. Upload initial file:
<img width="512" alt="image" src="https://github.com/user-attachments/assets/314394ed-8aa4-47f6-a079-60304f704d57" />

```bash
echo "First version of my file" > test.txt
aws s3 cp test.txt s3://YOUR-BUCKET-NAME --profile s3
```
<img width="579" alt="image" src="https://github.com/user-attachments/assets/092d1740-649e-4f5c-9d36-f0041aaa2e07" />

2. Upload a new version:
```bash
echo "Updated version" > test.txt
aws s3 cp test.txt s3://YOUR-BUCKET-NAME --profile s3
```
<img width="630" alt="image" src="https://github.com/user-attachments/assets/756ec28b-4d38-4427-a6b8-edb9ba213d16" />

3. List all versions:
```bash
aws s3api list-object-versions --bucket YOUR-BUCKET-NAME --profile s3
```
<img width="590" alt="image" src="https://github.com/user-attachments/assets/4c650e2e-f68f-4ea2-bdbc-87822409ca33" />
<img width="535" alt="image" src="https://github.com/user-attachments/assets/f532d5b5-b9be-4584-b222-54001484cea9" />

We can see two objects named “quotes.txt” with different VersionIds, where the latest file we uploaded is identified by the flag "IsLatest": true while the other is set to false.

### 4. Retrieving Specific Versions
This was really cool - I could get any version of my file:

```bash
aws s3api get-object \
    --bucket YOUR-BUCKET-NAME \
    --key "test.txt" \
    --version-id "YOUR-VERSION-ID" \
    output.txt \
    --profile s3
```
<img width="610" alt="image" src="https://github.com/user-attachments/assets/8116b2fb-47ea-42ca-8eee-f5573edc667e" />
<img width="601" alt="image" src="https://github.com/user-attachments/assets/65987ecd-275d-40ba-b23e-b50900173ad2" />

For deleting,
<img width="636" alt="image" src="https://github.com/user-attachments/assets/67578cbe-2805-4217-9ba3-e25b4db6251e" />

### 5. Suspending Versioning

versioning can’t be disabled once it’s been enabled, however it can be suspended. 
Suspending means that new objects won’t have versioning, but any object uploaded while versioning was enabled will still be versioned.
When I needed to stop versioning (but not disable it completely):

```bash
aws s3api put-bucket-versioning \
    --bucket YOUR-BUCKET-NAME \
    --versioning-configuration Status=Suspended \
    --profile s3
```
<img width="626" alt="image" src="https://github.com/user-attachments/assets/453e3d73-c53b-40f8-a7e7-96dd47fa4e80" />
<img width="546" alt="image" src="https://github.com/user-attachments/assets/ff378afc-641a-4d83-9101-f9d5d1b31c62" />

## Challenges I Faced

1. **Version IDs**: Initially, I found it confusing to keep track of version IDs. Solution: I started keeping notes of important version IDs and their contents.

2. **Command Syntax**: The AWS CLI commands were tricky at first. Solution: I created a cheat sheet of commonly used commands for quick reference.

3. **Understanding Suspend vs. Disable**: Learned that you can't disable versioning once enabled - you can only suspend it. Important lesson about AWS S3 features!

## Best Practices I Learned

1. Always verify commands worked by checking the status afterward
2. Keep track of version IDs for important files
3. Test recovery procedures before you actually need them
4. Use meaningful file names to make version tracking easier

## Key Takeaways

1. S3 versioning is super helpful for preventing accidental deletions
2. The AWS CLI is powerful but needs careful attention to command syntax
3. It's important to understand the difference between suspending and disabling versioning
4. Version management can help save earlier versions of important files


## Resources That Helped Me
- AWS CLI Documentation
- AWS S3 Versioning Documentation
- Stack Overflow (for troubleshooting specific issues)

## Final Thoughts
This project really helped me understand how S3 versioning works. While the AWS CLI commands took some getting used to, the ability to track and recover file versions is totally worth it. I'd definitely recommend this to anyone working with important files in S3.

