# 🐯 [Vue.js](https://vuejs.org/) 공부

## Reactivity
### [Object.defineProperty](./src/playground/vue-way.html)
***
객체에 새로운 속성을 직접 정의하거나 이미 존재하는 속성을 수정한 후, 해당 객체를 반환
```javascript
Object.defineProperty(대상
객체, 객체의
속성, { // 정의할 내용

    // 속성에 접근했을 때의 동작을 정의
    get: function () {
        console.log('접근')
    },

    // 속성에 값을 할당했을 때의 동작을 정의
    set: function (newValue) {
        console.log('할당', newValue)
        render(newValue);
    }
});
```
<br>
- Reactivity 코드 라이브러리화

```javascript
var div = document.querySelector('#app');
var viewModel = {};
(function () {  // 즉시 실행 함수
    // init과 render가 app logic에 노출되지 않게 해당 코드를 또 다른 scope에 넣어줌
    // 일반적으로 오픈 소스 라이브러리들이 이런식으로 변수의 유효 범위 관리

    function init() {
        Object.defineProperty(viewModel, 'str', {
            get: function () {
                console.log('접근')
            },
            set: function (newValue) {
                console.log('할당', newValue)
                render(newValue);
            }
        });
    }

    function render(value) {
        div.innerHTML = value;
    }

    init();
})();
```

## 인스턴스 & 컴포넌트

### [인스턴스](./src/playground/instance.html)
***
```javascript
// vue 에서 제공하는 api(기능)와 속성
var vm = new Vue({  // 생성자 내부에는 객체가 들어감 (객체 표기법으로 객체가 정의되어 있음)
    el: '#app',  // body 태그에서 id가 app 인 태그를 찾아 인스턴스를 붙임. -> vue의 기능과 속성이 유효해짐
    data: {
        message: 'hi'
    }
});
```

### [컴포넌트](./src/playground/component.html)
***
- 전역 컴포넌트   
  : 인스턴스를 생성하지 않아도 기본적으로 모든 인스턴스에 등록이 되어있음. 대부분 플러그인이나 라이브러리 형태로 전역으로 사용해야 되는 컴포넌트를 사용.

```javascript
Vue.component('컴포넌트 이름', {
    컴포넌트 내용
});
```   

- 지역 컴포넌트   
  : 인스턴스마다 컴포넌트를 새로 등록해주어야 하지만 서비스 실제 구현 시 대부분 인스턴스 하나에 컴포넌트를 추가하는 형식이기 때문에 큰 불편함 X. 하단에 어떤 컴포넌트가
  등록되는지 컴포넌트 속성으로 바로 알 수 있음

```javascript
new Vue({   // 인스턴스 생성 시 root component가 됨
    el: '#app',
    components: {
        '컴포넌트 이름': 컴포넌트 내용
    }
})
```

