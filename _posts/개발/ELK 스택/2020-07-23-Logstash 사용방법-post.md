---
title: "Logstash 사용방법"
date: 2020-07-23 15:44:00 -0400
categories: ELK스택
---

## 개요

-   Logstash는 입출력 도구이다.
-   다양한 종류의 로그 (System logs, webserver logs, error logs, application logs) 뿐만아니라 입력되는 모든 종류의 데이터를 가공하여 출력할 수 있다.
-   Elasticsearch와 궁합이 잘 맞아 지금은 Elasticsearch의 공식 패키지에 포함되어 있다고 한다.

## Logstash 예제

Logstash는 입출력 도구이며, input > filter > output 의 pipeline구조로 구성되어있다.

-   input : 데이터를 받아들인다
-   filter : 데이터를 가공한다
-   output : 데이터를 출력한다

이러한 input, filter, output 설정은 직접 configuration 파일을 작성하여 설정해야하는데, 문법은 아래와 같다

```
input { } // 데이터를 받아들인다
filter { } // 데이터를 가공한다
output { } // 데이터를 출력한다.
```

사용예제

```
$ cat logstash.conf
input { stdin { } }
output { stdout { codec => rubydebug { } } }
```

주목할 부분은 stdout 설정에 codec을 추가했다는 것이다. rubydebug는 출력을 보기좋은 포맷으로 바꿔 준다.<br>
이제 logstash 압축을 해제한 디렉토리의 /bin 디렉토리로 이동하여 아래와같은 명령어로 conf 파일과 함께 실행 시킨다.

```
$ ./logstash -f [configuration file path]/logstash.conf
```

실행이 되었으면 stdin 으로 'Hello world'를 입력해보자

```
$ ./logstash -f logstash.conf
Logstash startup completed

Hello world!
{
    "message" => "Hello world!"
    , "@version" => "1"
    , "@timestamp" => "2015-07-21T11:47:37.140Z"
    , "host" => "cweb02.ami"
}
```

표준 입력으로 Hello world! 라는 문자열을 입력했고, 표준 출력으로 출력이되며 rubydebug 코덱으로 인해 json 형식으로 출력된다.<br>
이때 message, @version, @timestamp 등의 필드는 Logstash에 내장 되어있는 필드라고 볼 수 있다.<br>
input, output 플러그인의 종류는 매우 많다.<br>
물론 데이터의 출력과 동시에 Elasticsearch에 색인작업을 해주는 elasticsearch output plugin도 존재한다.

아래의 문서들을 참고.
input plugins : https://www.elastic.co/guide/en/logstash/current/input-plugins.html
output plugins : https://www.elastic.co/guide/en/logstash/current/output-plugins.html
codec plugins : https://www.elastic.co/guide/en/logstash/current/codec-plugins.html

filter 설정을 적용하면, 입력으로 들어온 데이터를 가공하는 작업을 수행한다.

예를들어 json 형식으로 데이터가 입력되었다면 필드를 삭제, 추가하거나 필드의 값을 조작할 수 있다.

여기서는 json 데이터를 가공해주는 json filter를 적용해보자.

아래와 같이 conf 파일을 수정한다

```
input {
    stdin { }
}
filter {
    json {
        source => "message"
        add_field => {
            "new_field1" => "New field value!"
        }
        add_field => {
            "new_field2" => "My name is %{name}"
        }
        remove_field => [ "useless_field" ] } }
output {
    stdout {
        codec => rubydebug { }
    }
}
```

filter로서 'json' filter를 추가했다.

source 설정은 가공할 데이터가 들어있는 field를 가리킨다.

add_field는 말그대로 field를 추가하는 것으로서, static한 고정값을 가지고있는 field를 추가할 수도있고, 기존 source의 데이터의 필드값을 '%{field이름}' 을 사용해 접근할 수 있다.

이제 이러한 설정을 저장하고, 실행한 뒤 표준 입력으로 json 포맷을 던져보자.

```
$ ./logstash -f logstash.simple.conf

Logstash startup completed

{ "name" : "timmy", "address" : "boston", "useless_field" : "garbage value" }

{ "message" => "{ \"name\" : \"timmy\", \"address\" : \"boston\", \"useless_field\" : \"garbage value\" }", "@version" => "1", "@timestamp" => "2015-07-21T12:03:38.577Z", "host" => "cweb02.ami", "name" => "timmy", "address" => "boston", "new_field1" => "New field value!", "new_field2" => "My name is timmy" }
```

{ "name" : "timmy", "address" : "boston", "useless_field" : "garbage value" } 와 같은 json 데이터를 표준입력을 통해 입력했다.

name필드값으로 'timmy', address필드값으로 'boston'을 입력하였고, 필드 삭제 예제를 위해 'useless_field'도 추가했다.

결과는? 입력된 값 바로아래에서 볼 수 있다.

일단은 위에서 설명한것처럼 message, @version, @timestamp, host 필드는 Logstash의 내장 필드이다.

그 아래로, name과 address가 정상적으로 출력되고 있고, remove_field 설정으로 인해 'useless_field'는 삭제되었다.

그리고 add_field 설정에서 정의된 new_field1, new_field2 필드들이 새로 추가 되었으며, 주목할만한건 new_field2 의 값인데, %{name} 와 같이 기존 데이터의 필드의 값을 접근하여 새로운 필드의 값으로 저장된 것을 볼 수 있다.

짐작했겠지만 다양한 종류의 데이터를 가공하기 위해 필터의 종류도 매우 다양하다. 아래를 참고 하자.

fileter plugins : https://www.elastic.co/guide/en/logstash/current/filter-plugins.html

## 지원

### 로그 및 지표(Logs and Metrics)

-   모든 유형의 로깅 데이터 처리 가능.
-   Apache 와 같은 수많은 웹 로그 및 Java 용 Log4j와 같은 애플리케이션 로그 수집.
-   Syslog, 네트워크 및 방화벽 로그 등과 같은 많은 로그 형식 캡쳐.
-   보안로그 전달을 위해서는 Filebeat 로 가능.

### 웹(Web)

-   Http 요청을 통해 이벤트로 변환.

### 데이터 저장소 및 스트림 (Data Stores and Streams)

-   JDBC 인터페이스를 통해 관계형 데이터베이스 , NoSQL 저장소의 데이터와 연동.
-   Apache Kafka, RabbitMQ 및 Amazons SQS 와 같은 메세지 큐에서 다양한 데이터 스트림을 통합.

### 센서 및 IoT (Sensors and IoT)

-   광범위한 데이터 탐색 가능.

## 실제 필요한 내용

### 여러 Elasticsearch 노드에 쓰기

```
output{elasticsearch{hosts => ["IP 주소1:port1", "IP주소2:port2", "IP주소3:port3"]}}
```

hosts 매개변수가 여러 IP 주소를 나열하면 Logstash 는 주소 목록 전체에서 요청을 로드 밸런스합니다.
