S3 AccessDenied Troubleshooting Lab

## Objective
Simulated and resolved AWS S3 AccessDenied errors using IAM policies and AWS CLI.

## Services Used
- Amazon S3
- IAM
- AWS CLI

## Problem
IAM user could not upload files to S3 bucket.

## Root Cause
IAM user lacked required S3 permissions.

## Resolution
Attached AmazonS3FullAccess policy to the IAM user and verified successful upload.

## Skills Learned
- IAM troubleshooting
- S3 permissions
- AWS CLI
- Cloud security debugging
