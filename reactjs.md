#### List
```javascript
import React from 'react'

function NumberList(props){
    const numbers = props.numbers;
    const ListItems = numbers.map((number,index)=>
        //不建议用索引来用作key值，这样会导致性能变差，还可能引起组件状态的问题
        <li key={index}>
            {number}
        </li>
    )
    return(
        <ul>
            {
                numbers.map((number,index)=>
                    <li key={index}>
                        {number}
                    </li>
                )
            }
        </ul>
    )
}

export default NumberList;

```
