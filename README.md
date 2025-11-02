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

### 기술 스택

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

### 주요 구현 기능

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

### ESLint 네이밍 컨벤션 설정 수정

#### 구현 방식

1. 프로젝트 초기에 eslint/naming-convention 규칙을 세부적으로 설정하여 변수, 함수, 클래스, 인터페이스, enum, 타입, 객체 리터럴 키 등 네이밍 규칙을 강제.

2. 특히 인터페이스에는 I prefix, enum에는 Enum suffix, 타입 파라미터에는 T prefix를 사용하도록 설정.

#### 문제점
1. 규칙이 지나치게 엄격해 개발 속도를 저하시킴. (이곳저곳에서 오류남, 명세서 요구사항이랑 충돌함)

2. prefix, suffix 강제 규칙이 전역 타입 선언 시나 enum 사용 시 이름을 길게 만들어서 작성이 번거로웠음.

3. prefix, suffix 가 없어도 각자 역할이 뭔지 쉽게 알 수 있었음.

#### 해결 방법
1. 인터페이스, enum, 타입 파라미터에 대한 prefix/suffix 규칙 제거.

2. PascalCase 또는 camelCase 형식만 강제하여 가독성과 일관성은 유지하되, 불필요한 네이밍 강제는 완화.

3. 명세서와 충돌하는 사항은 프론트엔드 코드를 수정

<br>

### 첨부파일 단일 업로드 제한

#### 구현 방식
1.  multer의 upload.single('file') 메서드를 사용하여 단일 파일만 업로드 가능.

2. 업로드된 파일은 req.file 객체로 받아 Firebase Storage에 저장하고, 서명 URL(Signed URL)을 생성하여 응답으로 전달.

3. 파일명은 원본 파일명 + 업로드 시각(timestamp) + UUID 조합으로 생성해 중복 방지.

#### 문제점
1. 프론트엔드에서는 여러 파일을 동시에 업로드할 수 있도록 UI가 구현되어 있었으나, 백엔드에서 단일 파일만 허용.
  
2. 요청 필드명(file)이 프론트 요구사항(files)과 불일치.

#### 해결 방법
1. Multer 설정을 upload.array('files')로 변경해 다중 업로드 허용.

2. 컨트롤러에서 req.files를 배열로 받아 Promise.all을 사용해 병렬 업로드 처리.

3. 파일명 생성, Firebase 업로드, URL 생성 과정을 배열 순회하며 처리.

4. 응답 형식을 { data: uploadedUrls } 형태로 변경해 여러 파일 URL 반환.

```js
router.post('/', upload.array('files'), uploadFiles);

const files = req.files as Express.Multer.File[];
if (!files) throw { status: statusCode.badRequest, message: errorMsg.wrongRequestFormat };

const uploadedUrls = await Promise.all(
  files.map(async (file) => {
    const { originalname, buffer, mimetype } = file;
    const ext = path.extname(originalname);
    const baseName = path.basename(originalname, ext);
    const safeBaseName = baseName.replace(/[^a-zA-Z0-9_-]/g, '_');
    const timestamp = Date.now();
    const firebaseFileName = `${safeBaseName}_${timestamp}_${randomUUID()}${ext}`;
    const destination = `files/${firebaseFileName}`;

    const fileRef = bucket.file(destination);
    await fileRef.save(buffer, { metadata: { contentType: mimetype } });

    const [url] = await fileRef.getSignedUrl({ action: 'read', expires: '03-01-2500' });
    return url;
  })
);

return res.status(200).json({ data: uploadedUrls });
```

<br>

### orderBy 네이밍 컨벤션 오류

#### 구현 방식
1. 프론트엔드에서 할 일 목록을 정렬할 때, orderBy 쿼리 파라미터를 전송.

2. 백엔드에서 해당 값을 taskRepository.getAllTasks()의 orderBy 옵션으로 전달.

3. Prisma ORM이 DB 컬럼명을 기반으로 정렬을 수행 (createdAt, dueDate 등).

#### 문제점
1. 프론트엔드에서 endDate라는 키를 보내지만, DB 컬럼명은 dueDate.

2. Prisma가 인식하지 못하는 필드를 전달하면 쿼리 에러 발생.

3. 컬럼명이 불일치해 정렬 기능이 동작하지 않음.

#### 해결 방법

1. 백엔드에서 프론트 orderBy 값을 내부 컬럼명으로 변환하는 매핑 함수 추가.

2. endDate가 들어오면 dueDate로 변경, 나머지는 그대로 전달.

```js
// 매핑 함수 추가
const mapOrderByKey = (key: string) => (key === 'endDate' ? 'dueDate' : key);

const result = await taskRepository.getAllTasks({
  projectId,
  userId,
  status,
  assignee,
  keyword,
  order,
  orderBy: mapOrderByKey(orderBy) as TaskOrderBy, // 매핑 적용
  skip,
  take: limit,
});
```
- 스키마를 변경하는 방법도 있지만 매핑으로 해결해보고 싶었습니다.

