# AWS S3 Enumeration Lab

## Overview
This lab focuses on learning Amazon S3 enumeration techniques using AWS CLI, understanding the intricacies of S3 permissions, and exploring the interaction between identity-based and resource-based policies.

## Prerequisites
- AWS CLI installed and configured
- Basic understanding of AWS services
- An AWS account with appropriate access keys

## Lab Objectives
- Learn S3 enumeration using AWS CLI
- Understand S3-specific CLI commands and their quirks
- Explore IAM permissions and their impact on S3 access
- Investigate resource-based policies in S3

## Detailed Steps

### 1. Initial Setup
```bash
aws configure [--profile <NAME>]
# Input the following:
AWS Access Key ID: AKIAT6...
AWS Secret Access Key: sa3CB4...
Default region name: us-east-1
Default output format: [Enter for None or type json]
```

### 2. IAM Enumeration
First, verify identity using:
```bash
aws sts get-caller-identity
```
<img width="465" alt="image" src="https://github.com/user-attachments/assets/62340403-c2e8-455b-a44e-ed8b193863f5" />

List user policies:
```bash
aws iam list-user-policies --user-name <username>
```
<img width="623" alt="image" src="https://github.com/user-attachments/assets/90bff800-4573-414f-aa71-ced832ba187e" />

Get specific policy details:
```bash
aws iam get-user-policy --user-name <username> --policy-name <policy-name>
```
<img width="907" alt="image" src="https://github.com/user-attachments/assets/c6d36e04-1d6a-4559-a8cd-d29561fcf2b9" />
<img width="356" alt="image" src="https://github.com/user-attachments/assets/9e32c649-4882-4933-88d1-fbf351120665" />

### 3. S3 Enumeration
List all buckets:
```bash
aws s3api list-buckets
```
<img width="438" alt="image" src="https://github.com/user-attachments/assets/e9b558c3-7fdc-4af7-b45a-4e1969fafb65" />

List objects in a bucket:
```bash
aws s3api list-objects-v2 --bucket <bucket-name>
```
<img width="551" alt="image" src="https://github.com/user-attachments/assets/9e51f066-d728-443e-bbbb-0e9e8f78cae8" />

Download objects:
```bash
aws s3api get-object --bucket <bucket-name> --key <object-name> <local-path>
```
<img width="583" alt="image" src="https://github.com/user-attachments/assets/ad320f15-85f5-43e7-a2db-4fe41a7c5caa" />

## Challenges Faced

### 1. S3 CLI Command Structure
**Challenge:** The AWS S3 CLI has multiple command sets (s3, s3api, s3control, s3outposts) which can be confusing for beginners.

**Solution:** 
- Focused on using `s3api` commands as they provide more detailed control and output
- Created a command reference sheet for commonly used operations
- Documented the differences between command sets for future reference

### 2. Permission Complexities
**Challenge:** Despite having appropriate IAM permissions, access to certain buckets was denied.

**Solution:**
- Discovered the importance of understanding both identity-based and resource-based policies
- Learned to check bucket policies using `get-bucket-policy`
- Documented the layered security approach AWS implements

### 3. S3 Resource ARN Formatting
**Challenge:** Different S3 actions required different ARN formats (with or without `/*`).

**Solution:**
- Created a reference guide for ARN formats:
  - Bucket-level actions: `arn:aws:s3:::bucket-name`
  - Object-level actions: `arn:aws:s3:::bucket-name/*`

## Key Learnings

1. **S3 CLI Peculiarities**
   - Different command sets serve different purposes
   - `list-objects-v2` is preferred over `list-objects`
   - Command structure varies between high-level and API-level operations

2. **AWS Security Layers**
   - Identity-based policies (IAM) are not sufficient alone
   - Resource-based policies can override IAM permissions
   - Always check both policy types when troubleshooting access issues

3. **Best Practices**
   - Always verify identity before starting enumeration
   - Use `--profile` to avoid credential confusion
   - Document ARN formats for different resource types

## Future Improvements
1. Create scripts to automate the enumeration process
2. Develop a more comprehensive permission checking mechanism
3. Build templates for common S3 enumeration scenarios

## Security Considerations
- Always ensure proper access controls are in place
- Regularly review and audit bucket policies
- Follow the principle of least privilege when setting up permissions

## Resources
- [AWS S3 Documentation](https://docs.aws.amazon.com/s3/index.html)
- [AWS CLI Command Reference](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3/index.html)
- [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
