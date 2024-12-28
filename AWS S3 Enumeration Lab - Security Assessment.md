# AWS S3 Enumeration Lab - Security Assessment

## Overview
This lab focuses on understanding Amazon S3 enumeration techniques from a security perspective, including identifying potential misconfigurations and implementing proper access controls. The lab demonstrates how to safely use AWS CLI to enumerate S3 buckets and objects while understanding the security implications of different access policies.

## Lab Objectives
- Perform S3 bucket and object enumeration using AWS CLI
- Understand identity-based vs resource-based policies in S3
- Identify potential security misconfigurations
- Practice secure handling of AWS credentials

## Security Challenge Scenarios

### Challenge 1: Initial Access Configuration
**Challenge:** Securely configuring AWS credentials for enumeration while following the principle of least privilege.
**Solution:** 
- Used AWS CLI's profile feature to isolate lab credentials
- Configured specific region (us-east-1) to prevent unintended cross-region access
```bash
aws configure --profile s3-enum-lab
AWS Access Key ID [None]: [REDACTED]
AWS Secret Access Key [None]: [REDACTED]
Default region name [None]: us-east-1
```

### Challenge 2: Permission Analysis
**Challenge:** Understanding the complex interplay between IAM permissions and bucket policies.
**Solution:** Implemented systematic enumeration approach:
1. Identified current IAM context:
```bash
aws sts get-caller-identity
```
2. Analyzed user policies:
```bash
aws iam list-user-policies --user-name [USERNAME]
aws iam get-user-policy --user-name [USERNAME] --policy-name [POLICY_NAME]
```

### Challenge 3: Access Control Complexity
**Challenge:** Encountered scenario where IAM permissions suggested access but resource policies prevented it.
**Solution:** 
- Discovered dual-layer access control mechanism
- Documented the importance of checking both:
  - Identity-based policies (IAM)
  - Resource-based policies (Bucket Policies)

## Technical Implementation

### S3 Enumeration Commands
Key commands used during the assessment:

1. List all buckets:
```bash
aws s3api list-buckets
```

2. List objects in specific bucket:
```bash
aws s3api list-objects-v2 --bucket [BUCKET_NAME]
```

3. Retrieve bucket policies:
```bash
aws s3api get-bucket-policy --bucket [BUCKET_NAME]
```

## Security Best Practices Identified

1. **Access Control Layering**
   - Implement both IAM policies and bucket policies
   - Use explicit deny statements in bucket policies for stronger security
   - Follow principle of least privilege when granting permissions

2. **Resource Policy Implementation**
   - Use resource-based policies as an additional security layer
   - Implement conditions in bucket policies to restrict access based on:
     - Principal ARN
     - Source IP
     - VPC endpoints

3. **Enumeration Security**
   - Use separate AWS profiles for different security contexts
   - Regularly rotate access keys
   - Monitor and log S3 enumeration activities

4. **Common Misconfigurations to Check**
   - Public bucket access settings
   - Overly permissive bucket policies
   - Lack of encryption
   - Missing access logging

## Lab Findings

1. **Access Pattern Analysis**
   - Successfully identified two S3 buckets
   - Discovered different access levels between buckets
   - Demonstrated impact of layered security controls

2. **Security Implications**
   - Confirmed effective resource-based policy blocks
   - Validated principle of defense in depth
   - Identified potential enumeration vectors

## Tools Used
- AWS CLI
- AWS IAM
- AWS S3 API

## Lessons Learned
1. Always verify both identity and resource-based permissions
2. Use `list-objects-v2` instead of deprecated `list-objects`
3. Implement proper error handling for access denied scenarios
4. Regular security assessments should include both IAM and resource policy reviews

## Future Enhancements
1. Implement automated S3 security scanning
2. Develop custom scripts for comprehensive bucket policy analysis
3. Create monitoring system for unauthorized enumeration attempts

## References
- [AWS S3 Security Best Practices](https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html)
- [S3 Bucket Policies](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-policies.html)
