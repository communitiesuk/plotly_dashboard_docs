# Publication of a DLUHC owned dashboard.

## Table of contents
1. [Buckets](#s3-buckets)
   1. [Creating a bucket](#creating-a-bucket)
   2. [Key terms](#key-terms)
   3. [links](#links)
2. [Useful Cloud Foundry commands](#useful-cloud-foundry-commands)

## s3 buckets 

### Key terms
SERVICE_NAME = Unique identifier for the bucket

APP_NAME = The application set up within GovUK PaaS

SERVICE_KEY = Unique identifier for external access credentials

---

### Creating a bucket 

The following are steps to take inorder to create a s3 bucket backing service within GovUK PaaS

Create a private bucket
```bash
cf create-service aws-s3-bucket default SERVICE_NAME
```

--- 

Bind the bucket to the application
```bash
cf bind-service APP_NAME SERVICE_NAME -c '{"permissions": "PERMISSION"}'
```

Permissions can be `read-write` or `read-only`. The dashboard should only have readonly permissions.

---

Create credentials to allow for outside access to the bucket

```bash
cf create-service-key SERVICE_NAME SERVICE_KEY -c '{"allow_external_access": true}'
```

Credentials for the created key can be accessed by the following command:

```bash
cf service-key SERVICE_NAME SERVICE_KEY
```

---
### Accessing objects in a private bucket
**In a new repository** create an environment.yml file containing:

```yml
name: REPOSITORY_NAME
channels:
  - conda-forge
  - nodefaults
dependencies:
  - python=3.9
  - boto3=1.20.24
  # Version is hardcoded to ensure uniformity no matter the platform.
```

---

Generate a presigned URL in a Python script:

``` python
import boto3
import requests
url = boto3.client('s3').generate_presigned_url(
ClientMethod='put_object', 
Params={'Bucket': 'BUCKET_NAME', 'Key': 'OBJECT_KEY'},
ExpiresIn=3600)

response=requests.put(url=url, data='test.csv')
```

---

### links
https://docs.cloud.service.gov.uk/deploying_services/s3/#bind-an-aws-s3-bucket-to-your-app

## Useful Cloud Foundry commands

Get environment variables:
```bash
cf env APP_NAME
```

---

get service key credentials
```bash
cf service-key SERVICE_NAME SERVICE_KEY
```