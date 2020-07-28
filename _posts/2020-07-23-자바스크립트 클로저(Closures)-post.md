---
title: "자바스크립트 클로저(Closures)"
date: 2020-07-23 15:44:00 -0400
categories: 개발
tags: 개발 자바스크립트 클로저
---

## 클로저의 장점

- 은닉화 / 캡슐화 / 충돌 방지 / 객체지향적 프로그래밍

## 적용

### Private 함수 만들기

함수 내부에서만 사용할 수 있는 Private 멤버변수와 멤버함수 사용이 가능함.
클로저를 이용한 Private 함수만들기

```
var counter = (function() {
    var privateCounter = 0;         // private 멤버변수

    function changeBy(val) {        // private 멤버함수
        privateCounter += val;
    }

    return{
        increment:function() {      // 내부함수 1
            changeBy(1);
        },
        decrement:function() {      // 내부함수 2
            changeBy(-1);
        },
        value:function() {          // 내부함수 3
            return privateCounter;
        }
    }
})();

console.log(counter.value()); // logs 0
counter.increment();
counter.increment();
console.log(counter.value()); // logs 2
counter.decrement();
console.log(counter.value()); // logs 1
```

자바스크립트는 Private 함수를 지원하지 않지만
클로저를 이용하면 유사하게 이용할 수 있다.
