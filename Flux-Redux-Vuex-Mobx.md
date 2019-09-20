
* [Flux](#flux)
  * [Особенности Flux](#особенности-flux)
  * [Структура Flux](#структура-flux)
* [Redux](#redux)
  * [Три основных принципа Redux](#три-основных-принципа-redux)
  * [Другие особенности Redux](#другие-особенности-redux)
  * [Структура Redux](#структура-redux)
* [Vuex](#vuex)
  * [Особенности Vuex](#особенности-vuex)
  * [Структура Vuex](#структура-vuex)
* [MobX](#mobx)
* [Дополнительно](#дополнительно)
  * [Реализация Flux от Facebook и её использование](#реализация-flux-от-facebook-и-её-использование)
  * [Использование React с Redux](#использование-react-с-redux)

# Flux

**Flux** — *архитетура построения пользовательских интерфейсов*, основанная на *шаблоне "Наблюдатель"* (observer pattern, EventEmitter).  
Изначально разработана компанией *Facebook* для *React* и *React Native* приложений.  
 
## Особенности Flux
* *Однонаправленный поток данных*:  
**Action Creator -> Action -> Dispatcher -> Callbacks -> Stores -> Views**.  
Action создаётся при взаимодействии пользователя со View, но может создаваться и самим приложением.
* Может быть *несколько Stores*.
* *Store* может быть как *изменяемым* (mutable), так и *неизменяемым* (immutable).
* В приложении может быть только *один Dispatcher*, *региструющий все Callbacks*.

## Структура Flux

**Action** — *объект*, *описывающий происходящее* в приложении *действие*.  

*Action обязательно* должен иметь **тип** (type), может иметь *дополнительную информацию* (если полей несколько, то для удобства можно их объединить в одно поле-объект, например `payload: { /* ... */ }`).
```js
const CHANGE_FETCH_STATUS = 'CHANGE_FETCH_STATUS';
const FETCH_ITEMS = 'FETCH_ITEMS';
```
Пример Action.
```js
const FETCH_ITEMS_ACTION = ({
  type: FETCH_ITEMS,
  items: [1, 3],
});
```

**Action Creator** — *функция-строитель*, которая создаёт *Action* в зависимости от переданных ей аргументов.
```js
const changeFetchStatus = isFetching => ({
  type: CHANGE_FETCH_STATUS,
  isFetching,
});

const fetchItems = () => FETCH_ITEMS_ACTION;
```

**Store** — место, где хранятся данные.  
В простейшем случае — объект, в более сложном — класс или модуль.
```js
const store = {
  items: [],
  isFetching: false,
};
```
Flux допускает наличие нескольких Stores.  

**Callback** (в контексте Flux) — функция, которая принимает Action и в зависимости от него обновляет или не обновляет часть данных, лежащих в Stores.
```js
const itemCallback = (action) => {
  if (action.type === CHANGE_FETCH_STATUS) {
    store.isFetching = action.isFetching;
  } else if (action.type === FETCH_ITEMS) {
    store.items = action.items;
  }
} 
```
Каждый Callback должен быть зарегистрирован при помощи Dispatcher.

**Dispatcher** — это модуль или класс, который позволяет регистировать (register) Callbacks и вызывать их всех с параметром Action каждый раз, когда вызывается функция dispatch. 

<!-- Под капотом лежит шаблон "Наблюдатель" (Observer pattern, EventEmmiter) и происходит подписка на события (subscription). -->

```js
const dispatcher = new Dispatcher();

dispatcher.register(itemCallback);

// store: { items: [], isFetching: false }
dispatcher.dispatch(changeFetchStatus(true)); 
// store: { items: [], isFetching: true }
dispatcher.dispatch(fetchItems()); 
// store: { items: [1, 3], isFetching: true }
dispatcher.dispatch(changeFetchStatus(false)); 
// store: { items: [1, 3], isFetching: false }

// если Action не обрабатывается ни в одном Callback, то Store должен остаться без изменений
dispatcher.dispatch({ type: 'INCORRECT_ACTION' }); 
// store: { items: [1, 3], isFetching: false }
```

Во *Flux* разрешено, чтобы *Action Creator* *вызывал dispatch* сразу *при создании Action* (так можно упростить код выше):
```js
const fetchItems = () => dispatcher.dispatch({
  type: FETCH_ITEMS,
  items: [1, 3],
});
```

**View** — UI-компонента (обычно React-компонента).
```jsx
const Button = (<button onClick={fetchItems}>Fetch items</button>);
```

# Redux

**Redux** — библиотека, основанная на подходе Flux и вносящая в него некоторые правки.

## Три основных принципа Redux
* Только *один источник правды* (single source of truth) — *Store*.  
* *Состояние* доступно *только для чтения* (state is read-only). Его можно *изменить* лишь с помощью *Actions*.
* *Изменение состояния* происходит только с использованием *чистых функций* (pure functions) — *Reducers*.

## Другие особенности Redux
* *Однонаправленный поток данных*:  
**Action Creator -> Action -> Reducers -> Store -> Views**.  
* *Store* только один, но он может разбиваться на части, за каждую из которых отвечает отдельный Reducer.
* *Store* должен быть *неизменяемым* (immutable). Reducer не изменяет state напрямую, а работает с копией и возвращает её.
* *Отсутствует Dispatcher*, вместо него используется функция `store.dispatch()`.
* В схеме Redux изначально нет места асинхронным функциям, но есть возможность это исправить, подключив middleware (thunk, saga).

## Структура Redux

Части **Action**, **Action Creator**, **Store** и **View** определяются аналогично подходу Flux за исключением того, что в Redux может быть только один Store, а Action Creator не может вызывать функцию dispatch, при необходимости за него это делает **Bound Action Creator**:
```js
const ADD_ITEM = 'ADD_ITEM';

/* Action Creator */
const addItem = item => ({ type: ADD_ITEM, item });
dispatch(addItem(5)); // dispatch action ADD_ITEM

/* Bound Action Creator */
const boundAddItem = item => dispatch(addItem(item));
boundAddItem(5) // dispatch action ADD_ITEM
```

**Reducer** — *чистая функция*, определяющая, как изменится состояние приложения (state) в ответ на *Action*.  
Reducer имеет вид: `(previousState, action) => newState`, что напоминает функцию `reduce()`, откуда и название.  
```js
const reduce = (accumulator, currentValue) => accumulator + currentValue;
```

```js
const initialState = {
  items: [],
};

const itemReducer = (state = initialState, action) => {
  switch (action) {
    case ADD_ITEM:
      return ({
        ...state,
        items: [
          ...state.items,
          action.item,
        ],
      });

    default:
      return state;
  }
};
```
Почему используется деструктуризация `...` и нельзя просто напрямую изменить значение в state?  
Потому что объекты и массивы в JavaScript передаются по ссылке.  
Если изменить их значение напрямую, то произойдёт мутация и Redux не заметит изменений, а значит оно не дойдёт до View в нужный момент.

Когда Action не обрабатывается в текущем Reducer, то он попадает в блок default.  
Состояние должно остаться без изменений, поэтому оно возвращается без деструктуризации.  

В Redux может быть несколько Reducers, каждый из которых отвечает за какую-то часть State.  
Reducers комбинируются в главный Reducer, который передаётся в Store при создании:
```js
import { createStore, combineReducers } from 'redux';

/* ... */

const reducer = combineReducers({
  item: itemReducer,
  another: anotherReducer,
});

const store = createStore(reducer);
/*
const store = {
  item: { ... },
  another: { ... },
};
*/
```

Как и в случае Callbacks из Flux, все Reducers получают приходящий Action.  
Тем не менее между Reducer и Callback есть существенное отличие:  
Callback не принимает в параметрах и не возвращает state (как это делает Reducer), вместо этого он изменяет state напрямую.  
```js
const itemState = {
  items: [],
};

const itemCallback = (action) => {
  if (action.type === ADD_ITEM) {
    itemState.items = [
      ...items,
      action.item,
    ];
  }
};
```

В Redux отсутствует Dispatcher, его работу берёт на Store, предоставляя функцию **dispatch**.
```js
// item's state: { items: [] }
store.dispatch(addItem(7));
// item's state: { items: [7] }
console.log(store.getState())
```

# Vuex

**Vuex** — *шаблон управления состоянием* (state management pattern) приложения, а также *библиотека*, разработанная для Vue.js приложений. 
 
## Особенности Vuex
* *Однонаправленный поток данных*:  
**Action -> Mutations -> State -> Views**.  
Action создаётся при взаимодействии пользователя со View, но может создаваться и самим приложением.
* Может быть *только один State*, но в нём могут быть выделены отдельные *независимые блоки* — *Modules*.
* *State* *изменяем* (mutable), *единственный способ* его *изменить* — *совершить* (commit) *Mutation*.
* *Action* может использовать *асинхронные функции*, *Mutation* — не может.

## Структура Vuex
 
**State** — *объект*, в котором *хранится состояние приложения*.  
*Располагается* в *Store* и *инициализируется* вместе с ним.  
```js
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);
const store = new Vuex.Store({
  state: {
    items: [],
  },
});
```
Чтобы *State* был *доступен во всех компонентах*, нужно *встроить Store* в приложение.
```js
  const app = new Vue({
    /* ... */
    store,
  });
```
Использование *State внутри компонент*: `this.$store.state`.
```js
const Items = {
  template: `<ul>
  <li v-for="item in items">
    {{ item }}
  </li>
</ul>`,
  computed: {
    items () {
      return this.$store.state.items,
    }
  }
}
```

**Getter** — *вспомогательная функция*, *принимающая State* и *возвращающая* некоторую его *часть* или же *вычисляющая* на его основании *что-то новое*.
*Параметры Getter*: `(state, getters)`, `state` — *ссылка* на `store.state`, `getters` — другие Getters, которые можно использовать.
```js
const store = new Vuex.Store({
  /* ... */,
  getters: {
    itemsLength: state => state.items.length,
    numbers: state => state.items.filter(item => typeof item === 'number'),
    getItemByIndex: state => index => state.items[index],
  },
});
```
*Использование Getters*.
```js
store.getters.itemsLength;
store.getters.getItemByIndex(0);

// в компоненте
this.$store.getters.numbers;
```

**Mutation** — *функция*, в которой происходит *изменение State*.  
*Единственный способ изменить State* — *совершить* (commit) *Mutation*.  
*Mutation* — *синхронная транзакция*, для *асинхронности* используется *Action*.  
*Параметры Mutation*: `(state, payload)`, `state` — *ссылка* на `store.state`, `payload` — *объект с* переданными *параметрами*.
```js
// mutation-types.js
export const ADD_ITEM = 'ADD_ITEM';
```
```js
// store.js
import { ADD_ITEM } from './mutation-types';

const store = new Vuex.Store({
  /* ... */,
  mutations: {
    [ADD_ITEM] (state, payload) {
      // мутируем State (не можем испозовать state.items.push(), потому
      // что должна измениться ссылка на массив, произойти мутация)
      state.items = [...state.items, item];
    },
  },
});
```
*Совершение Mutation*: `commit(type, payload)`.
```js
// state: { items: [] }
store.commit(ADD_ITEM, { item: 7 });
// или
store.commit({ type: ADD_ITEM, item: 7 });
// state: { items: [7] }

// в компоненте
this.$store.commit(ADD_ITEM, { item: 7 });
```

**Action** — *функция*, которые *совершает* (commit) в своём теле *Mutations* и может выполнять *асинхронные операции*.  
*Параметры Action*: `(context, payload)`, `context` — объект, который содержит:
* `state` — то же, что и `store.state`
* `commit(type, payload)` — функция для *совершения Mutation*
* `dispatch(type, payload)` — функция для *отправки Action*
* `getters` — *Getters*

```js
import api from '/* ... */'; // связующее звено между клиентом и сервером

const store = new Vuex.Store({
  /* ... */,
  actions: {
    /* синхронный Action */
    addItem (context, payload) {
      context.commit('addItem', payload.item);
    },
    /* асинхронный Action */
    async fetchItem (context) {
      const item = await api.get(/*...*/); // получение item откуда-либо, допустим получили 7
      
      context.dispatch('addItem', { item });
      // или
      context.dispatch({ type: 'addItem', item });
    },
  },
});
```
*Отправка* (dispatch) *Action*: `dispatch(type, payload)`.
```js
// state: { items: [] }
store.dispatch('addItem', 5);
// state: { items: [5] }
store.dispatch('fetchItem');
// state: { items: [5, 7] }

// в компоненте
this.$store.dispatch('fetchItem');
```

**View** — UI-компонента (Vue.js) 

**Modules** позволяют создавать независимые блоки глобального State, в которых есть свои локальные State, Actions, Getters и Mutations. 
```js
const itemModule = {
  state: { /* ... */ },
  mutations: { /* ... */ },
  actions: { /* ... */ },
  getters: { /* ... */ },
};

const userModule = {
  state: { /* ... */ },
  mutations: { /* ... */ },
  actions: { /* ... */ },
  getters: { /* ... */ },
};

const store = new Vuex.Store({
  modules: {
    item: itemModule,
    user: userModule,
  }
});

store.state.item // item's state
store.state.user // user's state
```
Структура локальных Mutations, Actions, Getters такая же, только теперь в качестве `state` берётся локальный State.  
В локальных Actions и Getters есть возможность обратиться к глобальным State и Getters при помощи `rootState` и `rootGetters`.  
```js
const itemModule = {
  state: {
    items: [],
  },
  getters: {
    itemsLength (state, getters, rootState, rootGetters) { /* ... */ },
  },
  mutations: {
    ADD_ITEM (state, payload) {
      state.items = [...state.items, item];
    },
  },
  actions: {
    addItem (context, payload) {
      context.rootGetters;
    },
  },
};
```

По умолчанию Actions, Getters, Mutations вызываются так, словно они были определены в глобальном State.  
Это удобно, если все их названия уникальны, но может стать проблемой в большом приложении, где названия могут совпадать.  
```js
store.commit('ADD_ITEM');
store.dispatch('addItem');
store.getters.itemsLength;
```
Чтобы это исправить, можно замкнуть функционал в Module, задав ему namespace.
```js
const itemModule = {
  namespaced: true,
  /* ... */
};

const store = Vuex.Store({
  modules: {
    item: itemModule,
  },
});
```
Тогда в обращение добавится префикс с названием модуля, а предыдущее обращение перестанет работать.
```js
store.commit('item/ADD_ITEM');
store.dispatch('item/addItem');
store.getters['item/itemsLength'];
```
<!-- Обращения вроде `store.item.commit()` не работают. -->

Modules можно подключать и удалять динамически (многие Vue.js плагины подключают свои Modules так к приложению).
```js
const adminModule = { /* ... */ };

/* подключение модуля */
store.registerModule('user', userModule);
/* удаление модуля */
store.unregisterModule('user');
```
Module может содержать другие Modules.  
```js
const moderatorModule = { /* ... */ };

const userModule = {
  modules: {
    moderator: moderatorModule,
  },
};
```

В больших приложениях State может сильно разрастаться (становится неудобно и сложно его поддерживать), модули помогают решить эту проблему и сделать приложение более расширяемым.  

# MobX

**MobX** — библиотека, созданная для управления состоянием приложения, основанная на подходе прозрачного функционального программирования (Transparent functional reactive programming, TFRP).  

Сам по себе MobX не является архитектурой или хранилищем состояния, но обладает функционалом, с помощью которого можно это всё построить, из-за чего его часто считают альтернативой Redux.

## Основной принцип MobX
Всё, что может быть извлечено (be derived) из состояния приложения, должно быть извлечено. Автоматически.  
(речь идёт о текущем состоянии приложения: при его обновлении должно автоматически обновиться всё, что его использовало)

Суть того, к чему *стремится реактивное программирование*, можно показать на примере (достигается при помощи Observable).
```js
a = 5
b = 7
c = a + b // с = 12

b = 10 // c = 15
a -=4 // c = 11
```

## Особенности MobX

**Actions -> State -> Derivations (computed values) -> Reactions**.

## Структура MobX

### State

**State** — *данные приложения* (объекты, массивы, примитивы), составляющие в совокупности Модель приложения (как в подходах по типу MVC).  

State децентрализован.  
Любое значение, которое в приложении имеет тип Observable и его изменения отслеживаются при помощи Reactions, является частью State.

<!--Чтобы была возможность подписаться на обновление значения, нужно задать ему тип Observable. -->

Синтаксис:
* `observable(value)` (только для массивов и объектов)
* `observable.box(value)` (для примитивных значений)
* `@observable property = value` (только в классах)

```js
import { observable } from 'mobx';

// массивы и объекты
const items = observable([1, 2, 3]);
const item = observable({ title: 'Foo', description: 'Bar' });

// примитивы
const string = observable.box('string');
const number = observable.box(7);
const boolean = observable.box(true);

// Observable.box создаёт объект. Для получения примитивного значения нужно использовать метод get()
console.log(number.get()) // 7

// внутри классов можно использовать декораторы (официально пока ещё экспериментальные) или функции
class ItemStore {
  @observable item = { title: 'Foo', description: 'Bar' }
  @observable string = 'string' // в декораторе примитив без .box
  /* или */
  item = observable({ title: 'Foo', description: 'Bar' })
  string = observable.box('string')
}
```

### Action

**Action** — всё, что изменяет State.  

В отличие от архитектуры Flux и её производных, MobX не ставит ораничений на то, как должны быть обработаны события пользователя.

Простые Actions для массивов и объектов
```js
const item = observable({ title: 'foo', description: 'bar' });
const items = observable([1, 2, 3]);

// Actions
item.title = 'new';
item.title = `${item.title} title`;
item.description = 'new description';
items.pop()
items.push(4)
items[1] = 8;
```
Простые Actions для примитивов:
```js
let number = observable.box(1); // инициализация

// Action
number.set(3); // в консоль выведется 'Changed to 3'

// как ниже делать нельзя, поскольку это перезапишет переменную number, сделав её обычным примитивом
number = 2; // в консоль не выведется ничего
number.set(4); // TypeError: number.set is not a function
```
MobX сам заботится, чтобы все изменения State, произошедшие при помощи Action, автоматически обработались в Derivations и Reactions.

Если строить архитектуру управления состоянием при помощи MobX, то лучше всего использовать встроенный функционал `action`.
* `action(fn)`
* `action(name, fn)`
* `@action classMethod()`

В этом случае так же следует запретить любые изменения State (то есть изменения любого Observable) вне Actions.
```js
import { configure } from 'mobx';

configure({ enforceActions: 'always' });
```
С такой конфигурацией простые примеры выше с Actions не будут работать (после появления Reactions), поскольку будет ошибка: `Error: [mobx] Since strict-mode is enabled, changing observed observable values outside actions is not allowed`.
Нужно изменить код следующим образом:
```js
import { observable, action } from 'mobx';

const item = observable({ title: 'foo', description: 'bar' });
const items = observable([1, 2, 3]);

const changeItem = action(({ title, description } = {}) => {
  item.title = title;
  item.description = description;
  
  // нельзя переприсваивать, поскольку item перестанет быть Observable
  item = { title, description };
});

const addItem = action(item => items.push(item));
```

### Derivation

**Derivation (computed values)** — любое значение, которое может вычисляется автоматически после обновления State.  
Derivation может быть переменной, UI-компонентой и многим другим.  
Derivations используются в приложении, как и Observables.  

На практике Derivation — результат выполнения функции, которая имеет тип Computed.  
В этой функции не должно быть сайд-эффектов (side effects).

Синтаксис:
* `computed(() => derivation)`
* `@computed get property() { return derivation; }` (только в классе)

```js
const statement = observable.box(3 > 1); // true

const oppositeStatement = computed(() => !statement.get());

console.log(oppositeStatement); // false
trueStatement.set(false);
console.log(oppositeStatement); // true
```

### Reaction

**Reaction** — это функция, которая запускается автоматически после обновления State.  
Reaction похож на Derivation, но вместо генерации нового значения в нём обрабатываются сайд-эффекты: вывод в консоль, запросы к серверу и прочее.

```js
// Автоматически вызывается для всех Observable значений, использующихся в сайд-эффектах
autorun(() => { /* side effects here */ }));

// подписка на изменения
autorun(() => console.log(item)); // сработает только при инициализации (ссылка на объект не меняется) и выведет в консоль Observable
autorun(() => console.log('Title changed', item.title)); // при обновлении поля поля title объекта item
autorun(() => console.log('Title or description changed', item.title, item.description)); // при обновлении одного из полей title или description объекта item
autorun(() => console.log('Data changed', { ...item })); // при обновлении любого поля объекта item

// подписка на изменения
// Observable — объект. Чтобы получить значение переменной number, нужно использовать number.get()
autorun(() => console.log('Changed to', number.get()); 

autorun(() => { console.log('Item changed', item) });

when(() => condition, () => { /* side effects here */ });

reaction(() => data, data => { /* side effects here */ });

reaction(
  () => arr.map(item => [item.a, item.b]),
  data => console.log("CHANGED A,B:", data)
);
```

При использовании MobX с React наиболее популярный Reaction — `observer`.  
Он оборачивает метод класса `render()` или функциональный компонент в `autorun`, тогда в случае изменения любого Observable внутри них, компонент автоматически перерендерится.  
```jsx
import { Fragment } from 'react';
import { render } from 'react-dom';
import { observable } from 'mobx';
import { observer } from 'mobx-react';

const state = observable({
  number: 0,
});

const increment = () => {
  state.number += 1;
};

// пример функциональной компоненты
const Number = observer(({ state }) => (
  <button onClick={increment}>Click me to increase: {state.number}</button>
));

// пример класса
@observer
class NumberClass extends React.Component {
  render() {
    return <button onClick={increment}>Click me to increase: {this.props.state.number}</button>;
  }
}

const App = () => (
  <Fragment>
    <Number state={state} />
    <NumberClass state={state} />
  </Fragment>
);

render(<App />, document.getElementById('root'));
```

# Дополнительно

## Реализация Flux от Facebook и её использование
*Функционал Dispatcher*:
- `register(callback: function): string` — регистрирует Callback, возвращает его идентификатор id.
- `dispatch(action: object): void` — отправляет Action во все зарегистрированные Callbacks.
- `isDispatching(): boolean` — возвращает true, если происходит отправка (dispatching) в данный момент, false иначе.
- `waitFor(ids: string[]): void` — ожидает выполения Callbacks, имеющих идентификаторы ids, прежде, чем продолжать выполнять текущий Callback.
- `unregister(id): void` — разрегистрирует Callback по id.

*Функционал ReduceStore*:
* `getState(): T` — получение полного состояния текущего Store. Если Store неизменяемый (immutable), то следует переопределить метод и не передавать состояние напрямую.
* `getInitialState(): T` — задание начального состояния текущего Store. Вызывается только один раз: во время создания.
* `reduce(state: T, action: object)` — изменяет или не изменяет текущее состояние в зависимости от Action. Метод обязательно должен быть переопределён; должен быть чистым (pure), без сайд-эффектов.
* `areEqual(one: T, two: T): boolean` — проверяет, совпадают ли две версии состояния. Если Store неизменяемый, то не нужно переопределять этот метод.
```ts
import { Dispatcher } from 'flux';
import { ReduceStore } from 'flux/utils';

const ItemDispatcher = new Dispatcher();

/* Action Creators */
export const changeFetchStatus = isFetching => ItemDispatcher.dispatch({
  type: 'CHANGE_FETCH_STATUS',
  isFetching,
});

export const fetchItems = () => ItemDispatcher.dispatch({
  type: 'FETCH_ITEMS',
  items: [1, 3],
});

interface IItemStore {
  items: any[];
  isFetching: boolean;
}

class ItemStore extends ReduceStore<IItemStore> {
  constructor() {
    super(ItemDispatcher);
  }

  getInitialState(): IItemStore {
    return ({
      items: [],
      isFetching: false,
    });
  }

  getState(): IItemStore {
    return ({
      ...this._state,
    });
  }

  reduce(state: IItemStore, action: object): IItemStore {
    switch (action.type) {
      case 'CHANGE_FETCH_STATUS':
        return ({
          ...state,
          isFetching: action.isFetching,
        });
    
      case 'FETCH_ITEMS':
        return ({
          ...state,
          items: [...action.items],
        });
    
      default:
        return state;
    }
  }
}

export default new ItemStore();
```

## Использование React с Redux

### Возможная структура

Для маленьких проектов и быстрых решений:
```
  - resources/
  -- item.js
  -- store.js
```

Хорошо расширяемая структура (разбиение по смыслу):
```
 - resources/
 -- item/
 --- item.actions.js
 --- item.reducer.js
 --- item.selectors.js
 --- item.types.js
 -- store.js
```

Также расширяемая, но очень удобная (разбиение по типу файлов):
```
 - resources/
 -- actions/
 --- item.actions.js
 -- reducers/
 --- item.reducer.js
 -- selectors/
 --- item.selectors.js
 -- types/
 --- item.types.js
 -- store.js
```
### Настройка

**Thunk** - функция, которая оборачивает выражение, чтобы отложить его выполнение.  
При помощи библиотеки redux-thunk можно отложить вызов функции dispatch, чтобы появилась возможность вызывать асинхронные функции:
```js
// без redux-thunk
const doSomething = params => ({ type: 'DO_SOMETHING', ...params });

// redux-thunk без асинхронной операции
const doSomething = params => dispatch => dispatch({ type: 'DO_SOMETHING', ...params });

// redux-thunk c асинхронной операцией

const doSomething = params => async (dispatch) => {
  await new Promise(resolve => setTimeout(resolve, 1000));
  return dispatch({ type: 'DO_SOMETHING', ...params });
};
```
0) Устанавливаем зависимости
```npm
npm i redux react-redux redux-thunk
```

1) Создаём основные компоненты:

Types
```js
export const DELETE_ITEM = 'DELETE_ITEM';
```

Actions (Actions, Action Creators, Bound Action Creators)
```js
/* item.actions */
import { DELETE_ITEM } from './item.types';

const deleteItem = id => dispatch => dispatch({ type: DELETE_ITEM, id });
```

Reducer
```js
/* item.reducer.js */

const initialState = {
  items: [],
  /* ... */
};

const itemReducer = (state = initialState, action) => { /* ... */ };

export default itemReducer;
```

Selectors (вспомогательные функции для получения некоторой части state)
```js
/* item.selectors */

/* state - все данные в Store
   item - название Reducer, отвечающего за данные для этой компоненты
   items - массив элементов */
const getItemById = (state, id) => state.item.items.find(item => item.id === id);
const getItemsLength = state => state.item.items.length;
```

2) Создаём Store, комбинируя Reducers и добавляя redux-thunk в качестве middleware:
```js
/* store.js */
import {
  createStore,
  combineReducers,
  applyMiddleware,
} from 'redux';
import thunk from 'redux-thunk';
import { itemReducer } from './item/item.reducer';

const reducer = combineReducers({ /* ... */ });

const store = createStore(reducer, applyMiddleware(thunk));

export default store;
```

3) Оборачиваем главную компоненту `<App>` компонентой `<Provider>`, в которую передаём созданный Store.
```jsx
import React from 'react'
import { render } from 'react-dom'
import { Provider } from 'react-redux'
import App from './App'
import store from './store'

const AppContainer = () => (
  <Provider store={store}>
    <App />
  </Provider>
);

render(<AppContainer />, document.getElementById('root'));
```

4) Оборачиваем компоненту, которую хотим подключить к Redux, компонентой высшего порядка при помощи функции `connect(mapStateToProps, mapDispatchToProps)`.
* `mapStateToProps(state, props)` отвечает за передачу части Store в props оборачиваемой компоненте (эта часть будет передаваться каждый раз после обновления в Store).
* `mapDispatchToProps` отвечает за оборачивание переданных ему Action Creators в функцию dispatch (чтобы в оборачиваемой компоненте не нужно было вызывать dispatch).

```jsx
import { connect } from 'react-redux';
import { deleteItem } from 'resources/item/item.actions';
import { getItemsLength, getItemById } from 'resources/item/item.selectors';

const ItemView = ({
  item,
  itemsLength,
  dispatchDeleteItem
}) => (
  <>
    <div class="item">{item}</div>
    <div class="index">`Item 1 of ${itemsLength}`</div>
    <button onClick={dispatchDeleteItem}> 
  </>
);

// пусть props.id - параметр, который передаётся от родительской компоненты или от роутера
const mapStateToProps = (state, props) => ({
  item: getItemById(state, props.id),
  itemsLength: getItemsLength(state),
});

const mapDispatchToProps = {
  dispatchDeleteItem: deleteItem,
};

export default connect(mapStateToProps, mapDispatchToProps);
```
