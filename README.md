# Splunk_for_AWS_China
帮助你利用Splunk 7.20 版本管理AWS 中国宁夏区域和北京区域的资源，账单等.

Splunk 已经发布了2个非常重要的APPS可以帮助AWS用户管理和监控AWS资源，由于默认安装版本上有一些配置没有及时更新AWS宁夏区域，
这个Project会告诉您做如何的修改可以实现对宁夏区域的控制。

你可以有2个办法来实现splunk的安装，
第一种方法 手工下载并安装相应的Splunk Enterprise的安装文件，并配置相关参数。
第二种方法 利用已经安装好的Splunk Enterprise Free（ 仅支持512MB日志）。

方法一：直接用已经安装好的AMI

ami-064388904e0c293cc  宁夏区域有一个公有AMI，你可以在社区中找到，利用这个AMI 启动，在启动EC2之前，请参考方法二先创建好一个ROLE用于给EC2授权。
启动EC2时请注意在SECURITY GROUP要开通如下端口 8000，22，8065，8089，这些都是Splunk 需要使用的端口.
Splunk 会自动启动，启动以后你可以访问Http://xx.xx.xx.xx:8000:8000/zh-CN/, 这里的XX.XX.XX.XX用您得到的公网IP替代. 
安装好Splunk的机器启动以后您还需要做一些配置的工作，来告诉Splunk你的AWS AK/SK,DBR 位置，SQS 队列，CloudWatch 等信息。
1）使用user: splunk , password:　HANAabcd1234 登录到splunk 管理界面
2）访问Splunk应用Splunk Add-on for AWS, 点击配置，删除系统里已经有的一行 AWS CHINA的账户，添加您自己的aws 账户，并起名。
3）访问Splunk应用Splunk Add-on for AWS，点击输入，设置相关的数据源INPUT，并保存。
4）访问Splunk应用Splunk Apps for AWS, 就可以看到相关的AWS的Dashboard等资讯，也可以看到所有的账单明细等信息。


方法二：手工安装步骤如下：

1) 创建IAM Role用于给启动Splunk的 EC2 赋予IAM role
给你的ROLE起一个好记的名字.
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

2)