```js
// status 를 isDone 으로 변환
    if ('status' in data) {
      data.isDone = data.status === 'done';
      delete data.status;
    }
```
- 같은 방법으로 하위 할일의 체크박스 기능 오류를 수정했습니다.

<br>

### 할 일 상세 페이지에서 이미지 첨부 불가

#### 구현 방식
1. 할 일 업데이트 API는 날짜·상태·담당자·태그·첨부파일까지 한 요청에서 함께 수정하도록 설계.

2. 날짜는 startYear/Month/Day, endYear/Month/Day 를 받아 startedAt, dueDate 로 변환하여 저장.

3. 첨부파일은 프론트에서 넘어온 URL 배열을 받아 DB(taskFiles)에 매핑 후 응답으로 반환.

#### 문제점
 1. 첨부파일만 업데이트하는 경우 날짜 미전달로 에러
    - 프론트는 첨부파일만 수정할 때 날짜 필드를 보내지 않음 > new Date(undefined, …)가 되어 변환 에러.
      
 3. 응답 스키마 불일치(배열 형태 차이)
    - 프론트는 attachments: string[](URL 배열)을 기대했지만,
    - 백엔드는 { id, url }[] 형태로 반환
   
#### 해결 방법
1. 날짜 옵셔널 처리 (값이 있을때만 업데이트)

```js
const startedAt =
  startYear && startMonth && startDay
    ? new Date(startYear, startMonth - 1, startDay)
    : undefined;

const dueDate =
  endYear && endMonth && endDay
    ? new Date(endYear, endMonth - 1, endDay)
    : undefined;
```

2. 응답 방식 통일

```js
return {
attachments: task.taskFiles.map((file) => file.fileUrl), // ← string[]
}
```

#### 개선점
옵셔널은 현재의 오류만 해결해줄 뿐, 좋은 대안은 아닌 것 같다는 생각이 들어 작성합니다.
1. 프론트에서 항상 날짜를 보내도록 강제하는 방법
   
2. 값이 없다면, db에서 기존 날짜값을 읽어와 유지하는 방법

<br>

### 이미지 삭제 버튼 표시 안 됨

#### 문제점
1. flex-grow: 1 만 적용된 상태에서 긴 파일명일 경우, 파일명 영역이 버튼 영역을 침범해 버튼이 사라진 것처럼 보이는 현상 발생.

#### 해결 방법

1. .fileName의 flex-grow: 1을 **flex: 1;**로 변경.

2. flex: 1;은 flex-grow: 1; flex-shrink: 1; flex-basis: 0; 세 속성을 한 번에 지정하는 축약형.

3. 이를 통해 .fileName은 공간을 균등하게 차지하면서도, 삭제 버튼이 항상 표시될 수 있도록 공간이 보장됨.

```js
.fileName {
  flex: 1; // flex-grow:1; flex-shrink:1; flex-basis:0; 
  text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
}
```

<br>

### 상태 수정 시 태그·첨부파일이 삭제되던 문제

#### 구현 방식
1. 기존 업데이트 로직에서 taskTags와 taskFiles는 항상 deleteMany → create 패턴으로 처리.

2. 요청에 태그나 첨부파일이 없으면 deleteMany로 모두 삭제된 뒤 새 값이 없으니 빈 상태로 저장됨.

3. 기본값을 []로 설정하여 attachments = [], tags = []가 요청 시 자동 들어오도록 함.

#### 문제점
1. 다른 필드 (상태, 내용) 만 수정해도 기본값 [] 때문에 기존 태그·첨부파일이 전부 삭제됨.

2. 사용자는 단순 상태 변경만 했는데, 기존 첨부파일과 태그가 전부 사라지는 손실 발생.

#### 해결 방법
1. tags나 attachments가 undefined일 때는 수정 로직을 스킵하게 변경.

2. 기본값 [] 제거 → 요청에서 값이 빠지면 undefined로 인식.

```js
// repository
taskTags: tags !== undefined
  ? {
      deleteMany: {},
      create: tags.map((tagName) => ({
        tag: {
          connectOrCreate: {
            where: { tag: tagName },
            create: { tag: tagName },
          },
        },
      })),
    }
  : undefined,

taskFiles: attachments !== undefined
  ? {
      deleteMany: {},
      create: attachments.map((file) => ({
        fileName: file.name,
        fileUrl: file.url,
      })),
    }
  : undefined,

```

<br>

### description 필드 없음

#### 문제점
1. API 명세서에는 description 필드가 없었으나, 프론트엔드 예시에서는 할일 상세 페이지에 내용을 표시하고 있었음.

