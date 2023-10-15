## Понятия в Postman

Есть рабочее пространство (англ. `Workspace`), по умолчанию используется `My workspace`.
В рабочем пространстве можно создавать коллекции (англ. `Collections`) логически сгруппированных запросов.

Каждый запрос (`Request`) представляет собой сохранённую информацию об HTTP-запросе:
* классические метод, URL, параметры, заголовки, тело запроса
* но также тесты, Pre-request script и настройки запроса.

Сохраняя информацию о запросе в коллекцию, вы можете в любой момент переиспользовать её в качестве темплейта для новых запросов или же переодически вызывать один и тот же запрос в один клик в любое время.

У колекции также есть свои настройки, которые применяются ко всем запросам в ней:
* переменные окружения
* pre-request-script
* тесты и так далее
![image](https://github.com/Max-Starling/Notes/assets/22237384/9b118c4e-8cfa-47ae-b23d-7964764fe698).

Ниже представлен небольшой схематичный пример, как можно было бы использовать свое личное рабочее пространство:
* My workspace
  * Users dashboard Prod
    * Get users - GET https://users-dashboard.com/api/users
    * Get user by id - GET http://users-dashboard.com/api/users/:id
  * Users dashboard Local
    * Get users - GET http://localhost:3001/api/users
    * Get user by id - GET http://localhost:3001/api/users/:id
  * Calculator
    * Calculate - POST http://localhost:3002/api/calculate

но можно также для каждого проекта создавать новое рабочее пространство для наилучшей изолированности (вдруг вы собираетесь давать доступ кому-то к ворксейсам):
* Users dashboard
  * Production
    * Get users - GET https://users-dashboard.com/api/users
    * Get user by id - GET http://users-dashboard.com/api/users/:id
  * Localhost
    * Get users - GET http://localhost:3001/api/users
    * Get user by id - GET http://localhost:3001/api/users/:id
* Calculator workspace
  * Localhost
    * Calculate - POST http://localhost:3002/api/calculate

Часто бывает так, что в одной компании может быть несколько проектов, на каждый проект- отдельная коллекция, но все проекты в одном пространстве.

Если есть версионирование, можно разбивать коллекции по версиям.

* Users dashboard
  * Api v1.0
    * Get users - GET https://users-dashboard.com/api/v1.0/users
  * Api v1.1
    * Get users - GET https://users-dashboard.com/api/v1.1/users
    * Get users by name - GET https://users-dashboard.com/api/v1.1/users/:name
  * Api v2
    * Get users - GET https://users-dashboard.com/api/v2/users
    * Get users by ID - GET https://users-dashboard.com/api/v2/users/:id

## Использование переменных в Postman

Чтобы постоянно не клонировать коллекции с одними и теми же запросами и минимальными различиями в параметрах, можно задать переменные окружения. Сделать это можно в настройках коллекции:
![image](https://github.com/Max-Starling/Notes/assets/22237384/80a01de8-5c23-4b8f-be23-03d41f521def)

например, вместо того, чтобы создавать две коллекции `Users dashboard Local`, `Users dashboard Production`, досточно просто завести переменную `base_url`.
```
base_url = http://localhost:3001
```
и использовать её в запросе:
```
GET {{base_url}}/api/v1.0/users
```


## Postman pre-request script
Скрипт, который производит ре-аутентификацию с каждым запросом. Сохраняется в 
```js
const postRequest = {
  url: `${pm.collectionVariables.get("base_url")}${pm.collectionVariables.get("access_token_endpoint")}`,
  method: "post",
  header: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    clientId: pm.collectionVariables.get("client_id"),
    secret: pm.collectionVariables.get("secret")
  }),
};

pm.sendRequest(postRequest, (error, response) => {
  if (error) {
    console.log(error); // catch in Postamn debug console
  } else {
    pm.collectionVariables.set("access_token", response.json().accessToken);
  }
});
```
