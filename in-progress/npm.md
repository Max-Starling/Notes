- [Передача переменных](#передача-переменных)

## Передача переменных

### Linux, Mac
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
### Windows
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
### Кроссплатформенное решение
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
