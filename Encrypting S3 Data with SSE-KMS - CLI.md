# Encrypting S3 Data with SSE-KMS using AWS CLI

This guide demonstrates how to encrypt Amazon S3 objects using AWS Key Management Service (KMS) via the AWS Command Line Interface (CLI). You'll learn how to create a customer-managed KMS key, enable Server-Side Encryption with KMS (SSE-KMS) for an S3 bucket, and configure a Bucket Key to optimize costs.

## Prerequisites

- AWS CLI installed and configured
- Appropriate AWS permissions to manage KMS keys and S3 buckets
- An existing S3 bucket

## Configuration Steps

### 1. Configure AWS CLI Profile

Create a new AWS CLI profile for this setup:

```bash
aws configure --profile s3
AWS Access Key ID: [YOUR_ACCESS_KEY]
AWS Secret Access Key: [YOUR_SECRET_KEY]
Default region name: us-east-1
Default output format: [ENTER]
```

### 2. Create a Customer-Managed KMS Key

Create a new KMS key for S3 encryption:

```bash
aws kms create-key --description "KMS key for S3 SSE-KMS" --profile s3
```
<img width="553" alt="image" src="https://github.com/user-attachments/assets/11c608f5-082b-4bbf-930a-cb549e7f51a7" />

Create an alias for easier key management:

```bash
aws kms create-alias \
    --alias-name "alias/s3-key" \
    --target-key-id [YOUR_KEY_ID] \
    --profile s3
```

Note: Replace `[YOUR_KEY_ID]` with the KeyId from the create-key response.
<img width="770" alt="image" src="https://github.com/user-attachments/assets/ccb14ae5-facb-4142-b9fd-ff794b46070f" />

### 3. Configure S3 Bucket Encryption

<img width="310" alt="image" src="https://github.com/user-attachments/assets/198a3fd0-6b08-433a-8461-7264006ca1d1" />

First, check current encryption settings:

```bash
aws s3api get-bucket-encryption \
    --bucket [YOUR_BUCKET_NAME] \
    --profile s3
```
<img width="662" alt="image" src="https://github.com/user-attachments/assets/61eabb8d-c434-4721-9f2f-f9d78f37f96d" />

Create a configuration file (`config.json`) for SSE-KMS:

Use vim config.json

```json
{
  "Rules": [
    {
      "ApplyServerSideEncryptionByDefault": {
        "SSEAlgorithm": "aws:kms",
        "KMSMasterKeyID": "[YOUR_KEY_ID]"
      },
      "BucketKeyEnabled": true
    }
  ]
}
```
<img width="722" alt="image" src="https://github.com/user-attachments/assets/0716c699-582e-4870-84e9-37ac1ebca7b5" />

Apply the encryption configuration:

```bash
aws s3api put-bucket-encryption \
    --bucket [YOUR_BUCKET_NAME] \
    --server-side-encryption-configuration file://config.json \
    --profile s3
```
aws s3api put-bucket-encryption --bucket cybrlab-sample-bucket-272281913033 --server-side-encryption-configuration file://config.json --profile S3

<img width="647" alt="image" src="https://github.com/user-attachments/assets/0f184845-c9fd-497e-914b-311e31669351" />


### 4. Upload and Verify Encrypted Objects

Upload an object with SSE-KMS:

```bash
aws s3 cp [LOCAL_FILE] s3://[YOUR_BUCKET_NAME] --profile s3
```
<img width="575" alt="image" src="https://github.com/user-attachments/assets/9cbcd0e2-d1da-43e9-b9af-c38368068a31" />

For explicit key specification:

```bash
aws s3 cp [LOCAL_FILE] s3://[YOUR_BUCKET_NAME] \
    --sse aws:kms \
    --sse-kms-key-id [YOUR_KEY_ID] \
    --profile s3
```

Verify object encryption:

```bash
aws s3api get-object \
    --bucket [YOUR_BUCKET_NAME] \
    --key [OBJECT_KEY] \
    [OUTPUT_FILE] \
    --profile s3
```
<img width="650" alt="image" src="https://github.com/user-attachments/assets/e6442867-3506-4fe6-ae04-a8c2961ed5e3" />

## Key Features Implemented

- Custom KMS key creation
- KMS key alias management
- S3 bucket SSE-KMS configuration
- Bucket Key enablement for cost optimization
- Object-level encryption verification

## Cost Optimization

The implementation includes enabling Bucket Keys, which reduces the number of API calls to KMS and thereby lowers costs associated with SSE-KMS encryption.

## Security Considerations

- The KMS key permissions should be properly managed through key policies and IAM
- Bucket Keys help reduce exposure of your KMS keys
- SSE-KMS provides an additional layer of protection compared to SSE-S3 (AES256)

## Troubleshooting

If you encounter issues:
1. Verify your AWS CLI profile has the necessary permissions
2. Ensure the KMS key is enabled and accessible
3. Check that the bucket encryption configuration was applied successfully
4. Verify the correct key ID or ARN is being used in your commands

## References

- [AWS KMS Documentation](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html)
- [S3 Server-Side Encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/serv-side-encryption.html)
- [AWS CLI Command Reference](https://awscli.amazonaws.com/v2/documentation/api/latest/index.html)
