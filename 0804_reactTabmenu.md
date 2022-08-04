# 2022.08.04 TIL

## reactTabMenu

```jsx

import React, {useState, useEffect} from 'react'

function TabMenu () {
	const [activeIndex, setActiveIndex] = useState(0);
	const components = [<Contents1 />, <Contents2 />]
	return(
		<div>
			<ul>
				<li onClick={()=>{setActiveIndex(0)}}>button1</li>
				<li onClick={()=>{setActiveIndex(1)}}>button2</li>
			</ul>
			{components[activeIndex]}
			//components배열의 [activeIndex]번째 컴포넌트 보여주기
		</div>
	)
}
```
