# Домашнее задание к занятию "7.3. Основы и принцип работы Терраформ"
## Задача 1. Создадим бэкэнд в S3
Bucket:  
(![Screenshot](https://github.com/Smarzhic/netology/blob/main/07-terraform-03-basic/1.JPG) 

`Permissions:`

```bash
{
   "Version": "2012-10-17",
   "Statement": [
       {
           "Effect": "Allow",
           "Principal": {
               "AWS": "*"
           },
           "Action": "s3:ListBucket",
           "Resource": "arn:aws:s3:::lerbucket"
       },
       {
           "Effect": "Allow",
           "Principal": {
               "AWS": "*"
           },
           "Action": [
               "s3:GetObject",
               "s3:PutObject"
           ],
           "Resource": "arn:aws:s3:::lerbucket/*"
       }
   ]
}
```
`Access Point policy (bucket)`
```bash
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::---:root"
            },
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:eu-central-1:---:accesspoint/<namepoint>"
        },
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::---:root"
            },
            "Action": [
                "s3:GetObject",
                "s3:PutObject"
            ],
            "Resource": "arn:aws:s3:eu-central-1:---:accesspoint/<namepoint>/object/<iam role>/*"
        }
    ]
}
````
`versions.tf`
```bash
terraform {
  backend "s3" {
      bucket = "lerbucket"
      key = "s3/terraform.tfstate"
      region = "eu-central-1"
      dynamodb_table = "tstate-locks"
  }

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }
}
```
## Задача 2. Инициализируем проект и создаем воркспейсы.
1. Выполните `terraform init:` если был создан бэкэнд в S3, то терраформ создат файл стейтов в S3 и запись в таблице dynamodb.
```bash
$ terraform init

Initializing the backend...

Successfully configured the backend "s3"! Terraform will automatically
use this backend unless the backend configuration changes.

Initializing provider plugins...
- Reusing previous version of hashicorp/aws from the dependency lock file
- Using previously-installed hashicorp/aws v3.63.0

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```
