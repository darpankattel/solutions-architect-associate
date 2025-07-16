# S3 Static Website CI/CD Lab - Quick Notes

## Overview
Set up automated deployment of static website to S3 using GitHub Actions.

## Prerequisites - S3 Bucket Setup

### Bucket Configuration:
- **Public Access**: Disable "Block all public access"
- **ACL**: Enable "ACLs enabled" setting
- **Static Website Hosting**: Enable in bucket properties

### Bucket Policy:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*"
    }
  ]
}
```

## Implementation Steps

### 1. Code Preparation
- Create static website files
- Organize files in `./public/` directory

### 2. GitHub Repository Setup
```bash
# Create new repo on GitHub
git init
git add .
git commit -m "Initial commit"
git remote add origin <repo-url>
git push -u origin main
```

### 3. AWS Credentials Setup
**Location**: GitHub → Settings → Secrets and Variables → Actions → Repository Secrets

**Add these secrets:**
- `AWS_ACCESS_KEY_ID`: Your AWS access key
- `AWS_SECRET_ACCESS_KEY`: Your AWS secret key

### 4. GitHub Actions Workflow
**File**: `.github/workflows/main.yml`

```yaml
name: Upload Website

on:
  push:
    branches:
    - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout
      uses: actions/checkout@v2
      
    - name: Configure AWS Credentials
      uses: aws-actions/configure-aws-credentials@v2
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-1
        
    - name: Deploy static site to S3 bucket
      run: aws s3 sync ./public/ s3://your-bucket-name --delete
```

### 5. Deploy
```bash
git add .
git commit -m "Add GitHub Actions workflow"
git push origin main
```

## How It Works
1. **Push to main** → Triggers GitHub Actions
2. **Workflow runs** → Configures AWS credentials
3. **S3 sync** → Uploads files to S3 bucket
4. **Website updated** → Changes live instantly

## Key Points
- **`--delete` flag**: Removes files from S3 that don't exist locally
- **Automatic trigger**: Every push to main branch deploys
- **Secure**: AWS credentials stored as GitHub secrets
- **Fast**: Only changed files are uploaded

## Common Issues & Solutions
- **403 Errors**: Check bucket policy and public access settings
- **Workflow fails**: Verify AWS credentials and permissions
- **Files not updating**: Clear browser cache or check S3 sync output