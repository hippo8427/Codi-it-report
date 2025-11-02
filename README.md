# 고급 프로젝트 개인 개발 리포트 

**작성자**: 1팀 하상준

**프로젝트명**: Codi-it 

**프로젝트 레포**: [https://github.com/Codi-It-backend](https://github.com/nb02-codi-it-team1/Codi-It-backend)

<br>

## 배포 링크

**백엔드**: [https://codi-it-backend.site](https://codi-it-backend.site/health)

**프론트엔드**: [https://codi-it-frontend.vercel.app](https://codi-it-frontend.vercel.app/products)

**Swagger**: [https://codi-it-backend.site/api-docs](https://codi-it-backend.site/api-docs/)

<br>

## 프로젝트 개요

<br>

**CODI-IT**은 패션 이커머스 환경에서 판매자와 구매자를 연결하는 **고급 패션 커머스 플랫폼**입니다.

상품 등록부터 주문·결제·리뷰·알림·통계까지의 전 과정을 통합 관리할 수 있도록 설계되어,  
판매자에게는 효율적인 매출 관리 환경을, 구매자에게는 직관적이고 즐거운 쇼핑 경험을 제공합니다.

**진행 기간**: 2025.09.15 ~ 2025.11.03

**ERD 구조**: [https://github.com/nb02-codi-it-team1/Codi-It-backend/wiki/ERD](https://github.com/nb02-codi-it-team1/Codi-It-backend/wiki/ERD-%EA%B5%AC%EC%A1%B0)

<br>

## 기술 스택

|     분류     | 사용 도구                          |
| :----------: | ---------------------------------- |
|  프레임워크  | Node.js (Express)                  |
|     언어     | TypeScript                         |
|  스키마/ORM  | Prisma                             |
| 데이터베이스 | PostgreSQL                         |
|  API 문서화  | swagger                            |
| 실시간 알림  | SSE(Server-Sent Events)            |
|    테스트    | Jest + supertest                   |
|  코드 품질   | ESLint + Prettier, class-validator |
|    CI/CD     | Github Actions                     |
|     배포     | AWS EC2, S3, Nginx, PM2, RDS       |
|  협업 도구   | Discord, GitHub, Notion            |
|  일정 관리   | GitHub Issues + Notion             |

<br>

## 주요 구현 기능

#### 인증

1. 이메일 및 비밀번호 기반 로그인/로그아웃

2. 판매자와 구매자에 따라 접근 가능한 기능 구분

3. JWT 토큰 재발급

#### 유저
 
1. 판매자/구매자 유형 선택 회원가입

2. 개인정보 수정, 조회, 탈퇴

3. 관심 스토어 조회

#### 등급

1. 유저 등급 시딩 데이터 구현

#### CI-CD

1. dev 브랜치에 PR 할 경우 test 및 lint 를 실행하는 ci 코드 구현

2. main 브랜치에 push 할 경우 배포 서버에 자동 배포를 실행하는 cd 코드 구현
 

#### 배포

1. aws 서버에 nginx, pm2 를 사용해 백엔드 서버를 배포

2. goddady 에서 도메인을 구매하여 백엔드 서버에 연결

3. vercel 을 사용하여 프론트 서버를 배포

#### 이미지 업로드

1. aws s3 와 multer, multer-s3 를 이용하여 이미지 업로드 미들웨어를 구현

#### 유닛 테스트

1. 유저 유닛 테스트 코드를 구현

2. 인증 테스트 코드를 구현 

#### 통합 테스트

1. 개발이 완료되어있는 프로젝트 API 에 대한 통합 테스트를 구현

#### 팀 문서

1. README.md, Team wiki, 발표 ppt 작성

2. 팀원 각각 작성한 erd를 하나로 통합

<br>

## 트러블 슈팅

### 관심 스토어 조회 에러 수정

#### 구현 방식
1. db 관심 스토어 테이블에서 관심 스토어 조회 

2. 관심 등록된 스토어가 없거나 스토어의 length 가 0 일경우 에러를 던짐.

#### 문제점
1. 관심 스토어가 없어서 에러를 던질 경우, 웹 페이지에서 관심 스토어 목록을 로드할 수 없는 에러 발생

#### 해결 방법
1. 스토어가 없거나 length가 0 일경우 에러를 던지지 않고 빈 배열을 리턴하여 관심스토어가 없더라도 로드 될 수 있게 변경

```ts
 const likedStores = await this.userRepository.getUserLikedStores(userId);

    if (!likedStores || likedStores.length === 0) {
      return [];
    }

 ```
 
<br>

### 유저 아이콘 더미 이미지 오류

#### 구현 방식
1. 회원 가입시, User 테이블의 image 필드의 default 값으로 아무 기능이 없는 단순 문자열을 작성 ("https://example.com/default.png")

#### 문제점
1. 로그인 후 브라우저 유저 아이콘 로드 오류

#### 해결 방법
1. [placehold](https://placehold.co) 사이트를 이용하여 User 텍스트가 쓰여진 더미 이미지로 default 값을 교체

2. 브라우저에서 로드 완료

```ts
  image String?  @default("https://placehold.co/400.jpg?text=user")
```

<br>

### 자동 배포 실행중 npm ci 무한 로딩 오류

#### 구현 방식
1. GitHub Actions VM 으로 소스 코드 복사 후 npm ci 및 npm run build 명령을 실행하여 빌드 수행

2. 빌드된 결과물과 전체 소스 코드를 EC2 서버로 복사

3. 내부에서 .env 생성 등 주요 파일 생성 후  npm ci 실행

#### 문제점
1. ci 실행중 메모리 부족으로 무한 로드 현상 발생

2. 그로 인해서 이후 cd 스크립트가 동작하지 않음 

#### 해결 방법

1. npm ci --ignore-scripts 스크립트를 이용하여 메모리를 사용량이 높다고 추측되는 postinstall 스크립트를 건너뜀

2. 설치가 끝난 이후 메모리 공간이 확보 되면 필요한 설치 스크립트를 추가 실행.


<br>

### .env prod/dev 환경 분리 트러블

#### 구현 방식
1. 쿠키 옵션을 환경별로 다루기 위해 .env prod/dev 분리를 시도

2. .env 를 .env.prod, .env.dev 로 나눔

#### 문제점
 1. .env.dev와 .env.prod로 분리했지만, Prisma를 비롯한 여러 도구에 별도의 환경 파일을 지정해야 하는 번거로움이 생김

 2. 반면, 애초에 분리의 목적이 단순히 쿠키 옵션 하나를 구분하는 것이었기 때문에, 전체 복잡도에 비해 효용이 크지 않음
 
#### 해결 방법
1. 프로젝트의 크지않은 규모와 좋지 못 했던 효율을 고려하여 다시 .env 의 형태로 통합

2. 쿠키 옵션은 NODE_ENV 로 자동으로 분기되게 처리

```ts
export const IS_PROD = process.env.NODE_ENV === 'production';
export const COOKIE_SECURE = IS_PROD ? true : false;
export const COOKIE_SAMESITE = IS_PROD ? 'none' : 'lax';
```

2. 배포시엔 .env.prod 가 아니라 Actions secrets 에 등록되어있는 Repository secrets를 참조하여 
   .env를 새로 작성하도록 변경

<br>

### Nginx 와 Pm2 설정 파일 트러블

#### 구현 방식
1. ec2 내부에서 nginx 와 pm2 설정 파일을 작성

#### 문제점
1. nginx 설정을 바꾸고 싶을 경우 인스턴스에 재접속해 내부에서 수정해야하는 번거로움

2. pm2 설정파일을 ec2 내부에서 직접 작성 할 경우 , 추후 로컬 cd 코드로 배포시 로컬과 배포서버 내부 코드의 충돌 여부

#### 해결 방법

1. 로컬 소스코드 디렉토리에서 nginx 설정과 pm2 설정 파일을 작성 

2. nginx 파일은 cd 과정에서 CI/CD 도구가 소스코드를 ec2 내부에 복사 후  
   로컬 저장소에 포함된 nginx 설정 파일을 실제 nginx 설정 경로로 이동
   
   ```bash
   sudo cp ./nginx/nginx.conf /etc/nginx/conf.d/codi-it-proxy.conf
   ```

3. pm2 파일은 cd 과정에서 로컬 환경에서 작성된 설정 파일(ecosystem.config.js) 을  
   CI/CD 도구를 통해 ec2 서버에 복사하고 적용

<br>

## 협업 및 피드백

1. 개발 규칙을 팀 위키 및 팀 프로젝트 문서에 정의해 두어, 모든 팀원이 참고하기 편했고 규칙의 일관성을 유지할 수 있었습니다.

2. PR 작성 시 리뷰어를 지정하고, 리뷰를 받은 뒤 본인이 직접 머지하는 방식으로 진행했습니다.
   
   디스코드 깃훅 연동 코드를 직접 작성하여 PR, Approve, Merge 시 디스코드로 연동 알림이 가도록 적용 하였습니다.

3. 이슈 작성을 통해 작업중인 내용의 세부사항을 파악하기 편리 했고,  
   PR 작성시 관련 이슈들을 참조하여 PR의 내용이 좀더 간결해지고 깔끔해질 수 있었습니다.

4. 노션 프로젝트 계획서의 테이블 뷰 표를 이용해 프로젝트 진행도와 팀원들의 작업 내용, 기간, 상태를 명확하게 파악할 수 있었습니다.   

<br><br>

## 코드 품질 및 최적화

#### ESLint, Prettier 적용

1. ESLint와 Prettier 라이브러리를 도입하여 팀의 코딩 스타일을 통일시켰습니다.  

2. ESLint 검사를 CI 파이프라인에 포함시켜 코드 품질을 자동으로 점검할 수 있도록 구성했습니다.

#### Class-validator 적용
1. Class-validator 라이브러리를 활용하여 런타임에서 입력 데이터의 유효성을 자동으로 검증하도록 구성했습니다.

2. DTO 단에서 필수 값, 문자열 길이, 숫자 범위 등 데이터 규칙을 명확히 정의함으로써, 런타임 오류 발생 가능성을 최소화했습니다.

#### Jest, Supertest 기반 테스트 자동화

1. Jest를 활용하여 서비스 및 레포지토리 파일의 유닛 테스트를 구현했습니다.

2. Supertest를 함께 사용하여 API 엔드포인트 통합 테스트를 자동화함으로써, 실제 요청/응답 흐름의 안정성을 검증했습니다.

3. 테스트는 CI 과정에도 포함되어 있어, 코드 변경 시마다 자동 테스트 수행되도록 구성했습니다.

#### Feature-based Architecture 적용

1. 프로젝트 구조를 기능 단위로 설계하여,  
   각 기능(user, product, dashboard 등)이 독립적인 모듈로 동작하도록 구성했습니다.

2. 전통적인 레이어드 아키텍쳐에 비해 기능별 책임이 명확하고, 코드 탐색 및 유지보수가 용이하며,  
   새로운 기능 추가 시 다른 모듈에 영향을 주지 않는 장점이 있습니다.

3. 각 기능 폴더 내부에는 controller, service, repository, dto, router 등이 함께 포함되어  
   하나의 기능이 독립적으로 구분된 구조를 유지합니다.

<br>

## 향후 개선 사항

1. NestJS 프레임워크 도입

   Express 를 사용하면서 코드 가독성과 재사용성 그리고 자동 문서작업  
   그리고 나중에 추가된 TypeScript와의 낮은 호환성에서 어려움을 겪었습니다.  
   현재는 Express 기반으로 서버를 구성했지만, 향후 프로젝트에서는 nest.js 를 사용해볼 예정입니다.  
   
2. 상세 로깅 시스템 강화

   현재의 로깅은 단순한 콘솔 출력 중심으로 구성되어 있습니다.  
   향후에는 Winston 같은 전문 로깅 라이브러리를 도입하여,  
   오류 발생 시 어느 기능에서 어떤 상황으로 오류가 발생했는지를 상세히 기록할 수 있도록 개선할 계획입니다.  
   이를 통해서 오류 원인 추적 속도를 높이고, 문제에 대한 대응 효율성을 향상시킬 예정입니다.

3. Docker 기반 환경 구성

   프로젝트 개발 과정에서는 docker를 깊이 있게 적용하지 못했습니다.  
   그러나 docker의 환경 일관성 보장 및 배포 자동화 효율성을 직접 체감하며 그 필요성을 인식했습니다.  
   추후에는 docker를 활용하여 로컬 개발 환경과 배포 환경 간의 설정 차이 문제를 최소화하고,  
   docker-compose를 이용해 데이터베이스, 서버, Nginx 등 전체 서비스를 컨테이너 단위로 관리할 예정입니다.

4. 마이그레이션 파일 관리 체계화

   현재는 마이그레이션 파일 충돌을 방지하기 위해 .gitignore로 관리에서 제외하였습니다.  
   하지만 추후에는 마이그레이션을 한 명 또는 전담 프로세스에서 관리하여, 충돌을 방지할 계획입니다.

5. 테스트 코드 품질 개선

   현재 제가 작성한 테스트 코드는 기본적인 상태 코드 및 응답 값 검증 수준에 머물러 있습니다.  
   향후에는 테스트 커버리지 확대와 Mocking 기반의 테스트 코드 강화를 통해  
   비즈니스 로직과 API의 동작을 더 면밀히 검증할 계획입니다.

6. CI/CD 파이프라인 고도화

   현재 작성된 CI/CD 스크립트는 기본적인 테스트, 빌드 및 배포 기능만을 수행하고 있습니다.  
   향후에는 에러 핸들링, 헬스체크, 롤백 자동화 등을 추가하여 배포 과정의 안정성과 복원력을 강화할 계획입니다.

7. AWS 및 인프라 지식 고도화

   현재 AWS 배포 과정에서 VPC, Security Group, IAM 권한 구조 등에 대한 이해가 충분하지 않습니다.  
   향후에는 해당 영역을 심화 학습하여  보안 설정, 네트워크 구조 설계, Auto Scaling 구성 등  
   운영 환경 전반에 대한 이해도를 높일 계획입니다.
   
<br>

## 협업 및 관리 문서

**팀 위키**: [https://github.com/nb02-codi-it-team1/Codi-It-backend/wiki](https://github.com/nb02-codi-it-team1/Codi-It-backend/wiki)

**팀 이슈**: [https://github.com/nb02-codi-it-team1/Codi-It-backend/issues](https://github.com/nb02-codi-it-team1/Codi-It-backend/issues?q=is%3Aissue%20state%3Aclosed)

**팀 PR**: [https://github.com/nb02-codi-it-team1/Codi-It-backend/pull-request](https://github.com/nb02-codi-it-team1/Codi-It-backend/pulls?q=is%3Apr+is%3Aclosed)

