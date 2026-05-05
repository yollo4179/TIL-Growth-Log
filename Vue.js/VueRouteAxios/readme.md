# Routing이란 무엇인가? 

1. 사용자가 요청한 URL(자원의 위치,주소)에 따라 알맞은 화면을 연결하여 보여주는 기술입니다. <br>
2. 네트워크에서 `경로`를 `선택하는 프로세스`(웹 애플리케이션에서 다른 페이지 간의 전환과 경로를 관리하는 기술)
3. 메서드를 지정할 수 있으며, URL과 매서드 방식을 지정하면 외부 서버로부터 데이터를 가져오는 것도 가능합니다. 

```html

1. vue.js로 만드는 웹 애플리케이션은 보통 **spa** 방식을 따릅니다.
spa에서는 최초 접속 시 `하나의 html 페이지만 로드`한 뒤 사용자가 메뉴를 클릭해 url이 바뀌면 
페이지 전체를 새로고침하지 않고 화면의 필요한 (컴포넌트)부분만 `부드럽게` 교체합니다.

결론: "어떤 주소로 들어왔을 때 어떤 컴포넌트를 보여줄지" 교통정리를 해주는 것이 바로 라우팅


🛠️ Vue Router의 3가지 핵심 요소

1. Routes (경로 매핑 규칙)

- "어떤 URL로 접속하면, 어떤 컴포넌트를 보여줄 것인가?"에 대한 설계도입니다.
-  예: /home 주소에는 Home.vue를 보여주고, /profile 주소에는 Profile.vue를 보여줍니다.

2. <router-link> (네비게이션)

- HTML의 <a> 태그(링크)와 역할이 같습니다 .브라우저의 `URL 주소만 살짝 바꿔주는` Vue Router 전용 태그입니다
- `페이지를 아예 새로고침하지 않습니다.`

3. <router-view> (화면 출력 영역)

- 현재 URL에 매핑된 컴포넌트가 실제로 ```렌더링되어 화면에 표시되는 빈 공간(도화지)입니다.

```
![alt text](image.png)

* 서버가 사용자가 방문한 url 경로를 기반으로 응답을 전송
* `링크를 클릭`하면 브라우저는 `서버로부터` `HTML 응답`을 `수신`하고 새 HTML로 전체 페이지를 다시 로드합니다.

![alt text](image-1.png)


* SPA에서 routing은 브라우저의 `클라이언트 측`에서 수행합니다. 
* 클라이언트 측 JavaScript가 `새 데이터`를 `동적으로 가져와` 전체 페이지를 다시 로드하지 않습니다. 
* 페이지는 1개이지만, 링크에 따라 `여러 컴포넌트`를 `렌더링`하여 마치 여러 페이지를 사용하는 것처럼 보이도록 해야합니다.


# 만약 Routing이 없다면? 

```html

- 유저가 URL을 통한 페이지의 변화를 감지할 수 없음
- 페이지가 무엇을 렌더링중인지에 대한 상태를 알 수 없음 
    * url이 1개이기 때문에 새로 고침 시 처음 페이지로 되돌아감 
    * 링크를 공유할 시 첫 페이지만 공유 가능 
- 브라우저의 뒤로 가기 기능을 사용할 수 없습니다.


```

![alt text](image-2.png)

서버 실행 후 Router로 인한 프로젝트 변화 확인<br>
Home,About 링크에 따라 변경되는 URL과 새로 렌더링되는 화면<br>
![alt text](image-3.png)


# RouterLink
페이지를 다시 로드 하지 않고 `URL을 변경`하고 `URL 생성` 및 관련 로직을 처리<br>
HTML의 a 태그를 렌더링<br>


```javascript
<!--App.vue-->
<template>
  <header>
    <nav>
      <RouterLink to="/">Home</RouterLink>
      <RouterLink to="/about">About</RouterLink>
    </nav>
  </header>
  <RouterView />
</template>

```
<hr>

## `RouterView`는 `URL에 해당하는 컴포넌트를 표시`합니다. 어디에나 배치하여 레이아웃에 맞출 수 있습니다.(url에 해당하는 컴포넌트를 갈아끼웁니다.)  -->


## router/index.js
 `라우팅에 관련된 정보` 및 `설정`이 작성 되는 곳, router에 `URL과 컴포넌트를 매핑`

 ![alt text](image-4.png)

<hr>

# views

`RouterView 위치`에 렌더링 할 `컴포넌트를 배치`<br>
기존 components 폴더와 기능적으로 다른 것은 없으며 단순 분류의 의미로 구성됨<br>
❖ 일반 컴포넌트와 구분하기 위해 컴포넌트 이름을 View로 끝나도록 작성하는 것을 권장<br>
<hr>

```javascript
`components 폴더`: 버튼, 입력창, 네비게이션 바처럼 여러 화면에서 `재사용되는 작은 UI 조각들을` 모아둡니다.

