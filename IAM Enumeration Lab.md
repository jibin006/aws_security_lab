# AWS IAM Enumeration Lab - Security Assessment

## Overview
This lab focused on AWS Identity and Access Management (IAM) enumeration techniques, a critical skill for cloud security assessments. The exercise involved discovering and analyzing IAM users, groups, roles, and their associated permissions using AWS CLI commands.

## Lab Environment
- Region: us-east-1
- Tools Used: AWS CLI

## Security Objectives
- Understand IAM enumeration techniques for security assessments
- Identify potential security misconfigurations
- Document permission structures and access patterns
- Discover potential privilege escalation paths

## Key Enumeration Steps

### 1. Initial Access Verification
First, verified access using `get-caller-identity`, which is a crucial first step in any AWS security assessment:
```bash
aws sts get-caller-identity
```
<img width="548" alt="image" src="https://github.com/user-attachments/assets/075dddcf-4b90-40dc-bc5d-af0b5ed3cb97" />

**Security Note**: This command cannot be blocked by IAM policies, making it a reliable starting point for security assessments.

### 2. User Enumeration
Discovered all IAM users in the account:
```bash
aws iam list-users
```
<img width="628" alt="image" src="https://github.com/user-attachments/assets/ff73609a-cb40-4ec2-90b3-e19af52bd564" />

**Findings**:
- Identified 4 IAM users: Joel, Chris, Mary, and Mike
- All users created in the same timeframe
- All users follow consistent naming convention

### 3. Group Structure Analysis
Enumerated IAM groups and their memberships:
```bash
aws iam list-groups
aws iam list-groups-for-user --user-name iam-Enumeration-Joel
```
<img width="819" alt="image" src="https://github.com/user-attachments/assets/ce377f38-abb8-401e-b4c4-ac4de7cdc634" />
<img width="710" alt="image" src="https://github.com/user-attachments/assets/18edae36-ab03-4d8f-a19e-54a44104a0ce" />

**Discovered Structure**:
- Two distinct groups: iam-Enumeration-Developers and iam-Enumeration-Infrastructure
- Clear separation of duties between development and infrastructure teams

### 4. Permission Analysis
Analyzed permissions at multiple levels:
```bash
aws iam list-user-policies
aws iam list-group-policies
aws iam get-group-policy
```
<img width="713" alt="image" src="https://github.com/user-attachments/assets/c1aeecb0-c8c0-4d38-b85a-e54a2f08fe74" />
<img width="725" alt="image" src="https://github.com/user-attachments/assets/a74fe874-06a6-4b16-8850-0a090577983a" />
<img width="892" alt="image" src="https://github.com/user-attachments/assets/e2292d86-b9c0-4ffe-9015-953b0a65f75b" />
<img width="679" alt="image" src="https://github.com/user-attachments/assets/6a0cc19a-0eba-4073-9189-36275a6da6c0" />
<img width="685" alt="image" src="https://github.com/user-attachments/assets/06a73b4c-5b61-4fbc-bc8e-98bfb0fc1d4f" />
<img width="904" alt="image" src="https://github.com/user-attachments/assets/c131181f-2d2b-4887-a782-40703e230e74" />
<img width="616" alt="image" src="https://github.com/user-attachments/assets/d7e9d50a-e428-454c-8bad-027d9d1cc072" />
<img width="781" alt="image" src="https://github.com/user-attachments/assets/5178980d-ef4d-4473-9a58-d11e6508e19d" />


**Security Findings**:
1. Developer Group Permissions:
   - Limited IAM read-only actions
   - Resource-specific ListAccessKeys permissions
   - No write permissions identified

2. Infrastructure Group:
   - Additional read-only access to CloudFormation and S3
   - More extensive IAM enumeration capabilities

### 5. Role Assessment
Investigated IAM roles and trust relationships:
```bash
aws iam list-roles
aws iam list-role-policies
```
<img width="824" alt="image" src="https://github.com/user-attachments/assets/ef13f181-3b9b-440b-bda1-5c72aff968f2" />
<img width="658" alt="image" src="https://github.com/user-attachments/assets/ea6f440f-6863-41af-a24d-f7b70d9d9986" />
<img width="566" alt="image" src="https://github.com/user-attachments/assets/593f563a-2153-4768-94af-31999726db6e" />

**Critical Security Finding**: Discovered SupportRole with concerning permissions:
- Full S3 access (`s3:*`)
- Trust relationship limited to user "Mary"
- Potential privilege escalation target

## Security Concerns Identified

1. **Overly Permissive S3 Access**:
   - SupportRole has `s3:*` permissions
   - Recommendation: Implement least privilege principle with specific S3 actions

2. **Role Trust Relationships**:
   - Single user trust relationship could create bottleneck
   - Consider implementing role assumption based on group membership

3. **Access Key Management**:
   - All users appear to have access keys
   - Recommend regular key rotation and monitoring

## Best Practices Implementation

1. **Separation of Duties**:
   - Clear separation between developer and infrastructure groups
   - Distinct permission sets for different roles

2. **Resource-Level Permissions**:
   - ListAccessKeys limited to specific user ARNs
   - Demonstrates good principle of least privilege

3. **Access Management**:
   - Group-based access control implementation
   - Managed policies used for standard access patterns

## Challenges Encountered

1. **Permission Enumeration Complexity**:
   - Multiple layers of permissions (user, group, role)
   - Solution: Systematic enumeration approach and documentation

2. **Policy Analysis**:
   - Complex policy structures requiring detailed analysis
   - Solution: Created structured documentation approach

3. **Access Scope Understanding**:
   - Difficulty in determining effective permissions
   - Solution: Methodical testing of each permission type

## Security Improvement Recommendations

1. **Access Control**:
   - Implement stricter S3 bucket policies
   - Review and restrict broad `s3:*` permissions

2. **Role Management**:
   - Implement role assumption based on group membership
   - Add time-based access restrictions

3. **Monitoring**:
   - Implement CloudTrail logging for IAM activities
   - Set up alerts for sensitive role assumptions

## Tools and Commands Reference

### Essential IAM Enumeration Commands
```bash
# Basic Identity Check
aws sts get-caller-identity

# User Enumeration
aws iam list-users
aws iam get-user

# Group Enumeration
aws iam list-groups
aws iam list-groups-for-user
aws iam get-group

# Policy Enumeration
aws iam list-user-policies
aws iam list-group-policies
aws iam get-group-policy

# Role Enumeration
aws iam list-roles
aws iam list-role-policies
aws iam get-role-policy
```

## Conclusion
This lab provided valuable hands-on experience in AWS IAM enumeration techniques. The findings highlight the importance of regular security assessments and the implementation of least privilege principles in AWS environments.

