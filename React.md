- [Элементы и компоненты](#элементы-и-компоненты)
- [Жизненный цикл компонента](#жизненный-цикл-компонента)
- [React Hooks](#react-hooks)
- [Мемоизация](#мемоизация)

## Элементы и компоненты

### Элемент
**Элемент** (Element) в React имеет такое же предназначение, как и DOM-элемент: он является строительным блоком, при помощи которого задаётся разметка страницы. Но в отличие от DOM-элемета, является простым объектом.

JSX позволяет использовать синтаксис, похожий на HTML, в JavaScript.
```jsx
const element = (<span>Notes</span>);
```
На самом же деле, это преобразуется в
```jsx
const element = React.createElement('span', null, 'Notes');
```

### Компонент
Если *элемент* является *константой* (каким-то заданным, *неизменяемым блоком*), то **компонент** (Component) является *классом*, *функцией*, которая может *возвращать элементы* в *зависимости* от своих *параметров*.

Параметрами компонента выступает объект, называющийся **Props** (properties, свойства).
```jsx
/* функциональный компонент */
const Title = props => (<span>{props.text}</span>);
```
```jsx
/* классовый компонент */
class Title extends React.Component {
  render() {
    return (
      <span>{props.text}</span>  
    );
  }
}
```
JSX позволяет использовать компоненты как элементы. 
```jsx
<Title text="Notes" />
```
Такой *синтаксис эквивалентен вызову функции* `Title` или *созданию экземпляра класса* `Title` и *преобразуется* в
```jsx
React.createElement(Title, { text: 'Notes' }, null);
```

Вызов компонента как функции был запрещён в React.
```jsx
Title({ title });
```
Это ограничение можно снять, если сделать из функции фабрику. Это может понадобиться тем, кто по каким-то причинам не может использовать JSX.
```jsx
const Title = React.createFactory(({ text }) => (<span>{text}</span>));
```

### Функция React.createElement
Функция `React.createElement(element, props, children)` принимает три параметра.
* `element` — тип элемента строкой или компонент.
* `props` — аттрибуты элемента или Props компонента.
* `children` — список дочерних элементом элемента или компонента. 


## Жизненный цикл компонента
- [Инициализация]
- [Создание]
- [Обновление]
- [Уничтожение]

## React Hooks

### Мемоизация с useMemo, useCallback, memo

Компонент высшего порядка `memo` служит заменой метода `shouldComponentUpdate` и `PureComponent` для функциональных компонент.
```js
const Article = ({ title }) => (<div>{title}</div>);

const areEqual = (props, nextProps) => {
  if (props.title === nextProps.title) {
    return true; // компонент не будет перерендерен
  }
  return false; // компонент будет перерендерен
};

const React.memo(Article, areEqual);
```

### useCallback
Все действия в функциональном компоненте (не считая React Hooks) проделываются каждый раз, когда он рендерится. В том числе и создание переменных.
```js
const Form = ({ title }) => {
  /* функция onSubmit пересоздаётся в компоненте Form на каждый рендер */
  const onSubmit = () => console.log('submit', { title });
  /* console.log запускается каждый раз, когда рендерится компонента */
  console.log('rerender!');
  return (<button onClick={onSubmit}>{title}</button>);
};
```
Можно столкнуться со следующей проблемой.  
Пусть есть список из 100 элементов, состояние которых меняется по клику.
```jsx
import React, { memo, useState, useCallback } from "react";
import ReactDOM from "react-dom";

const ListItem = ({ value, index, onClick } => {
  console.log("rendered!");
  const onButtonClick = () => onClick(index, 1);
  return <button onClick={onButtonClick}>{value}</button>;
};

const List = () => {
  const [items, setItems] = useState(Array.from(Array(100).fill(0)));

  const onClick = (index, value) =>
    setItems(items => items.map((item, i) => (i === index ? value : item))),
  );

  const renderItem = (value, index) => (
    <ListItem key={index} index={index} value={value} onClick={onClick} />
  );
  return <div>{items.map(renderItem)}</div>;
};

ReactDOM.render(<List />, document.getElementById("root"));
```
При клике на любой элемент списка в консоли можно увидеть `(100) rendered!`. Это означает, что клик по одному элементу заставить перерендериться все остальные. Попробуем это исправить.

Обернём компонент `ListItem` в `memo`, чтобы он перерендеривался не каждый раз при рендере родительского компонента, а только при обновлении его `props`.
```jsx
const ListItem = memo({ value, index, onClick }) => {
  console.log("rendered!");
  const onButtonClick = () => onClick(index, 1);
  return <button onClick={onButtonClick}>{value}</button>;
});
```
При клике на любой элемент списка всё ещё можно видеть `(100) rendered!`. Это связано с тем, что функция `onClick` пересоздаётся каждый раз и в `Props` каждому элементу приходит её новая версия. Пересоздания функции можно избежать, если мемоизировать функцию при помощи `useCallback`.
```jsx
const onClick = useCallback(
  (index, value) =>
    setItems(items => items.map((item, i) => (i === index ? value : item))),
  []
);
```
`useCallback(callback, [dependencies])`.

### Инициализация

При инициализации проиходит установка Props и начального (initial) State.

### Создание (Mounting)

### Обновнение (Updating)

## Согласование

React самостоятельно следит за обновлениями.

**Согласование, сверка** (Reconciliation) — *алгоритм сравнения* (diffing algorithm) *двух деревьев*, используемый в *React* для *определения различий* между ними.

*Сравнение* двух *деревьев начинается* с их *корневых элементов*  (root elements), от типа которых зависит дальнейшее поведение. 

Если *тип корневых элементов*  *различен*, *React уничтожает* (tear down) *старое дерево* и *строит новое* с нуля.

При *уничтожении* дерева *старые DOM-узлы* (DOM nodes) *удаляются*. *Экземпляры компонента* (component instances) получают `componentWillUnmount()`. При *построении нового* дерева *новые DOM-узлы* *добавляются* в *DOM*. *Экземпляры компонента* получают `componentWillMount()` и затем `componentDidMount()`. *Любое состояние*, связанное со *старым деревом*, *теряется*.

Пример: `<div>`, `<p>`, `<Title>`, `<input>`, `<Description>` — все перечисленные далее элементы имеют *различные типы* и их *замена* друг на друга приведёт к *уничтожению дерева*, то есть корневого элемента вместе с его детьми.
```jsx
<!-- /* замена <div> */ -->
<div>
  <Title>Notes</Title>
  <Description>My simple notes abount everything.</Description>
</div>
<!-- /* на <article> */ -->
<article>
  <Title>Notes</Title>
  <Description>My simple notes abount everything.</Description>
</article>
<!-- /* приведёт к уничтожению (unmount) старых <Text>, <Description>
и созданию (remount) новых /* -->
```

Если *корневые элементы* имеют *одинаковый тип*, *React сравнивает* их *атрибуты* и *обновляет* *только изменённые атрибуты* (в случае *атрибута* `style` — *только изменённые стили*).
```jsx
<!-- /* при замене */ -->
<button
  className="btn"
  style={{ border: 'none', background: 'black' }}
  onClick={onClick}
  tabIndex={1}
/>
<!-- /* на */ -->
<button
  className="button"
  style={{ border: 'none', background: 'grey' }}
  onClick={onClick}
  tabIndex={1}
/>
<!-- /* React обновит только className и background в style  */ -->
```

*После обработки корневого элемента* *React* *рекурсивно проходится* по *дочерним элементам*, являющимися *корневыми* в *поддеревьях*.

Если *корневые элементы* являются *компонентами одного типа*, *React* *оставляет* тот же *экземпляр* компонента (его *состояние не теряется* между двумя `render()`), *обновляет Props экземпляра* и вызывает в нём `componentWillReceiveProps()` и `componentWillUpdate()`. Далее *вызывается* `render()` и *начинается рекурсивный алгоритм сравнения результатов предыдущего и нового* `render()`.
```jsx
<!-- /* при замене */ -->
<Article>Text</Article>
<!-- /* на */ -->
<Article>Notes</Article>
<!-- /* тип компонента остался без изменений: изменился только props.children;
значит экземпляр остаётся прежним и обновляются его Props */ -->
```
### Ключи

При *рекурсивном обходе дочерних элементов React* проходит по *двум спискам потомков* (старое и новое дерево) корневого элемента *одновременно* и *создаёт мутацию*, если находит *отличие*.

Таким образом, если удалить первый элемент списка, то порядок сместится и все элементы будут считаться различными.

Пусть есть список, отображающий элементы массива `items`, состоящего чисел от 0 до 99.
```jsx
const items = Array.from(Array(100).keys()); // [0, 1, ..., 99]

const renderItem = item => (<li>{item}</li>);
```
```jsx
<ul>{items.map(renderItem)}</ul>;
```
После удаления первого элемента (например, при помощи `items.shift()`) массива, элемент 0 в старом дереве будет сравниваться с элементом 1 в новом, 1 с 2, 2 с 3 и так далее. Все элементы окажутся различными и будут обновлены.

Этого можно избежать при помощи ключей, устанавливающихся в атрибут `key`.
```jsx
const renderItem = item => (<li key={item}>{item}</li>);
```
При наличии ключей `React` пытается сопоставить элементы по этим ключам. Если элементы по ключам совпадают, то они остаются без изменений.

Для *правильной работы* этого *алгоритма* важно, чтобы *ключи* были *уникальными*. Поэтому в ключ нужно *передавать уникальное значение*, *присущее элементу* (например, `id`). Если уникальные значения отсутствуют, лучше всего их разово сгенерировать и использовать (например, при помощи `uuid()`. В примере выше уникальным значением является сам `item` (число). 

`index` элемента в массиве использовать не рекомендуется. При его использовании в примере выше произойдёт аналогичная ситуация (под индексом 0 в старом массиве лежит 0, а в новом 1). Использовать индекс можно только в том случае, когда порядок элементов не меняется (например, когда элементы могут добавляться только в конец и оттуда же).

*Ключи* должны быть *стабильными*, *предсказуемыми* и *уникальными*. *Нестабильные ключи* (например, `key={Math.random()}`) вызовут необязательное пересоздание экземпляров компонента и DOM-узлов, что приводит к потере состояния дочерних компонентов и падению производительности.

## Мемоизация

**Мемоизация** (memoization, запоминание) — сохранение результатов выполнения функций для предотвращения повторных вычислений.

Мемоизация подразумевает проверку перед каждым вызовом мемоизируемой функции: если она ранее вызывалась с теми же параметрами, как сейчас, — вернуть результат из памяти, иначе — вычислить и записать в память.

<!--
Очевидно, что мемоизация предполагает работу с чистыми функциями, которые всегда при тех же параметрах вернут тот же результат.
```js
/* функция ниже не подойдёт для мемоизации */
const rand = (min, max) => min + Math.random() * (max - min);
```
-->

Напишем простую функцию инкремента.
```js
const inc = val => val + 1;
console.log(inc(5)); // 6
```
Мемоизируем её.
```js
let memory = {};

const incMemo = (val) => {
  if (memory[val] === void 0) {
    memory[val] = inc(val);
  } else {
    console.log('took from memory!');
  }
  return memory[val];
};

console.log(incMemo(5)); // 6
console.log(memory); // { 5: 6 }
console.log(incMemo(5)); // 'took from memory!', 6
```
Обобщим функцию мемоизации, чтобы её можно было переиспользовать для любой функции одного аргумента.
```js
const memo = fn => (val) => {
  if (memory[val] === void 0) {
    memory[val] = fn(val);
  } else {
    console.log('took from memory!');
  }
  return memory[val];
};

const dec = val => val - 1;

const decMemo = memo(dec);
console.log(decMemo(6)); // 5
console.log(decMemo(6)); // 'took from memory!', 5
```
Перепишем функцию `memo` таким образом, чтобы она могла принимать несколько параметров.
```js
const memo = fn => (...args) => {
  const key = args.toString();
  if (memory[key] === void 0) {
    memory[key] = fn(...args);
  } else {
    console.log('took from memory!');
  }
  return memory[key];
};

const mul = (a, b) => a * b;

memory = {};
const mulMemo  = memo(mul);
console.log(mulMemo(3, 7)); // 21
console.log(memory); // { "3, 7": 21 }
console.log(mulMemo(3, 7)); // 'took from memory!', 21
```
В примерах выше используется общее хранилище для всех функций. Перепишем функцию `memo` таким образом, чтобы у каждой функции `fn` было своё хранилище.

Для этого используем метод `toString()`, возвращающий строковое представление функции.
```js
console.log(mul); // "(a, b) => a * b"
```
Будем использовать это представление в качестве ключа в хранилище `memory`. Этому ключу будет соответствовать хранилище для конкретной функции `fn`.
```js
const memo = fn => (...args) => {
  const fnKey = fn.toString();
  memory[fnKey] = memory[fnKey] || {};
  const argsKey = args.toString();
  if (memory[fnKey][argsKey] === void 0) {
    memory[fnKey][argsKey] = fn(...args);
  } else {
    console.log('took from memory!');
  }
  return memory[fnKey][argsKey];
};

const divide = (a, b) => a / b;

memory = {};
const divideMemo = memo(divide);
console.log(divideMemo(21, 7)); // 3
console.log(memory); // { "(a, b) => a * b": { "3,7": 21 } }
console.log(memory[divide.toString()]); // { "3,7": 21 }
console.log(divideMemo(21, 7)); // 'took from memory!', 3
```
Сейчас хранилище `memory` очищается только вручную: результаты вычислений остаются даже тогда, когда функция больше не используется.
```js
let sum = (...args) => args.reduce((acc, val) => acc + val, 0);
const sumKey = sum.toString();

const sumMemo = memo(sum);
console.log(sumMemo(1, 2, 3)); // 6
console.log(sumMemo(1, 2, 3)); // 'took from memory!', 6

sum = null; // удаляем функцию
console.log(memory[sumKey]); // { "1,2,3": 6 } (данные о sum остались)
```

```js
let memory = new WeakMap();

const memo = fn => (...args) => {
  const fnKey = fn.toString();
  memory[fnKey] = memory[fnKey] || {};
  const argsKey = args.toString();
  if (memory[fnKey][argsKey] === void 0) {
    memory[fnKey][argsKey] = fn(...args);
  } else {
    console.log('took from memory!');
  }
  return memory[fnKey][argsKey];
};
```
