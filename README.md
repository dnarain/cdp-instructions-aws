# Cloudera Data Platform (CDP) - Public Cloud - Self On-Boarding in AWS

This is a series of steps to set up Clouder Data Platform (CDP) - Public Cloud 
in AWS. 

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
