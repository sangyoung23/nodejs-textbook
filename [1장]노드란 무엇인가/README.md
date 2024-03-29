# node.js 교과서

## 1장 노드 시작하기

### 노드란 ?
Node.js는 Chrome V8 Javascript 엔진으로 빌드된 자바스크립트 런타임입니다.

### 1.1.1 서버
노드를 통해 다양한 자바스크립트 애플리케이션을 실행할 수 있지만, 노드는 서버 애플리케이션을 실행하는 데 제일 많이 사용합니다.

서버는 네트워크를 통해 클라이언트에 정보나 서비스를 제공하는 컴퓨터 또는 프로그램을 말합니다. 클라이언트란 요청을 보내는 주체로 부라우저일 수도 있고, 데스크톱 프로그램일 수도 있고, 모바일 앱일 수도 있고, 다른 서버에 요청을 보내는 서버일 수도 있습니다.

> 예를 들어 네이버 웹 사이트를 방문한다고 생각해보자. 주소창에 네이버 웹 사이트 주소를 입력(요청)하면, 브라우저는 그 주소에 해당하는 네이버의 컴퓨터 위치를 파악한다. 그리고 그 컴퓨터로부터 네이버 웹 사이트 페이지를 받아와서 요청자의 브라우저(클라이언트)에 띄운다(응답). 이런 일을 하는 컴퓨터가 서버이다.

서버라고 해서 요청에 대한 응답만 하는 것은 아니다. 다른 서버에 요청을 보낼 수도 있다.
이때는 요청을 보낸 서버가 클라이언트 역할을 한다.

### 1.1.2 자바스크립트 런타임

노드는 자바스크립트 런타임이다. 런타임은 특정 언어로 만든 프로그램들을 실행할 수 있는 환경을 뜻한다. 노드는 자바스크립트 실행기라고 봐도 무방하다.

### 1.1.3 이벤트 기반
이벤트 기반이란 이벤트가 발생할 때 미리 지정해둔 작업을 수행하는 방식을 의미한다. 이벤트로는 클릭이나 네트워크 요청 등이 있을 수 있다.

이벤트 기반 시스템에서는 특정 이벤트가 발생할 때 무엇을 할지 밀 등록해둬야 한다. 이를 이벤트 리스너에 콜랙 함수를 등록한다고 표현한다.

노드도 이벤트 기반 방식으로 동작하므로, 이벤트가 발생하면 이벤트 리스너에 등록해둔 콜백 함수를 호출한다. 발생한 이벤트가 없거나 발생했던 이벤트를 다 처리하면, 노드는 다음 이벤트가 발생할 때까지 대기한다.

이벤트 기반 모델에서는 이벤트 루프라는 개념이 등장한다. 여러 이벤트가 동시에 발생했을 때 어떤 순서로 콜백 함수를 호출할지를 이벤트 루프가 판단한다.

노드는 자바스크립트 코드의 맨 위부터 한 줄씩 실행한다. 함수 호출 부분을 발견했다면 호출한 함수를 호출 스택(call stack)에 넣는다.

### 아래 코드를 예측해보자

```javascript
function first() {
    second()
    console.log("첫 번째")
}

function second() {
    third()
    console.log("두 번째")
}

function third() {
    console.log("세 번째")
}

first()
```
first 함수가 제일 먼저 호출되고, 그 안의 second 함수가 호출된 뒤, 마지막으로 third 함수가 호출된다. 호출된 순서와는 반대로 실행이 완료된다.

자바스크립트 코드는 실행 시 기본적으로 전역 콘텍스트 안에서 돌아간다고 생각하면 좋다.

함수는 실행되는 동안 호출 스택에 머물러 있다가 실행이 완료되면 호출 스택에서 지워진다. third, second, first, anonymous(전역 콘텍스트) 순으로 지워지고, anonymous 콘텍스트까지 실행이 모두 완료되었다면 호출 스택은 비어 있게 된다.

> <strong>출력결과</strong>
>
>세 번째<br/>
>두 번째<br/>
>첫 번째

### 아래의 코드 결과를 예측 해보자

```javascript
function run() {
    console.log('3초 후 실행')
}
console.log('시작')
setTimeout(run, 3000)

console.log('끝')
```

