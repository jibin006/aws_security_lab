# AWS Secrets Manager Enumeration Lab - Security Assessment

## Overview
This lab focused on enumerating AWS Secrets Manager configurations and accessing stored secrets using AWS CLI. The exercise demonstrated both the power and potential security risks of access to Secrets Manager.

## Lab Environment
- Region: us-east-1
- Tools Used: AWS CLI, Base64 Decoder
- Duration: 30 minutes

## Security Objectives
- Understand Secrets Manager enumeration techniques
- Identify potential misconfigurations in secrets management
- Assess permission structures for secrets access
- Document potential security risks

## Technical Approach & Findings

### 1. Initial IAM Reconnaissance
Started with IAM enumeration to understand available permissions:
```bash
aws sts get-caller-identity
aws iam list-user-policies --user-name sm-enumeration
```

**Key Finding**: User had specific policy `AllowReadSecretsManager` with permissions to:
- List secrets
- Get secret values
- Describe secrets
- List secret versions
- Get resource policies

### 2. Permission Analysis
Analyzed the policy document to understand the scope of access:
```json
{
    "Action": [
        "secretsmanager:GetSecretValue",
        "secretsmanager:ListSecretVersionIds",
        "secretsmanager:GetResourcePolicy",
        "secretsmanager:DescribeSecret"
    ],
    "Resource": [
        "arn:aws:secretsmanager:us-east-1:*:secret:sm-enumerate-password*",
        "arn:aws:secretsmanager:us-east-1:*:secret:sm-enumerate-api-key*"
    ]
}
```

**Security Concern**: Resource pattern matching using wildcards could potentially grant access to unintended secrets.

### 3. Secrets Enumeration
Executed comprehensive secrets enumeration:
```bash
aws secretsmanager list-secrets
aws secretsmanager list-secret-version-ids --secret-id sm-enumerate-password
aws secretsmanager get-resource-policy --secret-id sm-enumerate-password
```

**Critical Findings**:
1. Two secrets identified:
   - `sm-enumerate-password`
   - `sm-enumerate-api-key`
2. No resource policies configured
3. Both secrets using default encryption key

### 4. Secret Value Extraction
Successfully retrieved secret values:
```bash
aws secretsmanager get-secret-value --secret-id sm-enumerate-password
aws secretsmanager get-secret-value --secret-id sm-enumerate-api-key
```

**Security Issue**: API key was base64 encoded but not encrypted, demonstrating inadequate protection.

## Security Issues Identified

1. **Missing Resource Policies**:
   - No additional access controls via resource policies
   - Reliance solely on identity-based policies

2. **Encryption Configuration**:
   - Default encryption keys used
   - Recommendation: Use customer-managed KMS keys

3. **Secret Storage Practices**:
   - Base64 encoding used instead of proper encryption
   - Readable secret values in JSON format

## Best Practices Implementation

1. **Access Control**:
   - Implement resource-based policies
   - Use specific resource ARNs instead of wildcards

2. **Encryption**:
   - Use customer-managed KMS keys
   - Implement encryption context

3. **Secret Management**:
   - Enable automatic rotation
   - Use specific resource names
   - Implement proper secret value encryption

## Challenges Encountered

1. **Permission Scope Understanding**:
   - Complex interaction between identity and resource policies
   - Solution: Systematic testing of each permission

2. **Secret Value Formats**:
   - Encoded values requiring additional processing
   - Solution: Implemented base64 decoding

3. **Version Management**:
   - Understanding current vs. previous versions
   - Solution: Used version ID tracking

## Security Tooling Used

### Essential Commands Reference
```bash
# Identity Verification
aws sts get-caller-identity

# IAM Enumeration
aws iam list-user-policies
aws iam get-user-policy

# Secrets Enumeration
aws secretsmanager list-secrets
aws secretsmanager list-secret-version-ids
aws secretsmanager get-resource-policy
aws secretsmanager get-secret-value
```

## Risk Mitigation Recommendations

1. **Access Control Enhancement**:
   - Implement strict resource policies
   - Use conditional access based on IP/VPC
   - Enable AWS CloudTrail logging

2. **Secret Management Improvements**:
   - Enable automatic rotation
   - Use customer-managed KMS keys
   - Implement proper secret value encryption

3. **Monitoring and Auditing**:
   - Set up CloudWatch alerts for secret access
   - Regular access review
   - Implement secret usage tracking

## Lessons Learned

1. Default configurations in Secrets Manager may not provide optimal security
2. Multiple layers of access control are necessary
3. Proper encryption practices beyond simple encoding are crucial
4. Regular secret rotation and monitoring are essential

## Next Steps
- Implement automated secret rotation
- Set up enhanced monitoring
- Create secret access audit system
- Implement proper encryption for all secrets

This lab demonstrated the importance of proper secrets management and the potential risks of misconfiguration in AWS Secrets Manager.
