# Домашнее задание к занятию "7.3. Основы и принцип работы Терраформ"
## Задача 1. Создадим бэкэнд в S3
Bucket:  
(![Screenshot](https://github.com/Smarzhic/netology/blob/main/07-terraform-03-basic/1.JPG) 

Permissions:

```terraform
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