`views 폴더`: 라우터 주소에 매핑되어 하나의 독립적인 전체 `화면(페이지) 역할`을 하는 `큰 단위의 컴포넌트들`을 모아둡니다.
```
![alt text](image-5.png)


<hr>

# 라우터 세팅하기


```javascript

<!-- App.vue -->

<RouterLink to="/">Home</RouterLink>
<RouterLink to="/about">About</RouterLink>

// index.js

<script setup>
const router = createRouter({
  routes: [
    {
      path: '/',
      name: 'home',
      component: HomeView
    },
    ...
  ]
})
</script>

# 라우팅 기본

1. index.js에 라우터 관련 설정 작성(주소, 이름, 컴포넌트)

2. RouterLink의 ‘to’ 속성으로 index.js에서 정의한 주소 속성 값(path)을 사용


💡 Vue 전문가의 덧붙임:

먼저 `index.js에서 설계도`를 그립니다. (path: '/'로 접속하면 HomeView 컴포넌트를 보여주자!)

그리고 App.vue 화면에서는 사용자가 클릭할 수 있는 버튼(링크)을 만듭니다. 이때 <RouterLink to="/"> 처럼 index.js에서 약속한 path 값을 그대로 적어주는 것이 핵심입니다.

```
<hr>

# NamedRoutes(라우팅 경로에 이름 지정하기)

```html 

<!--app.vue 경로 -컴포넌트(뷰) 연결- v-bind:to와 props를 이용-->

<RouterLink :to ="{name : 'home'}" >Home</RouterLink>
<RouterLink :to ="{name : 'about'}" >About</RouterLink>

<script setup>
  const router = createRouter({
    routes:[
    {
      path:'/',
      name:'home',
      component: HomeView
    },
    {
      path:'/',
      name:'about',
      component: AboutView
    },]
  })

</script>
1. 하드 코딩 url을 방지(url 오타 방지)

```
# 매갸 변수를 사용한 동적 경로 매핑(Path Variable)

주어진 퍄톤 경로를 동일한 컴포넌트에 매핑해야하는 경우 활용<br>
예를 들어 모든 사용자의 ID를 활용하여 프로필 페이지 URL을 설계한다면?<br>


user/1 , user2, user3 .... 일정한 패턴의 url 작성을 반복해여 함

1. UserView 컴포넌트 작성 

```html
<template>
  <div>
    <h1>UserView</h1>
  </div>
  </template>
```

2. UserView 컴포넌트를 라우트 드옥 ( 매개 변수는 : (콜론)으로 표기 )

```javascript
  
  import UserView form '../views/UserView.vue'

const router = createRouter({
  routes:[

    {
      path: '/user/:id',
      name :'user',
      compoennt: UserView
    }
  ]

})
```
3. 라우트의 매개변수는 컴포넌트에서 $route.params로 참조 가능 

```html

<!--App.vue-->

<script setup>
  const userId = ref(1)
</script>
<RouterLink :to ="name:'user', params:{'id':userId}}">User</RouterLink>
<RouterLink :to ="/user/${userId}">User 200</RouterLink>

<!--UserView.vue-->
<template>
  <div>
    <h1>UserView</h1>
    <h2>{{$route.params.id}}번 User페이지</h2>
  </div>
  </template>
```
4. Vue 3의 Composition API를 사용하여 현재 라우트의 파라미터(id)를 가져오는 아주 정석적인 방법 주로 사용
```javascript

import { ref, watch } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const userId = ref(route.params.id)

