Includes the labs I completed during my learning process.

1. AWS IAM Enumeration Lab - Security Assessment
2. AWS Secrets Manager Enumeration Lab - Security Assessment
3. AWS S3 Enumeration Lab - Security Assessment
4. AWS S3 Bucket Creation with IAM Role Assumption Lab
5. Encrypting S3 Data with SSE-KMS - CLI
6. S3 Versioning - AWS CLI
7. S3 Presigned URLs
8. Launching an EC2 instance

I'll summarize the key takeaways from each lab, focusing on important points to remember:

**1. Getting Started with AWS CLI**
- Always secure your AWS credentials
- Use named profiles for different AWS accounts/roles
- Test permissions with `aws sts get-caller-identity`
- Keep CLI version updated for latest features
- Use `--profile` flag to specify which credentials to use

**2. IAM Enumeration**
- Users, roles, and policies are core IAM components
- Check permissions before performing actions
- IAM users should follow least privilege principle
- Group users for easier permission management
- Always review IAM credentials and rotate them regularly

**3. Secrets Manager Enumeration**
- Don't store secrets in code or config files
- Use KMS for encryption of secrets
- Rotate secrets regularly
- Use resource-based policies to control access
- Check costs - Secrets Manager charges per secret stored

**4. S3 Enumeration**
- Bucket names must be globally unique
- Check bucket policies and ACLs
- Use `aws s3 ls` to list buckets and contents
- Pay attention to bucket region
- Look for public access settings

**5. S3 Buckets and Objects**
- Different ways to upload: Console, CLI, SDK
- Use role assumption for temporary access
- Check object ownership settings
- Understand bucket vs object permissions
- Use appropriate storage classes for cost efficiency

**6. S3 Encryption with SSE-KMS**
- Server-side encryption protects data at rest
- KMS keys need proper permissions
- SSE-KMS provides additional audit capability
- Use bucket keys to reduce KMS costs
- Remember encryption applies to new objects only

**7. S3 Versioning**
- Once enabled, can't be disabled (only suspended)
- Protects against accidental deletions
- Increases storage costs
- Each version has unique ID
- Delete markers don't delete the object

**8. S3 Presigned URLs**
- Time-limited access to private objects
- Set appropriate expiration times
- Anyone with URL can access object
- Maximum expiration: 7 days (CLI), 12 hours (Console)
- Great for temporary file sharing

**9. EC2 Instance Launch**
- Keep track of key pairs - they can't be recovered
- Set appropriate security group rules
- Use appropriate instance type for needs
- Remember to terminate unused instances
- EC2 Instance Connect vs SSH access

**Overall Key Principles:**
1. **Security First**
   - Always follow least privilege
   - Regularly rotate credentials
   - Encrypt sensitive data
   - Keep private keys secure

2. **Cost Management**
   - Monitor resource usage
   - Clean up unused resources
   - Choose appropriate service tiers
   - Use cost optimization features

3. **Access Control**
   - Use roles instead of long-term credentials
   - Implement proper permissions
   - Regular access reviews
   - Document access patterns

4. **Best Practices**
   - Use version control
   - Implement proper tagging
   - Regular backups
   - Monitor service limits

5. **Automation**
   - Use CLI for repetitive tasks
   - Create reusable scripts
   - Document procedures
   - Test in non-production first

These labs provides me a solid foundation in AWS security and infrastructure management. The key is to always think about security, cost, and efficiency when working with AWS services.
