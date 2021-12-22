# AWS Demo

## Setup
1. Install the aws cli from [here](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
2. Create an AWS Account 
3. Create an IAM user [Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html#id_users_create_console)
    1. Set the region to us-east-2
    ![Iam](screenshots/region.png?raw=true "IAM")
    2. Search IAM on the search bar 
    3. On the left bar click on users then click add users
    4. Give any user name and select programmatic access
    ![user](screenshots/user.png?raw=true "user")
    5. For simplicty give the user account admin access. Normally a user account would have limited access but for a demo it is easier to give admin. 
    ![user](screenshots/admin.png?raw=true "user")
    6. leave tags blank 
    7. review and create!
    8. Copy down the access key and secret. 
4. In powershell from the home directory ($HOME) create an .aws directory and two files without extentions. One called credentials and the other config. [Reference](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)

``` PS C:\Users\kyle-razer> mkdir .aws
PS C:\Users\kyle-razer> cd .aws
PS C:\Users\kyle-razer\.aws> New-Item credentials
PS C:\Users\kyle-razer\.aws> New-Item config
```
5. In the config file enter the following 
```
[default]
region=us-east-2
output=json
```
6. in the credential file enter the following 
```
[default]
aws_access_key_id=(YOUR ACCESS KEY)
aws_secret_access_key=(YOUR SECRET KEY)
```
7. run aws configure to validate that your credentials have taken. 

# New Product
## Creating a bucket 
For AWS UI Steps follow this [guide](https://aws.amazon.com/getting-started/hands-on/getting-started-using-amazon-s3-glacier-and-s3-glacier-deep-archive/?trk=ha_a134p000007CXMQAA4&trkCampaign=psc_core-infra_storage_s3-glacier-da_get-started_v2&sc_ichannel=ha&sc_icampaign=Adoption_Campaign_psc_core-infra_s3-glacier-da_sm-s3-console&sc_ioutcome=CSI_Digital_Marketing) otherwise follow the cli steps below. 
1. Open powershell with setup complete 
2. Run 
```
aws s3api create-bucket --bucket kyle-backup-1234 --create-bucket-configuration LocationConstraint=us-east-2 
```

Note bucket names must be unique across all of AWS so your first bucket name may already be taken. It is really annoying but it is just the way it is. 

3. Add server-side encryption 
```
aws s3api put-bucket-encryption --bucket kyle-backup-1234 --server-side-encryption-configuration '{\"Rules\": [{\"ApplyServerSideEncryptionByDefault\": {\"SSEAlgorithm\": \"AES256\"}}]}'
```  
Note: On windows the " need to be escaped \\" 

4. Turn on Versioning 
```
aws s3api put-bucket-versioning --bucket kyle-backup-1234 --versioning-configuration Status=Enabled
```

# S3 Operations
[Reference](https://docs.aws.amazon.com/cli/latest/userguide/cli-services-s3-commands.html#using-s3-commands-managing-objects-move)

## Uploading a file 
```
aws s3 cp archive.zip s3://kyle-backup-1234/ --storage-class GLACIER_IR
```

## Downloading a file 
```
aws s3 cp s3://kyle-backup-1234/archive.zip .
```
## Syncing a directory (Upload)
```
aws s3 sync . s3://kyle-backup-1234/december --storage-class GLACIER_IR
```
## Sync (Download)
```
aws s3 sync s3://kyle-backup-1234/december ./download
```
# Old Product
## Creating a vault
[Reference](https://docs.aws.amazon.com/cli/latest/userguide/cli-services-glacier.html) 
```
aws glacier create-vault --account-id - --vault-name kylesvault 
```

## Uploading files 
```
aws glacier upload-archive --account-id - --vault-name kylesvault  --body archive.zip
```