// id가 변경될 때마다 userId를 업데이트
watch(
  () => route.params.id,
  (newId) => {
    userId.value = newId
    // 여기서 신규 데이터를 서버에서 받아오는 로직을 넣으면 완벽합니다.
  }
)
```

1. 핵심 요약: router.push()의 동작 원리<br>
History Stack (히스토리 스택): 브라우저는 사용자가 방문한 페이지의 기록을 차곡차곡 쌓아둡니다(Stack). <br>router.push()는 이 스택의 맨 위에 새로운 URL 경로를 추가(Push)하는 방식입니다.<br>

뒤로 가기: 스택에 기록이 남아있기 때문에, 사용자가 브라우저의 '뒤로 가기' 버튼을 누르면 이전 페이지로 <br>자연스럽게 돌아갈 수 있습니다.<br>

동일한 역할: 화면에서 &lt;RouterLink to="..."&gt;를 클릭하는 것과, 함수에서 router.push(...)를 실행하는<br> 것은 내부적으로 완전히 동일한 동작을 수행합니다.<br>

2. Composition API에서의 실전 활용법<br>
이전 질문에서 다루었던 Composition API 환경에서는 useRouter를 가져와서 사용합니다. <br>(주의: 앞서 파라미터를 읽을 때 쓴 useRoute와 철자가 다릅니다. 이동할 때는 useRouter를 사용합니다.)<br>

```javascript
import { useRouter } from 'vue-router'

// 1. 라우터 인스턴스 가져오기
const router = useRouter()

// 2. 특정 이벤트(예: 버튼 클릭, 로그인 성공, API 응답 완료 등)에서 호출
const goToNextPage = () => {
  // 방법 A: 리터럴 문자열 경로 사용
  router.push('/home')

  // 방법 B: 객체 형태 사용 (경로 명시)
  router.push({ path: '/home' })

  // 방법 C: 이름을 가지는 라우트 (가장 권장되는 방식 ⭐️)
  // URL 경로가 바뀌어도 라우트 이름만 같으면 유지보수하기 매우 좋습니다.
  router.push({ name: 'user', params: { userId: '123' } })

  // 방법 D: 쿼리 파라미터와 함께 사용 (결과: /search?keyword=seoul)
  router.push({ path: '/search', query: { keyword: 'seoul' } })
}

💡 추가 팁: push vs replace
첫 번째 슬라이드 하단에 router.replace()도 언급되어 있는데요, 이 둘의 차이를 알아두시면 화면 전환을 훨씬 매끄럽게 설계하실 수 있습니다.

router.push(): 새 페이지로 이동합니다. (뒤로 가기 가능)

예: 여행지 목록 ➔ 여행지 상세 페이지

router.replace(): 현재 페이지의 URL을 새 URL로 덮어씁니다. (히스토리에 남지 않아, 뒤로 가기를 누르면 그 이전 페이지로 돌아갑니다.)

예: 로그인 페이지 ➔ (로그인 성공 후) 메인 페이지

