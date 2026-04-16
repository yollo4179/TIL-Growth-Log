# Java Script

HTML이 췝페이지의 뼈대를 잡고 CSS가 디자인을 입힌다면, JavaScript는 클릭, 타이핑, 데이터 로드 등의 동적인 상호작용과 비즈니스 로직을 수행합니다. <br>

왜 쓰는가? why?<br>

브라우저가 기본적으로 이해하고 실행할 수 있는 유일한 프로그해밍 언어입니다. 프론트엔드 개발에서는 필수입니다.<br>

<strong>강력한 생태계와 확장성:</strong> 비동기 처리 패러다임을 통해 서버와 통신하며, 최근에는 Node.js를 통해 서버사이드까지 지원합니다. 객체지향 프로그해밍과 함수형 프로그래밍 패러다임을 모두 유연하게 지원합니다.<br>



# ES6 문법

## let과 const

```javascript
```let 예약어는 한번 선언한 똑같은 변수를 재선언할 수 없다```
let a = 10;
//```let a = 2  --->x```



``` const 예약어는 한번 할당한 값을 변경할 수 없다.```
const b = 20;   
b = 30;  ```Uncaught TypeError: Assignment to constant variable.```



``` 객체 { } 또는 배열 [ ]로 선언했을 경우 객체의 속성과 배열의 요소는 변경 가능 ```


const user = {
 name: '싸피인', age: 25,
};
console.log(user.name); ``` 싸피인```
user.name = '홍길동';
console.log(user.name); ``` 홍길동```
``` 배열 ```
const num = [];
console.log(num); // []
num.push(10);
console.log(num); // [10]

```var의 유효범위 : 블록 단위로 제한, 함수 스코프(function scope)```
자바스크립트의 var는 오직 '함수(Function)'의 중괄호만 유효 범위로 인정
1. var i = 10;     for (var i = 0; ...) 에 대하여
i는 완벽히 같은 변수입니다.
var의 재선언: 다른 언어(C++, Java 등)와 달리, 자바스크립트에서 var는 같은 스코프 내에서 몇 번이든 중복 선언이 가능합니다
엔진은 두 번째 var 키워드를 무시하고 단순한 `값 할당(i = 0)으로 덮어씌워` 버립니다.


for문 쓰고나서 따로 선언 안하고 i를 선언없이 그대로 쓸 수 있어?
for문 이후의 사용: 네, 루프가 끝난 후에도 따로 선언할 필요 없이 i를 그대로 쓸 수 있습니다.
루프를 탈출할 때의 최종값(예: 5)을 그대로 가지고 살아있습니다.

호이스팅 위치: 맞습니다. for문의 중괄호 {}나 조건문의 중괄호는 var에게 아무런 의미가 없습니다.
var i의 호이스팅은 for문을 뚫고 나가, 그 for문을 감싸고 있는 가장 가까운 함수(Function)의 최상단(함수가 없다면 전역 스코프 최상단)에서 일어납니다.

```여기까지는 다른 언어와 다를것이 없다. 지역 스코프에서는 이름이 같아도 지역 내의 변수를 우선시한다. 단!, 함수 안에서만 (이름이 같아도 다른 메모리 블럭, 영역에 존재...)```
var x = 100;
var x = 150; ```이것도 문제긴 한데```
function print() {
    var x = 200;
    console.log(x); //200
}
print();
console.log(x); //150  

```이게 진짜 띠용~임```
 var i = 10;
 for (var i = 0; i < 5; i++) { ```이거 전역(위의 i)으로 취급함```
    console.log(i);
 }
 console.log('i = ' + i);   //이게 5라고?!!


const 는 우리가 아는 상수임 , 호이스팅 범위도 let과 동일, 선언 + 할당 후 `재할당 안됨` 
let 은 우리가 아는대로임 , 호이스팅이 해당 블럭 내 최상단으로 일어나긴하지만,
메모리(스코프) 규칙은 철저함!

var j = 10;
for (let j = 0; j < 5; j++) {
    // let 변수 j의 유효범위가 {}로 제한
     console.log(j);
}
console.log('j = ' + j); //10

```


# Property Shorthand

