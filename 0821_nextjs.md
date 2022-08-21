# 2022.08.21 TIL

## NEXT JS 외부 데이터 가져오기

Next.js 에서 이 클래스형 컴포넌트에 getInitialProps 라는 메소드를 설정하여 해결한다. 이 메소드는 서버사이드에서도 실행될 수 있고, 클라이언트 사이드에서도 실행될 수 있다. 필요에 따라 prefetch도 될 수 있다.

```jsx
import Layout from '../components/Layout';

class SSRTest extends React.Component {
	static async getInitialProps ({req}){
		return req 
			? { from: 'server' }
			: { from : 'client' }
	}
	render(){
		return(
			<Layout>
				{this.props.from}에서 실행이 되었어요.
			</Layout>
		)
	}
}
```

getInitialProps에서 실행한 메소드에서 리턴하는 값이 해당 컴포넌트의 props로 전달이 된다.

파라미터로는 서버측의 req가 들어간다. client에서의 req는 undefined가 된다.

Header.js

```jsx
import Link from 'next/link';

const linkStyle = {
    marginRight: '1rem'
}
const Header = () => {
    return (
        <div>
            <Link href="/"><a style={linkStyle}>홈</a></Link>
            <Link href="/about"><a style={linkStyle}>소개</a></Link>
            <Link href="/ssr-test"><a style={linkStyle}>SSR 테스트</a></Link>
        </div>
    );
};

export default Header;
```

→ 링크버튼을 눌러서 ssr-test 컴포넌트 이동 시 : “client”에서 실행되었어요.

→ url주소 쳐서 이동 시 : “server”에서 실행되었어요 

10명의 userName가져오기

axios 설치 후

```jsx
import Layout from '../components/Layout';
import axios from 'axios'

class SSRTest extends React.Component {
	static async getInitialProps ({req}){
		const response = await axios.get('https://jsonplaceholder.typicode.com/users');
		return {
			users: response.data
		}
	}
	
	render(){
		const {users} = this.props;
		const userList = users.map(
			user => <li key={user.id}>{user.username}</li>
		)
	}

	return (
		<Layout>
			<ul>
				{userList}
			</ul>
		</Layout>
	)
}
```