만약 여기서 push를 쓰면, 메인 화면에서 뒤로 가기를 눌렀을 때 다시 로그인 화면이 나오는 어색한 상황이 발생할 수 있습니다. 이럴 때 replace를 사용합니다.
```

### 1. Vue Router 네비게이션 방식
| 도구 (Hook/Function) | 용도 | 사용 예시 |
| :--- | :--- | :--- | :--- |
| **useRoute()** | 현재 활성화된 라우트 정보(파라미터 등)를 가져올 때 사용 | const route = useRoute() | 
| **ref()** | 데이터를 반응형 변수로 선언하여 변화를 추적할 때 사용 | const userId = ref(route.params.id)| 




# 1. HTTP (HyperText Transfer Protocol)
HTTP는 웹상에서 데이터를 주고받기 위한 약속(프로토콜)입니다.<br>

`기본 정의:` HTML 문서와 같은 하이퍼텍스트를 교환하기 위해 만들어졌으며, 오늘날에는 이미지, 동영상, JSON 등 거의 모든 종류의 데이터를 전송하는 데 사용됩니다.<br>

`통신 구조:` 클라이언트가 서버에 요청(Request)을 보내면, 서버가 그에 대한 응답(Response)을 주는 구조로 동작합니다.<br>
`기반 기술:` 신뢰성 있는 데이터 전송을 위해 TCP/IP 위에서 동작합니다.<br>


# HTTP의 주요 특징: Stateless (무상태성)
`독립성:` 서버는 이전 요청의 상태를 기억하지 않습니다. 각 요청은 독립적으로 처리됩니다.<br>

`장점:` 서버가 클라이언트의 상태를 유지할 필요가 없어 서버 확장이 용이합니다.<br>

`보완:` 로그인 상태 유지처럼 "상태"가 필요한 경우에는 쿠키(Cookie)나 세션(Session), 혹은 토큰(JWT) 등을 별도로 사용해야 합니다.<br>



# HTTP 프로토콜 및 통신 특징

<table border="1" style="width:100%; border-collapse: collapse; text-align: left;">
  <thead style="background-color: #f2f2f2;">
    <tr>
      <th>항목</th>
      <th>주요 내용</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>정의</td>
      <td>하이퍼텍스트(HTML) 문서 교환을 위한 TCP/IP 기반 통신 규약</td>
    </tr>
    <tr>
      <td>구조</td>
      <td>클라이언트의 요청(Request)과 서버의 응답(Response)으로 구성됨</td>
    </tr>
    <tr>
      <td>Stateless</td>
      <td>각 요청은 독립적이며, 서버는 이전 요청의 상태를 저장하지 않음</td>
     </tr>
    <tr>
      <td>상태 유지/td>
      <td>데이터 유지가 필요할 경우 쿠키나 세션 등을 별도로 사용해야 함</td>
    </tr>
  </tbody>
</table>

| 항목 | 특징 및 설명 |
| :--- | :--- |
| **HTTP 정의** | 하이퍼텍스트 문서 교환을 위한 TCP/IP 기반 통신 규약 |
| **Stateless** | 서버가 클라이언트의 이전 상태를 저장하지 않는 독립적 통신 방식 |
| **Axios** | Vue에서 권고하는 Promise 기반의 HTTP 통신 라이브러리 |
| **Promise** | 비동기 로직 처리에 유용하며, 특정 처리가 끝날 때까지 기다려야 할 때 활용 |



# 2. REST API (Representational State Transfer)
REST는 HTTP의 장점을 최대한 살려 API를 설계하는 아키텍처 스타일입니다.<br>

`자원 중심 설계:` 모든 것을 '자원(Resource)'으로 간주하고, 각 자원에 고유한 URI를 부여합니다.<br>

`직관성:` URI와 HTTP Method만 보고도 "이 API가 무슨 일을 하는지" 명확하게 파악할 수 있는 것이 큰 장점입니다.<br>

`범용성:` HTTP를 따르는 모든 플랫폼(웹, 모바일 등)에서 공통적으로 사용할 수 있습니다.<br>


| 설계 규칙 | 상세 설명 | 예시 |
| :--- | :--- | :--- |
| **명사 사용 권고** | 리소스명은 동사보다 명사를 사용함 | `/users/userIdx` (O) <br> `/members/delete/userIdx` (X) |
| **계층 관계 표현** | 슬래시(`/`) 구분자는 자원 간의 계층 관계를 나타냄 | `/houses/apartments` |
| **URL Depth** | Depth가 깊어질수록 리소스의 상세 항목을 표현함 | `/animals/mammals/whales` |

### 2. REST API 설계 규칙 및 HTTP 메서드
| 메서드 (Method) | 역할 (Operation) | 상세 설명 (Description) | 요청/응답 바디 (Body) |
| :--- | :--- | :--- | :--- |
| **GET** | Read | 리소스(데이터) 조회 | 요청: None / 응답: 있음 |
| **POST** | Create | 새로운 리소스 생성 | 요청: 있음 / 응답: 있음 |
| **PUT** | Update | 기존 리소스의 전체 수정 | 요청: 있음 / 응답: None |
| **PATCH** | Update | 리소스의 일부 내용만 수정 | 요청: 있음 / 응답: 있음 |
| **DELETE** | Delete | 특정 리소스 삭제 | 요청: None / 응답: None |


# HTTP 통신 라이브러리: Axios 
Vue.js에서 서버와 데이터를 주고받을 때 가장 많이 사용되는 `Promise 기반 라이브러리`입니다.<br>

`Promise의 역할`: 자바스크립트의 단일 스레드 특성상 발생하는 `비동기 처리를 돕습니다.`<br> 서버에서 데이터를 다 `받아올 때까지 기다렸다가` `화면에 표시`하는 로직을 수행할 때 유용합니다.<br>

## Axios 설치 방식

| 설치 방식 | 실행 코드 및 태그 |
| :--- | :--- |
| **NPM (권장)** | `npm install axios` |
| **CDN** | `<script src="https://unpkg.com/axios/dist/axios.min.js"></script>` |


# Axios 주요 API 및 처리 결과<br>
Axios는 `HTTP 요청 시` `성공하면 .then()`을, `실패하면 .catch()` 로직을 `실행하는` `Promise` 기반 구조입니다.<br>

| API 유형 | 설명 | 처리 결과 |
| :--- | :--- | :--- |
| **`axios.get('URL 주소')`** | 데이터 조회 (Read) | 서버 데이터를 정상 수신하면 `then()`, 오류 시 `catch()` 실행 |
| **`axios.post('URL 주소')`** | 데이터 생성 (Create) | `then()`과 `catch()` 동작은 GET 방식과 동일함 |
| **`axios({옵션 속성})`** | 상세 설정 요청 | URL, HTTP 요청 방식, 보내는 데이터 유형 등 세부 속성을 직접 정의하여 전송 |

## 요청 방식 비교: 설정 객체 vs 축약형

`설정 객체 방식 (axios({...}))`: method, url, data, responseType 등 모든 속성을 하나의 객체 안에 정의합니다. (기본값은 GET 방식)<br>

`메서드 축약형 (axios.get(), axios.post() 등)`: 가장 많이 쓰이는 직관적인 방식으로, HTTP 메서드가 함수 이름으로 명시되어 코드가 간결해집니다.<br>

Spring REST API와의 실전 연동 (CRUD)

| 기능 | 컴포넌트 예시 | 주요 통신 로직 |
| :--- | :--- | :--- |
| **등록 (Create)** | `JoinView.vue` | `axios.post`를 사용하여 입력 폼의 데이터를 객체 형태로 묶어 전송 |
| **조회 (Read)** | `ListView.vue` | `axios.get`으로 받은 응답 데이터(`data`)를 반응형 변수(`users.value`)에 할당하여 화면에 출력 |
| **수정 (Update)** | `UpdateView.vue` | `axios.put` 호출 시 백틱(`` ` ``)을 이용해 URL 경로에 사용자 ID를 포함하고 수정된 데이터를 전송 |
| **삭제 (Delete)** | `ListView.vue` | `axios.delete`로 삭제 요청 후, 성공하면 `selectAll()` 함수를 다시 호출하여 목록을 갱신 |



