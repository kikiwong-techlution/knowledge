### 状态提升
```jsx
const scaleNames = {
  c: 'Celsius',
  f: 'Fahrenheit'
};

class TemperatureInput extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = {temperature: ''};
  }

  handleChange(e) {
    //this.setState({temperature: e.target.value});
    this.props.onTemperatureChange(e.target.value);
  }

  render() {
     const temperature = this.props.temperature;
    const scale = this.props.scale;
    return (
      <fieldset>
        <legend>Enter temperature in {scaleNames[scale]}:</legend>
        <input value={temperature}
               onChange={this.handleChange} />
      </fieldset>
    );
  }
}

function toCel(fahrenheit){
  return (fahrenheit - 32) * 5 / 9;
}

function toFah(celsius){
  return (celsius * 9 / 5) +32;
}

function tryConvert(temperature,convert){
  const input = parseFloat(temperature);
  
  if(Number.isNaN(input)){
    return '';
  }
  
  const output = convert(input);
  const rounded = Math.round(output * 1000)/1000;
  return rounded.toString();
}

class Calculator extends React.Component {
  constructor(props){
    super(props)
    this.handleCelChange = this.handleCelChange.bind(this)
    this.handleFahChange = this.handleFahChange.bind(this)
    this.state = {temperature:'',scale:'c'};
  }
  
  handleCelChange(temperature){
    this.setState({scale:'c',temperature})
  }
  
  handleFahChange(temperature){
    this.setState({scale:'f',temperature})
  }
  
  render() {
    const scale = this.state.scale;
    const temperature = this.state.temperature;
    const celsius = scale === 'f' ? tryConvert(temperature,toCel) : temperature;
    const fahrenheit = scale === 'c' ? tryConvert(temperature,toFah) : temperature;
    
    return (
      <div>
        <TemperatureInput scale="c" temperature={celsius} onTemperatureChange={this.handleCelChange} />
        <TemperatureInput scale="f" temperature={fahrenheit} onTemperatureChange={this.handleFahChange} />
        <Boil cel={parseFloat(celsius)} />
      </div>
    );
  }
}

function Boil(props){
  if(props.cel >= 100){
    return <p>The water would boil.</p>
  }
  return <p>The water would no boil.</p>
}

ReactDOM.render(
  <Calculator />,
  document.getElementById('root')
);
```

### List-列表
```jsx
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
```jsx
const numbers=[1,2,3,4,5]
return (
    <div>
        <NumberList numbers={numbers}>
    </div>
    
)

```
