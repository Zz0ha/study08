# 2022.09.07 TIL

## forwardref 

ref전달은 컴포넌트를 통해 자식 중 하나에 ref를 자동으로 전달하는 기법이다.

react에서 특수한 목적으로 사용되기 때문에 일반적인 용도로 사용할 수 없는 prop이 있는데 대표적인 예로 루프를 돌면서 동일한 컴포넌트 여러번 랜더링할 때 사용하는 key prop이 있다. ref prop도 마찬가지로 HTML 엘리먼트 접근이라는 특수한 용도로 사용되기 때문에 일반적인 prop으로 사용할 수 없다.

HTML엘리먼트가 아닌 react컴포넌트에서 ref prop을 사용하려면 react에서 제공하는 forwardRef()라는 함수를 사용해야한다. react 컴포넌트를 forwardRef()함수로 감싸주면 해당 컴포넌트 함수는 두번째 매개 변수를 갖게 되는데, 이를 통해 외부에서 ref prop을 넘길 수 있다.

기본 예제)

```jsx
import React { forwardRef, useRef } from 'react'

const Input = forwardRef((props, ref)=>{
	return <input type="text" ref={ref} />;
})

function Field(){
	const inputRef = useRef(null);

	function handleFocus(){
		inputRef.current.focus();
	}

	return (
		<>
			<Input ref={inputRef} />
			<button onClick={handleFocus}>입력란 포커스</button>
		</>
	)
}
```

예제 - audio 엘리먼트 제어

```jsx
//Player.jsx

import React, { useRef } from 'react'
import Audio from './Audio';
import Controls from './Controls';

function Player(){
	const audioRef = useRef(null);

	return (
		<>
			<Audio ref={audioRef} />
			<Controls audio={audioRef} />
		</>
	)
}
```

- <Audio>,<Controls> 자식 컴포넌트로 이루어진 <Player>부모 컴포넌트
- useRef() 훅으로 audioRef객체를 생성한 후, <Audio>컴포넌트에는 ref prop으로, <Controls>컴포넌트에는 audio prop으로 넘겨주고있다.
- <Player>컴포넌트에서 넘기는 ref prop을 제대로 받으려면 <Audio>컴포넌트는 forwardRef()함수를 사용해야한다. 두번째 매개변수를 통해 ref 객체가 넘어오게되고, 내부에 있는 <audio>엘리먼트로 다시 넘겨 (forward)줄 수 있다. 이를 통해 부모 컴포넌트인 <Player>에서 자식 컴포넌트인 <Audio>의 내부에 있는 <audio>엘리먼트에 직접 접근할 수 있게 된다.

```jsx
//Audio.jsx

import React, { forwardRef } from 'react'
import music from './music.mp3'

function Audio(props, ref){
	return (
		<figure>
			<figcaption>Eyes on You(Sting) - Network 415:</figcaption>
			<audio src={music} ref={ref}>
				Your browser does not support the <code>audio</code> element
			</audio>
		</figure>
	)
}
export default forwardRef(Audio);
```

```jsx
//Controls.jsx

import React from 'react';

function Controls({audio}){
	const hndlePlay = () => {
		audio.current.play();
	}
	const handlePause = () => {
		audio.current.pause();
	}

	return (
		<>
			<button onClick={handlePlay}>재생</button>
			<button onClick={handlePause}>중지</button>
		</>
	)
};

export default Controls;
```

- <Controls>컴포넌트에는 ref가 아닌 audio라는 일반적인 prop으로 audioRef 객체가 넘기기 때문에 굳이 forwardRef()함수를 사용할 필요가 없다. <Controls>컴포넌트 내의 이벤트 핸들러 함수는 <Player>컴포넌트로부터 넘어온 audioRef 객체를 통해서 <audio>엘리먼트의 play()와 pause()함수를 호출할 수 있다.

**forwardRef 디버깅 팁**

forwardRef() 함수를 호출할 때 다음과 같이 익명함수를 넘기면 브라우저에서 React 개발자 도구를 사용할 때 컴포넌트의 이름이 나오지 않아서 불편하다.

```jsx
const Input = forwardRef((props, ref)=>{
	return <input type="text" ref={ref} />
})
```

React개발자 도구에서 forwardRef()함수를 사용해도 컴포넌트 이름이 나오게 하는 방법

1. forwardRef()함수에 익명함수 대신 이름이 있는 함수를 넘긴다.

```jsx
const Input = forwardRef(function Input(props, ref){
	return <input type="text" ref={ref} />
})
```

1. forwardRef()함수의 호출 결과로 기존 컴포넌트를 대체한다.

```jsx
function Input(props, ref){
	return <input type="text" ref={ref} />
}
Input = forwardRef(Input);
```

1. forwardRef()함수 호출 결과의 displayName 속성에 컴포넌트 이름을 설정한다.

```jsx
const Input = forwardRef((props, ref)=>{
	return <input type="text" ref={ref} />
})

Input.displayName="Input";
```

일반적으로 forwardRef()함수는 HTML엘리먼트 대신에 사용되는 최말단 컴포넌트 (ex. <Input>, <Button>)를 대상으로 주로 사용되며, 그보다 상위컴포넌트에서는 forwardRef()함수를 사용하는 것이 권장되지 않는다. 어떤 컴포넌트의 내부에 있는 HTML엘리먼트의 레퍼런스를 외부에 있는 다른 컴포넌트에서 접근하도록 하는 것은 컴포넌트 간의 결합도를 증가시켜 애플리케이션의 유지보수를 어렵게 만들기 때문