```JavaScript
const id = 'ssafy';
const name = '싸피';
const age = 3;

const user = {
  id: id,      // 왼쪽은 객체의 Key, 오른쪽은 변수명 Value
  name: name,
  age: age
};
이제 이렇게ㅐ 합니다. 
const user = {
  id,    // 자바스크립트 엔진이 알아서 id: id 로 해석합니다.
  name,  // 알아서 name: name 으로 해석
  age    // 알아서 age: age 로 해석
};
```
# Consise Method - 함수이름 +(function 생략)

```javascript
const id = 'ssafy',
      name = '싸피',
      age = 30;

const user = {
          id: id,
          name: name,
          age: age,
          info: function () {
            console.log('user info 1 : ' + this.id + ' ' + this.name + ' ' + this.age);
          },
        }
 const user = {
          id,
          name,
          age,
          info() {
            console.log('user info 2: ' + this.id + ' ' + this.name + ' ' + this.age);
          },
        };
        user.info();
```
# 분할 할당 

```javascript
 const user = {
        id: 'ssafy',
        name: '김싸피',
        age: 25,
};

// 기존 Object
let id = user.id;
let name = user.name;
let age = user.age;
console.log('기존 : ', id, name, age);


분할할당  [원소 <= 객체]
let { id, name, age } = user;
console.log('Destructuring : ', id, name, age);

let { id: ssafyid, name: ssafyname, age: ssafyage } = user;
console.log('이름을 다르게 : ', ssafyid, ssafyname, ssafyage);

일반 할당
const area = ['서울', '대전', '구미', '광주'];

const a1 = area[0];
const a2 = area[1];
const a3 = area[2];
const a4 = area[3];
console.log('기존 배열 : ', a1, a2, a3, a4);

분할할당  [원소 <= 배열]
//const [a1, a2, a3, a4] = area;
//console.log('Destructuring 배열 : ', a1, a2, a3, a4);
const [a1,  , a3, a4] = area; //특정 index 빼기
console.log('Destructuring 배열 : ', a1, a3, a4);


객체를 return 하는 함수
function getUser() {
        return {
          id: 'ssafy',
          name: '김싸피',
          age: 25,
        };
      }

기본 할당
let user = getUser();
let id = user.id;
let name = user.name;
let age = user.age;
console.log('기존 객체 : ', id, name, age);


let { id, name, age } = getUser(); [원소 <= 객]
        console.log('Destructuring 객체 : ', id, name, age);

인자 멤버 명시하기 (객체 풀어)
function showUserInfo2({ id, name, age }) {
        console.log('userInfo2 : ');
        console.log('아이디 : ', id, 
                    '이름 : ', name,
                    '나이 : ', age);
      }
showUserInfo2(user);
```

# 스프레드 신택스 (2차원 배열 x 원소만 합치기)

```javascript
 // Spread Syntax (전개 구문)
 const user1 = { id: 'ssafy1' };
 const user2 = { id: 'ssafy2' };

 const array = [user1, user2];

 // array copy 
 // 새로운 배열을 만든다. 
 const arrayCopy = [...array];

 console.log(array, arrayCopy);
 // !!!! Object or Object 배열을 출력하면 ▶로 접혀서 출력될 수 있는데, 
 // 펼칠때 reference하고 있는 값을 출력한다. 
 // ==> user.id 형태로 값을 출력하면 console에 출력됨. 

 console.log(array[0].id, arrayCopy[0].id);
 user1.id = 'ssafy5';
 console.log(array[0].id, arrayCopy[0].id);

 // add
 const arrayAdd = [...array, { id: 'ssafy3' }];
 console.log(arrayAdd);

 // object copy
 const user3 = { ...user1 };
 console.log(user3);

 // array concat
 const team1 = ['대전', '광주'];
 const team2 = ['구미', '서울'];
 const team = [...team1, ...team2];
 console.log(team);

 // obejct merge(병합)
 const u1 = { id1: 'ssafy1' };
 const u2 = { id2: 'ssafy2' };
 const u = { ...u1, ...u2 };
 console.log(u);

 // 주의점 : key가 같은 obejct를 병합 하게 되면 마지막 obj가 기존 값을 덮어쓴다.
 const user = { ...user1, ...user2 };
 console.log(user);
```

