parcel.js 이용
vanilla js 와는 달리 샛팅 필요

# 1.Typescript 환경설정

- index.html
- app.js (.js -< .ts>)

2. tsconfig.json (설정파일) 만들기

- ts는 `트랜스파일러`여서 브라우저에서 실행시키기 위해서는 js로 변환을 해야함
- 이 js에서 ts로 변환을 하는 과정에서 여러가지 옵션을 줄 수 있다.
  [참고](https://typescript-kr.github.io/pages/tsconfig.json.html)

- 기존 js : 느슨한 type체계
- `소프트 랜딩` 전략 : 많은 옵션을 줄 수 있는 기능(중요!) -> 바꿀게 100개면 차차 바꾸어 갈 수 있도록 선택권을 줌 -> 완전한 ts

3. 실행하기

- parcel index.html -> node_modules, dist 등의 폴더 등이 생성 (ts로 앱을만드는데 필요한 필요한 다양한 application들이 있음)
  (parcel과 달리 webpack은 설정을 하나하나 다 해야한다!)

4. Source Map
   dist 폴더 내부 : ts파일을 js로파일로 변환시킨 파일들이 있음
   ~~.js.map = Source Map(소스의 지도) 파일 : ts --- js 연결정보를 가짐.

<br>

# 2 <TS 핵심기능 : `변수`에 `타입` 작성하기>

- 원시값(primitive value) : string, number, boolean, null, undefined
- 객체타입 : 종류 엄청많음...

```js
const store = {
  currentPage: 1,
  feeds: [],
};
```

store의 타입은 객체고, 두가지 속성을 가지고 있다. currentPage라는 number, feeds라는 array속성을 가지고 있다.

## 2-1. store의 타입을 지정하는 두가지 방법 (중 타입알리아스)

1. type알리아스 [x]

```js
type Store = {
  currentPage: number,
  feeds: NewsFeed[],
};

type NewsFeed = {
  id: number,
  comments_count: number,
  url: string,
  user: string,
  time_ago: string,
  points: number,
  title: string,
  read?: boolean, // [optional 속성] 처음에 네트워크를 통해 가져왔을때는 없음!! (이후에 추가되는 속성)
};
```

2. 인터페이스

## 2-2. 타입추론

타입스크립트 컴파일러가 코드 상의 상황을 보고 인지하고, 타입을 유추하는 상황도 있다.
아래는 따로 i에 타입을 따로 지정해 주지 않아도 된다.

```js
for (let i = 0; i < store.feeds.length; i++) {
  if (store.feeds[i].id === Number(id)) {
    store.feeds[i].read = true;
    break;
  }
}
```

## 2-3. 타입가드

```js
const container: HTMLElement | null = document.getElementById('root'); // HTMLElement 또는 null을 반환한다.

container.innerHTML = template;
```

container가 null 일 수 있으므로, container.innerHTML에 대한 에러를 막기위해 방어코드를 작성해야 한다.

```js
if (container) {
  container.innerHTML = template;
} else {
  console.error('최상위 컨테이너가 없어 ui를 진행할 수 없습니다.');
}
```

위의 코드가 많이 사용되는 경우 함수로 묶을수 있다.

```js
function updateView(html) {
  if (container) {
    container.innerHTML = html;
  } else {
    console.error('최상위 컨테이너가 없어 ui를 진행할 수 없습니다.');
  }
}
```

# <3. 함수의 규격 작성하기>

## 3-1. type알리아스에서 제공하는 문법인 `intersection`

- 중복되는 요소들을 줄일 수 있다!

```ts
type News = {
  id: number;
  time_ago: number;
  title: string;
  url: string;
  user: string;
  content: string;
};

type NewsFeed = News & {
  comments_count: number;
  points: number;
  read?: boolean; // [optional 속성] 처음에 네트워크를 통해 가져왔을때는 없음!! (이후에 추가되는 속성)
};

type NewsDetail = News & {
  comments: [];
};

type NewsComments = News & {
  comments: [];
  level: number;
};
```

## 3-1. Generic

- 입력이 ABCD유형 중 입력이 A면, 출력도 A. B면, 출력도 B

```ts
function getData<AjaxResponse>(url: String): AjaxResponse {
  ajax.open('GET', url, false); //비동기로 가져오기
  ajax.send();

  return JSON.parse(ajax.response);
}

newsFeed = store.feeds = makeFeed(getData<NewsFeed[]>(NEWS_URL));

const newsContent = getData<NewsDetail>(CONTENT_URL.replace('@id', id));
```

- `AjaxResponse`라는 이름으로 Generic을 표현
- 위처럼 getData를 호출하는 곳에서 type을 정의함

## void (함수의 리턴 값이 없을 때)

```ts
function updateView(html: string): void {
  if (container) {
    container.innerHTML = html;
  } else {
    console.error('최상위 컨테이너가 없어 ui를 진행할 수 없습니다.');
  }
}
```
