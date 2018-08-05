# AWS SAM Examples

### Development Environment Requirements
* Dotnet Core SDK 2.0
* Mono SDK - _**macOS only**_
* Java 8 JDK
* Maven 3.5
* AWS CLI
* Docker
* AWS SAM CLI
* Authenticate AWS credentials with AWS CLI

```
brew install mono
brew cask install dotnet
brew cask install java8
brew install maven
brew install awscli
brew cask install docker
pip install aws-sam-cli
aws configure
```
* I'm using [Homebrew](https://brew.sh/) for macOS. Use [Chocolatey](https://chocolatey.org/) if you are using Windows. Linux users, you know what to do.
* If you are using Windows run the PowerShell script instead of the Shell script. They have the same parameters.
    * Will be: ``` build.ps1 --target=Package ``` instead of ``` sh build.sh --target=Package ```
* I setup my pip to be a soft link of pip3.

----

### **Dotnet Core 2.0 Example**

#### Define Basic App Info (Name, Type, & Package Destination)
```
appName=dotnet-app
appRuntime=dotnetcore2.0
s3PkgBucket=creoslate-package
```

#### Create/Build App
```
sam init --runtime $appRuntime --name $appName && cd $appName
sh build.sh --target=Package
```

* Make change to template.yml to fix Dotnet Bug Only
    
    ``` CodeUri: artifacts/HelloWorld.zip ```

#### Package Application & send to S3 Bucket
```
sam package \
    --template-file template.yaml \
    --output-template-file packaged.yaml \
    --s3-bucket $s3PkgBucket
```

#### Deploy CloudFormation Stack to AWS
```
aws cloudformation deploy \
    --template-file packaged.yaml \
    --stack-name $appName \
    --capabilities CAPABILITY_IAM
```

----

### **Java 8 Example**

#### Define Basic App Info
```
appName=java-app
appRuntime=java8
s3PkgBucket=creoslate-package
```

#### Create/Build App
```
sam init --runtime $appRuntime --name $appName && cd $appName
mvn package
```

#### Package Application & send to S3 Bucket
```
sam package \
    --template-file template.yaml \
    --output-template-file packaged.yaml \
    --s3-bucket $s3PkgBucket
```

#### Deploy CloudFormation Stack to AWS
```
aws cloudformation deploy \
    --template-file packaged.yaml \
    --stack-name $appName \
    --capabilities CAPABILITY_IAM
```

---

### Unit Testing

* Java 8

    * ``` mvn test ```

* Dotnet Core (2 Options)

    * ``` dotnet test test/HelloWorld.Test ```
    * ``` sh build.sh --target=Test ```

### CloudFormation Configuration Validation

* ``` sam validate ```

### Run Local Function

* Must have Docker running in background

    ``` open /Applications/Docker.app ```

* Start the local api service

    ``` sam local start-api ```

    * This will pull down the latest lambda docker image for the runtime and run it

---

### Destroy CloudFormation Stacks when Done

* Find the name of the CloudFormation Stack in your stack list

    * ``` aws cloudformation list-stacks ```

* Specify & Delete the CloudFormation Stack

    * ``` devStack=java-app ```
    * ``` aws cloudformation delete-stack --stack-name $devStack ```
