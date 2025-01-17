# 2주차: TodoList 리팩토링 + 번들링

## 📣 요구사항

- [ ] 리팩토링
  - [X] ECMAScript에 대한 조사
    - [X] ECMAScript, Javascript 용어 정리
    - [X] ES5 vs ES6 차이점 정리
  - [ ] 파일을 기능 단위로 분리해본다.
    - [X] core: 어플리케이션의 베이스 코드
    - [X] components: 컴포넌트 코드
    - [ ] utils: 유틸리티 성향의 코드
    - [ ] constants: 상수
    - [X] app.js (entry point)
  - [ ] 다음과 같은 규칙을 지켜가며 코딩한다.
    - [X] 한 메소드(함수)에 indent(tab)는 최대 2depth로 유지하기
    - [X] else 예약어(keyword)를 쓰지 않는다.
    - [ ] 상수를 적극적으로 사용한다.
    - [X] 한 줄에 점을 하나만 찍는다.
    - [X] 줄여쓰지 않는다 (축약 금지)
- [X] 번들러 조사 및 적용
  - [X] 번들러에 대해 알아보기
    - [X] javascript 번들링
    - [X] 번들링을 하는 이유, 필요한 이유
    - [X] 번들러로 할 수 있는 일들
  - [X] 번들러 종류 알아보기
    - [X] parcel
    - [X] webpack
    - [X] rollup
    - [X] vite
  - [X] 모듈 시스템에 대해 알아보기
    - [X] CommonJS
    - [X] AMD
    - [X] RequireJS
    - [X] ESM
  - [X] 브라우저 모듈에 대해 알아보기
  - [X] 번들러 적용
    - [X] 번들러 설치를 위해 nodejs + npm 설치
    - [X] Parcel, Webpack, Rollup, Vite 중 택 1
  
## ECMAScript에 대한 조사

### ECMAScript, Javascript 용어 정리

- ECMAScript는 Ecma 인터내셔널에 의해 제정된 ECMA-262 기술 규격에 의해 정의된 범용 스크립트 언어이다.
- ECMAScript는 2015년 이후 매년 새로운 버전이 배포되고 있으며, 이전 명세의 문제 해결 및 문법 추가를 목표로 하고 있다.
- 그 중 특히 2015년에 배포된 ES6에서 Promise, Class, Arrow function 등과 같은 중요한 기능들이 추가되었다.

### ES5 vs ES6 차이점 정리

#### Class 도입

- ES5에서 클래스 선언은 프로토타입을 통해서 실현되었다.
- 다음은 ES5에서의 클래스 선언 방식이다.

```js
var Person = function(name, age) {
    this.name = name;
    this.age = age;
};

Person.prototype.sayHi = function() {
    console.log("Hi " + this.name);
};

var person = new Person('Jack', 20);
person.sayHi();
```

- 위 코드는 ES6에서 도입된 클래스를 이용해 다음과 같이 표현할 수 있다.

```js
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    
    sayHi() {
      console.log("Hi " + this.name);
    }
}

var person = new Person('Jack', 20);
person.sayHi();
```

####  Arrow function 도입

- ES5에서의 함수 선언 방식은 다음과 같다.

```js
var foo = function(name) {
    console.log("Hello " + name);
}
```

- 위 코드는 ES6에서 도입된 Arrow function을 이용해 다음과 같이 표현할 수 있다.

```js
var foo = name => console.log("Hello " + name);
```

- Arrow function을 통해 함수를 간결하게 표현할 수 있다는 장점이 있다.
- 하지만 기존의 함수와 this 바인딩 방식이 다르기 때문에 이 부분에 유의하며 사용해야 한다.
- 기존 함수의 this 바인딩
  - 함수가 호출될 때 어떻게 호출되었는지에 따라 this에 바인딩할 객체가 동적으로 결정된다.
- Arrow function의 this 바인딩
  - 함수를 선언할 때 this에 바인딩할 객체가 정적으로 결정된다. 
  - Arrow function의 this는 언제나 상위 스코프의 this를 가리킨다. -> Lexical this
  - 여기서 유의할 점은 Arrow function으로 메소드를 정의할 경우, Arrow function 내부의 this는 메소드를 호출할 객체가 아닌 상위 컨텍스트 전역 객체인 window를 가리키기 때뭉네 Arrow function으로 메소드를 정의하는 것은 바람직하지 않다.

#### 템플릿 리터럴 도입

- ES5에서는 문자열 관리를 다음과 같이 하였다.

```js
var first = 'Paul';
var last = 'George';
var str = 'His name is ' + first + ' ' + last;
console.log(str);   // His name is Paul George
```

