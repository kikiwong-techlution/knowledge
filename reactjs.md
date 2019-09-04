### 可搜索的产品数据
```jsx
class ProductCategoryRow extends React.Component {
  render() {
    const category = this.props.category;
    return (
      <tr>
        <th colSpan="2">
          {category}
        </th>
      </tr>
    );
  }
}

class ProductRow extends React.Component {
  render() {
    const product = this.props.product;
    const name = product.stocked ?
      product.name :
      <span style={{color: 'red'}}>
        {product.name}
      </span>;

    return (
      <tr>
        <td>{name}</td>
        <td>{product.price}</td>
      </tr>
    );
  }
}

class ProductTable extends React.Component {
  render() {
    const filterText = this.props.filterText;
    const inStockOnly = this.props.inStockOnly;

    const rows = [];
    let lastCategory = null;

    this.props.products.forEach((product) => {
      if (product.name.indexOf(filterText) === -1) {
        return;
      }
      if (inStockOnly && !product.stocked) {
        return;
      }
      if (product.category !== lastCategory) {
        rows.push(
          <ProductCategoryRow
            category={product.category}
            key={product.category} />
        );
      }
      rows.push(
        <ProductRow
          product={product}
          key={product.name}
        />
      );
      lastCategory = product.category;
    });

    return (
      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Price</th>
          </tr>
        </thead>
        <tbody>{rows}</tbody>
      </table>
    );
  }
}

class SearchBar extends React.Component {
  constructor(props) {
    super(props);
    this.handleFilterTextChange = this.handleFilterTextChange.bind(this);
    this.handleInStockChange = this.handleInStockChange.bind(this);
  }
  
  handleFilterTextChange(e) {
    this.props.onFilterTextChange(e.target.value);
  }
  
  handleInStockChange(e) {
    this.props.onInStockChange(e.target.checked);
  }
  
  render() {
    return (
      <form>
        <input
          type="text"
          placeholder="Search..."
          value={this.props.filterText}
          onChange={this.handleFilterTextChange}
        />
        <p>
          <input
            type="checkbox"
            checked={this.props.inStockOnly}
            onChange={this.handleInStockChange}
          />
          {' '}
          Only show products in stock
        </p>
      </form>
    );
  }
}

class FilterableProductTable extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      filterText: '',
      inStockOnly: false
    };
    
    this.handleFilterTextChange = this.handleFilterTextChange.bind(this);
    this.handleInStockChange = this.handleInStockChange.bind(this);
  }

  handleFilterTextChange(filterText) {
    this.setState({
      filterText: filterText
    });
  }
  
  handleInStockChange(inStockOnly) {
    this.setState({
      inStockOnly: inStockOnly
    })
  }

  render() {
    return (
      <div>
        <SearchBar
          filterText={this.state.filterText}
          inStockOnly={this.state.inStockOnly}
          onFilterTextChange={this.handleFilterTextChange}
          onInStockChange={this.handleInStockChange}
        />
        <ProductTable
          products={this.props.products}
          filterText={this.state.filterText}
          inStockOnly={this.state.inStockOnly}
        />
      </div>
    );
  }
}


const PRODUCTS = [
  {category: 'Sporting Goods', price: '$49.99', stocked: true, name: 'Football'},
  {category: 'Sporting Goods', price: '$9.99', stocked: true, name: 'Baseball'},
  {category: 'Sporting Goods', price: '$29.99', stocked: false, name: 'Basketball'},
  {category: 'Electronics', price: '$99.99', stocked: true, name: 'iPod Touch'},
  {category: 'Electronics', price: '$399.99', stocked: false, name: 'iPhone 5'},
  {category: 'Electronics', price: '$199.99', stocked: true, name: 'Nexus 7'}
];

ReactDOM.render(
  <FilterableProductTable products={PRODUCTS} />,
  document.getElementById('root')
);
```
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
