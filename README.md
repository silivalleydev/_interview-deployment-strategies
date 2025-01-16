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
- [Next.js App Router 기반 모놀리식 배포 가이드 (Nginx \& Next.js 분리)](#nextjs-app-router-기반-모놀리식-배포-가이드-nginx--nextjs-분리)
  - [**1. 개발 및 구현 순서**](#1-개발-및-구현-순서)
    - [**1.1 프로젝트 설정**](#11-프로젝트-설정)
  - [**2. Docker 환경 설정**](#2-docker-환경-설정)
    - [**2.1 Next.js 서버용 Dockerfile 작성**](#21-nextjs-서버용-dockerfile-작성)
    - [**2.2 Nginx용 Dockerfile 작성**](#22-nginx용-dockerfile-작성)
    - [**2.3 Docker Compose 작성**](#23-docker-compose-작성)
  - [**3. Nginx 설정 파일 작성**](#3-nginx-설정-파일-작성)
  - [**4. AWS CodePipeline으로 무중단 배포 설정**](#4-aws-codepipeline으로-무중단-배포-설정)
    - [**4.1 CodePipeline 구성 파일 작성**](#41-codepipeline-구성-파일-작성)
    - [**4.2 ECS 서비스 구성**](#42-ecs-서비스-구성)
  - [**5. 결론 및 최적화**](#5-결론-및-최적화)
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
  - [A/B 테스트 배포](#ab-테스트-배포)
    - [**설명**](#설명-1)
    - [**구현 방법**](#구현-방법-1)
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
                "Action": "s3:ListBucket",
                "Resource": "arn:aws:s3:::sili-cdn-deployment"
            },
            {
                "Effect": "Allow",
                "Principal": "*",
                "Action": [
                    "s3:GetObject",
                    "s3:PutObject",
                    "s3:DeleteObject"
                ],
                "Resource": "arn:aws:s3:::sili-cdn-deployment/*"
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
   - 생성 후 원본탭, 원본 편집에 들어가 origin domain을 `<bucket-name>.s3-website.<region>.amazonaws.com` 형식으로 변경
     - 변경후 무효화탭 -> 무효화 생성 -> 객체 경로 추가에 `/*` 입력하고 무효화 생성 누르고 도메인으로 접속

---

### **3단계: CodePipeline 생성**
1. **IAM 역할 생성**:
   - AWS 콘솔 → IAM → 역할 생성.
   - 정책 연결:
     - **AWSCodePipelineFullAccess**
     - **AWSCodeBuildAdminAccess**
     - **AmazonS3FullAccess**
   - role 신뢰관계에 아래내용추가
    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Principal": {
                    "AWS": "arn:aws:iam::xxxxxx:root"
                },
                "Action": "sts:AssumeRole",
                "Condition": {}
            },
            {
                "Effect": "Allow",
                "Principal": {
                    "Service": "codepipeline.amazonaws.com"
                },
                "Action": "sts:AssumeRole"
            }
        ]
    }
    ```
    - 예를들어 `codebuild-cdn-deployment-service-role` 과 같은 `code build용 role`이 생겼다면 해당 role에 `CloudFrontFullAccess` 추가

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

# Next.js App Router 기반 모놀리식 배포 가이드 (Nginx & Next.js 분리)

## **1. 개발 및 구현 순서**

### **1.1 프로젝트 설정**
1. **Next.js 설치 및 초기화**
   - AWS Management Console에서 CodeCommit으로 이동합니다.
   - "리포지토리 생성" 버튼을 클릭합니다.
   - 리포지토리 이름으로 `my-monolith-app`을 입력하고 생성합니다.

2. **폴더 구조 설정**
   ```
   /app/
     - /a/ (사이트 A 페이지)
     - /b/ (사이트 B 페이지)
     - /c/ (사이트 C 페이지, 정적 사이트)
     - /shared/ (공용 컴포넌트 및 유틸리티)
   ```
   **App Router 사용 시** 각 사이트를 `/app/{site}` 디렉토리에 배치합니다.

3. **정적 사이트 페이지 (`/c`) 구성**
   `app/c/page.js`
   ```javascript
   export const dynamic = 'force-static'; // 정적 페이지로 강제 설정

   export default function CPage() {
       return <div>Welcome to Site C!</div>;
   }
   ```

4. **이미지 최적화 및 캐싱 설정**
   Next.js의 `next/image`를 사용하여 이미지 최적화를 구현합니다.

   ```javascript
   import Image from 'next/image';

   export default function HomePage() {
       return (
           <div>
               <h1>Welcome to Site A!</h1>
               <Image
                   src="/images/example.jpg"
                   alt="Example"
                   width={800}
                   height={600}
               />
           </div>
       );
   }
   ```
   이미지 캐싱은 빌드 시 적용되며, Nginx나 CDN과 함께 최적화 가능합니다.

---

## **2. Docker 환경 설정**

### **2.1 Next.js 서버용 Dockerfile 작성**
프로젝트 루트에 `Dockerfile.nextjs` 생성:
```dockerfile
# Base image
FROM node:18 AS builder

# 작업 디렉토리를 설정합니다.
WORKDIR /app

# 필요한 종속성을 설치합니다.
COPY package.json yarn.lock ./
RUN yarn install

# 소스 코드를 복사하고 빌드를 수행합니다.
COPY . ./
RUN yarn build

# Production stage
FROM node:18 AS runner

# 작업 디렉토리를 설정합니다.
WORKDIR /app

# 빌드 결과물을 복사합니다.
COPY --from=builder /app .

# 어플리케이션 포트를 노출합니다.
EXPOSE 3000

# 애플리케이션 실행 명령어를 정의합니다.
CMD ["yarn", "start"]
```

### **2.2 Nginx용 Dockerfile 작성**
프로젝트 루트에 `Dockerfile.nginx` 생성:
```dockerfile
# Base Nginx image
FROM nginx:alpine

# Nginx 설정 파일 복사
COPY nginx.conf /etc/nginx/nginx.conf

# 정적 파일 복사
COPY out /usr/share/nginx/html

# Nginx 포트 노출
EXPOSE 80
```

### **2.3 Docker Compose 작성**
`docker-compose.yml` 파일:
```yaml
version: "3.8"
services:
  nginx:
    build:
      context: .
      dockerfile: Dockerfile.nginx
    ports:
      - "80:80"
    depends_on:
      - nextjs

  nextjs:
    build:
      context: .
      dockerfile: Dockerfile.nextjs
    ports:
      - "3000:3000"
    environment:
      NODE_ENV: production
```

---

## **3. Nginx 설정 파일 작성**
`nginx.conf`
```nginx
server {
    listen 80;

    server_name example.com;

    # Next.js 정적 파일 캐싱
    location /_next/static/ {
        root /usr/share/nginx/html;
        expires 1y;
        add_header Cache-Control "public, max-age=31536000";
    }

    # 정적 HTML 파일 서빙
    location /c/ {
        root /usr/share/nginx/html;
        index index.html;
        try_files $uri $uri/ /index.html;
    }

    # 모든 기타 요청은 Next.js 서버로 전달
    location / {
        proxy_pass http://nextjs:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

---

## **4. AWS CodePipeline으로 무중단 배포 설정**

### **4.1 CodePipeline 구성 파일 작성**
`buildspec.yml`
```yaml
version: 0.2
phases:
  install:
    runtime-versions:
      nodejs: 18
    commands:
      - echo Installing dependencies...
      - npm install
  build:
    commands:
      - echo Building the project...
      - npm run build
      - npx next export
  post_build:
    commands:
      - echo Logging in to Amazon ECR...
      - aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com
      - echo Building Next.js image...
      - docker build -f Dockerfile.nextjs -t my-monolith-app-nextjs .
      - docker tag my-monolith-app-nextjs:latest $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/my-monolith-app-nextjs:latest
      - docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/my-monolith-app-nextjs:latest
      - echo Building Nginx image...
      - docker build -f Dockerfile.nginx -t my-monolith-app-nginx .
      - docker tag my-monolith-app-nginx:latest $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/my-monolith-app-nginx:latest
      - docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/my-monolith-app-nginx:latest
      - echo Updating ECS services...
      - aws ecs update-service --cluster my-monolith-cluster --service my-monolith-service-nextjs --force-new-deployment
      - aws ecs update-service --cluster my-monolith-cluster --service my-monolith-service-nginx --force-new-deployment
artifacts:
  files:
    - '**/*'
```

### **4.2 ECS 서비스 구성**
1. **Next.js 서비스**:
   - 컨테이너 이름: `my-monolith-app-nextjs`.
   - 포트 매핑: `3000`.
2. **Nginx 서비스**:
   - 컨테이너 이름: `my-monolith-app-nginx`.
   - 포트 매핑: `80`.

---

## **5. 결론 및 최적화**

1. **정적 및 동적 요청 분리**:
   - 정적 파일은 Nginx에서 처리하여 서버 부하를 줄임.
   - 동적 요청은 Next.js 서버에서 처리.

2. **이미지 최적화**:
   - Next.js `next/image`와 Nginx/CloudFront를 조합해 글로벌 이미지 캐싱.

3. **무중단 배포**:
   - CodePipeline과 ECS를 활용해 새로운 배포가 기존 서비스에 영향을 주지 않도록 구현.

4. **구조적 유지보수**:
   - Nginx와 Next.js 서버를 분리하여 관리.
   - 도커 기반으로 CI/CD를 쉽게 구성.


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

## A/B 테스트 배포

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
