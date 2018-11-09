
## 免责说明
建议测试过程中使用此方案，生产环境使用请自行考虑评估。<br>
当您对方案需要进一步的沟通和反馈后，可以联系 nwcd_labs@nwcdcloud.cn 获得更进一步的支持。<br>
欢迎联系参与方案共建和提交方案需求, 也欢迎在 github 项目issue中留言反馈bugs。   

## 项目介绍   

帮助你利用Splunk 7.20 版本管理AWS 中国宁夏区域和北京区域的资源，账单等.   

![](https://raw.githubusercontent.com/p31415926/Splunk_for_AWS_China/master/splunk2.png)    

Splunk 已经发布了2个非常重要的APPS可以帮助AWS用户管理和监控AWS资源，由于默认安装版本上有一些配置没有及时更新AWS宁夏区域，这个Project会告诉您做如何的修改可以实现对宁夏区域的控制。    

你可以有2个办法来实现splunk的安装：    
* 第一种方法 利用已经安装好的Splunk Enterprise Free（仅支持512MB日志）。  
* 第二种方法 手工下载并安装相应的Splunk Enterprise　Free（仅支持512MB日志）的安装文件，并配置相关参数。  


### 方法一：直接用已经安装好的AMI

宁夏区域有一个公有AMI（ami-064388904e0c293cc），你可以在社区中找到，利用这个AMI启动，在启动EC2之前，请参考方法二先创建好一个ROLE用于给EC2授权。    
启动EC2时请注意在SECURITY GROUP要开通如下端口 8000，22，8065，8089，这些都是Splunk 需要使用的端口.    
Splunk 会自动启动，启动以后你可以访问Http://xx.xx.xx.xx:8000/zh-CN/, 这里的XX.XX.XX.XX用您得到的公网IP替代.  

安装好Splunk的机器启动以后您还需要做一些配置的工作，来告诉Splunk你的AWS AK/SK,DBR 位置，SQS 队列，CloudWatch 等信息。   

1. 使用user: splunk , password:　HANAabcd1234 登录到splunk 管理界面
2. 访问Splunk应用Splunk Add-on for AWS, 点击配置，删除系统里已经有的一行 AWS CHINA的账户，添加您自己的aws 账户，并起名。
3. 访问Splunk应用Splunk Add-on for AWS，点击输入，设置相关的数据源INPUT，并保存。   
4. 访问Splunk应用Splunk Apps for AWS, 就可以看到相关的AWS的Dashboard等资讯，也可以看到所有的账单明细等信息。



### 方法二：手工安装

1. 创建IAM Role用于给启动Splunk的 EC2 赋予IAM role，给你的ROLE起一个好记的名字.     
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "sqs:GetQueueAttributes",
                "sqs:ListQueues",
                "sqs:ReceiveMessage",
                "sqs:GetQueueUrl",
                "sqs:SendMessage",
                "sqs:DeleteMessage",
                "s3:ListBucket",
                "s3:GetObject",
                "s3:GetBucketLocation",
                "s3:ListAllMyBuckets",
                "s3:GetBucketTagging",
                "s3:GetAccelerateConfiguration",
                "s3:GetBucketLogging",
                "s3:GetLifecycleConfiguration",
                "s3:GetBucketCORS",
                "config:DeliverConfigSnapshot",
                "config:DescribeConfigRules",
                "config:DescribeConfigRuleEvaluationStatus",
                "config:GetComplianceDetailsByConfigRule",
                "config:GetComplianceSummaryByConfigRule",
                "iam:GetUser",
                "iam:ListUsers",
                "iam:GetAccountPasswordPolicy",
                "iam:ListAccessKeys",
                "iam:GetAccessKeyLastUsed",
                "autoscaling:Describe*",
                "cloudwatch:Describe*",
                "cloudwatch:Get*",
                "cloudwatch:List*",
                "sns:Get*",
                "sns:List*",
                "sns:Publish",
                "logs:DescribeLogGroups",
                "logs:DescribeLogStreams",
                "logs:GetLogEvents",
                "ec2:DescribeInstances",
                "ec2:DescribeReservedInstances",
                "ec2:DescribeSnapshots",
                "ec2:DescribeRegions",
                "ec2:DescribeKeyPairs",
                "ec2:DescribeNetworkAcls",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeSubnets",
                "ec2:DescribeVolumes",
                "ec2:DescribeVpcs",
                "ec2:DescribeImages",
                "ec2:DescribeAddresses",
                "lambda:ListFunctions",
                "rds:DescribeDBInstances",
                "cloudfront:ListDistributions",
                "elasticloadbalancing:DescribeLoadBalancers",
                "elasticloadbalancing:DescribeInstanceHealth",
                "elasticloadbalancing:DescribeTags",
                "elasticloadbalancing:DescribeTargetGroups",
                "elasticloadbalancing:DescribeTargetHealth",
                "elasticloadbalancing:DescribeListeners",
                "inspector:Describe*",
                "inspector:List*",
                "kinesis:Get*",
                "kinesis:DescribeStream",
                "kinesis:ListStreams",
                "kms:Decrypt",
                "sts:AssumeRole"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

2. 在AWS控制面板快速启动一台Ubuntu 16，请将EBS大小调整到20GB.并注意加载IAM ROLE，同时设置Security Group 打开端口：8000,22,8065,8069。    

3. 下载Splunk Enterprise安装文件到本地并安装    
```
https://www.splunk.com/en_us/download/splunk-enterprise.html
```
或者直接下载:   

```
cd /tmp
wget https://download.splunk.com/products/splunk/releases/7.2.0/linux/splunk-7.2.0-8c86330ac18-Linux-x86_64.tgz
cd /tmp
tar xvzf splunk-.tgz -C /opt
cd /opt/splunk/bin
./splunk  start --accept-license  (这个过程中系统会提示输入管理员帐户和密码）
./splunk  enable boot-start -user root  （这句是设置重新启动机器时自动启动Splunk)
```

4. 访问你已经安装好的splunk 并安装 splunk apps for aws 以及 splunk add-on for aws. （安装文件在github的splunk package 目录中，您也可以创建自己的splunk base 网站的账户，直接在线安装)    

