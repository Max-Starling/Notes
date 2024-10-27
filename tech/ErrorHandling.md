# Обработка ошибок в Node.js

Обработка ошибок в приложении на Node.js — одна из важнейших частей разработки, ведь она позволяет предотвратить аварийные ситуации и обеспечивает стабильную работу приложения.

Далее будут представлены несколько подходов для обработки ошибок на Node.js:

## Использование try-catch и async/await
Для обработки ошибок в асинхронных функциях с `async..await` можно использовать блоки `try..catch`:
```js
async function fetchData() {
  try {
    const data = await call();
    return data;
  } catch (error) {
    console.error('Error fething data:', error);
    throw new Error('Не удалось получить данные');
  }
}
```
Важно логировать ошибки и выдавать пользователю информативные сообщения, но при этом не раскрывать внутренние детали приложения.

## Централизованная обработка ошибок (Middleware в Express)
В Express можно настроить *централизованный обработчик ошибок* с помощью специального middleware:
```js
const express = require('express');
const app = express();

// Пример маршрута
app.get('/data', async (req, res, next) => {
  try {
    const data = await call();
    res.send(data);
  } catch (error) {
    next(error); // Передаем ошибку в следующий middleware
  }
});

// Централизованный обработчик ошибок
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ message: 'Internal server error' });
});

app.listen(3000, () => {
  console.log('Server is listening at 3000');
});
```

Здесь используется функция `next()`, которая *передает ошибку* в *централизованный обработчик ошибок*.
Это позволяет *отделить логику обработки ошибок от основной логики маршрутов*.

## Использование пользовательских классов ошибок
Создание пользовательских классов ошибок позволяет делать сообщения более специфичными и легко различать типы ошибок:

```js
class NotFoundError extends Error {
  constructor(message) {
    super(message);
    this.name = 'NotFoundError';
    this.statusCode = 404;
  }
}

// Использование пользовательской ошибки
app.get('/user/:id', async (req, res, next) => {
  try {
    const user = await getUserById(req.params.id);
    if (!user) {
      throw new NotFoundError('User not found');
    }
    res.json(user);
  } catch (error) {
    next(error);
  }
});
```
В зависимости от нужд приложения можно создавать сколь угодно много классов ошибок, например, для валидации `ValidationError`, аутентификации `AuthError`, таймаутов `TimeoutError` и т.д.

Это помогает централизованно обрабатывать разные типы ошибок в одном месте, предоставляя соответствующие HTTP-коды и сообщения.
Затем эти ошибки можно централизованно отлавливать
```js
try {

} catch (err) {
  if (err instanceof NotFoundError) { /* ... */ }
  else if (err instanceof ValidationError) { /* ... */ }
  else if (err instanceof AuthError { /* .. */}
}
```

## Обработка необработанных ошибок и необработанных отклоненных промисов
Для обработки ошибок, которые не были перехвачены в коде, можно использовать *глобальные обработчики* `uncaughtException` и `unhandledRejection`. В них можно завершить приложение, логировать ошибки и так далее.
```js
process.on('uncaughtException', (error) => {
  console.error('Uncaught exception:', error);
  // Обычно на этом этапе приложение завершает работу, чтобы избежать непредсказуемых состояний
  process.exit(1);
});

process.on('unhandledRejection', (reason, promise) => {
  console.error('НUnhandled promise rejection:', reason);
  // Здесь также можно выполнить какую-то логику, например, логирование, алерты и т.д.
});
```
Важно помнить, что после `uncaughtException` лучше завершить работу приложения, чтобы избежать неконсистентного состояния.

## Логирование ошибок
Логирование играет важную роль в обработке ошибок, и его следует реализовать с помощью специализированных библиотек, таких как winston или pino:
```js
const winston = require('winston');

const logger = winston.createLogger({
  level: 'error',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: 'error.log' })
  ]
});

// Пример использования логгера
app.use((err, req, res, next) => {
  logger.error(err.message);
  res.status(500).json({ message: 'Ошибка сервера' });
});
```
Логирование ошибок помогает отслеживать проблемы в продакшене.
Также можно настроить отправку уведомлений, если возникла критическая ошибка.

## Валидация входящих данных

Используйте библиотеки, такие как Joi или express-validator, чтобы валидировать данные до выполнения бизнес-логики:

```js
const { body, validationResult } = require('express-validator');

app.post('/user', [
  body('email').isEmail(),
  body('password').isLength({ min: 6 })
], (req, res, next) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({ errors: errors.array() });
  }
  next();
});
```
Это позволяет избежать выполнения лишнего кода, если входные данные невалидны.
Позволяет централизованно обрабатывать ошибки валидации.
Заключение
Наилучшие практики обработки ошибок в Node.js включают использование try-catch, централизованную обработку ошибок с middleware, создание пользовательских ошибок, логирование и валидацию входящих данных. Эти подходы помогают сделать код более надежным, улучшить обслуживание и быстрее находить и исправлять проблемы.
