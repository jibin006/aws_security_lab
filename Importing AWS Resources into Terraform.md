#  Importing AWS Resources into Terraform

This documentation outlines the steps to import existing AWS resources into Terraform for management. It includes two approaches: using the Terraform CLI command and the Terraform import block.

---

## Overview
When managing infrastructure with Terraform, you might encounter existing AWS resources not initially created with Terraform. To manage such resources, Terraform provides mechanisms to import them into your configuration and state files.

---

## Prerequisites
- AWS CLI installed and configured.
- Terraform installed.
- AWS credentials with sufficient permissions to view and manage IAM users.

---

## Step 1: Configure AWS CLI and List Users
1. Run the AWS CLI configuration command and provide your credentials:
   ```bash
   aws configure
   ```
2. List existing IAM users in the AWS account:
   ```bash
   aws iam list-users
   ```
   Example output:
   ```json
   {
       "Users": [
           { "UserName": "hgranger" },
           { "UserName": "hpotter" },
           { "UserName": "LabUser" }
       ]
   }
   ```

   Focus on non-LabUser accounts (e.g., `hgranger` and `hpotter`).

---

## Step 2: Terraform CLI Command Method
### Initialize Directory and Define Resources
1. Create a new directory (`import-resources`) and files:
   - `provider.tf`:
     ```hcl
     terraform {
       required_providers {
         aws = {
           source  = "hashicorp/aws"
           version = "5.63.1"
         }
       }
     }

     provider "aws" {
       region = var.aws_region
     }
     ```
   - `variables.tf`:
     ```hcl
     variable "aws_region" {
       type    = string
       default = "us-east-1"
     }
     ```
   - `main.tf`:
     ```hcl
     resource "aws_iam_user" "user_to_import" {}
     ```

2. Initialize the directory:
   ```bash
   terraform init
   ```

### Import Resource
1. Identify user details:
   ```bash
   aws iam get-user --user-name hpotter
   ```

2. Import user:
   ```bash
   terraform import aws_iam_user.user_to_import hpotter
   ```

3. View imported resource state:
   ```bash
   terraform state show aws_iam_user.user_to_import
   ```

4. Update `main.tf` to match state:
   ```hcl
   resource "aws_iam_user" "user_to_import" {
     name = "hpotter"
   }
   ```

   Add tags if required as per the state.
   ```hcl
   tags = {
              "Description": "Bucket used to practice importing resources into Terraform.",
              "cybr-lab": "auto-deployed"

   }  
   ```
5. Verify synchronization:
   ```bash
   terraform plan
   ```

---

## Step 3: Terraform Import Block Method
### Define Import Block
1. Create `imports.tf`:
   ```hcl
   import {
     to = aws_iam_user.user2
     id = "hgranger"
   }
   ```

2. Generate configuration:
   ```bash
   terraform plan --generate-config-out=generated_config.tf
   ```

3. Review and integrate generated configuration:
   ```hcl
   resource "aws_iam_user" "user2" {
     name = "hgranger"
     path = "/"
   }
   ```

4. Copy to `main.tf` and verify:
   ```bash
   terraform plan
   ```

---

## Notes
- Always review generated configuration before applying changes.
- Use the `terraform state` commands to inspect imported resources.

#create main,provider,variable.tf
#command to import 
#terraform import aws_s3_bucket.bucket cybrlab-import-bucket-014498641717
#try terraform plan and confirm no changes

---

## Conclusion
Terraform's import capabilities streamline the process of managing existing AWS resources. Both CLI and import block methods are effective depending on your workflow.