> <strong>출력결과</strong>
>
>시작<br/>
>끝<br/>
>3초 후 실행

- 이벤트 루프: 이벤트 발생 시 호출할 콜백 함수들을 관리하고, 호출된 콜백 함수의 실행  순서를 결정하는 역할을 담당한다. 노드가 종료될 때까지 이벤트 처리를 위한 작업을 반복하므로 루프(loop)라고 부른다.

- 백그라운드: setTimeout 같은 타이머나 이벤트 리스너들이 대기하는 곳이다. 자바스크립트가 아닌 다른 언어로 작성된 프로그램이라고 봐도 무방하며, 여러 작업이 동시에 실행될 수 있다.

- 태스크 큐: 이벤트 발생 후, 백그라운드에서는 태스크 큐로 타이머나 이벤트 리스너의 콜백 함수를 보낸다. 정해진 순서대로 콜백들이 줄을 서 있으므로 콜백 큐라고도 한다. 콜백들은 보통 완료된 순서대로 줄을 서 있지만, 특정한 경우 순서가 바뀌기도 한다.

위 코드의 이벤트 루프는 먼저 전역 콘텍스트인 anonymous가 호출 스택에 들어간다. 그 뒤 setTimeout이 호출 스택에 들어간다.

호출 스택에 들어간 순서와 반대로 실행되므로, setTimeout이 먼저 실행된다. setTimeout이 실행되면 타이머와 함께 run 콜백을 백그라운드로 보내고, setTimeout은 호출 스택에서 빠진다. 그다음으로 anonymous가 호출 스택에서 빠진다. 백그라운드에서는 3초를 센 후 run 함수를 태스크 큐로 보낸다. 3초를 세었다는 것은 백그라운드에 맡겨진 작업이 완료되었다는 것으로 이해한다.

이벤트 루프는 호출 스택이 비어 있으면 태스크 큐에서 하나씩 함수를 가져와 호출 스택에 넣고 실행한다.

호출 스택으로 올려진 run은 실행되고, 실행 완료 후 호출 스택에서 제거된다. 이벤트 루프는 태스크 큐에 콜백 함수가 들어올 때까지 계속 대기한다.

> 만약 호출 스택에 함수가 너무 많이 들어 있으면 3초가 지난 후에도 run 함수가 실행되지 않을 수 있다. 이벤트 루프는 호출 스택이 비어 있을 때만 태스크 큐에 있는 run 함수를 호출 스택으로 가져오기 때문이다.

### 1.1.4 논블로킹 I/O

이벤트 루프를 잘 활용하면 오래 걸리는 작업을 효율적으로 처리할 수 있다. 작업에는 두 가지 종류가 있는데, 동시에 실행될 수 있는 작업과 동시에 실행될 수 없는 작업이다. 기본적으로 자바스크립트 코드는 동시에 실행될 수 없다. 하지만 자바스크립트상에서 돌아가는 것이 아닌 I/O 작업 같은 것은 동시에 처리될 수 있다.

I/O는 입력(Input)/출력(Output)을 의미한다. 파일 시스템 접근이나 네트워크를 통한 요청 같은 작업이 I/O의 일종이다. 이러한 작업을 할 때 노드는 논블로킹 방식으로 처리하는 방법을 제공한다. <strong>논블로킹</strong>이란 이전 작업이 완료될 때까지 대기하지 않고 다음 작업을 수행하는 것을 의미한다. 반대로 <strong>블로킹</strong>은 이전 작업이 끝나야만 다음 작업을 수행하는 것을 의미한다.

작업 순서에 따라 큰 성능 차이가 난다. 동시에 처리될 수 있는 I/O 작업이더라도 논블로킹 방식으로 코딩하지 않으면 의미가 퇴색되므로 논블로킹 방식으로 코딩하는 습관을 들여야 한다.

### 1.1.5 싱글 스레드
이벤트 기반, 논블로킹 모델과 더불어 노드를 설명할 때 자주 나오는 용어가 하나 더 있다. 바로 싱글 스레드이다. 싱글 스레드란 스레드가 하나뿐이라는 것을 의미한다. 자바스크립트 코드가 동시에 실행될 수 없는 이유이기도 하다. 스레드를 이해하기 위해서는 프로세스부터 알아야 한다. 둘의 차이를 보자

