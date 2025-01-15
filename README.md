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
  - [3. 모놀리식 배포 (Monolithic Deployment)](#3-모놀리식-배포-monolithic-deployment)
    - [**설명**](#설명-2)
    - [**구현 방법**](#구현-방법-2)
  - [4. CI/CD 파이프라인을 통한 자동화 배포](#4-cicd-파이프라인을-통한-자동화-배포)
    - [**설명**](#설명-3)
    - [**구현 방법**](#구현-방법-3)
  - [5. A/B 테스트 배포](#5-ab-테스트-배포)
    - [**설명**](#설명-4)
    - [**구현 방법**](#구현-방법-4)
  - [학습 및 실습을 위한 준비 사항](#학습-및-실습을-위한-준비-사항)


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

---

## 3. 모놀리식 배포 (Monolithic Deployment)

### **설명**
모놀리식 배포는 프론트엔드와 백엔드를 단일 애플리케이션 패키지로 묶어 배포하는 방식입니다.

### **구현 방법**
1. **AWS EC2** 인스턴스를 생성하고 애플리케이션을 배포.
2. **SSH**로 EC2에 접속하여 코드를 업로드.
3. **NGINX**를 설치하여 정적 파일 서빙 및 백엔드 요청 프록시 처리.
4. 애플리케이션 실행 및 상태 모니터링.

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

---

## 학습 및 실습을 위한 준비 사항
1. **AWS 계정 생성**:
   - IAM 사용자를 만들고 권한 설정.
2. **필수 도구 설치**:
   - AWS CLI, Node.js, 프론트엔드 프레임워크(React/Vue/Angular).
3. **소규모 애플리케이션 설계**:
   - 정적 파일 기반의 간단한 SPA 또는 기본 서버 기반 애플리케이션.

각 전략을 단계적으로 실습하면서 AWS의 다양한 서비스를 익힐 수 있습니다.
