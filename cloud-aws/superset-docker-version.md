# Apache Superset - Setup on EC2
This is a go to setup instructions for people looking to setup Apache Superset on an EC2. I listed it below for others looking for such use case. 
EC2 Instance Type - t2.small
EC2 AMI - Amazon Linux 2 

## Assumptions:
1. EC2 is already created with public access / VPN Access (in my usecase I had a restricted AWS Envrionment where EC2 could only be accessed via VPN).
2. Users have some working knowledge about AWS and linux

## Steps to be followed:

### Login to EC2 via any sshclient with username : ec2-user
I prefer using putty / Winscp. Let me know what client you prefer in comments below.

### install docker
```
sudo yum update -y
sudo yum install docker -y
sudo usermod -a -G docker ec2-user
sudo service docker start
```

### install docker-compose
```
sudo curl -L https://github.com/docker/compose/releases/download/1.25.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

### if root access needed docker-compose
```
sudo cp /usr/local/bin/docker-compose /usr/bin/
```
 
### Install git in your EC2 instance
```
sudo yum install git -y
git version
```

### Clone superset repo into your instance and simply run the docker-compose.
```
git clone https://github.com/apache/superset.git
cd superset
docker -> .env-nondev = set SUPERSET_LOAD_EXAMPLES to no
sudo docker-compose -f docker-compose-non-dev.yml up
```