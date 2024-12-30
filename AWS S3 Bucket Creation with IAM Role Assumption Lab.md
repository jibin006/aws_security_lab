# AWS S3 Bucket Creation with IAM Role Assumption Lab

## Overview
This lab focuses on assuming IAM roles to interact with AWS S3 using temporary credentials instead of long-term credentials. The lab demonstrates AWS security best practices by showing how to switch from IAM User credentials to IAM Role credentials for S3 operations.

## Prerequisites
- AWS CLI installed and configured
- Python and pip (for optional console access)
- Basic understanding of AWS IAM and S3 services
- AWS account with appropriate access keys

## Lab Objectives
- Learn how to assume IAM roles using AWS CLI
- Configure and use temporary credentials
- Create S3 buckets and upload objects using assumed role
- (Optional) Access AWS Console using assumed role credentials

## Detailed Steps

### 1. Initial AWS CLI Configuration
```bash
# Configure with IAM User credentials
aws configure --profile s3
AWS Access Key ID: [Your-Access-Key]
AWS Secret Access Key: [Your-Secret-Key]
Default region name: us-east-1
Default output format: [Enter for None]
```

### 2. Role Assumption Process
```bash
# Verify current identity
aws sts get-caller-identity --profile s3

# List and find assumable role
aws iam list-roles --query "Roles[?RoleName=='AssumableS3Role']" --profile s3

# Assume the role
aws sts assume-role --role-arn arn:aws:iam::[Account-ID]:role/AssumableS3Role --role-session-name S3Role --profile s3
```

### 3. Configure Temporary Credentials
```bash
# Configure new profile with temporary credentials
aws configure --profile s3role

# Set the session token
aws configure set aws_session_token "[Session-Token]" --profile s3role
```

### 4. S3 Operations with Assumed Role
```bash
# List buckets
aws s3 ls --profile s3role

# Create bucket
aws s3api create-bucket --bucket our-new-bucket-cybr --profile s3role

# Upload file
aws s3 cp example.txt s3://our-new-bucket-cybr --profile s3role
```

### 5. (Optional) AWS Console Access
```bash
# Install awsume
pip install awsume
awsume-configure

# Install console plugin
pip3 install awsume-console-plugin

# Generate console access URL
awsume s3role -cl
```

## Challenges Faced

### 1. Managing Multiple Credential Sets
**Challenge:** Juggling between IAM user credentials and temporary role credentials.

**Solution:** 
- Implemented clear profile naming convention (s3 vs s3role)
- Documented each credential configuration step
- Verified identity after each credential change using `get-caller-identity`

### 2. Session Token Management
**Challenge:** Temporary credentials require additional session token configuration.

**Solution:**
- Created separate step for session token configuration
- Documented the importance of including `--profile` flag
- Implemented verification steps after token configuration

### 3. Permission Verification
**Challenge:** Understanding which operations were allowed under user vs. role credentials.

**Solution:**
- Tested same commands with both profiles
- Documented access denied scenarios
- Created clear examples of permission differences

## Key Learnings

1. **IAM Role Benefits**
   - Temporary credentials enhance security
   - Role assumption provides granular access control
   - Clear separation between long-term and temporary access

2. **AWS CLI Profile Management**
   - Importance of proper profile configuration
   - Session token requirements for temporary credentials
   - Profile switching for different permission sets

3. **Security Best Practices**
   - Using roles instead of long-term credentials
   - Regular credential rotation through temporary access
   - Proper permission verification and testing

## Future Improvements
1. Automate credential rotation process
2. Create scripts for common role assumption patterns
3. Implement role assumption monitoring
4. Add error handling for expired credentials

## Security Considerations
- Always verify identity after assuming roles
- Monitor and audit role assumptions
- Regularly rotate credentials
- Use minimum required permissions
- Set appropriate session durations

## Troubleshooting Tips
1. Always include `--profile` flag with commands
2. Verify credentials after configuration
3. Check session token expiration
4. Confirm role ARN before assumption
5. Validate permissions after assumption

## Resources
- [AWS IAM Roles Documentation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html)
- [AWS CLI Configuration Guide](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)
- [AWS STS AssumeRole API](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html)
- [AWS S3 Best Practices](https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html)
