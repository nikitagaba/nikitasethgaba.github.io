# CI/CD Pipeline - Sonar Setup for Blazor

## Step 1: Take microsoft sdk 5 image as base image
```
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS base
```

## Step 2: Set required environment variables for sonar analysis
```
ENV SONAR_MSBUILD_ZIP_VERSION=5.2.1.31210-net5.0 \
    SONAR_MSBUILD_VERSION=5.2.1.31210 \
    SONAR_VERSION=4.6.1.2450 \
    SONAR_MSBUILD_PATH=/opt/sonar-scanner-msbuild \
    BUILD_DIR=/build_dir
	
ARG PROJECT_KEY
ARG PROJECT_NAME
ARG HOST
ARG LOGIN_KEY
```
    
## Step 3: Update and install required packages for sonar    
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
        unzip -y
```


## Step 4: Install nodejs
```
RUN wget https://deb.nodesource.com/setup_16.x \
    && bash setup_16.x \
    && apt-get install -y nodejs
```

## Step 5: Install Java
```
ARG JRE_URL=http://javadl.oracle.com/webapps/download/AutoDL?BundleId=234464_96a7b8442fe848ef90c96a2fad6ed6d1
ARG JAVA_HOME_NAME=java-8-oracle

RUN \
  apt-get update && \
  apt-get install -y wget && \
  wget --no-check-certificate -c --header "Cookie: oraclelicense=accept-securebackup-cookie" $JRE_URL -O java-linux-x64.tar.gz && \
  mkdir -p /usr/lib/jvm/$JAVA_HOME_NAME && \
  tar -C /usr/lib/jvm/$JAVA_HOME_NAME --strip-components=1 -xvf java-linux-x64.tar.gz && \
  update-alternatives --install "/usr/bin/java" "java" "/usr/lib/jvm/$JAVA_HOME_NAME/bin/java" 1 && \
  rm -rf /var/lib/apt/lists/* && \
  rm *linux-x64.tar.gz

ENV JAVA_HOME /usr/lib/jvm/$JAVA_HOME_NAME
```

## Step 6: Sonar scanner for msbuild download and install
```
RUN wget https://github.com/SonarSource/sonar-scanner-msbuild/releases/download/$SONAR_MSBUILD_VERSION/sonar-scanner-msbuild-$SONAR_MSBUILD_ZIP_VERSION.zip -O /opt/sonar-scanner-msbuild.zip \
  && mkdir -p $SONAR_MSBUILD_PATH \
  && mkdir -p $BUILD_DIR \
  && unzip /opt/sonar-scanner-msbuild.zip -d $SONAR_MSBUILD_PATH \
  && rm /opt/sonar-scanner-msbuild.zip \
  && chmod -R 775 $SONAR_MSBUILD_PATH \
  && chmod -R 775 $BUILD_DIR

ENV PATH="$SONAR_MSBUILD_PATH:$SONAR_MSBUILD_PATH/sonar-scanner-$SONAR_VERSION/bin:${PATH}"
```


## Step 7: setup working directory
```
copy . $BUILD_DIR
VOLUME $BUILD_DIR
WORKDIR $BUILD_DIR
```

## Step 8: run sonar analysis
```
RUN dotnet restore 
RUN dotnet /opt/sonar-scanner-msbuild/SonarScanner.MSBuild.dll begin /d:sonar.host.url=$HOST /d:sonar.login=$LOGIN_KEY /k:$PROJECT_KEY /n:$PROJECT_NAME
RUN dotnet build 
RUN dotnet /opt/sonar-scanner-msbuild/SonarScanner.MSBuild.dll end /d:sonar.login=$LOGIN_KEY
```

