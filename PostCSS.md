# PostCSS Setup
```
npm install postcss-cli stylelint stylelint-config-standard postcss-cssnext precss postcss-cssnext cssnano --save-dev
npm install stylelint -g
```
package.json
```json
{
  "scripts": {
    "styles": "postcss input.postcss --config postcss.config.js --output output.min.css --watch"
  },
}
```
postcss.config.js
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
.stylelintrc
```json
{
  "extends": "stylelint-config-standard",
  "rules": {
    "selector-max-class": 2
  }
}
```
VS Code settings: settings.json
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
Webpack
```js
{
  loader: 'postcss-loader',
  options: {
    ident: 'postcss',
    plugins: [
      require('stylelint')(),
      /* ... */,
    ]
  }
}
```

VS Code extensions
* postcss-sugarss-language
* Prettier - Code formatter (format document with Alt + Shift + F)
* stylelint
