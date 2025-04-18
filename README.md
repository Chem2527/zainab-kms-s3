# AWS  customer managed KMS + S3 Encryption Demo (with IAM Users)

## Step by step process

 - **step 1** Create a  customer managed KMS key
 - **step 2** Create and configure an S3 bucket
- **step 3** Controlling who can encrypt/decrypt via IAM users and key policies
-  **step 4** Using AWS CLI to test access and upload
- **step 5** Demonstrating encryption with a real-world scenario

## Prerequisites

- AWS account with admin access

- AWS CLI installed

- Text files to upload, e.g., secret.txt, secret2.txt

- Two IAM users to test encryption permissions:

- zainab

- zainab1

## Step 1: Create a Customer-Managed KMS Key
- Go to the AWS Key Management Service (KMS) console and create a new key.
- Key Settings
  - Key type: Symmetric (default; used for both encryption and decryption)
  - Key usage: Encrypt and Decrypt
  - Key material origin: KMS (Recommended)
  - Regionality: Single-Region (e.g., eu-north-1)
  - Alias (key name): zainab key
  - Save the KMS Key ARN — you'll need this for configuring the S3 bucket and CLI.
- Permissions
  - Key administrators (optional)
     - Can manage the key (enable, disable, delete, rotate)
     - Add yourself or any admin IAM role
- Key users:
  - Add:
  - zainab (only this user should be able to encrypt/decrypt using the key for now)

## step 2:  Create an S3 Bucket and Enable Encryption
- Go to the S3 Console and create a bucket named:   zainab252725

- During creation:

  - Region: Same as KMS key (e.g., eu-north-1)

  - Uncheck "Block all public access" only if needed for testing (not recommended in prod)

  - Enable default encryption:

  - Choose AWS Key Management Service key (SSE-KMS)

  - Choose "Choose from your AWS KMS keys"

  - Select your customer-managed key: zainab key

- any object uploaded to this bucket by default will be encrypted with your KMS key.

## Step 3:  Create IAM Users
- Create 2 users in IAM:

  - zainab

  - zainab1

- For both:

  - Enable programmatic access

  - Copy the Access Key and Secret Key

  - Attach no permissions initially
 
## Step 4:  Configure AWS CLI for Each User
- For zainab:

  - aws configure
    - Provide:

      - Access key

      - Secret key

      - Default region (e.g., eu-north-1)

- Try listing buckets:

- aws s3 ls
- You’ll get Access Denied → user has no S3 permissions yet.

- Grant zainab Full S3 Access
- Attach the policy: **AmazonS3FullAccess**

- Try again:
  - aws s3 ls
- You should now see zainab252725
  - Upload a file to the encrypted bucket:
    - aws s3 cp secret.txt s3://zainab252725/ --region eu-north-1
- Now Test With **zainab1** provide **AmazonS3FullAccess** policy
  - aws configure
    - Use credentials for zainab1.
    - aws s3 ls
- Now try to upload an encrypted file:
- aws s3 cp secret2.txt s3://zainab252725/ --region eu-north-1
    - You’ll get AccessDenied error from KMS:
    - zainab1 is not allowed to use the key zainab key

