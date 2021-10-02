# Deployment of Blazor application on AWS S3 Website via Docker

## Step 1: Take microsoft sdk 5 image as base image
```
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS base
```
## Step 2: Set required environment variables for deployment
```
ARG AWS_ACCESS_KEY
ARG AWS_SECRET_ACCESS
ARG AWS_REGION
ARG S3_BUCKET

ENV BUILD_DIR=/build_dir
ENV AWS_ACCESS_KEY=$AWS_ACCESS_KEY
ENV AWS_SECRET_ACCESS=$AWS_SECRET_ACCESS
ENV AWS_REGION=$AWS_REGION
```

## Step 3: Update and install required packages
```
RUN apt-get update
RUN apt-get install \
	mono-devel \
	mono-complete \
	ca-certificates-mono \
	mono-xsp4 \
	libunwind8 \
	gettext \
	apt-transport-https \
	wget \
	unzip \
	-y 
```
## Step 4: setup working directory
```
copy . $BUILD_DIR
VOLUME $BUILD_DIR
WORKDIR $BUILD_DIR
```

## Step 5: create publish package for s3
```
RUN dotnet restore 
RUN dotnet publish -c Release -o  $BUILD_DIR/publish 
```
## Step 6: Install required aws cli and python packages for copy to S3 and run copy command
```
RUN apt-get update && \
    apt-get  --assume-yes install python python-pip ca-certificates groff less && \
    pip install awscli && \
    rm -rf /var/cache/apk/*

RUN aws s3 cp $BUILD_DIR/publish/wwwroot/ s3://$S3_BUCKET --recursive --acl public-read
RUN aws s3 sync $BUILD_DIR/publish/wwwroot/ s3://$S3_BUCKET --delete --only-show-errors --exact-timestamps --acl public-read
```