5. 由于当前的7.20版本的SPLUNK ENTERPRISE 没有将AWS 宁夏区域配置进去，您需要手工修改下面的文件:
```
cp /opt/splunk/etc/apps/splunk_app_aws/lookups/regions.csv /opt/splunk/etc/apps/splunk_app_aws/lookups/regions.csv.bak
vi /opt/splunk/etc/apps/splunk_app_aws/lookups/regions.csv
```
在最后一行，添加   
```
cn-northwest-1,NingXia,37.51,105.18,"China (NingXia)"
```
6，修改/opt/splunk/etc/apps/Splunk_TA_aws/bin/3rdparty/boto/endpoints.json 文件
添加cn-northwest-1 这个配置，或者您也可以用github这个目录中的endpoints.json直接覆盖您安装好的endpoints.json文件.
```
     "regions": {
        "cn-northwest-1":{
          "description": "China (Ningxia)"
        },
        "cn-north-1": {
          "description": "China (Beijing)"
        }
```
7. 重新启动splunk    
```
./opt/splunk/bin/splunk restart
```

8. 在SPLUNK应用中设置你的AK/SK 以及其他信息
访问Http://xx.xx.xx.xx:8000/zh-CN/  点击屏幕上面的应用，选择Splunk Add-on for AWS. 并设置AWS账户，以及输入源，包括billing, cloudwatch,cloudtrial, etc.   


9. 访问splunk apps for aws.
访问 Http://xx.xx.xx.xx:8000/zh-CN/  并使用在安装时设置的用户名和密码登录，然后点上面应用这里的，splunk apps for aws.   
