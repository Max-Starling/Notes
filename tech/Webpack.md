# webpack

**webpack** — [*сборщик модулей*](../Bundling.md#бандл-и-граф-зависимостей): он *строит граф зависимостей*, *начиная* с *точек входа*, и *объединяет* все *нужные модули* в *один* или *несколько бандлов* ([документация webpack](https://webpack.js.org/concepts/)).

*Общие понятия сборки* — транспиляция, встряска дерева, чанки, минификация, карты кода — разобраны в заметке про [*сборку проекта*](../Bundling.md).

- [Точка входа и выход](#точка-входа-и-выход)
- [Загрузчики](#загрузчики)
- [Плагины](#плагины)
- [Режимы сборки](#режимы-сборки)
- [Сервер для разработки](#сервер-для-разработки)
- [Разбиение на чанки](#разбиение-на-чанки)
- [Карты кода](#карты-кода)
- [Пример конфигурации](#пример-конфигурации)

## Точка входа и выход

**Точкой входа** (англ. `entry point`) называют *модуль*, с которого *webpack начинает строить граф зависимостей*. *Настройка* `output` *указывает*, *куда складывать готовые бандлы* и *как их называть* ([документация webpack](https://webpack.js.org/concepts/)).

```js
/* webpack.config.js */
module.exports = {
  entry: './src/index.js',
  output: {
    path: __dirname + '/dist',
    filename: '[name].[contenthash].js',
  },
};
```

*Подстановка* `[contenthash]` *добавляет* в *имя файла хэш его содержимого* — это *нужно* для [*кэширования чанков*](../Bundling.md#разбиение-на-чанки).

## Загрузчики

*Сам webpack понимает* только *JavaScript* и `JSON`. **Загрузчики** (англ. `loaders`) *позволяют ему обрабатывать файлы других типов* и *превращать их* в *модули*, которые *попадают в граф зависимостей* ([документация webpack](https://webpack.js.org/concepts/)).

```js
/* webpack.config.js */
module.exports = {
  module: {
    rules: [
      { test: /\.jsx?$/, exclude: /node_modules/, use: 'babel-loader' },
      { test: /\.css$/, use: ['style-loader', 'css-loader'] },
    ],
  },
};
```

*Правило читается так*: *файлы*, *имена которых подходят* под *регулярное выражение* `test`, *обрабатываются загрузчиками* из `use`. *Если загрузчиков несколько*, они *применяются справа налево* ([документация webpack](https://webpack.js.org/concepts/loaders/)): в *примере выше* `css-loader` *превращает* `CSS` в *модуль*, а `style-loader` *подключает* его *на страницу*.

## Плагины

**Плагины** (англ. `plugins`) *решают задачи*, которые *не сводятся* к *преобразованию отдельных файлов*: *оптимизация бандла*, *работа с ресурсами*, *подстановка переменных окружения* ([документация webpack](https://webpack.js.org/concepts/)).

**DefinePlugin** позволяет создавать глобальные константы, которые могут быть заданы во время компиляции. Это особенно полезно, когда есть разные версии сборок (builds): например, `production` и `development`.
```sh
NODE_ENV=production npm start
```
```js
new webpack.DefinePlugin({
  'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV),
});
/* после этого переменную можно использовать в JavaScript */
```

## Режимы сборки

*Настройка* `mode` *принимает три значения* — `development`, `production` и `none` — и *включает встроенные оптимизации*, *подходящие* для *каждого случая* ([документация webpack](https://webpack.js.org/configuration/mode/)).

## Сервер для разработки

`webpack-dev-server` *даёт простой веб-сервер* и *живую перезагрузку страницы* ([документация webpack](https://webpack.js.org/guides/development/)).

```js
/* webpack.config.js */
module.exports = {
  devServer: {
    static: './dist',
  },
};
```

## Разбиение на чанки

За [*разбиение на чанки*](../Bundling.md#разбиение-на-чанки) *отвечает* `SplitChunksPlugin`. *Настраивается* он через `optimization.splitChunks`, а *в документации отмечено*, что *значений по умолчанию хватает большинству проектов* ([документация webpack](https://webpack.js.org/plugins/split-chunks-plugin/)).

```js
/* webpack.config.js */
module.exports = {
  optimization: {
    splitChunks: { chunks: 'all' },
  },
};
```

## Карты кода

*Настройка* `devtool` *определяет*, *создавать* ли [*карты кода*](../Bundling.md#карты-кода-source-maps) и *какого вида* ([документация webpack](https://webpack.js.org/configuration/devtool/)).

## Пример конфигурации

```js
/* webpack.config.js */
const path = require('path');

module.exports = {
  mode: 'production',
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[contenthash].js',
    clean: true,
  },
  module: {
    rules: [
      { test: /\.jsx?$/, exclude: /node_modules/, use: 'babel-loader' },
      { test: /\.css$/, use: ['style-loader', 'css-loader'] },
    ],
  },
  optimization: {
    splitChunks: { chunks: 'all' },
  },
  devtool: 'source-map',
};
```

*Настройка* `output.clean` *очищает папку сборки* перед *каждой сборкой* ([документация webpack](https://webpack.js.org/configuration/output/)).
