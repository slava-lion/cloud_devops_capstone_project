# cloud_devops_capstone_project
jenkins pipelene for building docker image and deploying it with a blue / green strategy to the AWS kubernates cluster

Setup
```
 > cd CloudFormation/
 > bash create.sh capstone infrastructure-kubernates.yml infrastructure-kubernates-parameters.json 
```
Blue/Green Deployment IAM Policy
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "s3:*",
                "cloudwatch:*",
                "route53:*",
                "ec2:*",
                "ec2:DescribeAccountAttributes",
                "elasticloadbalancing:*"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": "iam:CreateServiceLinkedRole",
            "Resource": "arn:aws:iam::*:role/aws-service-role/*"
        }
    ]
}
```

Create new user 'jenkins' with group jenkins and assigned bllue/green deployment policy

Create Jenkins EC2 instance with inbiund rules
Custom  TCP	    8080	myIP	-
SSH	    TCP	    22	    myIP	-


