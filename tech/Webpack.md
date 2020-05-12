
## Плагины

**DefinePlugin** позвоняет созначать глобальные константы, которые могут быть заданы во время компиляции. Это особенно полезно, когда есть разные версии сборок (builds): например, `production` и `development`.
```sh
npm start NODE_ENV=production
```
```js
new webpack.DefinePlugin({
  'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV),
});
/* после этого переменную можно использовать в JavaScript */
```
