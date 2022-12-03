- [Зависимости в `package.json`](#зависимости-в-packagejson)
  - [Нормальные зависимости `dependencies`](#нормальные-зависимости-dependencies)
  - [Зависимости разработчика `devDependencies`](#зависимости-разработчика-devdependencies)
  - [Одноранговые зависимости `peerDependencies`](#одноранговые-зависимости-peerdependencies)
    - [`peerDependenciesMeta`](#peerdependenciesmeta)
 - [Другие свойства `package.json`](#другие-свойства-packagejson)  
    - [Переопределения `overrides`](#переопределения-overrides)
    - [Скрипты `scripts`](#скрипты-scripts)
      - [Передача параметров скрипту](#передача-параметров-скрипту)

# Зависимости в `package.json`

**Зависимостями** (англ. `devendencies`) в приложении выступают *сторонние пакеты* (модули), которые *декларируются* в `package.json`. Чаще всего зависимости скачиваются из [NPM Registry](https://www.npmjs.com/) 

## Нормальные зависимости `dependencies`

Свойство `dependencies` (англ. `зависимости`) содержит *зависимости приложения*, то есть пакеты (модули), которые используются в приложении.

Каждая зависимость представлена в виде пары *двух строковых значений*:
1) *Уникальное название пакета*. Например, [`"explicit"`](https://www.npmjs.com/package/explicit), [`"implicit"`](https://www.npmjs.com/package/implicit).
2) *Версия пакета*. *Версия пакета* может быть задана *явно* (то есть *тремя конкретными числами*, например, `2.3.21`) или *неявно* (например, в виде *промежутка*: `>= 2.0.0 < 3.0.0`, `>= 2.0.0 < 3.0.0`, `2.x`, `^2.3.21`)
```json
{
  "name": "my-project",
  "version": "1.0.0",
  "dependencies": {
    "explicit": "2.3.21",
    "implicit": "^2.3.21",
    "implicit-foo": "2.0.0 - 3.0.0",
    "implicit-bar": ">= 2.0.0 < 3.0.0",
    "implicit-baz": "2.x"
  }
}
```

Далее *детально разберём все возможные способы задания версии*.


## Зависимости разработчика `devDependencies`


## Одноранговые зависимости `peerDependencies`
- [`peerDependenciesMeta`](#peerdependenciesmeta)

Если в некотором пакете `foo` указаны `peer dependencies` (рус. `одноранговые зависимости`, иногда `одноуровневые` или `прямые`), то *при установке* пакета `foo` в ваше *приложение* *необходимо будет* также *установить* все *зависимости* из этого *списка*.

Таким образом, если пакет `foo` имеет следующий `package.json`
```js
{
  "name": "foo",
  "version": "2.1.1",
  "peerDependencies": {
    "bar": "^18.0.0",
    "baz": "7.1.x"
  }
} 
```
то `package.json` вашего приложения после установки `foo` и его одноранговых зависимостей с помощью `npm install` должен выглядить примерно следующим образом
```js
{
  "name": "my-awesome-app",
  "version": "1.0.0",
  "dependencies": {
    "foo": "2.1.1",
    "bar": "^18.0.0",
    "baz": "7.1.x"
  }
} 
```
а *дерево зависимостей*, например, так
```terminal
├── ...
├── foo@2.1.1
├── bar@18.0.3
└── baz@7.1.1
```
Это будет *отображено* у вас в *консоли* и в несколько другом виде *отражено* в `package-lock.json`.

Если же пакеты `bar` и `baz` *не будут установлены*, то `npm` *выдаст ошибку*.

Пример *реальной ошибки* со [*Stackoverflow*](https://stackoverflow.com/questions/35207380/how-to-install-npm-peer-dependencies-automatically):
```terminal
npm install --save angular2
temp@1.0.0 /Users/doug/Projects/dougludlow/temp
├── angular2@2.0.0-beta.3 
├── UNMET PEER DEPENDENCY es6-promise@^3.0.2
├── UNMET PEER DEPENDENCY es6-shim@^0.33.3
├── UNMET PEER DEPENDENCY reflect-metadata@0.1.2
├── UNMET PEER DEPENDENCY rxjs@5.0.0-beta.0
└── UNMET PEER DEPENDENCY zone.js@0.5.11

npm WARN angular2@2.0.0-beta.3 requires a peer of es6-promise@^3.0.2 but none was installed.
npm WARN angular2@2.0.0-beta.3 requires a peer of es6-shim@^0.33.3 but none was installed.
npm WARN angular2@2.0.0-beta.3 requires a peer of reflect-metadata@0.1.2 but none was installed.
npm WARN angular2@2.0.0-beta.3 requires a peer of rxjs@5.0.0-beta.0 but none was installed.
npm WARN angular2@2.0.0-beta.3 requires a peer of zone.js@0.5.11 but none was installed.
```

*Автоматическая установка* `peer dependencies` была *удалена* из `NPM v3`, поскольку *создавала больше проблем*, чем *решала*. Тем не менее, она была *возвращена* в версии `NPM v7` - начиная с этой версии все `peer dependencies` снова *устанавливаются автоматически*. На версиях `NPM 4, 5, 6` необходимо устанавливать каждую `peer dependency` вручную при помощи `npm install package_name@version`.

### `peerDependenciesMeta`
Для `peer dependencies` есть свойство `peerDependenciesMeta`, хранящее метаданные об одноранговых зависимостях. Эти данные позволяют объяснить NPM, как именно используется та или иная `peer dependency`.

В основном, данные `peerDependenciesMeta` используют, чтобы пометить некоторую `peer dependency` вашего пакета как *необязательную*, *опциональную* (англ. `optional`)  для установки зависимость. Это позволит *избежать предупреждений* (ошибок) о неустановленных `peer dependencies`.

Например, ниже `peer dependency` `baz` помечена как опциональная зависимость для пакета `foo`, а значит, что если при установке пакета `foo` в ваше приложение не установить зависимость `baz`, то ошибки не будет.
```js
{
  "name": "foo",
  "version": "2.1.1",
  "peerDependencies": {
    "bar": "^18.0.0",
    "baz": "7.1.x"
  },
  "peerDependenciesMeta": {
    "baz": {
      "optional": true
    }
  }
}
```


# Другие свойства `package.json`

## Переопределения `overrides`

Указание свойства `overrides` (рус. `переопреедения`) позволяет *принудительно установить* (*перезаписывать*) *версии* некоторых *пакетов* в приложении.

Возможны несколько случаев применения.

<!-- Например, необходимо, чтобы в приложении использоваласть только одна версия пакета `foo` безоговорочно, тогда
```json
{
  "overrides": {
    "foo": "1.0.0"
  }
}
```

установить во всём проекте единую версию конкретного пакета вне зависимости от того, какая 

Overrides provide a way to replace a package in your dependency tree with another version, or another package entirely. These changes can be scoped as specific or as vague as desired.

To make sure the package foo is always installed as version 1.0.0 no matter what version your dependencies rely on: -->

https://docs.npmjs.com/cli/v8/configuring-npm/package-json#overrides

## Свойство `scripts` (скрипты)

### Передача параметров скрипту
- [UNIX (Linux, Mac)](#unix-linux-mac)
- [Windows](#windows)
- [Кроссплатформенное решение](#кроссплатформенное-решение)

#### UNIX (Linux, Mac)
* *Передача строки*
```JSON
"scripts": {
  "start:prod": "NODE_ENV=production npm start"
}
```
```js
process.env.NODE_ENV // 'production'
```
* *Передача числа*
```JSON
"scripts": {
  "start:prod": "PORT=5001 npm start"
}
```
```js
parseInt(process.env.PORT, 10) // 5001
```
#### Windows
```cmd
"scripts": {
  "start:prod": "set NODE_ENV=production& npm start"
}
```
```js
process.env.NODE_ENV // 'production'
```
Если поставить пробел перед `&`
```cmd
"scripts": {
  "start:prod": "set NODE_ENV=production & npm start"
}
```
```js
process.env.NODE_ENV // 'production ' (появляется пробел на конце)
```
#### Кроссплатформенное решение
* Передача *одной переменной*.
```cmd
npm i --save-dev cross-env
```
```cmd
"scripts": {
  "start:prod": "cross-env NODE_ENV=production npm start"
}
```
* Передача *нескольких переменных*.
```cmd
"scripts": {
  "start:prod": "cross-env NODE_ENV=production PORT=5001 npm start"
}
```
