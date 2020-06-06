# cloud_devops_capstone_project
jenkins pipelene for building docker image and deploying it with a blue / green strategy to the AWS kubernates cluster

Helpfull information
https://medium.com/rackbrains/understanding-blue-green-deployments-c941a841bbdb
https://medium.com/@andresaaap/jenkins-pipeline-for-blue-green-deployment-using-aws-eks-kubernetes-docker-7e5d6a401021
https://medium.com/@andresaaap/simple-blue-green-deployment-in-kubernetes-using-minikube-b88907b2e267


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

Create Jenkins EC2 instance with inbound rules
```
 > `Custom  TCP	    8080	myIP	-`
 > `SSH	    TCP	    22	    myIP	-`
```

install jenkins

install pip3
```
sudo apt install python3-pip
```
install pylint
```
sudo pip3 install pylint
```

install brew 
```
sudo apt install linuxbrew-wrapper
brew update
Ctrl-D
```
install hadolint  
```
brew install hadolint
```

add hadolint to the path
`vi ~/.profile`
`export PATH="$PATH:/home/ubuntu/.linuxbrew/Cellar/hadolint/1.18.0/bin/hadolint"`

install docker https://docs.docker.com/engine/install/ubuntu/#installation-methods
```
sudo apt install docker.io
```

to fix "Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post http://%2Fvar%2Frun%2Fdocker.sock/v1.40/build"
```
sudo chmod 666 /var/run/docker.sock
```

create file with password for dockerHub
```
touch dockerHubPassword
chmod 777 dockerHubPassword
vi dockerHubPassword 
```

Install the AWS CLI https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html
```
sudo curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
ubuntu@ip-10-0-1-47:~$ aws --version
aws-cli/2.0.19 Python/3.7.3 Linux/4.15.0-1065-aws botocore/2.0.0dev23
aws configure
... copy jenkins user credentials from csv file
```

Install and configure kubectl
```
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.16.8/2020-04-16/bin/linux/amd64/kubectl
curl -o kubectl.sha256 https://amazon-eks.s3.us-west-2.amazonaws.com/1.16.8/2020-04-16/bin/linux/amd64/kubectl.sha256
openssl sha1 -sha256 kubectl
chmod +x ./kubectl
mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$PATH:$HOME/bin
echo 'export PATH=$PATH:$HOME/bin' >> ~/.bashrc
sudo mv ./kubectl /usr/local/bin/kubectl
ubuntu@ip-10-0-1-47:~$ kubectl version --short --client
Client Version: v1.16.8-eks-e16311
```

install eksctl
```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
ubuntu@ip-10-0-1-47:~$ eksctl version
0.21.0
```

test that the cluster is ok
```
ubuntu@ip-10-0-1-47:~$ kubectl get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.100.0.1   <none>        443/TCP   8m46s
```

create / update config https://docs.aws.amazon.com/eks/latest/userguide/create-kubeconfig.html
```
ubuntu@ip-10-0-1-47:/var/lib/jenkins$ aws eks --region us-west-2 update-kubeconfig --name capstoneEks
Updated context arn:aws:eks:us-west-2:980543251014:cluster/capstoneEks in /home/ubuntu/.kube/config
```

Replication controller
https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/
Pod overview 
https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/ 

