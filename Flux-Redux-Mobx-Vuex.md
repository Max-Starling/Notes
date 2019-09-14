# Flux

**Flux** — архитетура построения пользовательских интерфейсов, основанная на *шаблоне "Наблюдатель"* (observer pattern, EventEmitter).  
Изначально разработана компанией *Facebook* для *React* и *React Native* приложений.  


<!-- На данных момент есть несколько реализаций архитектуры и её видоизменённых форм:
* [Flux](https://github.com/facebook/flux) (реализация от самих Facebook).
* Redux
* Vuex -->

<!-- Главные *конкурентные* технологии: 
* Mobx  -->

 
## Особенности Flux
* *Однонаправленный поток данных*:  
**Action Creator -> Action -> Dispatcher -> Callbacks -> Stores -> View**.  
Action создаётся при взаимодействии пользователя со View, но может создаваться и самим приложением.
* Может быть *несколько Stores*.
* *Store* может быть как *изменяемым* (mutable), так и *неизменяемым* (immutable).
* В приложении может быть только *один Dispatcher*, *региструющий все Callbacks*.

## Структура Flux

**Action** — *объект*, *описывающий происходящее* в приложении *действие*.  

*Action обязательно* должен иметь **тип** (type), может иметь *дополнительную информацию* (если полей несколько, то можно их объединить в один объект, например payload).
```js
const CHANGE_FETCH_STATUS = 'CHANGE_FETCH_STATUS';
const FETCH_ITEMS = 'FETCH_ITEMS';
```
Пример Actions.
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
## Реализация Flux от Facebook
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

# Redux

**Redux** — библиотека, основанная на подходе Flux и вносящая в него некоторые правки.

## Три основных принципа Redux
* Только *один источник правды* (single source of truth) — *Store*.  
* *Состояние* доступно *только для чтения* (state is read-only). Его можно *изменить* лишь с помощью *Actions*.
* *Изменение состояния* происходит только с использованием *чистых* (pure) *функций* — *Reducers*.

## Другие особенности Redux
* *Однонаправленный поток данных*:  
**Action Creator -> Action -> dispatch -> Reducers -> Store -> View**.  
* *Store* только один, но он может разбиваться на части, за каждую из которых отвечает отдельный Reducer.
* *Store* должен быть *неизменяемым* (immutable).
* *Отсутствует Dispatcher*, вместо него используется функция `store.dispatch()`.

## Структура Redux

Части **Action**, **Action Creator** и **Store** определяются аналогично подходу Flux за исключением того, что Action Creator в Redux не может вызывать функцию dispatch, при необходимости за него это делает **Bound Action Creator**:
```js
const ADD_ITEM = 'ADD_ITEM';

/* Action Creator */
const addItem = item => ({ type: ADD_ITEM, item });
dispatch(addItem(5)); // dispatch action ADD_ITEM

/* Bound Action Creator */
const boundAddItem = item => dispatch(addItem(item));
boundAddItem(5) // dispatch action ADD_ITEM
```

**Reducer** — чистая (pure) функцией вида `(previousState, action) => newState`.  
Reducer определяет, как изменинтся состояние приложения (state) в ответ на Action.  
Называется так потому, что похож на функцию `reduce`:
```js
const reduce = (accumulator, currentValue) => accumulator + currentValue;
```
