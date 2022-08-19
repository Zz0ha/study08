# 2022.08.19 TIL

## next.js

## components

### _document.tsx

meta태그를 정의하거나, 전체 페이지에 관련하는 컴포넌트

```jsx
// pages/_document.tsx
import Document, { Html, Head, Main, NextScript } from "next/document";
export default class CustomDocument extends Document {
  render() {
    return (
      <Html>
        <Head>
          // 모든페이지에 아래 메타테크가 head에 들어감 // 루트파일이기에 가능한
          적은 코드만 넣어야함 전역 파일을 엉망으로 만들면 안된다 // 웹 타이틀,
          ga 같은것 넣음
          <meta property="custom" content="123123" />
        </Head>
        <body>
          <Main />
        </body>
        <NextScript />
      </Html>
    );
  }
}
```

- console은 서버에서만 보이고 클라이언트에서는 안보인다.
- render요소는 반영하지만 페이지 구성 요소만 반영되고 js는 반영하지 않기 때문에console은 보이지 않는다. componentDidMount같은 훅도 실행되지 않는다. static한 상황에만 사용함

### _app.tsx

```jsx
function MyApp({ Component, pageProps }){
	return <Component {...pageProps} />
}
export default MyApp;
```

- _app.tsx에서 렌더링 하는 값은 모든 페이지에 영향을 준다.
- 최초로 실행되는 것은 _app.tsx
- _app.tsx은 클라이언트에서 띄우길 바라는 전체 컴포넌트 → 공통 레이아웃임으로 최초 실행되며 내부에 컴포넌트들을 실행함
- 내부에 컴포넌트가 있다면 전부 실행하고 html의 body로 구성
- Component, pageProps를 받는다.
여기서 props로 받은 Component는 요청한 페이지이다. GET / 요청을 보냈다면, Component에는 /pages/index.js파일이 props로 내려오게된다.
- pageProps는 페이지 getInitialProps를 통해 내려받은 props들을 말한다.
- 그 다음 _document.tsx가 실행됨
- 페이지를 업데이트 하기 전에 원하는 방식으로 페이지를 업데이트 하는 통로
- _app.tsx에서 console.log 실행 시 client, server 둘 다 콘솔에 찍힘

### import style component

```jsx
import styles from './test.module.css';

function Heading(props){
	return (
		<div className="title">
			<h1 className={styles.red}>{props.heading}</h1>
		</div>
	);
}

export default function Home(){
	return(
		<div>
			<Heading heading="heading" />
		</div>
	)
}
```

```css
/* test.module.css */

h1.red {
	color : blue;
}
```

## Sass사용

따로 config파일을 정의하지 않고 css파일을 scss로 바꾸고 yarn add sass —dev를 해주면 next에서 알아서 설정해준다

## Link사용하기

보통 페이지간 이동은 a태그를 사용하나 next에서는 사용하지 않는다.

a태그를 사용하면 처음 페이지에 진입시 번들 파일을 받고, a태그에 의해 라우팅 되면 다시 번들 파일을 받기 때문이다. 또한 redux를 사용하는 경우 store의 state값이 증발되는 현상도 일어난다.  그렇기 때문에 a태그는 전혀 다른 사이트로 페이지를 이동시켜 다시 돌아오지 않는 경우만 사용하고, 그 이외에는 모두 Link태그를 사용한다.

```jsx
import Link from 'next/link';

const Index = ()=>(
	<div>
		<Link href="/blog"><a>Blog</a></Link>

		<!-- 동적 link시 [] 사용 -->
		<Link href="/blog/[address]"><a>Blog</a></Link>
	</div>
)
```

link 컴포넌트가 뷰포트에 보였을 때 관련 페이지를 백그라운드에서 미리 가져다 놓기 때문에 사용자가 링크를 클릭했을 때 매우 빠르게 해당 페이지로 이동할 수 있게 해준다.

## 동적 url

가변적으로 변하는 url에 대해 동적 url을 지원한다. [] 문법으로 동적 페이지를 생성하는 동적 url을 만들 수 있다.

```jsx
// pages/[id].tsx

import {useRouter} from 'next/router';

export default () => {
	const router = useRouter();

	return (
		<>
			<h1>post</h1>
			<p>postid: {router.query.id}</p>
		</>
	)
}
```

### 직관적인 페이지 기반 라우팅 시스템

프로젝트의 가장 바깥 폴더인 /pages 폴더에서 컴포넌트를 export하면 폴더명이 페이지 route가 된다. ( /pages/index.s → / , /pages/store/t-shirt → /store/t-shirt)
또, /look/[id]와 같은 dynamic route도 지원한다.

### prefetching

백그라운드에서 페이지를 미리가져온다. 기본값은 true. <Link /> 뷰포트에 있는 모든 항목 (초기 또는 스크롤을 통해) 미리 로드된다. 정적 생성을 사용하는 JSON페이지는 더 빠른 페이지 전환을 위해 데이터가 포함 된 파일을 미리 로드한다.

Link컴포넌트를 렌더링 할 때 <Link prefetch href=”…”>형식으로  prefetch값을 전달해주면 데이터를 먼저 불러온 다음에 라우팅을 시작

## next/router 사용법

```jsx
import { useEffect } from 'react';
import { useRouter } from 'next-router';
import posts from '../posts.json'

export default () => {
	const router = useRouter();

	const post = posts[router.query.id as string];
	if(!post) return 
}
```

### getInitialProps

reference [https://velog.io/@cyranocoding/Next-js-구동방식-과-getInitialProps](https://velog.io/@cyranocoding/Next-js-%EA%B5%AC%EB%8F%99%EB%B0%A9%EC%8B%9D-%EA%B3%BC-getInitialProps)

클라이언트사이드렌더링에서는 react로직에 따라 componentDidMount or useEffect로 컴포넌트가 마운트 되고 나서 하는 경우가 많은데 이 과정을 서버에서 미리 처리하도록 도와주는것이 getInitialProps이다.

기획에 따라 사용법이 달라진다.

해당페이지에만 미리 데이터를 불러오는 로직을 넣거나
전체 페이지에 대해 동일한 Data Fetching을 할것인지 정한다.

```jsx
import axios from 'axios';

const Page = ({stars})=>{
	return <div>Next stars : {stars}</div>
};

Page.getInitialProps = async ctx => {
	const { data } = await axios.get('...url');

	return {stars : data}; 
}
export default Page;
```

## Pathname 파라미터

pathname파라미터는 /post/:id 에서 id부분을 칭한다. Next.js에서는 pathname파라미터를 사용하기 위해 커스텀 서버 API를 사용해야한다. 
Next.js에서 서버렌더링을 하기 때문에 구조상 /post/13 이런식으로 요청이 들어오면 pages/post/13.js파일을 렌더링 하려고 시도한다.
