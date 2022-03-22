# Publication of a DLUHC owned dashboard.

## Table of contents
1. [Buckets](#s3-buckets)
   1. [Creating a bucket](#creating-a-bucket)
   2. [Using a private bucket](#using-a-private-bucket)
      1. [Connecting to s3](#connecting-to-s3)
      2. [Accessing a bucket](#accessing-a-bucket)
      3. [Uploading a file to a bucket](#uploading-a-file-to-a-bucket)
      4. [Accessing a file within the bucket](#accessing-a-file-within-the-bucket)
   3. [Key terms](#key-terms)
   4. [References](#references)
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
### Using a private bucket

#### Connecting to s3
In order to connect to s3, you will need an AWS access key and an AWS secret access key.

##### To get credentials for the APP to the backing service use:
```bash
cf env APP_NAME
```

These will be under VCAP_SERVICES

```bash
VCAP_SERVICES: {
  "aws-s3-bucket": [
    {
      "binding_guid": "GUID",
      "binding_name": null,
      "credentials": {
        "aws_access_key_id": "ACCESS_KEY_ID",
        "aws_region": "eu-west-2",
        "aws_secret_access_key": "SECRET_ACCESS_KEY",
        "bucket_name": "paas-s3-broker-prod-lon-XXXX",
        "deploy_env": ""
      },
      "instance_guid": "GUID",
      "instance_name": "SERVICE_NAME",
      "label": "aws-s3-bucket",
      "name": "SERVICE_NAME",
      "plan": "default",
      "provider": null,
      "syslog_drain_url": null,
      "tags": [
        "s3"
      ],
      "volume_mounts": []
    }
  ]
}
```

---

##### To get credentials for a service key use:
```bash
cf service-key SERVICE-NAME SERVICE-KEY
```
The output will look like:
```bash
{
   "aws_access_key_id": "ACCESS_KEY_ID",
   "aws_region": "eu-west-2",
   "aws_secret_access_key": "SECRET_ACCESS_KEY",
   "bucket_name": "paas-s3-broker-prod-lon-XXXX",
   "deploy_env": ""
}
```

---

Within python, we need to use the boto3 package to connect to the s3 backing service.

We use boto3 to create a resource object, which represent an object-oriented interface to Amazon Web Services (AWS).

```python
import boto3

s3_client=boto3.resource(
  's3',
  aws_access_key_id='ACCESS_KEY_ID',
  aws_secret_access_key='SECRET_ACCESS_KEY', 
  region_name='eu-west-2'
)
```

---

#### Accessing a bucket

Using the resource object, we can connect to a given bucket using the following:

```python
bucket=s3_client.Bucket(name='paas-s3-broker-prod-lon-XXXX')
```

---

#### Uploading a file to a bucket

Once connected to a bucket, we can then manage files within it.

```python
bucket.upload_file(key='mykey.txt', filename='mykey.txt')
```

---

#### Accessing a file within the bucket

As we are no longer wanting to store CSV's within the repo, we need to make a request to our s3 backing service to get out data.
Pandas ```read_csv(...)``` is able to read directly from a file-like object such as our expected response from S3, which we get using ```s3.Object(...).get()['Body]```.

```python
response_content = s3.Object('paas-s3-broker-prod-lon-XXXX', "mykey.txt").get()['Body']

df = pd.read_csv(response_content)
```

---

### References
https://docs.cloud.service.gov.uk/deploying_services/s3/#bind-an-aws-s3-bucket-to-your-app

https://boto3.amazonaws.com/v1/documentation/api/latest/guide/quickstart.html

---

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