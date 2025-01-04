# Finding S3 Security Misconfigurations Using Prowler - Detailed Analysis

## Project Overview
As part of my first month on the job, I was assigned to identify security misconfigurations in AWS S3 buckets. Using Prowler, an open-source security assessment tool, I discovered four specific misconfigurations that needed attention.

## Environment Setup and Initial Steps

### 1. AWS CLI Configuration
First, I configured AWS CLI with the provided credentials:
```bash
aws configure --profile prowler
```

### 2. Prowler Installation Options
I had several installation options:
- Brew installation (MacOS): `brew install prowler`
- Pip installation: `pip install prowler`
- Docker installation for a clean environment

### 3. Verifying Prowler Installation
I verified the installation by listing available S3 checks:
```bash
prowler aws --list-checks -s s3
```
<img width="623" alt="image" src="https://github.com/user-attachments/assets/a0686b82-e1d4-4466-8b44-888a07d08a22" />

### 4. Identifying the Target Bucket
Listed the S3 buckets in the environment:
```bash
aws s3 ls --profile prowler
```

## Security Assessment Execution

Ran the security assessment with:
```bash
prowler aws -s s3 --profile prowler
```
The -s flag stands for service, so you could also type --services. Here, we’re only running checks for the S3 service, but Prowler supports many more services.

## Result:

<img width="412" alt="image" src="https://github.com/user-attachments/assets/82b6cb86-6db9-44ba-847a-d03af60145fc" />

## Detailed Findings Analysis

<img width="412" alt="image" src="https://github.com/user-attachments/assets/42a7a11b-34c6-4b06-9620-737897b2d5ca" />


### Finding 1: Missing Account Level Public Access Block
- **Severity**: High
- **Status Detail**: "Block Public Access is not configured for the account 272281913033"
- **Impact**: Without account-level public access blocks, individual buckets could potentially be exposed to public access
- **Region**: us-east-1
- **Resource Type**: AwsS3Bucket
- **Resource ID**: arn:aws:s3:us-east-1:272281913033:account

### Finding 2: KMS Encryption Not Enabled
- **Severity**: Medium
- **Status Detail**: "Server Side Encryption is not configured with kms for S3 Bucket cybr-lab-s3-prowler-272281913033"
- **Impact**: Data at rest is not protected with AWS KMS encryption
- **Region**: us-east-1
- **Resource Type**: AwsS3Bucket
- **Resource ID**: arn:aws:s3:::cybr-lab-s3-prowler-272281913033

### Finding 3: MFA Delete Disabled
- **Severity**: Medium
- **Status Detail**: "S3 Bucket cybr-lab-s3-prowler-272281913033 has MFA Delete disabled"
- **Impact**: Critical bucket operations (delete) not protected by multi-factor authentication
- **Region**: us-east-1
- **Resource Type**: AwsS3Bucket
- **Resource ID**: arn:aws:s3:::cybr-lab-s3-prowler-272281913033

### Finding 4: Server Access Logging Disabled
- **Severity**: Medium
- **Status Detail**: "S3 Bucket cybr-lab-s3-prowler-272281913033 has server access logging disabled"
- **Impact**: No audit trail of bucket access and operations
- **Region**: us-east-1
- **Resource Type**: AwsS3Bucket
- **Resource ID**: arn:aws:s3:::cybr-lab-s3-prowler-272281913033

## Result Analysis Methods

### Using the Dashboard
```bash
prowler dashboard
```
<img width="383" alt="image" src="https://github.com/user-attachments/assets/78e94135-59a4-4cb7-88fe-aab6ca80b9a4" />
<img width="377" alt="image" src="https://github.com/user-attachments/assets/63abf0ee-51a4-42c9-bde8-ebf354bce3a5" />

- Accessed via local browser at http://127.0.0.1:11666/
- Provided visual representation of findings
- Showed pass/fail status and severity distribution

### Using JQ for JSON Analysis
```bash
cat prowler-output.json | jq '.[] | select(.status_code == "FAIL") | {
    status_code,
    status_detail,
    severity,
    description: .finding_info.desc,
    region: .resources[0].region,
    name: .resources[0].name,
    type: .resources[0].type,
    uid: .resources[0].uid,
    account_id: .cloud.account.uid
}'
```

### Alternative Python Script Method
Created a Python script to parse results for those without jq:
```python
import json

# Read and parse JSON file
with open('prowler-output.json', 'r') as file:
    data = json.load(file)

# Filter failed checks
filtered_results = []
for item in data:
    if item['status_code'] == 'FAIL':
        filtered_result = {
            'status_code': item['status_code'],
            'status_detail': item['status_detail'],
            'severity': item['severity'],
            'description': item['finding_info']['desc'],
            'region': item['resources'][0]['region'],
            'name': item['resources'][0]['name'],
            'type': item['resources[0]['type'],
            'uid': item['resources'][0]['uid'],
            'account_id': item['cloud']['account']['uid']
        }
        filtered_results.append(filtered_result)

print(json.dumps(filtered_results, indent=4))
```

## Assessment Statistics
- Total Checks Run: 14
- Failed Checks: 4 (28.57%)
- Passed Checks: 10 (71.43%)
- Muted Checks: 0 (0%)

## Severity Distribution of Failed Checks
- Critical: 0
- High: 1
- Medium: 3
- Low: 0

## Recommendations for Remediation

1. **High Priority**
   - Implement account-level S3 Block Public Access settings

2. **Medium Priority**
   - Enable AWS KMS encryption for the S3 bucket
   - Enable MFA Delete for critical bucket operations
   - Configure server access logging for audit purposes

## Lessons Learned
1. The importance of using security assessment tools to systematically identify misconfigurations
2. The value of understanding different severity levels for prioritizing fixes
3. The benefit of having multiple ways to analyze results (dashboard, JSON parsing, Python script)
4. The critical nature of S3 security best practices like encryption, access controls, and logging

## Next Steps
1. Present findings to management in order of severity
2. Create a remediation plan for each finding
3. Schedule follow-up assessment to verify fixes
4. Document the remediation process for future reference
