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

Create an alias for easier key management:

```bash
aws kms create-alias \
    --alias-name "alias/s3-key" \
    --target-key-id [YOUR_KEY_ID] \
    --profile s3
```

Note: Replace `[YOUR_KEY_ID]` with the KeyId from the create-key response.

### 3. Configure S3 Bucket Encryption

First, check current encryption settings:

```bash
aws s3api get-bucket-encryption \
    --bucket [YOUR_BUCKET_NAME] \
    --profile s3
```

Create a configuration file (`config.json`) for SSE-KMS:

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

Apply the encryption configuration:

```bash
aws s3api put-bucket-encryption \
    --bucket [YOUR_BUCKET_NAME] \
    --server-side-encryption-configuration file://config.json \
    --profile s3
```

### 4. Upload and Verify Encrypted Objects

Upload an object with SSE-KMS:

```bash
aws s3 cp [LOCAL_FILE] s3://[YOUR_BUCKET_NAME] --profile s3
```

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
