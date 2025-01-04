# Automated SSH Security Group Remediation with AWS Config

## Project Overview
I implemented an automated security solution to detect and fix exposed SSH ports in AWS security groups. This project taught me how to use AWS Config and Systems Manager (SSM) to automatically fix security issues.

## The Problem I Solved
Many EC2 instances had SSH ports (port 22) open to the world (0.0.0.0/0), which is a major security risk. I needed to:
1. Find these security risks automatically
2. Fix them without manual intervention
3. Verify the fixes worked

## Implementation Steps & Challenges

### 1. Setting Up AWS Config

**What I Did:**
- Set up AWS Config service
- Created a 'restricted-ssh' rule
- Configured initial monitoring

**Challenge Faced:**  
Access denied errors kept popping up in the console.

**Solution:**  
Learned this was normal with least-privilege access! Instead of getting frustrated, I focused on the specific permissions I needed for the task.

### 2. Testing with a "Bad" Security Group

**What I Did:**
- Created an EC2 instance with intentionally bad security
- Opened SSH to 0.0.0.0/0 (the wrong way!)
- Used this as my test case

**Challenge Faced:**  
Config rule initially showed compliant even with the bad security group.

**Solution:**
- Waited a few minutes
- Used the "Re-evaluate" button
- Learned about AWS Config's evaluation timing

### 3. Setting Up Automated Fixes

**What I Did:**
Created an automated remediation using:
- AWS Systems Manager
- AWS-RestrictIncomingTraffic document
- IAM role for permissions

**Challenge Faced:**  
Needed the right IAM role permissions.

**Solution:**
Created a focused IAM policy:
```json
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Action": [
            "ssm:StartAutomationExecution",
            "ssm:GetAutomationExecution",
            "ec2:DescribeSecurityGroups",
            "ec2:RevokeSecurityGroupIngress"
        ],
        "Resource": ["*"]
    }]
}
```

### 4. Testing the Solution

**What I Did:**
- Triggered the remediation
- Monitored the changes
- Verified security group updates

**Challenge Faced:**  
Status updates were slow to appear.

**Solution:**
- Built in refresh checks
- Added verification steps
- Learned to be patient with AWS eventual consistency

## Cool Things I Learned

1. **AWS Config Power:**
   - Automatically finds security issues
   - Can fix problems without human help
   - Works across multiple resources

2. **Security Best Practices:**
   - Never open SSH to 0.0.0.0/0
   - Automate security fixes
   - Always verify remediation

3. **Automation Tips:**
   - Use SSM for automated fixes
   - Build in verification steps
   - Plan for AWS timing delays

## Practical Tips from Experience

1. **When Setting Up Config:**
   - Start with one rule
   - Test thoroughly
   - Check permissions first

2. **For Remediation:**
   - Test on non-production first
   - Verify changes manually
   - Keep IAM permissions tight

3. **For Monitoring:**
   - Use multiple verification methods
   - Don't trust first status update
   - Build in wait times

## What I Would Do Differently

1. Set up monitoring before creating test cases
2. Create better verification procedures
3. Document AWS timing expectations
4. Add more detailed logging

## Future Improvements

1. Add more security group rules
2. Implement better reporting
3. Add notification system
4. Create dashboard for compliance

## Key Lessons Learned

1. **Automation is Your Friend:**
   - Manual fixes don't scale
   - Automated fixes are consistent
   - Worth the setup time

2. **Patience Required:**
   - AWS services take time to sync
   - Multiple checks are important
   - Don't assume instant updates

3. **Testing Matters:**
   - Test in safe environments
   - Verify every step
   - Document what works

## Tools Used
- AWS Config
- AWS Systems Manager
- AWS IAM
- AWS EC2
- Security Groups

## Conclusion
This project taught me that good security can be automated! While setting up AWS Config and SSM took some time, having automatic security fixes is worth the effort. The key is patience, thorough testing, and good verification procedures.

Remember: Security is an ongoing process, and automation is your best friend!
