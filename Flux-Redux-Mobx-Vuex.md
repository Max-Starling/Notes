# Flux

**Flux** — архитетура построения пользовательских интерфейсов, основанная на *шаблоне "Наблюдатель"* (observer pattern).  
Изначально разработанна компанией Facebook для *React* и *React Native* приложений.  

На данных момент есть несколько реализаций архитектуры и её видоизменённых форм:
* [Flux](https://github.com/facebook/flux) (реализация от самих Facebook).
* Redux

Конкурентные технологии: 
* Mobx

## Основные особенности Flux

Однонаправленный поток данных.

Может быть несколько хранилищ Store.  

Диспетчер регистрирует все хранилища.
В одном приложении может быть только один диспетчер Dispatcher, с которым связаны все Хранилища.

## Структура Flux

**Action** — объект, описывающий происходящее в приложении действие.  

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

**Store** — место, где хранятся данные. Представлено объектом.  
```js
const store = {
  articles: [],
  isFetching: false,
};
```
Flux допускает наличие нескольких хранилищ.  

**Callback** (в контексте Flux) — функция, которая принимает Action и в зависимости от него обновляет или не обновляет данные какого-то Store.
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

**Dispatcher** — это модуль, который позволяет регистировать Callbacks и отравлять в каждый них Action каждый раз, когда вызывается Dispatch. 

Под капотом лежит шаблон "Наблюдатель" (Observer pattern, EventEmmiter) и происходит подписка на события (subscription). 

Реализация Dispatcher от Facebook имеет следующий функционал:
- **register(callback)** — регистрирует Callback, чтобы он вызывался при каждом отправленном (dispatched) Actions; возвращает идентификатор id для Callback.
- **dispatch(action)** — отправляет Action во все зарегистрированные Callbacks.
- **isDispatching()** — возвщатает true, если происходит отправка (dispatching) в данный момент, false иначе.
- **waitFor(ids)** — ожидает выполения Callbacks, имеющих идентификаторы ids, прежде, чем продолжать выполнять текущий Callback.
- **unregister(id)** — разрегистрирует Callback по токену.

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



