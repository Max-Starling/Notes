# PostCSS Setup
## NPM
```
npm install postcss-cli stylelint stylelint-config-standard postcss-cssnext precss postcss-cssnext cssnano --save-dev
npm install stylelint -g
```
## package.json
```json
{
  "scripts": {
    "styles": "postcss input.postcss --config postcss.config.js --output output.min.css --watch"
  },
}
```
## postcss.config.js
```js
module.exports = () => ({
  plugins: {
    'stylelint': {},
    'precss': {},
    'postcss-cssnext': {},
    'cssnano': {
      'autoprefixer': false
    }
  }
});
```
## .stylelintrc
```json
{
  "extends": "stylelint-config-standard",
  "rules": {
    "selector-max-class": 2
  }
}
```
## VSCode Settings (settings.json)
```json
{
  "emmet.includeLanguages":{
    "postcss": "css"
  },
  "emmet.syntaxProfiles": {
    "postcss": "css"
  },
  "css.validate": false,
  "less.validate": false,
  "scss.validate": false,
  "postcss.validate": false,
  "files.associations": {
    "*.postcss": "postcss",
  },
  "[postcss]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  }
}
```

## VSCode extensions
* postcss-sugarss-language
* Prettier - Code formatter (format document with Alt + Shift + F)
* stylelint

## PostCSS Setup (Webpack)
```js
{
  test: /\.css$/,
  use: [
    'style-loader',
    'css-loader',
     {
       loader: 'postcss-loader',
       options: {
         ident: 'postcss',
         plugins: [
           require('stylelint')(),
           /* ... */,
         ],
       },
     },
  ],
}
{
  loader: 'postcss-loader',
  options: {
    ident: 'postcss',
    plugins: [
      require('autoprefixer')(),
      require('stylelint')(),
      /* ... */
    ]
  }
}
```

## CSS Modules Setup (Webpack)
```js
{
  test: /\.css$/,
  use: [
    'style-loader',
    {
      loader: 'css-loader',
      options: {
        modules: true,
        importLoaders: 1,
        localIdentName: '[name]__[local]___[hash:base64:4]',
      },
     },
     {
       loader: 'postcss-loader',
       options: { /* ... */ },
     },
  ],
}
```
