# 2022.08.17 TIL

## Next.js 기본 개념
**React로 만드는 서버사이드 렌더링 프레임워크**

### next.js의 장점

react 클라이언트 렌더링의 문제점을 보완할 수 있음

1. js파일을 로드하고 사용자는 웹을 보게되는데 처음 렌더링 하는 시간이 오래걸려 user가 첫 렌더링이 될 때까지 많은 시간을 대기해야했다.
    
    → 서버에서 자바스크립트를 로딩함으로 클라이언트 측에서 자바스크립트를 로딩하는 시간이 줄어들게 됨
    
2. 자바스크립트가 로드되지 않은 경우 아무런 정보를 보이지 않는다. 구글 검색엔진의 경우 자바스크립트가 로드되지 않은 페이지를 검색엔진으로 스캔하여 검색에 아무 페이지도 걸리지 않게 됨.
    
    → 검색엔진이 자바스크립트를 읽는것이 아닌 서버측에서 자바스크립트, html, css를 만들어 컨텐츠를 직접 업로드 함으로 검색엔진에 게시글이 걸림, meta태그를 자유롭게 추가함으로 seo를 용이하게 할 수 있다.
    

## next.js가 제공하는 주요 기능

### hot reloading

개발 중 저장되는 코드는 자동으로 새로고침 된다.

### automatic routing

pages폴더에 있는 파일은 해당 파일 이름으로 라우팅된다.

(’pages/page1.tsx’ → ‘[localhost:3000/page1](http://localhost:3000/page1)’)

### single file components

style.jsx를 사용함으로 컴포넌트 내부에 해당 컴포넌트만 스코프를 가지는 css를 만들 수 있다.

<style jsx global> 을 사용하면 글로벌로 스타일 정의가 가능하다.

```jsx
function Heading(props) {
  const variable = "red";
  return (
    <div className="title">
      <h1>{props.heading}</h1>
      <style jsx>
        {`
          h1 {
            color: ${variable};
          }
        `}
      </style>
    </div>
  );
}

export default function Home() {
  return (
    <div>
      // red
      <Heading heading="heading" />
      // block
      <h1>ttt</h1>
    </div>
  );
}
```

### 글로벌 스타일 정의가 가능하다.

_app.tsx에만 정의가 가능하다.

```jsx
Global CSS cannot be imported from files other than your Custom <App>. Please move all global CSS imports to pages/_app.tsx. Or convert the import to Component-Level CSS (CSS Modules).
```

```jsx
// _app.tsx
import "./globals.css";

function MyApp({component, pageProps}){
	return <Component ponent {...pageProps}> /
}

export default MyApp;
```

### server landing

서버렌더링을 한다. 클라이언트 렌더링과 다르게 서버렌더링을 한 페이지의 페이지 소스보기를 클릭하면 내부에 소스가 있다.

### code splitting

dynamic import를 이용하면 손쉽게 코드 스플리팅이 가능하다.
코드 스플리팅 - 내가 원하는 페이지에서 원하는 자바스크립트와 라이브러리를 렌더링하는것이다. 모든 번들(chunk.js)이 하나로 묶이지 않고 각각 나뉘어 좀 더 효율적으로 자바스크립트 로딩 시간을 개선할 수 있다.

### typescript

타입스크립트 활용을 위해 웹팩을 만지거나 바벨을 만질 필요가 없다. 타입스크립트를 설치하고 (yarn add typescript @types/node @types/react) 명령어 yarn run dev → tsconfig,  next-end.d.ts가 생성되어 타입스크립트로 코딩이 가능해짐
