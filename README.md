# Cloudera Data Platform (CDP) Environment Creation with Terraform  

During this how-to, we're going to create a CDP Environment using terraform.  We'll also be using the AWS CLI, but this is optional.

Step 1. Confirm Terraform Install

You can download terrform from Hashicorp's webuste [here](https://www.terraform.io/downloads.html)
After installation for your particular OS, you can confirm the install by running the below command.

```
terraform --version
```

You should see something like the following.

```
Terraform v0.12.23
```

Step 1a. (Optional) Confirm you have the aws cli installed
```
aws -- version
```

You should see something like the following.

```
aws-cli/2.0.1 Python/3.7.4 Darwin/19.3.0 botocore/2.0.0dev5
```

Step 2. Prepping your AWS account for CDP

A new account AWS account defaults to small EC2 quota which isn’t enough to finish an onboarding. We'll want to confirm our service quotas for “Running On-Demand Standard (A, C, D, H, I, M, R, T, Z) Instances” is at least 256. (*BTW, that’s 256 vCPUs - not instances. No, we wont be provisioning 256 vCPUs, this just gives us plenty of headroom should we need to scale up our environment*)

We can check this using the AWS CLI by running this command.
```
aws service-quotas get-service-quota     --service-code ec2     --quota-code L-1216C47A
```

The output of this command will tell you what your quote is set to.  For the account I'm working with, this 1152

```
QuotaName": "Running On-Demand Standard (A, C, D, H, I, M, R, T, Z) instances",
        "Value": 1152.0,
```

Step 3. Create AWS Resources (IAM, and S3) Using Terraform.

In this step, we are going to create the proper S3 buckets and IAM policies needed for a CDP Environment in AWS.  An explanation of these can be found [here](https://docs.cloudera.com/management-console/cloud/environments/topics/mc-idbroker-minimum-setup.html)

First, let's download the terraform github from here.

```
git clone https://github.com/dnarain/cdp-aws
```

then, let's navigate into the S3 directory
```
cd cdp-aws/
cd s3/
```

If this is your first time using terraform, you might need to run terraform init

```
terraform init

Initializing the backend...

Initializing provider plugins...
- Checking for available provider plugins...
- Downloading plugin for provider "aws" (hashicorp/aws) 2.52.0...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.aws: version = "~> 2.52"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

Next, we are going specify an S3 bucket to use for CDP.  If this name you provide does not exist, it will be created for you.

To do a dry run first type
```
terraform plan
```

In this example, I'm going to run terraform plan with the bucket name first-cdp-datalake.  You should see an output similar to the below

```
terraform plan
var.DATALAKE_BUCKET
    Enter the bucket name for the datlake (without the leading  s3://).
    If the bucket doesn't exist, it will be created.
    Default encryption will be enabled (even for pre-existing buckets)
    A folder called logs will be created in this bucket
     NOTE: terraform destroy WILL destroy the bucket, even if it pre-existed

  Enter a value: first-cdp-datalake

Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_s3_bucket.the_bucket will be created
  + resource "aws_s3_bucket" "the_bucket" {
      + acceleration_status         = (known after apply)
      + acl                         = "private"
      + arn                         = (known after apply)
      + bucket                      = "first-cdp-datalake"
      + bucket_domain_name          = (known after apply)
      + bucket_regional_domain_name = (known after apply)
      + force_destroy               = false
      + hosted_zone_id              = (known after apply)
      + id                          = (known after apply)
      + region                      = (known after apply)
      + request_payer               = (known after apply)
      + tags                        = {
          + "Name" = "first-cdp-datalake"
        }
      + website_domain              = (known after apply)
      + website_endpoint            = (known after apply)

      + server_side_encryption_configuration {
          + rule {
              + apply_server_side_encryption_by_default {
                  + sse_algorithm = "AES256"
                }
            }
        }

      + versioning {
          + enabled    = (known after apply)
          + mfa_delete = (known after apply)
        }
    }

  # aws_s3_bucket_object.logs_folder will be created
  + resource "aws_s3_bucket_object" "logs_folder" {
      + acl                    = "private"
      + bucket                 = (known after apply)
      + content_type           = "application/x-directory"
      + etag                   = (known after apply)
      + force_destroy          = false
      + id                     = (known after apply)
      + key                    = "logs/"
      + server_side_encryption = (known after apply)
      + storage_class          = (known after apply)
      + version_id             = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.

```

Now, let's actually perform the above actions.

```
terraform apply
```

The output will be similar to above, but it will then ask you if you want to complete these actions.

```
Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_s3_bucket.the_bucket: Creating...
aws_s3_bucket.the_bucket: Creation complete after 4s [id=first-cdp-datalake]
aws_s3_bucket_object.logs_folder: Creating...
aws_s3_bucket_object.logs_folder: Creation complete after 1s [id=logs/]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

Success! Next, we are going to create IAM policies.

Change your directory location to be inside the IAM directory

Then, we'll run terraform apply again

```
terraform apply
```

And terraform will create the needed policies and trusts.  Again, an explanation of these can be found [here](https://docs.cloudera.com/management-console/cloud/environments/topics/mc-idbroker-minimum-setup.html)





















Steps are:
1. [Log in, Accept Terms](cdp-login.md)
2. [Prep your AWS account](aws-account-prep.md)
3. [Create the CDP Credential](cdp-credential.md)
4. [Set up an Environment and Datalake](aws-env.md)
5. Set up a Datahub cluster - TBD
6. Set up Cloudera Data Warehouse (CDP) - TBD

# Assumptions
The following assumptions are being made, which have been validated as "good
enough" over the course of several on-boardings:
* All EC2 instances will use Private IPs (this requires the CCM feature of CDP)
* All S3 buckets will be deployed with Default AWS Encryption (AKA AES-256)[^1]

[^1]: We can easily upgrade from this setup to Customer Managed Keys once we've proven the solution. Its just a matter of updating an AWS IAM Policy.
