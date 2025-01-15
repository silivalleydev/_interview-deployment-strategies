# 배포 전략 및 구현 방법

- [배포 전략 및 구현 방법](#배포-전략-및-구현-방법)
  - [1. CDN 배포 (Content Delivery Network Deployment)](#1-cdn-배포-content-delivery-network-deployment)
    - [**설명**](#설명)
    - [**구현 방법**](#구현-방법)
- [AWS DevOps 설정: S3, CloudFront, CodePipeline으로 프론트엔드 배포](#aws-devops-설정-s3-cloudfront-codepipeline으로-프론트엔드-배포)
  - [**1. AWS 서비스 설명**](#1-aws-서비스-설명)
    - [**S3 (Simple Storage Service)**](#s3-simple-storage-service)
    - [**CloudFront**](#cloudfront)
    - [**CodePipeline**](#codepipeline)
  - [**2. 설정 순서**](#2-설정-순서)
    - [**1단계: S3 버킷 생성 및 정적 웹 호스팅 설정**](#1단계-s3-버킷-생성-및-정적-웹-호스팅-설정)
    - [**2단계: CloudFront 배포 설정**](#2단계-cloudfront-배포-설정)
    - [**3단계: CodePipeline 생성**](#3단계-codepipeline-생성)
  - [**3. CodePipeline 설정 파일**](#3-codepipeline-설정-파일)
    - [**`buildspec.yml` 작성**](#buildspecyml-작성)
      - [**파일 내용**](#파일-내용)
      - [**옵션 설명**](#옵션-설명)
  - [**4. PRD 및 DEV 환경 설정**](#4-prd-및-dev-환경-설정)
    - [**S3 및 CloudFront 구분**](#s3-및-cloudfront-구분)
    - [**CodePipeline 환경 구분**](#codepipeline-환경-구분)
      - [**환경별 `buildspec.yml`**](#환경별-buildspecyml)
  - [2. 무중단 배포 (Zero-Downtime Deployment)](#2-무중단-배포-zero-downtime-deployment)
    - [**설명**](#설명-1)
    - [**구현 방법**](#구현-방법-1)
      - [Blue-Green Deployment](#blue-green-deployment)
      - [Canary Deployment](#canary-deployment)
      - [Rolling Deployment](#rolling-deployment)
- [React 프로젝트 배포 전략: Blue-Green, Canary, Rolling Deployment](#react-프로젝트-배포-전략-blue-green-canary-rolling-deployment)
  - [**1. 배포 전략 개요**](#1-배포-전략-개요)
    - [**Blue-Green Deployment**](#blue-green-deployment-1)
    - [**Canary Deployment**](#canary-deployment-1)
    - [**Rolling Deployment**](#rolling-deployment-1)
  - [**2. 사용 서비스 및 설정 순서**](#2-사용-서비스-및-설정-순서)
    - [**AWS 서비스**](#aws-서비스)
  - [**3. 설정 순서**](#3-설정-순서)
    - [**Blue-Green Deployment 설정**](#blue-green-deployment-설정)
      - [**CodePipeline 설정 파일 (`buildspec.yml`)**](#codepipeline-설정-파일-buildspecyml)
    - [**Canary Deployment 설정**](#canary-deployment-설정)
      - [**CodePipeline 설정 파일 (`buildspec.yml`)**](#codepipeline-설정-파일-buildspecyml-1)
    - [**Rolling Deployment 설정**](#rolling-deployment-설정)
      - [**CodePipeline 설정 파일 (`buildspec.yml`)**](#codepipeline-설정-파일-buildspecyml-2)
    - [**EC2 기반 구성과 React 프로젝트 세팅**](#ec2-기반-구성과-react-프로젝트-세팅)
  - [3. 모놀리식 배포 (Monolithic Deployment)](#3-모놀리식-배포-monolithic-deployment)
    - [**설명**](#설명-2)
    - [**구현 방법**](#구현-방법-2)
- [Monolithic Deployment: React 프론트엔드와 Express 백엔드](#monolithic-deployment-react-프론트엔드와-express-백엔드)
  - [**1. Monolithic Deployment 개요**](#1-monolithic-deployment-개요)
  - [**2. 생성해야 할 리소스 및 AWS 서비스 설명**](#2-생성해야-할-리소스-및-aws-서비스-설명)
    - [**AWS EC2 (Elastic Compute Cloud)**](#aws-ec2-elastic-compute-cloud)
    - [**AWS S3 (Simple Storage Service)**](#aws-s3-simple-storage-service)
    - [**AWS IAM (Identity and Access Management)**](#aws-iam-identity-and-access-management)
    - [**AWS CloudWatch**](#aws-cloudwatch)
  - [**3. 설정 순서**](#3-설정-순서-1)
    - [**1단계: 프로젝트 구조**](#1단계-프로젝트-구조)
    - [**2단계: NGINX 설정**](#2단계-nginx-설정)
    - [**3단계: Express 백엔드 구성**](#3단계-express-백엔드-구성)
  - [**4. 코드 파이프라인 설정**](#4-코드-파이프라인-설정)
    - [**1. CodePipeline 단계**](#1-codepipeline-단계)
    - [**2. buildspec.yml 파일**](#2-buildspecyml-파일)
    - [**3. 배포 스크립트**](#3-배포-스크립트)
  - [**5. 추가적인 구성 및 유지 관리**](#5-추가적인-구성-및-유지-관리)
    - [**자동화된 시작 스크립트 (User Data)**](#자동화된-시작-스크립트-user-data)
  - [4. CI/CD 파이프라인을 통한 자동화 배포](#4-cicd-파이프라인을-통한-자동화-배포)
    - [**설명**](#설명-3)
    - [**구현 방법**](#구현-방법-3)
- [CI/CD 파이프라인을 통한 React 프로젝트 자동화 배포](#cicd-파이프라인을-통한-react-프로젝트-자동화-배포)
  - [**1. CI와 CD의 의미**](#1-ci와-cd의-의미)
    - [**1.1 CI (Continuous Integration)**](#11-ci-continuous-integration)
    - [**1.2 CD (Continuous Deployment)**](#12-cd-continuous-deployment)
  - [**2. AWS에서 사용되는 서비스와 설명**](#2-aws에서-사용되는-서비스와-설명)
    - [**2.1 AWS CodePipeline**](#21-aws-codepipeline)
    - [**2.2 AWS CodeBuild**](#22-aws-codebuild)
    - [**2.3 AWS S3**](#23-aws-s3)
    - [**2.4 AWS CloudFront**](#24-aws-cloudfront)
    - [**2.5 AWS IAM (Identity and Access Management)**](#25-aws-iam-identity-and-access-management)
  - [**3. CI/CD 파이프라인 설정 순서**](#3-cicd-파이프라인-설정-순서)
    - [**3.1 S3 버킷 생성 및 설정**](#31-s3-버킷-생성-및-설정)
    - [**3.2 IAM 역할 생성**](#32-iam-역할-생성)
    - [**3.3 CodeBuild 프로젝트 생성**](#33-codebuild-프로젝트-생성)
    - [**3.4 CloudFront 배포 설정**](#34-cloudfront-배포-설정)
    - [**3.5 CodePipeline 생성**](#35-codepipeline-생성)
  - [**4. `buildspec.yml` 작성 방법**](#4-buildspecyml-작성-방법)
    - [**4.1 파일 내용**](#41-파일-내용)
    - [**4.2 옵션 설명**](#42-옵션-설명)
  - [**5. 결과 확인**](#5-결과-확인)
  - [5. A/B 테스트 배포](#5-ab-테스트-배포)
    - [**설명**](#설명-4)
    - [**구현 방법**](#구현-방법-4)
- [A/B 테스트 배포 설정](#ab-테스트-배포-설정)
  - [**1. A/B 테스트를 위한 AWS 리소스**](#1-ab-테스트를-위한-aws-리소스)
    - [**1.1 AWS S3**](#11-aws-s3)
    - [**1.2 AWS CloudFront**](#12-aws-cloudfront)
    - [**1.3 AWS Lambda@Edge**](#13-aws-lambdaedge)
    - [**1.4 AWS CodePipeline**](#14-aws-codepipeline)
  - [**2. 설정 순서**](#2-설정-순서-1)
    - [**2.1 S3 버킷 생성 및 파일 업로드**](#21-s3-버킷-생성-및-파일-업로드)
    - [**2.2 CloudFront 설정**](#22-cloudfront-설정)
    - [**2.3 Lambda@Edge 생성 및 연결**](#23-lambdaedge-생성-및-연결)
    - [**2.4 CodePipeline 설정**](#24-codepipeline-설정)
  - [**3. 결과 확인**](#3-결과-확인)


## 1. CDN 배포 (Content Delivery Network Deployment)

### **설명**
CDN 배포는 정적 파일(HTML, CSS, JS)을 전 세계 엣지 서버에 분산 배포하여 사용자가 가장 가까운 서버에서 빠르게 콘텐츠를 로드할 수 있도록 하는 방식입니다.

### **구현 방법**
1. **AWS S3**를 통해 정적 웹 애플리케이션을 업로드.
2. S3 버킷의 "정적 웹 호스팅" 활성화.
3. **AWS CloudFront**를 설정하여 S3를 소스로 사용.
4. HTTPS 설정 및 사용자 정의 도메인(Route 53)을 연결.
5. CloudFront 캐싱 및 TTL 설정으로 성능 최적화.

# AWS DevOps 설정: S3, CloudFront, CodePipeline으로 프론트엔드 배포

## **1. AWS 서비스 설명**

### **S3 (Simple Storage Service)**
- **역할**: 정적 파일(HTML, CSS, JS 등)을 저장하고 웹 호스팅 제공.
- **주요 기능**:
  - 웹 호스팅: 정적 웹사이트를 호스팅할 수 있음.
  - 확장성: 대규모 트래픽 및 데이터 처리 가능.

### **CloudFront**
- **역할**: S3에 저장된 정적 파일을 CDN(Content Delivery Network)으로 글로벌 사용자에게 빠르게 전달.
- **주요 기능**:
  - 전 세계 엣지 서버를 통해 콘텐츠 제공.
  - HTTPS를 기본 지원하여 보안 강화.

### **CodePipeline**
- **역할**: CI/CD 파이프라인을 통해 소스 코드 변경 사항을 자동으로 배포.
- **주요 기능**:
  - GitHub, CodeCommit 등과 연동.
  - CodeBuild와 함께 빌드 및 배포 자동화.

---

## **2. 설정 순서**

### **1단계: S3 버킷 생성 및 정적 웹 호스팅 설정**
1. **S3 버킷 생성**:
   - AWS 콘솔에서 S3로 이동 → 새 버킷 생성.
   - 버킷 이름: `my-frontend-app`.
   - 퍼블릭 엑세스 허용.

2. **정적 웹 호스팅 활성화**:
   - S3 버킷 → **속성(Properties)** → **정적 웹 호스팅(Static website hosting)** 클릭.
   - Index Document: `index.html`.
   - Error Document: `index.html`.

3. **버킷 정책 추가**:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": "*",
         "Action": "s3:GetObject",
         "Resource": "arn:aws:s3:::my-frontend-app/*"
       }
     ]
   }
   ```

4. **빌드 결과 업로드**:
   - React 프로젝트 디렉토리에서 `npm run build` 실행.
   - 빌드된 `/build` 폴더를 S3 버킷에 업로드.

---

### **2단계: CloudFront 배포 설정**
1. **CloudFront 생성**:
   - AWS 콘솔 → CloudFront → 배포 생성 클릭.
   - **원본 도메인**: S3 버킷의 웹 호스팅 URL 선택.
   - **기본 캐시 동작(Default Cache Behavior)**:
     - Viewer Protocol Policy: **Redirect HTTP to HTTPS**.
     - Allowed HTTP Methods: **GET, HEAD**.
   - 배포 생성 완료 후 도메인 이름 확인.

---

### **3단계: CodePipeline 생성**
1. **IAM 역할 생성**:
   - AWS 콘솔 → IAM → 역할 생성.
   - 정책 연결:
     - **AWSCodePipelineFullAccess**
     - **AWSCodeBuildAdminAccess**
     - **AmazonS3FullAccess**

2. **CodePipeline 생성**:
   - AWS 콘솔 → CodePipeline → 새 파이프라인 생성.
   - 단계별 설정:
     - **소스(Sources)**: GitHub에서 소스 연결.
     - **빌드(Build)**: AWS CodeBuild 사용.
     - **배포(Deploy)**: S3에 결과 배포.

---

## **3. CodePipeline 설정 파일**

### **`buildspec.yml` 작성**
`buildspec.yml`은 CodeBuild에서 빌드 및 배포를 제어하는 파일입니다.

#### **파일 내용**
```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      nodejs: 16
    commands:
      - echo "Installing dependencies..."
      - npm install
  build:
    commands:
      - echo "Building the project..."
      - npm run build
  post_build:
    commands:
      - echo "Build completed. Preparing artifacts..."
      - aws s3 sync build/ s3://my-frontend-app --delete
artifacts:
  files:
    - '**/*'
cache:
  paths:
    - node_modules/**/*
```

#### **옵션 설명**
1. **`version`**:
   - 파일 형식 버전(현재 0.2 사용).

2. **`phases`**:
   - **`install`**: Node.js 환경 설정 및 의존성 설치.
   - **`build`**: 프로젝트 빌드 명령 실행(`npm run build`).
   - **`post_build`**: 빌드 후 S3에 파일을 업로드.

3. **`artifacts`**:
   - 빌드 결과물을 저장 및 배포할 파일 패턴 정의.

4. **`cache`**:
   - `node_modules`를 캐싱하여 빌드 속도 최적화.

---

## **4. PRD 및 DEV 환경 설정**

### **S3 및 CloudFront 구분**
1. **S3 버킷 이름**:
   - DEV: `my-frontend-app-dev`
   - PRD: `my-frontend-app-prd`

2. **CloudFront 배포 구분**:
   - DEV: `my-frontend-app-dev.cloudfront.net`
   - PRD: `my-frontend-app-prd.cloudfront.net`

### **CodePipeline 환경 구분**
- 파이프라인 이름:
  - DEV: `frontend-app-dev-pipeline`
  - PRD: `frontend-app-prd-pipeline`

#### **환경별 `buildspec.yml`**
- DEV:
  ```yaml
  post_build:
    commands:
      - aws s3 sync build/ s3://my-frontend-app-dev --delete
  ```

- PRD:
  ```yaml
  post_build:
    commands:
      - aws s3 sync build/ s3://my-frontend-app-prd --delete
  ```

---

## 2. 무중단 배포 (Zero-Downtime Deployment)

### **설명**
무중단 배포는 애플리케이션 업데이트 중에도 사용자가 중단 없이 서비스를 이용할 수 있도록 하는 배포 방식입니다. 대표적으로 Blue-Green, Canary, Rolling 배포 방식이 포함됩니다.

### **구현 방법**
#### Blue-Green Deployment
1. **AWS Elastic Beanstalk**에서 두 개의 환경(Blue, Green) 설정.
2. 새 버전을 Green 환경에 배포 후, 테스트를 통해 안정성 검증.
3. 트래픽을 Green 환경으로 전환.

#### Canary Deployment
1. **AWS CodeDeploy**를 통해 Canary 배포를 구성.
2. 소수 사용자에게 새로운 버전 배포.
3. 모니터링 및 테스트 후, 점진적으로 트래픽 확대.

#### Rolling Deployment
1. **AWS ECS**를 사용하여 클러스터 내 컨테이너를 점진적으로 업데이트.
2. Auto Scaling 설정으로 배포 프로세스를 자동화.

# React 프로젝트 배포 전략: Blue-Green, Canary, Rolling Deployment

## **1. 배포 전략 개요**

### **Blue-Green Deployment**
- **특징**: 두 개의 환경(Blue, Green)을 번갈아 가며 트래픽을 전환하여 배포.
- **목적**: 무중단 배포와 롤백 용이성 제공.

### **Canary Deployment**
- **특징**: 새로운 버전을 일부 사용자에게 먼저 배포하고, 점진적으로 확대.
- **목적**: 새로운 버전의 안정성을 검증하며 리스크 최소화.

### **Rolling Deployment**
- **특징**: 기존 서버의 버전을 점진적으로 교체하며 배포.
- **목적**: 무중단 배포와 자원 효율성을 강조.

---

## **2. 사용 서비스 및 설정 순서**

### **AWS 서비스**
1. **Elastic Beanstalk**:
   - Blue-Green Deployment를 지원하며, 환경 간 트래픽 전환이 간단.
2. **CodeDeploy**:
   - Canary 및 Rolling Deployment를 지원하며, 세분화된 배포 전략 설정 가능.
3. **CodePipeline**:
   - CI/CD 파이프라인으로 소스 코드 변경 시 자동으로 배포.

---

## **3. 설정 순서**

### **Blue-Green Deployment 설정**

1. **Elastic Beanstalk 애플리케이션 생성**:
   - AWS 콘솔 → Elastic Beanstalk → **Create Application** 클릭.
   - 애플리케이션 이름 입력: `ReactApp`.
   - 플랫폼: **Node.js** 선택.
   - 샘플 애플리케이션 배포 후 두 개의 환경(예: `Blue-env`, `Green-env`) 생성.

2. **환경 설정 및 배포**:
   - **Blue 환경**:
     - Elastic Beanstalk → `Blue-env` 선택.
     - `npm run build`로 빌드한 애플리케이션을 `.zip` 파일로 압축:
       ```bash
       zip -r build.zip ./build
       ```
     - Blue 환경에 `.zip` 파일 업로드.
   - **Green 환경**:
     - Elastic Beanstalk → **Create Environment** 클릭.
     - 동일한 애플리케이션 `.zip` 파일을 Green 환경에 배포.

3. **트래픽 전환**:
   - Elastic Beanstalk → **환경(Environment)** → **Swap Environment URLs** 클릭.
   - 트래픽을 Blue에서 Green으로 전환.

#### **CodePipeline 설정 파일 (`buildspec.yml`)**
```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      nodejs: 16
    commands:
      - npm install
  build:
    commands:
      - npm run build
  post_build:
    commands:
      - echo "Zipping the build folder..."
      - zip -r build.zip ./build
      - echo "Deploying to Elastic Beanstalk"
      - aws elasticbeanstalk create-application-version --application-name ReactApp \
          --version-label $CODEBUILD_RESOLVED_SOURCE_VERSION --source-bundle S3Bucket=my-bucket,S3Key=build.zip
      - aws elasticbeanstalk update-environment --application-name ReactApp \
          --environment-name Green-env --version-label $CODEBUILD_RESOLVED_SOURCE_VERSION
```

---

### **Canary Deployment 설정**

1. **CodeDeploy 애플리케이션 생성**:
   - AWS 콘솔 → CodeDeploy → **Applications** → **Create Application** 클릭.
   - 애플리케이션 이름 입력: `ReactAppCanary`.
   - 컴퓨팅 플랫폼: **EC2/On-premises** 선택.

2. **Deployment Group 생성**:
   - CodeDeploy → 애플리케이션 선택 → **Create Deployment Group** 클릭.
   - 배포 그룹 이름: `CanaryGroup`.
   - 서비스 역할 선택: `AWSCodeDeployRole`.
   - 배포 유형: **Canary** 선택.
     - 첫 배포: 10% 트래픽.
     - 5분 후 90% 트래픽.

3. **배포 구성**:
   - CodeDeploy → **Create Deployment** 클릭.
   - 배포 애플리케이션 및 배포 그룹 선택.
   - 소스 버전 업로드.

#### **CodePipeline 설정 파일 (`buildspec.yml`)**
```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      nodejs: 16
    commands:
      - npm install
  build:
    commands:
      - npm run build
  post_build:
    commands:
      - echo "Zipping the build folder..."
      - zip -r build.zip ./build
      - echo "Deploying to CodeDeploy"
      - aws deploy create-deployment --application-name ReactAppCanary \
          --deployment-group-name CanaryGroup \
          --revision file://build.zip \
          --deployment-config-name CodeDeployDefault.Canary10Percent5Minutes
```

---

### **Rolling Deployment 설정**

1. **CodeDeploy 애플리케이션 생성**:
   - AWS 콘솔 → CodeDeploy → **Applications** → **Create Application** 클릭.
   - 애플리케이션 이름 입력: `ReactAppRolling`.
   - 컴퓨팅 플랫폼: **EC2/On-premises** 선택.

2. **Deployment Group 생성**:
   - CodeDeploy → 애플리케이션 선택 → **Create Deployment Group** 클릭.
   - 배포 그룹 이름: `RollingGroup`.
   - 서비스 역할 선택: `AWSCodeDeployRole`.
   - 배포 유형: **Rolling** 설정.
     - `CodeDeployDefault.OneAtATime` 구성 선택.

3. **배포 구성**:
   - CodeDeploy → **Create Deployment** 클릭.
   - 배포 애플리케이션 및 배포 그룹 선택.
   - 소스 버전 업로드.

#### **CodePipeline 설정 파일 (`buildspec.yml`)**
```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      nodejs: 16
    commands:
      - npm install
  build:
    commands:
      - npm run build
  post_build:
    commands:
      - echo "Zipping the build folder..."
      - zip -r build.zip ./build
      - echo "Deploying to CodeDeploy with Rolling Update"
      - aws deploy create-deployment --application-name ReactAppRolling \
          --deployment-group-name RollingGroup \
          --revision file://build.zip \
          --deployment-config-name CodeDeployDefault.OneAtATime
```

---

### **EC2 기반 구성과 React 프로젝트 세팅**

1. **EC2 인스턴스 생성**:
   - AWS 콘솔 → EC2 → **Launch Instance** 클릭.
   - Amazon Linux 2 또는 Ubuntu 선택.
   - 보안 그룹에서 HTTP(80)와 SSH(22) 포트를 열어줌.

2. **Node.js 설치**:
   - EC2에 SSH로 접속:
     ```bash
     ssh -i my-key.pem ec2-user@<EC2_PUBLIC_IP>
     ```
   - Node.js 설치:
     ```bash
     curl -fsSL https://rpm.nodesource.com/setup_16.x | sudo bash -
     sudo yum install -y nodejs
     ```

3. **React 프로젝트 배포**:
   - 프로젝트 코드 클론:
     ```bash
     git clone https://github.com/your-repo.git
     cd your-repo
     ```
   - 의존성 설치 및 빌드:
     ```bash
     npm install
     npm run build
     ```
   - HTTP 서버 실행:
     ```bash
     npx serve -s build -l 80
     ```

4. **PM2로 프로세스 관리**:
   - PM2 설치:
     ```bash
     sudo npm install -g pm2
     ```
   - React 애플리케이션 실행:
     ```bash
     pm2 start npx --name "react-app" -- serve -s build -l 80
     pm2 save
     ```

---

## 3. 모놀리식 배포 (Monolithic Deployment)

### **설명**
모놀리식 배포는 프론트엔드와 백엔드를 단일 애플리케이션 패키지로 묶어 배포하는 방식입니다.

### **구현 방법**
1. **AWS EC2** 인스턴스를 생성하고 애플리케이션을 배포.
2. **SSH**로 EC2에 접속하여 코드를 업로드.
3. **NGINX**를 설치하여 정적 파일 서빙 및 백엔드 요청 프록시 처리.
4. 애플리케이션 실행 및 상태 모니터링.

# Monolithic Deployment: React 프론트엔드와 Express 백엔드

## **1. Monolithic Deployment 개요**
- **특징**: 프론트엔드와 백엔드가 하나의 애플리케이션으로 구성되어 단일 서버에서 실행 및 관리되는 배포 방식.
- **구조**:
  - 프론트엔드: React 애플리케이션 (폴더: `frontend`).
  - 백엔드: Express.js 서버 (폴더: `backend`).
  - NGINX를 사용하여 정적 파일과 API 요청을 분기 처리.
- **장점**: 설정이 단순하며 초기 개발 및 배포가 빠름.
- **단점**: 확장성과 유지보수에서 한계.

---

## **2. 생성해야 할 리소스 및 AWS 서비스 설명**

### **AWS EC2 (Elastic Compute Cloud)**
- **역할**: React 프론트엔드와 Express 백엔드 서버를 실행.
- **특징**:
  - 인스턴스 내부에서 Node.js와 NGINX를 사용하여 애플리케이션 서빙.

### **AWS S3 (Simple Storage Service)**
- **역할**: 정적 파일(HTML, CSS, JS) 백업 및 저장소로 사용 가능.

### **AWS IAM (Identity and Access Management)**
- **역할**: EC2와 S3 접근 권한 관리.

### **AWS CloudWatch**
- **역할**: 애플리케이션 및 서버 모니터링.

---

## **3. 설정 순서**

### **1단계: 프로젝트 구조**

```plaintext
project-root/
├── backend/           # Express.js 서버 코드
│   ├── package.json
│   ├── server.js
│   └── routes/
├── frontend/          # React 애플리케이션
│   ├── package.json
│   ├── public/
│   ├── src/
│   └── build/         # React 빌드 결과
└── Dockerfile         # 전체 애플리케이션을 위한 Dockerfile
```

### **2단계: NGINX 설정**

1. **NGINX 설치**:
   - EC2에 접속 후 NGINX 설치:
     ```bash
     sudo yum update -y
     sudo amazon-linux-extras enable nginx1
     sudo yum install -y nginx
     ```

2. **NGINX 설정 파일 편집**:
   - 설정 파일 위치: `/etc/nginx/conf.d/default.conf`.
   - 내용:
     ```nginx
     server {
         listen 80;

         location / {
             root /var/www/frontend;
             index index.html;
             try_files $uri /index.html;
         }

         location /api/ {
             proxy_pass http://localhost:3000;
             proxy_http_version 1.1;
             proxy_set_header Upgrade $http_upgrade;
             proxy_set_header Connection 'upgrade';
             proxy_set_header Host $host;
             proxy_cache_bypass $http_upgrade;
         }
     }
     ```

3. **정적 파일 및 서버 실행**:
   - React 빌드 파일 복사:
     ```bash
     sudo mkdir -p /var/www/frontend
     sudo cp -r /home/ec2-user/project-root/frontend/build/* /var/www/frontend/
     ```
   - NGINX 재시작:
     ```bash
     sudo systemctl restart nginx
     ```

### **3단계: Express 백엔드 구성**

1. **백엔드 서버 설정**:
   - `backend/server.js`:
     ```javascript
     const express = require('express');
     const app = express();

     // Serve static files from React build
     app.use(express.static('../frontend/build'));

     // API routes
     app.get('/api/hello', (req, res) => {
         res.json({ message: 'Hello from server!' });
     });

     // Catch-all handler for React routing
     app.get('*', (req, res) => {
         res.sendFile(require('path').join(__dirname, '../frontend/build', 'index.html'));
     });

     const PORT = process.env.PORT || 3000;
     app.listen(PORT, () => {
         console.log(`Server is running on port ${PORT}`);
     });
     ```

2. **Express 실행**:
   ```bash
   cd backend
   npm install
   node server.js
   ```

3. **PM2로 Express 관리**:
   ```bash
   sudo npm install -g pm2
   pm2 start backend/server.js --name "backend-server"
   pm2 save
   ```

---

## **4. 코드 파이프라인 설정**

### **1. CodePipeline 단계**
1. **Source 단계**:
   - GitHub 또는 CodeCommit을 소스 리포지토리로 사용.

2. **Build 단계**:
   - CodeBuild를 사용하여 React 애플리케이션 빌드 및 Express 백엔드 준비.

3. **Deploy 단계**:
   - EC2 인스턴스에 빌드 결과 배포.

### **2. buildspec.yml 파일**
```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      nodejs: 16
    commands:
      - echo "Installing dependencies..."
      - cd frontend && npm install
      - cd ../backend && npm install
  build:
    commands:
      - echo "Building React app..."
      - cd ../frontend && npm run build
  post_build:
    commands:
      - echo "Packaging files..."
      - zip -r deployment-package.zip frontend/build backend
artifacts:
  files:
    - deployment-package.zip
```

### **3. 배포 스크립트**
- EC2에서 실행할 배포 스크립트 예시:
```bash
#!/bin/bash
# Extract deployment package
unzip deployment-package.zip -d /home/ec2-user/project-root

# Move frontend build to NGINX directory
sudo cp -r /home/ec2-user/project-root/frontend/build/* /var/www/frontend/

# Restart backend server with PM2
pm2 restart backend-server
```

---

## **5. 추가적인 구성 및 유지 관리**

### **자동화된 시작 스크립트 (User Data)**
- EC2 생성 시 초기 설정 자동화:
  ```bash
  #!/bin/bash
  yum update -y
  amazon-linux-extras enable nginx1
  yum install -y nginx git nodejs
  systemctl start nginx

  git clone https://github.com/your-repo.git /home/ec2-user/project-root
  cd /home/ec2-user/project-root

  cd frontend
  npm install
  npm run build
  sudo cp -r build/* /var/www/frontend/

  cd ../backend
  npm install
  pm2 start server.js --name "backend-server"
  pm2 save
  ```

---

## 4. CI/CD 파이프라인을 통한 자동화 배포

### **설명**
CI/CD 파이프라인은 코드 변경 사항을 자동으로 통합, 테스트, 배포하는 자동화된 워크플로를 제공합니다.

### **구현 방법**
1. **AWS CodePipeline** 설정:
   - **Source**: GitHub, CodeCommit 등에서 코드 가져오기.
   - **Build**: CodeBuild를 사용해 빌드.
   - **Deploy**: S3, ECS, 또는 Elastic Beanstalk로 배포.
2. **빌드 실패/성공 알림**: AWS SNS를 사용하여 개발자에게 알림 전송.
3. **환경별 파이프라인 구성**: 개발, 스테이징, 프로덕션 환경으로 구분.

# CI/CD 파이프라인을 통한 React 프로젝트 자동화 배포

CI/CD 파이프라인을 통해 React 프로젝트를 자동으로 빌드, 테스트, 배포하는 환경을 AWS에서 설정합니다. 이를 위해 사용되는 AWS 서비스와 설정 절차를 자세히 설명하고, 자동화 배포를 위한 `buildspec.yml` 작성 방법을 포함합니다.

---

## **1. CI와 CD의 의미**

### **1.1 CI (Continuous Integration)**
- **의미**: 지속적인 통합. 개발자들이 작성한 코드를 주기적으로 통합하여, 충돌을 조기에 발견하고 해결하는 프로세스.
- **특징**:
  - 코드 변경 사항을 자동으로 빌드하고 테스트.
  - 팀 간 협업을 원활하게 하고 코드 품질을 유지.

### **1.2 CD (Continuous Deployment)**
- **의미**: 지속적인 배포. CI 단계에서 성공적으로 빌드된 코드를 자동으로 배포하여 사용자에게 제공하는 프로세스.
- **특징**:
  - 릴리스 과정을 자동화하여 시간 절약.
  - 안정성과 신속성을 강화.

---

## **2. AWS에서 사용되는 서비스와 설명**

### **2.1 AWS CodePipeline**
- **역할**: 전체 CI/CD 파이프라인을 관리.
- **특징**:
  - 단계별 워크플로를 정의하여 소스 변경 사항을 자동으로 배포.

### **2.2 AWS CodeBuild**
- **역할**: 소스 코드를 빌드하고 테스트 실행.
- **특징**:
  - 다양한 런타임 환경 지원 (Node.js, Python 등).
  - 빌드 아티팩트를 생성하여 S3 또는 배포 단계로 전달.

### **2.3 AWS S3**
- **역할**: React 빌드 결과물을 저장하고, 정적 파일을 호스팅.
- **특징**:
  - 글로벌 스케일에서 정적 파일 호스팅 가능.
  - CodePipeline과 통합하여 빌드 결과를 저장.

### **2.4 AWS CloudFront**
- **역할**: S3에서 제공되는 정적 파일을 CDN을 통해 전 세계로 빠르게 전달.
- **특징**:
  - HTTPS를 기본 지원하여 보안 강화.
  - 캐싱을 통해 성능 최적화.

### **2.5 AWS IAM (Identity and Access Management)**
- **역할**: AWS 리소스 접근을 제어.
- **특징**:
  - CodePipeline, CodeBuild, S3 간 권한을 설정.

---

## **3. CI/CD 파이프라인 설정 순서**

### **3.1 S3 버킷 생성 및 설정**
1. AWS 콘솔 → **S3** → **Create Bucket**:
   - 버킷 이름: `react-project-artifacts` (빌드 결과 저장용).
   - 퍼블릭 액세스 비활성화.

2. **정적 웹 호스팅 활성화**:
   - S3 버킷 → **Properties** → **Static Website Hosting**.
   - Index Document: `index.html`.

### **3.2 IAM 역할 생성**
1. AWS 콘솔 → **IAM** → **Roles** → **Create Role**:
   - 서비스: **CodePipeline** 선택.
   - 정책 연결:
     - **AmazonS3FullAccess**.
     - **AWSCodeBuildAdminAccess**.

2. 동일한 방식으로 **CodeBuild** 전용 역할 생성.

### **3.3 CodeBuild 프로젝트 생성**
1. AWS 콘솔 → **CodeBuild** → **Create Build Project**:
   - **Source**: CodePipeline (Source를 직접 받음).
   - **Environment**:
     - 런타임: **Node.js**.
     - 운영 체제: Amazon Linux 2.
     - 권한: 생성한 CodeBuild IAM 역할.
   - **Buildspec**:
     - `buildspec.yml` 파일 사용.

### **3.4 CloudFront 배포 설정**
1. AWS 콘솔 → **CloudFront** → **Create Distribution**:
   - Origin: S3 버킷(`react-project-artifacts`).
   - Default Cache Behavior:
     - Viewer Protocol Policy: Redirect HTTP to HTTPS.
   - 배포 완료 후 URL 확인.

### **3.5 CodePipeline 생성**
1. AWS 콘솔 → **CodePipeline** → **Create Pipeline**:
   - **Source**: GitHub 또는 CodeCommit.
   - **Build**: CodeBuild 프로젝트 연결.
   - **Deploy**: S3 버킷에 React 빌드 결과 저장.

---

## **4. `buildspec.yml` 작성 방법**

### **4.1 파일 내용**
```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      nodejs: 16
    commands:
      - echo "Installing dependencies..."
      - npm install
  build:
    commands:
      - echo "Building the project..."
      - npm run build
  post_build:
    commands:
      - echo "Syncing build output to S3..."
      - aws s3 sync ./build s3://react-project-artifacts --delete
artifacts:
  files:
    - '**/*'
cache:
  paths:
    - node_modules/**/*
```

### **4.2 옵션 설명**
1. **`version`**:
   - Buildspec 파일의 형식 버전을 나타냄.

2. **`phases`**:
   - **`install`**: 빌드에 필요한 환경과 의존성 설치.
   - **`build`**: React 애플리케이션 빌드 실행.
   - **`post_build`**: 빌드 후 결과를 S3에 동기화.

3. **`artifacts`**:
   - 생성된 빌드 결과를 파이프라인에서 사용할 수 있도록 설정.

4. **`cache`**:
   - 의존성을 캐싱하여 빌드 속도를 최적화.

---

## **5. 결과 확인**
1. **S3 및 CloudFront URL 확인**:
   - CloudFront 도메인에서 애플리케이션 배포 확인.
2. **CodePipeline 자동화 테스트**:
   - GitHub에서 소스 변경 후 CodePipeline이 실행되는지 확인.

이 설정을 통해 React 프로젝트의 빌드, 테스트, 배포를 완전 자동화할 수 있습니다. 추가 질문이 있다면 알려주세요!

---

## 5. A/B 테스트 배포

### **설명**
A/B 테스트 배포는 사용자 그룹을 나누어 서로 다른 애플리케이션 버전을 제공하여 최적의 사용자 경험을 찾는 방식입니다.

### **구현 방법**
1. **AWS Elastic Load Balancer (ELB)**:
   - 두 개의 버전(A와 B)을 호스팅하는 서버를 설정.
   - ELB의 트래픽 분배 규칙을 설정하여 사용자 그룹별로 요청을 라우팅.
2. **Lambda@Edge**:
   - CloudFront와 Lambda@Edge를 사용하여 요청을 분석하고 사용자에게 다른 버전 제공.
3. **AWS Amplify**:
   - Amplify Console에서 A/B 테스트를 위한 여러 배포 브랜치 구성.
4. **결과 분석**:
   - AWS CloudWatch 또는 다른 분석 도구를 통해 사용자 피드백 및 성과를 수집.

# A/B 테스트 배포 설정

A/B 테스트 배포는 두 가지 버전(A와 B)의 애플리케이션을 서로 다른 사용자 그룹에 배포하여 성능, 안정성, 사용자 경험을 비교하는 방식입니다. AWS에서 이를 설정하기 위해 **CloudFront**, **S3**, **Lambda@Edge**를 활용하며, **CodePipeline**을 통해 자동화 배포도 설정할 수 있습니다.

---

## **1. A/B 테스트를 위한 AWS 리소스**

### **1.1 AWS S3**
- **역할**: 정적 파일을 호스팅하며 두 가지 버전(A와 B)을 각각 저장.
- **설정**:
  1. S3 버킷 2개 생성:
     - `frontend-a`: A 버전을 저장.
     - `frontend-b`: B 버전을 저장.
  2. 각 버킷에 React 빌드 파일 업로드.

### **1.2 AWS CloudFront**
- **역할**: 사용자 요청을 A 또는 B 버전의 S3로 라우팅.
- **설정**:
  1. CloudFront 배포 생성:
     - **Origin**: 두 개의 S3 버킷(`frontend-a`, `frontend-b`) 추가.
     - **Default Behavior**: Lambda@Edge를 연결하여 요청 분기 처리.

### **1.3 AWS Lambda@Edge**
- **역할**: CloudFront 요청을 처리하며, A/B 버전의 리소스를 반환.
- **설정**:
  1. Lambda 함수 생성:
     - **언어**: Node.js.
     - **로직**: 쿠키 또는 헤더 기반으로 요청 분기 처리.
     - 예제:
       ```javascript
       exports.handler = async (event) => {
           const request = event.Records[0].cf.request;
           const headers = request.headers;

           // 쿠키 기반 A/B 테스트
           if (headers.cookie && headers.cookie[0].value.includes("version=B")) {
               request.origin.s3.domainName = "frontend-b.s3.amazonaws.com";
           } else {
               request.origin.s3.domainName = "frontend-a.s3.amazonaws.com";
           }

           return request;
       };
       ```
  2. Lambda 함수 배포:
     - Lambda@Edge로 배포하여 CloudFront에 연결.

### **1.4 AWS CodePipeline**
- **역할**: 소스 코드 변경 시 A와 B 버전의 S3로 자동 배포.

---

## **2. 설정 순서**

### **2.1 S3 버킷 생성 및 파일 업로드**
1. AWS 콘솔에서 **S3**로 이동 → **Create Bucket** 클릭:
   - 버킷 이름: `frontend-a` (A 버전 저장).
   - 버킷 이름: `frontend-b` (B 버전 저장).

2. 로컬에서 React 프로젝트 빌드:
   ```bash
   npm run build
   ```
3. 빌드 결과를 S3에 업로드:
   ```bash
   aws s3 sync ./build s3://frontend-a
   aws s3 sync ./build s3://frontend-b
   ```

### **2.2 CloudFront 설정**
1. AWS 콘솔에서 **CloudFront**로 이동 → **Create Distribution** 클릭.
2. **Origin**:
   - Origin 1: `frontend-a.s3.amazonaws.com`.
   - Origin 2: `frontend-b.s3.amazonaws.com`.
3. **Default Behavior**:
   - Viewer Protocol Policy: Redirect HTTP to HTTPS.
   - Add Lambda Function: Lambda@Edge를 요청 처리에 추가.

### **2.3 Lambda@Edge 생성 및 연결**
1. Lambda 함수 작성:
   ```javascript
   exports.handler = async (event) => {
       const request = event.Records[0].cf.request;
       const headers = request.headers;

       // 쿠키 기반 A/B 테스트
       if (headers.cookie && headers.cookie[0].value.includes("version=B")) {
           request.origin.s3.domainName = "frontend-b.s3.amazonaws.com";
       } else {
           request.origin.s3.domainName = "frontend-a.s3.amazonaws.com";
       }

       return request;
   };
   ```
2. Lambda@Edge 배포:
   - AWS Lambda 콘솔에서 함수 생성 후 CloudFront 배포에 연결.

### **2.4 CodePipeline 설정**
1. **Source 단계**:
   - GitHub 또는 CodeCommit에서 React 프로젝트 연결.

2. **Build 단계**:
   - CodeBuild로 React 프로젝트 빌드:
     ```yaml
     version: 0.2

     phases:
       install:
         runtime-versions:
           nodejs: 16
         commands:
           - npm install
       build:
         commands:
           - npm run build
       post_build:
         commands:
           - echo "Syncing with S3..."
           - aws s3 sync ./build s3://frontend-a
           - aws s3 sync ./build s3://frontend-b
     ```

3. **Deploy 단계**:
   - S3에 동기화된 결과가 CloudFront로 자동 반영.

---

## **3. 결과 확인**
1. 브라우저에서 CloudFront 배포 URL 확인.
2. 브라우저 개발자 도구에서 쿠키를 설정하여 A 또는 B 버전을 확인:
   - A 버전:
     ```
     document.cookie = "version=A; path=/";
     ```
   - B 버전:
     ```
     document.cookie = "version=B; path=/";
     ```

이 설정을 통해 A/B 테스트를 구현하고, CodePipeline을 통해 자동화된 배포를 설정할 수 있습니다. 추가 질문이 있다면 말씀해주세요!