- 반면 ES6에서 템플릿 리터럴이 도입되면서 문자열을 보다 간편하게 관리할 수 있게 되었다.

```js
var first = 'Paul';
var last = 'George';
var str = `His name is ${first} ${last}`;
console.log(str);   // His name is Paul George
```

#### let, const 도입

- ES5에서는 모든 변수를 키워드 var을 통해 선언하였고, 동일한 이름의 변수를 재선언할 수 있었다.

```js
var x = 10;
console.log(x); // 10
var x = 15;
console.log(x); // 15
```

- 반면 ES6부터는 let, const가 도입되었고, 변수 재선언 및 재할당을 할 수 없게 되었다.

```js
let x = 10;
console.log(x); // 10
let x = 15;     // Identifier 'x' has already been declared

const y = 10;
y = 15;         // TypeError: Assignment to constant variable.
```

## 파일 분리 예시
- [Vanilla Javascript로 컴포넌트 만들기](https://junilhwang.github.io/TIL/Javascript/Design/Vanilla-JS-Component/)
- 사실 위의 포스트만 볼 경우 굉장히 혼란스러울 수 있다.
- 이 부분은 온라인 세션에서 설명할 예정

## 코딩 규칙 예시

참고링크
- [객체지향 생활 체조 학습하기](https://7942yongdae.tistory.com/8)
- [[Java] 객체지향 생활 체조 원칙 9가지 (from 소트웍스 앤솔러지)](https://jamie95.tistory.com/99)

### 한 메소드(함수)에 indent(tab)는 최대 2depth로 유지하기

- 한 함수에 들여쓰기가 여러 개 존재한다면, 해당 함수는 여러가지 일을 하고 있을 가능성이 높다.
- 함수가 맡은 일이 적을수록(잘게 쪼갤수록), 재사용성이 높고 디버깅도 용이하다.

```js
function 단웅이_10번씩_5줄_반복() {
  let str = "";
  const raw = 10;
  const repeat = 5;
  for (let i = 0; i < raw; i++) {
    for (let j = 0; j < repeat; j++) {
      str += "단웅이";
      str += " ";
    }
    str += "\n";
  }
  return str;
}
```

위의 코드를 다음과 같이 표현할 수 있다.
```js
function 단웅이_10번씩_5줄_반복() {
  const raw = 10;
  const repeat = 5;
  return 단웅이_줄바꿈_반복(raw, repeat);
}

function 단웅이_줄바꿈_반복(raw, repeat) {
  let str = "";
  for (let i = 0; i < raw; i++) {
    str += 단웅이_반복(repeat);
    str += "\n";
  }
  return str;
}

function 단웅이_반복(repeat) {
  let str = "";
  for (let i = 0; i < repeat; i++) {
    str += "단웅이 ";
  }
  return str;
}
```

다시 위의 코드는 `Array`와 `Map`을 이용하여 다음과 같이 표현할 수 있다.

```js
function 단웅이_10번씩_5줄_반복() {
  const raw = 10;
  const repeat = 5;
  return 단웅이_줄바꿈_반복(raw, repeat);
}

function 단웅이_줄바꿈_반복(raw, repeat) {
  return Array(repeat).fill(단웅이_반복(repeat));
}

function 단웅이_반복(repeat) {
  return Array(repeat).fill("Jamie ");
}
```

### else 예약어(keyword)를 쓰지 않는다.

- 조건문은 복제의 원인이 되기도 하며 가독성도 좋지 않다.
- 참고링크: https://woowacourse.github.io/javable/post/2020-07-29-dont-use-else/
```js
function 단웅이의_일과(hour, isStudy) {
    let status = "";
    if (hour > 4 && hour <= 12) {
        status = "취침";
    } else {
        if (isStudy) {
            status = "공부";
        } else {
            status = "여가";
        }
    }
    return status;
}
```
위의 코드는 다음과 같이 표현할 수 있다.
```js
function 단웅이의_일과(hour, isStudy) {
  if (hour > 4 && hour <= 12) {
    return "취침";
  }
  if (isStudy) {
    return "공부";
  }
  return "여가";
}
```
다시 다음과 같이 표현할 수 있다.
```js
function 단웅이의_일과(hour, isStudy) {
    if (hour > 4 && hour <= 12) {
      return "취침";
    }
    return isStudy ? "공부" : "여가";
}
```
이것도 다시 이렇게 표현할 수 있다.
```js
function 단웅이의_일과(시간, 공부중) {
    const 취침시간 = 4 < hour && hour <= 12;
    return 취침시간 ? '취침' :
           공부중   ? '공부' : '여가';
}
```

그런데 사실 마지막 케이스의 경우 호불호가 조금 있는 편이다.

### 상수를 적극적으로 사용한다.

앞선 경우를 예로 들자면 다음과 같다.

```js
const 취침 = "취침";
const 공부 = "공부";
const 여가 = "여가";
function 단웅이의_일과(hour, isStudy) {
  if (hour > 4 && hour <= 12) {
    return 취침;
  }
  return isStudy ? 공부 : 여가;
}
```

혹은 다음과 같이 표현할 수 있다.

```js
const 단웅이의_상태 = {
  "취침": 취침,
  "공부": 공부,
  "여가": 여가,
}

function 단웅이의_일과(hour, isStudy) {
  if (hour > 4 && hour <= 12) {
    return 단웅이의_상태.취침;
  }
  return isStudy ? 단웅이의_상태.공부 : 단웅이의_상태.여가;
}
```

여기서 `4`와 `12`도 상수로 만들 수 있다.


```js
const 단웅이의_상태 = {
  "취침": 취침,
  "공부": 공부,
  "여가": 여가,
}
const 취침_시간 = {
  시작: 4,
  끝: 12,
}

function 단웅이의_일과(hour, isStudy) {
  if (취침_시간.시작 < hour && hour <= 취침_시간.끝) {
    return 단웅이의_상태.취침;
  }
  return isStudy ? 단웅이의_상태.공부 : 단웅이의_상태.여가;
}
```

작성하고 보니 단웅이녀석 이상한 시간에 자고 있다.

### 한 줄에 점을 하나만 찍는다.

이 부분은 순수하게 가독성을 위함입니다.

```js
function 랜덤_숫자_100개_만들기() {
  return Array(100).fill(0).map(() => Math.random()).map(v => v * 100).map(Math.round);
}
```
위에 처럼 된 코드를 다음과 같이 표현합시다.

```js
function 랜덤_숫자_100개_만들기() {
  return Array(100).fill(0)
                   .map(() => Math.random())
                   .map(v => v * 100)
                   .map(Math.round);
}
```

혹은

```js
function 랜덤_숫자_100개_만들기() {
  return Array(100)
          .fill(0)
          .map(() => Math.random())
          .map(v => v * 100)
          .map(Math.round);
}
```


### 줄여쓰지 않는다 (축약 금지)

- 길게 설명하지 않겠다.
- 아니, 내가 설명하지 않겠다.
- [이 포스트](https://velog.io/@mowinckel/%EB%88%84%EA%B0%80-%EC%9D%B4%EB%A6%84%EC%9D%84-%ED%95%A8%EB%B6%80%EB%A1%9C-%EC%A7%93%EB%8A%94%EA%B0%80)로 대체한다. 

## 번들러 조사 및 적용

### javascript 번들링

- 번들링의 개념은 여러개의 파일 중에 종속성이 존재하는 파일을 하나의 파일로 묶어 패키징을 시키는 과정을 의미한다.

### 번들링을 하는 이유, 필요한 이유

- 번들링을 하지 않을 경우 발생할 수 있는 문제점
  - 웹 페이지에서 모듈을 사용하려면 해당 모듈과 모듈에서 사용하는 라이브러리들을 선후 관계를 따져서 순서대로 로드해야 한다. 몇 개의 모듈을 사용한다면 <script> 태그를 이용하여 간단하게 로드 할 수 있겠지만, 만약, 수십, 수백 개의 모듈을 사용해야 한다면 매우 복잡해지게 된다. 또한, 이렇게 많은 양의 파일을 한번에 로드 할 경우 네트워크의 병목현상이 발생할 수 있다.
- 번들링이 필요한 이유
  - 번들링은 위와 같은 문제점들을 해결해주는데, 번들링을 하게 되면 모듈을 번들링하는 과정에서 모듈이나 외부 라이브러리의 의존성들을 로드하기 때문에 선후 관계를 따질 필요가 없어지고, 여러 파일로 작성된 모듈들을 특정 단위로 묶어서 하나의 파일로 만들기 때문에 병목현상도 예방할 수 있게 된다.

### 번들러로 할 수 있는 일들

- 여러 파일들을 묶어 하나의 번들로 만들어주고, 이때 종속성을 알아서 파악하여 사용하지 않는 파일들은 포함하지 않는다.

## 번들러 종류 알아보기

### parcel

- parcel은 별도의 설정 없이 번들링을 제공해주는 매우 편리한 번들러이다.
- 또한 캐싱 및 병렬 처리를 통해 빠른 빌드 속도를 제공해준다.

### webpack

- webpack은 현재 가장 널리 쓰이는 번들러로, 자바스크립트뿐만 아니라 CSS, Image 파일 등 리소스의 의존성도 관리한다.
- 또한 Minify/Uglify, Banner, CSS Preprocess 작업을 자동화해 주는 Task Runner 기능을 포함하고 있다.

### rollup

- rollup은 webpack과 마찬가지로 크고 복잡한 코드의 모듈(파일)들을 라이브러리나 어플리케이션으로 작게 만들어 주는 번들러이다.
- 이러한 rollup은 ESM형태로 번들할 수 없는 webpack과 달리 ESM형태로 번들이 가능하다.
- 또한 모듈 간에 import/export 과정이 사라지기 때문에 중복되는 코드가 제거된다.

### vite

- vite는 모던 브라우저에서 지원하는 <script module>을 이용해 개발시 번들링하지 않고 필요한 모듈만을 HTTP 요청으로 불러와서 실행하게 해주고, 프로덕션 빌드시에는 rollup으로 코드를 번들링해주는 기능을 가지고 있는 떠오르는 웹 개발 도구이다.
- 매력적인 점은 아무리 프로젝트가 커져도 웹팩은 핫 리로딩도 늦고 개발 서버 구동도 늦어지는 반면 Vite는 일관적인 속도를 가진다. 그리고 개발 시 실제 수정이 일어난 곳만 재컴파일해서 보여주므로 훨씬 빠른 핫 리로딩도 제공한다.

## 모듈 시스템에 대해 알아보기

### CommonJS

- CommonJS는 동기 방식으로 require 함수로 의존성 모듈을 가져오고, module.exports 객체로 모듈의 API를 정의한다
- 다음은 CommonJS의 예제이다.

```js
var $ = require('jquery');

function foo() {};

module.exports = {
    foo: foo
};
```

- 위에서 볼 수 있듯이 문법이 직관적이다.
- CommonJS는 자바스크립트를 브라우저 이외 환경에서 사용하고자 만들어졌기 때문에 브라우저에서 바로 사용할 수 없고, 브라우저에서 CommonJS로 작성한 자바스크립트를 실행하기 위해서는 번들러로 변환과정을 거쳐야한다.
- 하지만 Node.js는 CommonJS를 사용하기 때문에 Node.js 기반의 서버나 도구를 개발할 때는 CommonJS을 바로 사용할 수 있다.

### AMD

- AMD는 비동기 방식으로 define 함수를 사용하여 모듈의 API와 의존성 관계를 정의한다.
- 다음은 AMD의 예제이다.

```js
define(['jquery'], function($) {
    function foo() {};
    
    return {
        foo: foo
    };
});
```

- AMD는 CommonJS보다는 문법이 직관적이지 않다.
- 반면 브라우저에서 바로 사용이 가능하고, 동적 로딩을 지원한다.
- AMD를 지원하는 대표적인 라이브러리로는 RequireJS가 있다.

### RequireJS

- AMD 스펙을 구현한 자바스크립트 라이브러리로, 브러우저에 최적화 되어 있지만, 다른 자바스크립트 환경(Rhino와 Node)에서도 사용할 수 있다.
- 스크립트를 모듈화하여 관리할 수 있다는 장점이 있다.

### ESM

- ESM은 ECMAScript에서 지원하는 자바스크립트 공식 모듈 시스템으로, 모듈 정의를 위해 export와 import 키워드를 사용한다.
- 모듈을 외부로 공개하기 위해서는 아래와 같이 export 키워드를 사용한다.

```js
export function sayHi() {
    console.log('Hi');
}
```

- 또한 모듈을 가져올 때는 아래와 같이 import 키워드를 쓰며, as 키워드로 별칭을 붙일 수도 있다.

```js
import { sayHi as hi } from './somewhere';

hi();
```

## 브라우저 모듈에 대해 알아보기

- 브라우저 환경에서의 type="module"
- 모듈 스크립트가 항상 지연 실행된다. -> 내부적으로 defer속성을 붙인 것처럼 실행됨.
- src 속성값이 동일한 외부 스크립트는 한 번만 실행된다.
- import 키워드를 통해 모듈을 가져올 때는 반드시 상대 혹은 절대 경로가 와야 하며, 경로가 없는 모듈은 허용되지 않는다.

## 번들러 관련 자료
- [모듈화의 역사](https://medium.com/@chullino/%EC%9B%B9%ED%8C%A9-3-4-js%EB%AA%A8%EB%93%88%ED%99%94-%EC%97%AD%EC%82%AC-%EB%8F%8C%EC%95%84%EB%B3%B4%EA%B8%B0-1-9df997f82002)
- [module](https://gitlab.com/siots-study/topics/-/wikis/module)
- [모듈 소개](https://ko.javascript.info/modules-intro)
- [babel과 webpack을 이용한 ES6 환경 구축](https://poiemaweb.com/es6-babel-webpack-1)
