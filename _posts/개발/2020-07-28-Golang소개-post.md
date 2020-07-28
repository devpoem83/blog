---
title: "Go! 있어보이지만 별거 없는 녀석"
date: 2020-07-28 11:04:00 -0400
categories: 개발
Tags: 개발 Golang Go 고랭
---

## 객체지향 언어? 함수형 언어?

- 함수형 프로그래밍 (Go)<br>
  자료처리를 수학적인 함수의 계산으로 취급하고 상태와 가변적인 데이터를 배제하는 프로그래밍으로써 실행 순서를 지정할 필요가 없는 비 절차형 언어.

- 객체지향 프로그래밍 (Java)<br>
  모든 데이터르르 개체(Object)로 취급하고 처리 요청을 받은 객체가 자기 안에 가지고 있는 기능으로 처리하는 프로그래밍 언어로써 모듈화, 재사용 등으로 확장 및 유지보수에 용이한 언어.

## 포인터? 설마 C 개발할때 나를 짜증나게 했던 그 포인터?

### 자바 개발자가 Go를 접했을때에 어려워하는 점

- 자바는 값을 전달할 때에 int, float와 같은 primitive type인 경우 Pass by Value로 전달하고 Class type 인 경우 Pass by Reference로 전달한다.
- Go 언어는 기본적으로 Pass by Value를 지원하는데 c/c++ 언어에서 사용하는 포인터 개념을 통해 Pass by Reference를 지원한다.

## 함수에서 한 개 이상 Return이 가능해?

- 자바는 1개의 리턴만을 지원합니다.

- Go는 1개 이상의 리턴을 반활할 수 있습니다.<br>
  당연히 리턴받는 변수도 동일한 수로 설정되어야 합니다.

```
func searchPost() ([]Post, int, error) {
    posts := db.Find()
    totalCount := db.Count()
    return posts, totalCount, nil
}
```

## 파일단위 처리 VS 디렉토리단위 처리

- Java는 클래스파일 단위로 import 하여 작업을 수행한다.

- Go는 디렉토리 단위로 import 하여 작업을 수행한다.

```
package ibm

import (
    "github.com/gin-gonic/gin"
)

func IbmRouter(r *gin.Engine) {
    ibm_v1 := r.Group("ibm")
    {
        ibm_v1.GET("/banners", Banners)
        ibm_v1.GET("/banners/:bnrNo/preview", Preview)
        ibm_v1.POST("/banners/:bnrNo/display", Display)
        ibm_v1.POST("/banners/:bnrNo/click", Click)
    }
}
```

## 인터셉터가 없다는데? 핸들러 그건모지?

- Java에서는 인터셉터를 통해 모든 요청(일부 제외 가능)에 대해 인증이나 로그 등 공통으로 처리해야하는 작업을 수행한다.
- Go에서도 이와 유사한 기능을 적용할 수 있다. 핸들러라고 하는 녀석으로 공통로직 또는 일부로직에 특수 작업(인증, 로그 등)을 적용할 수 있다.

```
package alarm

import (
    "github.com/eduwill/jarvis-api/app/base/handler"
    "github.com/gin-gonic/gin"
)

func AlarmRouter(r *gin.Engine) {
    alarm_v1 := r.Group("alarms")
    alarm_v1.Use(handler.LoginCheckHandler()) {
        alarm_v1.GET("/", List)
        alarm_v1.GET("/count", Count)
        alarm_v1.PUT("/:noticeIdx", Confirm)
        alarm_v1.DELETE("/:noticeIdx", Delete)
    }
}
```

## 컨트롤러 VS 라우터

- Java에서는 컨트롤러를 통해 요청Method와 URL을 통해 처리하고 ViewResolver를 통해 사용자에게 데이터를 전달하는 역할을 담당한다.

- Go에서는 컨트롤러에서 요청 Mehod와 URL에 따라 처리 함수을 연결한다. (사용자에게 데이터를 전달하는역할도 할 수 있지만 주로 연결한 함수를 통해 처리한다.)

## 변수 선언 및 할당이 이상하다

- Java의 선언 및 할당

```
String strTest; // 변수 선언
strTest = "변수입니다."; // 변수 할당
String strTest = "변수입니다."; // 변수 선언 및 할당
```

- Go에서의 자바의 선언 및 할당