```javascript

// POST 요청 예시
axios({
  method: 'post',
  url: '/user',
  data: {
    name: '홍길동',
    userid: 'ssafy'
  }
});

// GET 요청 예시 (응답 타입 지정)
axios({
  method: 'get',
  url: '/user/ssafy',
  responseType: 'json'
}).then(function (response) {
  // 성공 시 로직
});
```
# 메서드 축약형 방식 (권장)
## 코드가 훨씬 직관적이고 간결해집니다.

```javascript

// GET (조회)
axios.get('/board/10');

// POST (생성)
axios.post('/board', {
  userid: 'ssafy',
  subject: '안녕하세요'
});

// PUT (수정)
axios.put('/board/10', {
  subject: '안녕하세요',
  content: '오늘도 즐거운 하루 되세요 ^^'
});

// DELETE (삭제)
axios.delete('/board/10');
```

# Create (등록 - JoinView.vue)
```javascript

import { ref } from 'vue';
import axios from 'axios';

// 폼 입력값 반응형 변수 (가정)
const userid = ref('');
const username = ref('');
// ... 기타 변수들

function createHandler() {
  axios.post('/admin/user', {
    userid: userid.value,
    username: username.value,
    userpwd: userpwd.value,
    email: email.value,
    address: address.value
  })
  .then(() => {
    alert('등록이 완료되었습니다.');
  })
  .catch((error) => {
    console.error('등록 실패:', error);
  });
}
```
# Read (목록 조회 - ListView.vue)
```javascript

import { ref } from 'vue';
import axios from 'axios';

const users = ref([]); // 목록 데이터를 담을 변수

function selectAll() {
  axios.get('/admin/user')
  .then(({ data }) => {
    // 백엔드에서 받은 데이터를 반응형 변수에 할당
    users.value = data; 
  })
  .catch(() => {
    alert('에러가 발생했습니다.');
  });
}
```
# Update (수정 - UpdateView.vue)
```javascript

function updateHandler() {
  // 백틱(`)을 사용하여 URL에 동적으로 ID 값을 삽입
  axios.put(`/admin/user/${userid.value}`, {
    userid: userid.value,
    username: username.value,
    userpwd: userpwd.value,
    email: email.value,
    address: address.value
  })
  .then(() => {
    alert('수정이 완료되었습니다.');
  })
  .catch((error) => {
    console.error('수정 실패:', error);
  });
}
```
# Delete (삭제 - ListView.vue)
```javascript

function deleteUser(id) {
  // 삭제 전 사용자에게 확인
  let isConfirm = confirm("정말로 삭제하시겠습니까?");
  
  if(isConfirm) {
    axios.delete(`/admin/user/${id}`)
    .then(() => {
      alert('삭제 성공!');
      // 삭제 후 목록을 다시 불러와 화면 갱신
      selectAll(); 
    })
    .catch((error) => {
      console.error('삭제 실패:', error);
    });
  }
}
```