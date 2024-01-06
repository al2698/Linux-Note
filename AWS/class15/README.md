## 使用 CloudFront 連接到網站的 S3 儲存桶

![Alt text](image.png)

![Alt text](image-1.png)

![Alt text](image-2.png)

![Alt text](image-3.png)

![Alt text](image-4.png)

![Alt text](image-5.png)

![Alt text](image-6.png)

```
{
        "Version": "2008-10-17",
        "Id": "PolicyForCloudFrontPrivateContent",
        "Statement": [
            {
                "Sid": "AllowCloudFrontServicePrincipal",
                "Effect": "Allow",
                "Principal": {
                    "Service": "cloudfront.amazonaws.com"
                },
                "Action": "s3:GetObject",
                "Resource": "arn:aws:s3:::test-z22756392z/*",
                "Condition": {
                    "StringEquals": {
                      "AWS:SourceArn": "arn:aws:cloudfront::803647625959:distribution/EU9V24PLPS4QG"
                    }
                }
            }
        ]
      }
```

![Alt text](image-7.png)

![Alt text](image-8.png)

## WAF（Web 應用程式防火牆）

![Alt text](image-9.png)

![Alt text](image-10.png)

![Alt text](image-11.png)

![Alt text](image-12.png)

![Alt text](image-13.png)

![Alt text](image-14.png)

![Alt text](image-15.png)

![Alt text](image-16.png)

此腳本來捲曲我們的雲端前端IP以達到速率限制

```py
for i in {1..110}
do
  echo $i
  result=curl -o /dev/null -s -w %{http_code} https://d36xl9iuaw2xue.cloudfront.net/
  if [[ "$result" == "200" ]] ; then
    echo "ok"
  else
    echo "fail"
  fi
done
```

![Alt text](image-17.png)

![Alt text](image-18.png)

![Alt text](image-19.png)

使用 Python 的 Boto3 套件來操作 AWS 的 DynamoDB。

```py
import boto3
import os
os.environ['AWS_DEFAULT_REGION'] = 'us-east-1'



_TableName_ = "addrbook"

client = boto3.client('dynamodb')

DB = boto3.resource('dynamodb')
table = DB.Table(_TableName_)

response = table.get_item(
    Key={
        'name': "mary"
    }
)
response["Item"]

print(response['Item'])
```

![Alt text](image-20.png)

## 重要提醒
>要記得刪除web ACL，會被算錢。
![Alt text](image-21.png)