# 디폴트 파라매터(rlwhs ansqjqdlfkd Ehrkxdma )
```javascript
function print2(msg = 'inital msg') {
  console.log(msg);
}
print2('hello ssafy'); //'hello ssafy'
print2(); //'inital msg'
```



# 템플릿 스트링 

```javascript 
// template string (`, 백틱 사용.)

      // 기존
const id = 'ssafy',
      name = '싸피',
      age = 30;
console.log(name + '(' + id + ')님 \n나이는 ' + age + '입니다.');
console.log(` ${name}(${id})님 나이는 ${age}입니다. `);

```
# 람다 함수

```javascript

// arrow function (화살표 함수)
      // function(param) {code} 형태를 축약
      // (param) => {code}
      // 함수 이름이 없는 익명 함수 이므로 사용시 변수에 담아서 사용.
      // (param) => {code};
      // param => {code};
      // () => {code};
      // () => code;
      
      const func1 = function () {
        console.log('함수표현식');
      };
      func1();

      const func2 = () => {
        console.log('화살표 함수');
      };
      func2();

      const func3 = function (num) {
        console.log('함수표현식', num);
      };
      func3(3);

      const func4 = (num) => {
        console.log('adsfad');
        console.log('화살표 함수', num);
      };
      func4(4);

      const func5 = num => console.log('화살표 함수', num)
      func5(5);

      const func6 = function (num1, num2) {
        console.log('함수표현식', num1, num2);
      };
      func6(6, 7);

      const func7 = (num1, num2) => {
        console.log('화살표 함수', num1, num2);
      };
      func7(6, 7);

      // 불가능.
      // const func8 = num1, num2 => {
      //   console.log('화살표 함수', num1, num2);
      // };
      // func8(6, 7);

      const func8 = function (num) {
        return num * num;
      };
      console.log('func8', func8(10));

      const func9 = (num) => {
        return num * num;
      };
      console.log('func9', func9(10));

      //한줄은 그 줄이 바로리턴
      const func10 = (num) => num * num;
      console.log('func10', func10(10));

      // parameter와 화살표 사이에 개행이 불가능.
      // 문법적으로 틀렸음.
      // const func11 = num
      // => num * num;
      // console.log(func11(10));
```


# 배열 다루기

```javascript
 let tempArray = ['a', 'b', 'c'];
      console.log("tempArray:" + tempArray);
      
      tempArray.push('d'); // 배열의 끝에 추가
      console.log("push('d') :" + tempArray); // a, b, c, d
      
      tempArray.unshift('x');  // 배열의 앞쪽에 추가
      console.log("unshift('x') :" + tempArray); // x, a, b, c, d

      tempArray.splice(2, 0, 'y'); // index 2 위치에 요소를 삽입. index 0부터시작.
      console.log("splice(2, 0, 'y') :" + tempArray); //x, a, y, b, c, d

      // 삭제 : pop(), shift(), splice()
      let popped = tempArray.pop(); // 마지막 요소를 삭제. 삭제한 값 리턴
      console.log("pop() :" + tempArray + ", 제거된 요소 : "+ popped); // x,a,y,b,c, 제거된 요소 : d
      let shifted = tempArray.shift(); //첫번째 요소를 삭제, 삭제한 값 리턴
      console.log("shift() :" + tempArray + ", 제거된 요소 : "+ shifted); // a,y,b,c, 제거된 요소 : x
      let removed = tempArray.splice(1, 1); // index 1 부터 1개의 요소를 삭제, 삭제한 값 리턴
      console.log("splice() :" + tempArray + ", 제거된 요소 : "+ removed); // a, b, c, 제거된 요소 : y
      //delete
      let deleted = delete tempArray[1]; //삭제하고, 삭제된 자리는 빈채로 둔다. 결과 true/false리턴.
      console.log("delete() :" + tempArray + ", 결과 : "+ deleted); // a, , c, 결과 : true
      console.log(tempArray[1]);  //undefined
```
# json2obj(parse) & obj2json(stringify)  

```javascript
let obj = {
        id:101,
        name:"홍길동"
}
console.log("javascript object : ", obj);

let stringifiedObj = JSON.stringify(obj);
console.log("JSON string : ", stringifiedObj);
let jsObj = JSON.parse(stringifiedObj)
console.log("javascript object : ", jsObj);
```



