#### 해결 방법

1. description 필드 추가
 
2. 각 기능 응답 객체에 description 필드를 포함시켜 프론트 표시 요구사항 충족.

<br><br>

## 협업 및 피드백

1. 개발 규칙을 팀 위키에 정리해 두어, 모든 팀원이 참고하기 편했고 규칙의 일관성을 유지할 수 있었습니다.

2. ESLint, Prettier, Husky, lint-staged 사용을 제안하고 적용했습니다.
   
   초기에는 설정이 과도하여 개발 속도에 영향을 주었으나, 일부 규칙을 완화하여 실용성을 높였습니다.

3. PR 작성 시 리뷰어를 지정하고, 리뷰를 받은 뒤 본인이 직접 머지하는 방식으로 진행했습니다.
   
   리뷰 과정에서 적극적으로 소통하며 문제점을 빠르게 보완할 수 있었습니다.

4. 테스트를 진행한 후 수정이 필요한 부분을 프로젝트 계획서 하단에 팀원들이 기록해주어서, 트러블슈팅 과정이 훨씬 수월했습니다.

<br><br>

## 코드 품질 및 최적화

#### ESLint, Prettier, Husky, lint-staged 적용

1.코드 일관성과 품질 유지를 위해 ESLint와 Prettier를 도입하여 정적 분석 및 자동 코드 포매팅을 적용하였습니다.

2. Husky와 lint-staged를 통해 커밋 전 자동으로 린트 및 포맷 검사를 수행함으로써
   불필요한 코드 스타일 차이와 기본적인 오류를 사전에 방지하였습니다.

#### Layered Architecture 적용

1. 유지보수성과 확장성을 높이기 위해 Layered Architecture를 적용하였습니다.
   
   이를 통해 API 인터페이스, 비즈니스 로직, db접근 로직을 명확히 분리하여 모듈화된 개발 환경을 구현했습니다.

#### Node/NPM 버전 통일

1. 멘토님의 조언에 따라 협업 시 버전 차이로 인한 환경 불일치와 충돌을 예방하기 위해 Node.js와 npm 버전을 통일하였습니다.

    - Node.js: 22.17.1
    - npm: 10.9.2
      
<br><br>

## 향후 개선 사항

1. Prisma 자동 생성 타입 활용

   현재는 직접 정의한 타입을 사용했으나, Prisma가 제공하는 자동 생성 타입을 활용하면 더 깔끔한 코드 작성이 가능할 것 같습니다.

2. 초기 설계 및 명세 파악 강화

   코드 작성 후 테스트·수정 과정에서 코드가 점점 복잡해졌습니다.

   프로젝트 초기에 API 명세서와 프론트엔드 예시를 충분히 분석하여 설계 단계에서 구조를 깔끔히 잡는 것이 중요하다고 느꼈습니다.

3. 클래스 기반 구조 적용

   현재는 Express 미들웨어 함수와 객체 리터럴 방식만 사용했습니다.

   향후에는 클래스 기반 서비스·컨트롤러 구조를 적용해 확장성과 보안, 재사용성을 높이고 싶습니다.

4. 중복 인증 로직의 미들웨어화

   인증 관련 검증 로직이 여러 곳에 중복되어 있습니다.

   이를 공통 미들웨어로 분리하면 코드 중복을 줄이고 가독성을 개선할 수 있겠다는 생각을 했습니다.

5. 페이지네이션 심화 학습

   페이지네이션 로직에 대한 이해가 부족하다고 느꼈습니다.

   다양한 페이지네이션 방식(오프셋·커서 기반 등)과 깔끔한 작성 방법, 성능 최적화 기법에 대해 추가 학습이 필요합니다.

6. 트랜잭션 관리 적용

   다음 파트에선 데이터 무결성과 일관성을 보장하기 위해, 여러 쿼리를 묶어 처리하는 트랜잭션 로직을 적용해볼 생각입니다.

   DB 연산이 동시에 이뤄지는 기능에서는 트랜잭션을 적용하면 안정성을 높일 수 있을 것 같다는 생각이 들었습니다.

<br>

## 협업 및 관리 문서

**팀 위키**: [https://github.com/nb02-codi-it-team1/Codi-It-backend/wiki](https://github.com/nb02-codi-it-team1/Codi-It-backend/wiki)

**팀 이슈**: [https://github.com/nb02-codi-it-team1/Codi-It-backend/issues](https://github.com/nb02-codi-it-team1/Codi-It-backend/issues?q=is%3Aissue%20state%3Aclosed)

**팀 PR**: [https://github.com/nb02-codi-it-team1/Codi-It-backend/pull-request](https://github.com/nb02-codi-it-team1/Codi-It-backend/pulls?q=is%3Apr+is%3Aclosed)

