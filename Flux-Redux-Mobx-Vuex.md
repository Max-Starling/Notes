# Flux

**Flux** — архитетура построения пользовательских интерфейсов, основанная на *шаблоне "Наблюдатель"* (observer pattern).  
Изначально разработанна компанией *Facebook* для *React* и *React Native* приложений.  

*Особенности Flux*:
* *Однонаправленный поток данных*:  
Action Creator -> Action -> Dispatcher -> Callback -> Store -> View.
* Может быть *несколько Stores*.
* *Store* может быть как *изменяемым* (mutable), так и *неизменяемым* (immutable).
* В приложении только *один Dispatcher*, *региструющий все Callbacks*.

На данных момент есть несколько реализаций архитектуры и её видоизменённых форм:
* [Flux](https://github.com/facebook/flux) (реализация от самих Facebook).
* Redux

Главные *конкурентные* технологии: 
* Mobx

## Структура Flux

**Action** — *объект*, *описывающий происходящее* в приложении *действие*.  

*Action обязательно* должен иметь **тип** (type), может иметь *дополнительную информацию* (если полей несколько, то можно их объединить в один объект, например payload).
```js
const CHANGE_FETCH_STATUS = 'CHANGE_FETCH_STATUS';
const FETCH_ARTICLES = 'FETCH_ARTICLES';
```
Пример Actions.
```js
const FETCH_START = ({
  type: CHANGE_FETCH_STATUS,
  isFetching: true, 
});

const FETCH_END = ({
  type: CHANGE_FETCH_STATUS,
  isFetching: false,
});
```

**Action Creator** — *функция-строитель*, которая создаёт *Action* в зависимости от переданных ей аргументов.
```js
const fetchStart = () => FETCH_START;
const fetchEnd = () => FETCH_END;

const fetchArticles = articles => ({
  type: FETCH_ARTICLES,
  payload: articles,
});
```

**Store** — место, где хранятся данные.  
В простейшем случае — объект, в более сложном — класс или модуль.
```js
const store = {
  articles: [],
  isFetching: false,
};
```
Flux допускает наличие нескольких Stores.  

Реализация Store от Facebook имеет следующий функционал:
* getState(): T — получение полного состояния текущего Store. Если Store неизменяемый (immutable), то следует переопределить метод и не передавать состояние напрямую.
* getInitialState(): T — задание начального состояния текущего Store. Вызывается только один раз: во время создания.
* reduce(state: T, action: object) — изменяет или не изменяет текущее состояние в зависимости от Action. Метод обязательно должен быть переопределён; должен быть чистым (pure), без сайд-эффектов.
* areEqual(one: T, two: T): boolean — проверяет, совпадают ли две версии состояния. Если Store неизменяемый, то не нужно переопределять этот метод.


**Callback** (в контексте Flux) — функция, которая принимает Action и в зависимости от него обновляет или не обновляет часть данных, лежащих в Stores.
```js
const articleCallback = (action) => {
  if (action.type === CHANGE_FETCH_STATUS) {
    store.isFetching = action.isFetching;
  } else if (action.type === FETCH_ARTICLES) {
    store.articles = action.articles;
  }
} 
```
Каждый Callback должен быть зарегистрирован при помощи Dispatcher.

**Dispatcher** — это модуль, который позволяет регистировать (register) Callbacks и вызывать их всех с параметром Action каждый раз, когда вызывается функция dispatch. 

Под капотом лежит шаблон "Наблюдатель" (Observer pattern, EventEmmiter) и происходит подписка на события (subscription).  

Реализация Dispatcher от Facebook имеет следующий функционал:
- **register(callback: function): string** — регистрирует Callback, возвращает его идентификатор id.
- **dispatch(action: object): void** — отправляет Action во все зарегистрированные Callbacks.
- **isDispatching(): boolean** — возвращает true, если происходит отправка (dispatching) в данный момент, false иначе.
- **waitFor(ids: \[string\]): void** — ожидает выполения Callbacks, имеющих идентификаторы ids, прежде, чем продолжать выполнять текущий Callback.
- **unregister(id): void** — разрегистрирует Callback по id.

```js
const dispatcher = new Dispatcher();

const articleCallbackId = dispatcher.register(articleCallback);

// store: { articles: [], isFetching: false }
dispatcher.dispatch(FETCH_START); 
// store: { articles: [], isFetching: true }
dispatcher.dispatch(fetchArticles([1, 3])); 
// store: { articles: [1, 3], isFetching: true }
dispatcher.dispatch(FETCH_END); 
// store: { articles: [1, 3], isFetching: false }

// если Action не обрабатывается ни в одном Callback, то Store должен остаться без изменений
dispatcher.dispatch({ type: 'INCORRECT_ACTION' }); 
// store: { articles: [1, 3], isFetching: false }

// если Callback был разрегистрирован, то обрабатываемые им Actions не должны менять что-либо в Store
dispatcher.unregister(articleCallbackId);
dispatcher.dispatch(fetchArticles([2, 4]));
// store: { articles: [1, 3], isFetching: false } 
```

