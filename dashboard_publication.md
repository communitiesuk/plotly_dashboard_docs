# Publication of a DLUHC owned dashboard.

## Table of contents
1. [Creating a S3 bucket](#creating-a-s3-bucket)
2. [Accessing a private S3 bucket](#using-a-private-bucket)
   1. [Connecting to s3](#connecting-to-s3)
   2. [Connecting to a bucket](#connecting-to-a-bucket)
   3. [Uploading a file to a bucket](#uploading-a-file-to-a-bucket)
   4. [Accessing a file within the bucket](#accessing-a-file-within-the-bucket)
3. [Setting up GitHub manual reviewers for deployment](#setting-up-github-manual-reviewers-for-deployment)
4. [Setting up Ip filtering](#setting-up-ip-filtering)
   1. [Creating the ip filtering application](#creating-the-ip-filtering-application)
   2. [Setting up Gov UK PaaS](#setting-up-gov-uk-paas)
5. [Key terms](#key-terms)
6. [References](#references)

## Creating an AWS S3 backing service


The following are steps to take inorder to create a s3 bucket backing service within GovUK PaaS

Create a private bucket
```bash
cf create-service aws-s3-bucket default SERVICE_NAME
```

--- 

Once the service has been created you will need to create credentials for the application to the bucket. 
This is done by binding the service to the application.

Bind the bucket to the application
```bash
cf bind-service APP_NAME SERVICE_NAME -c '{"permissions": "PERMISSION"}'
```

Permissions can be `read-write` or `read-only`. The dashboard should only have readonly permissions.

These credentials can be found by running ```cf env APP_NAME``` after binding.

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

**NOTE**

Having the environment variables set in this way will not work when using boto3. 
See [Accessing a private S3 bucket in python](#accessing-a-private-s3-bucket-in-python) 

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
## Accessing a private S3 bucket in Python

### Connecting to S3
In order to connect to S3, you will need an AWS access key and an AWS secret access key.

#### To get credentials for a service key use:
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

It is also possible to connect to an S3 bucket without providing the credentials in the python code.
The boto3 package will first look in the environment variables for credentials if not provided. E.g.:

```python
import boto3

s3_client = boto3.resource('s3')
```

Using environment variables for credentials requires the following environment variables to set on the server. 
See [Setting environment variables using cloudfoundry](#setting-environment-variables-using-cloudfoundry).

```
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_REGION
```

---

#### Setting environment variables using cloudfoundry

```bash
cf set-env APP_NAME ENVIRONMENT_VARIABLE VALUE
```

### Connecting to a bucket

Using the resource object, we can connect to a given bucket using the following:

```python
bucket=s3_client.Bucket(name='paas-s3-broker-prod-lon-XXXX')
```

---

### Uploading a file to a bucket

Once connected to a bucket, we can then manage files within it.

```python
bucket.upload_file(key='mykey.txt', filename='mykey.txt')
```

---

### Accessing a file within the bucket

As we are no longer wanting to store CSV's within the repo, we need to make a request to our s3 backing service to get out data.
Pandas ```read_csv(...)``` is able to read directly from a file-like object such as our expected response from S3, which we get using ```s3.Object(...).get()['Body]```.

```python
response_content = s3.Object('paas-s3-broker-prod-lon-XXXX', "mykey.txt").get()['Body']

df = pd.read_csv(response_content)
```

If you have different buckets for different environments the bucket name should be set as an environment variable that can be retrieved through the code.
See [Setting environment variables using cloudfoundry](#setting-environment-variables-using-cloudfoundry).

#### Accessing the environment variable within Python:

```python
import os
bucket_name = os.environ("bucket_name").get()
response_content = s3.Object(bucket_name, "mykey.txt").get()['Body']
```

---

## Setting up GitHub manual reviewers for deployment
A manual review process is ideal to have for a production environment as it prevents code that is yet to be manually tested reaching the public.
This instead allows for time to be taken between a staging deployment, to make sure everything is working as expected.

1. From the GitHub repository click ```Settings```, then ```New environment```, provide a name for the environment and click ```Configure environment```.
![](images/github-envrionments.png)
2. Tick ```Required reviewers``` and enter usernames/team-name, then click ```Save protection rules```. GitHub Actions will then require someone from that group to approve the job with the environment set, before it can run.
3. Reference the new environment within the GitHub Actions workflow file at the job level e.g.:
```
jobs:
  deploy-production:
    name: 'Deploy to production Gov PaaS'
    runs-on: ubuntu-20.04
    environment: '<environment_name>'
    concurrency: production_environment
    needs: [deploy-staging]
```
4. From the GitHub repository click ```Settings```, then ```Environments```, click on the environment to configure and click ```Add secret```.
5. Secrets can be used within GitHub Actions using: ```${{secrets.<secret_name>}}```
   1. Secrets at the environment level will take precedence over repository level secrets. 

### Setting up manual reviewers from different groups

If your application requires approval from two groups e.g. Approval from a product manager and an approval from the development team,
follow these instructions:

1. Create separate environments for each of the groups you require approval from. See [Setting up GitHub manual reviewers for deployment](#setting-up-github-manual-reviewers-for-deployment).
2. Add the required reviewers as appropriate. e.g. Developers in the developer environment and product managers in product manager environment.
3. Within the GitHub action, create a job for each of the environments e.g.:
```yml
jobs:
   tech_approval:
    name: 'Tech approval'
    environment: 'prod-tech'
    runs-on: ubuntu-20.04
    steps:
      - name: 'Tech approved'
        run: echo 'Technical approved'
   
   product_approval:
    name: 'Product manager approval'
    environment: 'prod-product'
    runs-on: ubuntu-20.04
    steps:
      - name: 'Product manager approved'
        run: echo 'Product manager approved'
```
4. For the job that you require manual approval on, add the ```needs: [tech_approval, product_approval]``` to that job. e.g.:
```yml
jobs:
  deploy-production:
    name: 'Deploy to production Gov PaaS'
    runs-on: ubuntu-20.04
    needs: [product_approval, tech_approval]
```

---

## Setting up Ip filtering

Due to how Gov UK PaaS works, it is not possible to enable IP filtering alongside basic authentication.
It's not possible to set up two routing services on the same hostname meaning only one or the other can be used.
It is possible however to implement these checks into your application code if required. We will not go through that here.

### Creating the ip filtering application

In order to provide ip filtering the following application can be downloaded from github:

```bash
git clone https://github.com/alphagov/paas-ip-authentication-route-service.git
```

Follow the read me for that application on how to add ip addresses you wish to allow access to.

### Setting up Gov UK PaaS

Follow the instructions in the readme on how to push the application up to Gov UK PaaS.

Once the application is on the PaaS, it will need to be set up as a route service using the following commands:

```bash
cf create-user-provided-service SERVICE_INSTANCE -r ROUTE_SERVICE_URL
cf bind-route-service DOMAIN SERVICE_INSTANCE --hostname HOSTNAME
```

---

### Key terms
SERVICE_NAME = Unique identifier for the bucket.

APP_NAME = The application set up within GovUK PaaS.

SERVICE_KEY = Unique identifier for external access credentials.

SERVICE_INSTANCE = A unique name for a service.

ROUTE_SERVICE_URL = The url of the route service endpoint. An example of this is ```https://my-basic-auth-service-app.london.cloudapps.digital```

HOSTNAME = The host or app name assigned to an app.

---

## References
https://docs.cloud.service.gov.uk/deploying_services/s3/#bind-an-aws-s3-bucket-to-your-app

https://boto3.amazonaws.com/v1/documentation/api/latest/guide/quickstart.html

https://cloudlumberjack.com/posts/github-actions-approvals/ 
