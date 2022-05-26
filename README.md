# 🐯 Vue.js 공부

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
### 같은 컴포넌트 레벨 간 통신 방법
event를 이용해 상위 컴포넌트로 event 처리 후 props로 원하는 하위 컴포넌트로 전달
