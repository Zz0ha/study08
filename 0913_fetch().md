# 2022.09.13 TIL

## javascript fetch()

```jsx
//POST 메서드 구현 예제
async function postDatat(url='', data={}){
	//옵션 기본값은 *로 강조
	const response = await fetch(url, {
		method: 'POST', //*GET, POST, PUT, DELETE
		headers: {
			'Content-Type': 'application/json',
		},
		body: JSON.stringify(data), //body의 데이터 유형은 반드시 헤더의 Content-Type과 일치해야함
	});
	return response.json()
}
postData('https://example.com/answer', {answer:42}).then((data)=>{
	console.log(data); //json데이터가 `data.json()`호출에 의해 파싱됨
})
```

JSON 데이터 업로드

```jsx
const data = {username: 'example'};

fetch('http://djWjrhwjWjrhurl', {
	method: 'POST',
	headers: {
		'Content-Type' : 'application/json'
	},
	body: JSON.stringify(data),
})
.then((response)=> response.json())
```

## GET호출

```jsx
fetch('url')
.then((response)=>console.log(response))
.then((data)=>console.log(data));
```

→ 이렇게 호출하면 응답(response)객체로부터 JSON포멧의 응답 전문을 자바스크립트 객체로 변환하여 얻을 수 있다.

ex ) 

```json
{
	"userID":1,
	"id" : 1,
	"title" : "어쩌고저쩌고",
	"body" : "어쩌고저쩌고"
}
```

## POST호출

```jsx
fetch("url" , {
	method: "POST",
	headers : {
		"Content-Type" : "application/json"
	},
	body: JSON.stringify({
		title : "Test",
		body: "I am testing!",
		userId : 1
	})
}).then((response)=>console.log(response))
```

→ 응답코드 201 Created 

```jsx
Response {type: "cors", url:"https://jsonplaceholder.typicode.com/posts", redirected: false, status: 201, ok:true}
```

```jsx
fetch('url', {
	mothod: "POST",
	headers: {
		"Content-Type" : "application/json"
	},
	body: JSON.stringify({
		title: "Test",
		body: "I am testing!",
		userId: 1,
	})
})
.then((response)=> response.json())
.then((data)=>console.log(data))
```

→ 응답 객체의 json() 메서드를 호출하면 응답 전문을 객체 형태로 얻을 수 있다.

```jsx
{title: "Test", body: "I am testing!", userId:1, id:101}
```

## PUT, DELETE 호출

### PUT 호출

PUT호출은 POST방식과 매우 흡사하다.

```jsx
fetch("url", {
	method: "PUT",
	headers: {
		"Content-Type" : "application/json"
	},
	body: JSON.stringify({
		title: "Test",
		body: "I am testing!",
		userId: 1,
	})
})
.then((response)=>response.json())
.then((data)=>console.log(data))
```

### DELETE

DELETE방식에서는 보낼 데이터가 없기 때문에, headers와 body옵션이 필요 없다.

```jsx
fetch("url", {
	method: "DELETE"
})
.then((response)=>response.json())
.then((data)=>console.log(data));
```

### fetch함수를 여러번 사용해야 할 때

그때그때 fetch()함수를 직접 사용하기 보다는 별도의 함수나 모듈로 빼서 사용하는것이 좋다. 프로젝트의 상황에 맞게 async/await 키워드를 이용하여 HTTP 방식 별로 비동기 함수를 작성하고 모듈화 하여 사용하는 방법이 있다.

```jsx
async function post(host, path, body, headers = {}){
	const url = `https://${host}/${path};`
	const options = {
		method: "POST",
		headers: {
			"Content-type": "application/json",
			...headers,
		},
		body: JSON.stringify(body),
	};
	const res = await fetch(url, options);
	const data = await res.json();
	if(res.ok){
		return data
	} else {
		throw Error(data)
	}
}

post("jsonplaceholde.typicode.com", "posts", {
	title: "Test",
	body: "I am testing!",
	userId: 1,
})
.then((data)=>console.log(data))
.then((error)=>console.log(error))
```