- 프로세스는 운영체제에서 할당하는 작업의 단위이다. 노드나 웹 브라우저 같은 프로그램은 개별적인 프로세스이다. 프로세스 같에는 메모리 등의 자원을 공유하지 않는다.

- 스레드는 프로세스 내에서 실행되는 흐름의 단위이다. 프로세스는 스레드를 여러 개 생성해 여러 작업을 동시에 처리할 수 있다. 스레드들은 부모 프로세스의 자원을 공유한다. 같은 주소의 메모리에 접근 가능하므로 데이터를 공휴할 수 있다.

노드를 실행하면 먼저 프로세스가 하나 생성된다. 그리고 그 프로세스에서 스레드들을 생성하는데, 이때 내부적으로 스레드를 여러 개 생성한다. 그중에서 직접 제어할 수 있는 스레드는 하나뿐이다. 그래서 흔히 노드가 싱글 스레드라고 여겨지는 것이다.

### 싱글 스레드 블로킹 방식과 논블로킹 방식

한 음식점에 점원이 한 명 있다. 손님은 여러명이다. 점원 한 명이 주문을 받아 주방에 넘기고, 주방에서 요리가 나오면 손님에게 서빙을 한다. 그 후 다음 손님의 주문을 받는다.

이런 구조라면 다음 손님은 이전 손님의 요리가 나올 때까지 아무것도 하지 못하고 기다리고 있어야 한다. 이것이 바로 싱글 스레드, 블로킹 모델이다. 매우 비효율적이다.

이번에는 점원이 한 손님의 주문을 받고, 주방에 주문 내역을 넘긴 뒤 다음 손님의 주문을 받는다. 요리가 끝나기까지 가다리는 대신, 주문이 들어왔다는 것만 주방에 계속 알려주는 것이다. 주방에서 요리가 완료되면 완료된 순서대로 손님에게 서빙한다. 요리의 특성(블로킹인지 논블로킹인지)에 따라 완료되는 순서가 다를 수 있으므로, 주문이 들어온 순서와 서빙하는 순서가 일치하지 않을 수도 있다.

이것이 싱글 스레드, 논블로킹 모델이다. 바로 노드가 채택하고 있는 방식이다. 스레드 하나로 많은 일을 처리할 수 있다.

멀티 스레드 방식에서는 손님 한 명이 올 때마다 점원도 한 명씩 붙어 주문을 받고 서빙한다. 언뜻 보면 싱글 스레드보다 좋은 방법처럼 보이지만, 장단점이 있다. 손님의 수가 늘어날수록 점원의 수도 늘어난다. 손님 수가 줄어들었을 때 일을 하지 않고 노는 점원이 있다는 것도 문제가 된다. 점원을 새로 고용하거나 기존 직원을 해고하는 데는 비용이 발생한다.

### 노드의 장단점

| 장점 | 단점 |
|---|---|
| 멀티 스레드 방식에 비해 적은 컴퓨터 자원 사용 | 기본적으로 싱글 스레드라서 CPU 코어를 하나만 사용 |
| I/O 작업이 많은 서버로 적합 | CPU 작업이 많은 서버로는 부적합 |
| 멀티 스레드 방식보다 쉬움 | 하나뿐인 스레드가 멈추지 않도록 관리 필요 |
| 웹 서버가 내장되어 있음 | 서버 규모가 커졌을 때 서버를 관리하기 어려움 |
| 자바스크립트를 사용함 | 어중간한 성능 |
| JSON 형식과 쉽게 호환됨 | |

### I/O 작업과 CPU 작업의 예시
| I/O 작업 | CPU 작업 |
| --- | --- |
| 파일 시스템 엑세스(파일 읽기, 쓰기, 수정등의 작업) | 암호화 및 해싱 |
| 네트워크 요청(HTTP 요청, 데이터베이스 쿼리 등의 네트워크 요청) | 이미지 처리(이미지 크기가 크고 복잡한 이미지 처리 알고리즘) |