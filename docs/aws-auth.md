# AWS Authentication for the awss3 exporter

The awss3 exporter uses the default AWS credential provider chain.

Common options:

## Option A: Environment variables (simple)
Set these on the host running the collector:
- AWS_ACCESS_KEY_ID
- AWS_SECRET_ACCESS_KEY
- AWS_REGION

## Option B: AWS profile (shared config)
Create a profile in `~/.aws/credentials` and `~/.aws/config`,
then run the service with:
- AWS_PROFILE=yourprofile

## Option C: Instance/host role (best on AWS)
If running on EC2, attach an IAM role with permissions for the target buckets.
No keys required.