## 컴포넌트 통신
뷰 컴포넌트는 각각 고유한 데이터 유효 범위를 갖기 때문에 데이터를 공유하기 위해서 props와 event 속성 이용  
![component-communication 2bb1d838](https://user-images.githubusercontent.com/60397314/170423015-301ed5eb-733d-45f8-98cb-5ca3aba20d59.png)

### [프롭스](./src/playground/props.html)
`<app-header v-bind:프롭스 속성 이름="상위 컴포넌트의 데이터 이름"></app-header>`  
reactivity가 props에도 반영돼 상위 컴포넌트의 데이터가 바뀌었을 때 props도 함께 변경됨

### [이벤트](./src/playground/event-emit.html)
`<app-header v-on:하위 컴포넌트에서 발생한 이벤트 이름="상위 컴포넌트의 메서드 이름"></app-header>`

### this
객체 안에서 다른 속성을 가리킬 때 **this**를 쓰면 그에 대한 **객체**를 바라봄  
아래 경우에서 this 는 obj를 가리킴
```javascript
var obj = {
  num: 10,
  getNumber: function() {
    console.log(this.num);
  }
}
```
### [같은 컴포넌트 레벨 간 통신 방법](./src/playground/component-same-level.html)
event를 이용해 상위 컴포넌트로 event 처리 후 props로 원하는 하위 컴포넌트로 전달

## 라우터
### [뷰 라우터](./src/playground/router.html)

싱글 페이지 애플리케이션을 구현하거나 페이지간의 이동 기능을 구현할 때 사용하는 뷰 공식 라이브러리

---
페이지 url이 변경됐을 때 특정 url에 따라 뿌려지는 컴포넌트를 `router-view` 태그로 정의할 수 있음  
이는 뷰 인스턴스의 라우터 인스턴스를 연결해야만 사용 가능  
path에 정의한 url로 이동했을 때 component를 router-view 태그에 표현  
```javascript
<div id="app">
  <router-view></router-view>
</div>
```

---
페이지 이동 시 사용자는 url을 다 쳐서 이동하지 않고 간단한 키보드나 마우스로 이동  
`router-link` 태그는 라우터에서 페이지 이동을 하기 위한 링크 태그. 최종적으로 앵커 태그(일반적으로 링크를 이동할 때 사용)로 변환되어 화면에 나타남
```javascript
<router-link to="이동할 URL">Login</router-link>    // <a href="/login">Login</a> 와 동일
```

---
`history`: URL의 해쉬 값(#) 제거 속성 (#/login -> /login)  
`routes`: 페이지의 라우팅(어떤 url로 이동했을 때 어떤 페이지가 뿌려지는지) 정보 배열
```javascript
routes: [
    {
        path: '/login',    // 페이지의 url
        component: LoginComponent   // 해당 url에서 표시될 컴포넌트
    }, // 첫 번째 페이지에 대한 정보
    {}  // 두 번째 페이지에 대한 정보
        // 페이지의 개수만큼 객체 필요
]
```

## [액시오스](./src/playground/axios.html)
_jsonplaceholder: 자바스크립트로 API를 요청할 때 테스트 해볼 수 있는 사이트_

뷰에서 권고하는 Promise(자바 스크립트의 비동기 처리 패턴) 기반의 HTTP 통신 라이브러리  

자바 스크립트의 비동기 처리 패턴
1. callback
2. promise
3. promise + generator
4. async & await

### axios.get() 내부 this
axios.get() 호출 전에서의 this는 기본적인 instance나 component를 바라보고 함수 내부의 this는 비동기 처리에 의해 자연스럽게 실행 context가 바뀌면서 this에 대한 내용이 바뀜
```javascript
getData: function () {
  this  // here !!
  axios.get('https://jsonplaceholder.typicode.com/users/')
  .then(function (response) {
    this.users = response.data; // here !!
  })
  .catch(function (error) {
    console.log(error);
  });
}
```

## 뷰의 템플릿 문법
뷰로 화면을 조작하는 속성과 기능

### [데이터 바인딩](./src/playground/data-binding.html)
뷰 인스턴스에서 정의한 속성들을 화면에 표시하는 방법.  
가장 기본적인 데이터 바인딩 방식 콧수염 괄호(Mustache Tag)를 이용해 뷰 인스턴스에서 정의한 데이터 및 로직들을 HTML로 변환해 화면에 나타냄
```html
<div>{{ message }}</div>
```
```javascript
new Vue({
  data: {
      message: 'Hello Vue.js'
  }
})
```

### [디렉티브](./src/playground/directive.html)
뷰로 화면의 요소를 더 쉽게 조작하기 위한 문법들을 모아 디렉티브 형태로 제공  
html 태그에서 일반적인 id 혹은 class인 표준 속성을 제외하고 `v-` 로 붙는 속성들을 모두 뷰 디렉티브라고 함  
아래와 같이 특정 속성 값에 따라 화면의 영역을 표시하거나 표시하지 않을 수 있음
```html
<div>
  Hello <span v-if="show">Vue.js</span>
</div>
```
```javascript
new Vue({
  data: {
      show: false
  }
})
```
- `v-if`와 `v-show` 차이점 
  - show는 css 스타일에서 `display: none` 상태로 육안상으로만 보이지 않게 하고 정보는 남아있음

### [이벤트 처리](./src/playground/methods.html)
`v-on: click="메서드 이름"`을 붙일 경우 버튼을 클릭했을 때 스크립트의 뷰 인스턴스에 정의한 메서드가 실행 됨  
`v-on:keyup.enter`: event modifier. 엔터가 눌렸을 때만 실행

### 고급 템플릿 기법
- [watch](./src/playground/watch.html)
  - 기본적으로 데이터를 대상으로 넣을 수 있고 데이터의 변화에 따라 특정 로직을 수행할 수 있는 뷰의 속성
- [computed](./src/playground/data-binding.html)
  - 데이터의 연산을 정의하는 영역
  - [computed 속성을 class binding이나 기본적인 binding에 엮을 수 있음](./src/playground/computed-usage.html)
  - 장점: data 속성 값이 변경되면 전체 값 다시 한번 계산, 캐싱
  - methods VS computed
    - methods는 호출할 때만 해당 로직 수행, computed는 대상 데이터 값이 변하면 자동으로 수행
- [watch vs computed](./src/playground/watch-vs-computed.html)
  - watch는 실제로 무거운 로직(ex. 매번 실행하기 부담스러움), 특히 데이터 요청에 적합
  - computed는 단순한 값에 대한 계산, 특히 단순 text 입력을 받아 validation 값을 계산할 때 많이 사용