```
var strTest: string // 변수 선언
strTest = "변수입니다." // 변수 할당
var strTest: string = "변수입니다." // 변수 선언 및 할당
var strTest = "나도 변수입니다." // 변수 선언 및 할당
strTest := "나도 변수입니다." // 변수 선언 및 할당
```

## 콜론과 괄호는 어디갔지?

- Java에서는 행의 마지막에 콜론을 붙히고 조건문, 반복문 등의 조건식에 괄호를 필수로 입력해야 한다.

- Go에서는 콜론과 조건식 괄호를 사용하지 않는다.

```
var strTest: string = "변수입니다."
if strTest != "" {
    strTest = "변수값 변경합니다."
}
```

## 라이브러리 의존성 관리는 몰로해?

- Java 특히 스프링에서는 gradle 이나 maven으로 라이브러리들의 의존성을 관리한다.<br>초기 Go에서는 의존성 관리할 수 있는 도구가 없었지만 go 1.11 버전부터 Go Modules가 도입되어 의존성을 쉽게 관리할 수 있다.

```
module github.com/eduwill/jarvis-api

go 1.14

require (
    github.com/bkeepers/dotenv v2.7.5+incompatible // indirect
    github.com/denisenkom/go-mssqldb v0.0.0-20190820223206-44cdfe8d8ba9
    github.com/fatih/color v1.7.0 // indirect
    github.com/foolin/gin-template v0.0.0-20190415034731-41efedfb393b
    github.com/gin-contrib/cors v1.3.0
    github.com/gin-contrib/static v0.0.0-20190511124741-c1cdf9c9ec7b
    github.com/gin-gonic/gin v1.4.0
    github.com/hokaccha/go-prettyjson v0.0.0-20190818114111-108c894c2c0e
    github.com/jmoiron/sqlx v1.2.0
    github.com/lib/pq v1.0.0
    github.com/mattn/go-colorable v0.1.2 // indirect
    github.com/patrickmn/go-cache v2.1.0+incompatible
    github.com/pbnjay/memory v0.0.0-20190104145345-974d429e7ae4
    github.com/sirupsen/logrus v1.4.2
    github.com/spf13/viper v1.4.0
    github.com/stretchr/testify v1.5.1
    github.com/t-tomalak/logrus-easy-formatter v0.0.0-20190827215021-c074f06c5816
    rsc.io/quote v1.5.2
)
```

## 실행파일이 만들어진다는데?

- Java는 빌드하면 Jar 또는 War 파일이 생성되고 컴파일된 코드를 해석하고 서비스 하기 위해서는 JVM이 필요합니다.

- Go는 빌드하면 바이너리로 되어있는 실행가능한 파일이 생성된다.<br>
  (예, 윈도우 - xxx.exe)<br>
  OS에서 바로 실행가능한 파일이 만들어지기 때문에 실행하는 서버에서 별도 프로그램을 요구하지 않는다.

## 웹서버 없이 서비스가 돼?

- 자바로 웹서비스를 하려면 WAS (Web Application Server)가 필요하다.<br>
  스프링부트로 개발시에는 내장 Tomcat을 사용할 수 있어 편리해졌지만 WAS가 필요한것은 변함없기에 서버를 구동하기 위한 리소스가 많이 필요하다.

- Go는 자체적으로 http 서비스를 지원한다.<br>
  따라서 별도의 WAS나 웹서버가 필요하지 않아 리소스가 상대적으로 절약된다.

## 빌드는 어떻게 하나요?

아주쉬워요.<br>
그냥 메인 파일이 있는곳에서 아래 명령어만 입력하면 되니까요.

```
# go build      // 빌드처리
```

## jUnit 같은 테스트 도구가 있나요?

- Go에서는 자체적으로 테스트환경을 가지고 있기에 jUnit과 같이 별도 3rd Party 도구가 필요하지 않다.

```
package main

import (
    "net/http"
    "net/http/httptest"
    "testing"
    "github.com/stretchr/testify/assert"
    "github.com/eduwill/jarvis-api/app/services"
)

func TestMain(t *testing.T) {
    router := services.DefaultRouter()
    w := httptest.NewRecorder()
    req, _ := http.NewRequest("GET", "/l4_check", nil)
    router.ServeHTTP(w, req)

    assert.Equal(t, http.StatusOK, w.Code)
    assert.Equal(t, "SUCCESS", w.Body.String())
}
```

## 코드 컨벤션은 어떻게 맞추나요?

- 걱정하지 말아요. Go와 VSCode가 알아서 맞춰줍니다
