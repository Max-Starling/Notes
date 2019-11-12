

## Согласование

React самостоятельно следит за обновлениями.

**Согласование, сверка** (Reconciliation).

### Алгоритм сравнения (Diffing Algorithm)

Сравнение двух деревьев начинается с их корневых элементов (root elements), от типа которых зависит дальнейшее поведение. 

Если *тип* корневых элементов *различен*, *React уничтожает* (tear down) *старое дерево* и *строит новое* с нуля.

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
