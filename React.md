- [Элементы и компоненты](#элементы-и-компоненты)
- [Жизненный цикл компонента](#жизненный-цикл-компонента)
- [React Hooks](#react-hooks)
- [Контролируемые и неконтролируемые компоненты](#контролируемые-и-неконтролируемые-компоненты)
- [Компоненты высшего порядка](#компоненты-высшего-порядка)

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

TODO
<!-- 
### Инициализация

При инициализации проиходит установка Props и начального (initial) State.

### Создание (Mounting)

### Обновнение (Updating) -->

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

## Контролируемые и неконтролируемые компоненты

**Контролируемый компонент** (Controlled component) *контролирует данные форм* при помощи возможностей *React*. Происходит *двухстороннее связывание* при помощи `value` и `onChange` с *полями ввода*, а *данные* этих полей *должны* где-то *сохранятся* (например, в React State или Redux Store).
```jsx
const Form = ({ onSend, value, onChange }) => (
  <form onSubmit={onSend}>
    <input
      type="text"
      value={value}
      onChange={onChange}
    />
    <button type="submit">Send</button>
  </form>
);
```
**Неконтролируемый компонент** (Uncontrolled component) *контролирует данные форм* при помощи *DOM*. *Данные полей ввода сохраняются* в *атрибутах* их *DOM-элементов*.
```jsx
const Form = ({ onSend }) => (
  <form onSubmit={onSend}>
    <input type="text" />
    <button type="submit">Send</button>
  </form>
);
```
Можно задать *значение по умолчанию* для *некотролируемого компонента* при помощи свойства `defaultValue` и *получить текущее значение*, используя `ref`.
```jsx
const Form = ({ onSend }) => {
  const inputRef = React.useRef(null);
  
  const onSubmit = (event) => {
    event.preventDefault();
    console.log('value', inputRef.current.value);
    onSend(event);
  }

  return(
    <form onSubmit={onSubmit}>
      <input
        ref={inputRef}
        defaultValue="Text"
        type="text"
      />
      <button type="submit">Send</button>
    </form>
  );
};
```
*Неконтролируемые компоненты* могут использоваться
* когда нужно написать код быстро (например, что-то проверить).
* когда нет необходимости контролировать промежуточное состояние полей ввода, важен только конечный результат при нажатии на `submit`.
* когда нужно интегрировать в приложение не React-код (HTML + JavaScript, Web Components или что-то ещё).
* всегда в случае `<input type="file" />`, поскольку его значение нельзя контролировать программно.

Во всех остальных случаях не рекомендуется использовать неконтролируемые компоненты, поскольку они хранят свои данные в DOM и React перестаёт быть единственным местом хранения данных.

## Компоненты высшего порядка

Минимальная реализация компонента высшего порядка `withRouter`.
```jsx
const router = { route: 'qq' };
const withRouter = Component => props => (
  <Component {...props} router={router} />
);
```
```jsx
const Navbar = withRouter(props => <div>Route: {props.router.route}</div>));

const render = () => (<Navbar />); // <div> Route: qq </div>
```
Минимальная реализация компонента высшего порядка `connect`.
```jsx
const state = { username: 'Max' };
const dispatch = action => console.log(action);

const connect = (mapStateToProps, mapDispatchToProps) => Component => props => (
  <Component
    {...props}
    {...mapStateToProps(state, props)}
    {...mapDispatchToProps(dispatch)}
  />
));
```
```jsx
const ProfileComponent = props => (
  <div>
    <p>{props.username}</p>
    <button onClick={props.changeUsername}>Change name</button>
  </div>
);

const mapStateToProps = state => state.username;

const mapDispatchToProps = dispatch => ({
  changeUsername: name => dispatch({ type: 'SET_USERNAME', payload: name }),
});

const Profile = connect(ProfileComponent)(mapStateToProps, mapDispatchToProps);

const render = () => (<Profile >/);
```

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
