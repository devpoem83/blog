---
title: "NodeJs 기반의 서버 구축"
date: 2020-07-23 15:44:00 -0400
categories: devpome83 update.
---

## Node 프로젝트 구성시 고려사항

### 목적

-   마이크로 서비스를 위한 웹 아키텍쳐 필요.

### 처리속도

-   효율적인 서버 리소스

### 아키텍쳐 고려사항

1.  gzip 압축 사용

    -   Gzip 압축을 사용하면 응답 본문의 크기를 크게 줄일 수 있으며, 따라서 웹 앱의 속도를 높일 수 있다.

2.  동기식 함수 미사용

    -   많은 트래픽이 발생하는 웹사이트에서 이러한 동기식 호출들이 합산되면 앱의 성능이 낮아짐.

            	--trace-sync-io 명령행 플래그를 사용하면 애플리케이션이 동기식 API를 사용할 때마다 경고 및 스택 추적이 출력됨.

3.  미들웨어를 사용하여 정적 파일 제공

    -   res.sendFile()을 이용해 정적 파일을 제공할 수 있지만
        이 함수는 모든 파일 요청에 대해 파일 시스템을 읽어야 하며, 따라서 상당한 대기 시간이 발생하고 앱의 전체적인 성능에 영향을 미침.
        res.sendFile()은 효율성을 훨씬 더 높일 수 있는 sendfile 시스템 호출을 이용해 구현되지 않았음.
        => Express 앱을 위한 파일 제공에 최적화된 serve-static 미들웨어 사용.

4.  정확한 로깅 - 앱 활동을 로깅하는 경우(예: 트래픽 또는 API 호출을 추적)에는 console.log()를 사용하는 대신
    Winston 또는 Bunyan과 같은 로깅 라이브러리를 사용

5.  올바른 예외 처리 - try-catch는 동기식 코드에 대해서만 작동함.
    Node 플랫폼은 기본적으로 비동기식이므로 try-catch를 통해 많은 예외를 처리할 수는 없음.
    => then()을 사용하는 비동기식 코드 블록 내에서는 프라미스를 통해 모든 예외(명시적 예외 및 암시적 예외 모두)를 처리할 수 있음.

## 프로젝트 구성

| 구분        |     OS      |         Language | Package Manager | Framework       | Logger               | Static Resource | SQL Mapper    | DBMS           | Nodemon |
| ----------- | :---------: | ---------------: | --------------- | --------------- | -------------------- | --------------- | ------------- | -------------- | ------- |
| 개발환경    |   Windows   |   NodeJs v8.11.1 | NPM V5.6.0      | Express v4.13.1 | Morgan v.1.9.0       | Serve-static    | Sqlmap .0.1.7 | SQL Server2012 |
| Compression | 메모리 캐싱 | Service 환경설정 | 보안설정        | Utility         | Favicon              | 빌드            | 배포          | 가상화         |
| 사용        |             |    dotenv v5.0.1 | helmet v3.12.1  | lodash v4.17.10 | Serve-favicon v2.5.0 |

http://dog-paw.tistory.com/entry/MEAN-%EC%8A%A4%ED%83%9D-Express-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%83%9D%EC%84%B1-%EC%8B%A4%ED%96%89-nodemon?category=181797
