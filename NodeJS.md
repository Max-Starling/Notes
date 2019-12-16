
## Работа с файлами

### Удаление файла

```js
const fs = require('fs')

const removeFile = (filePath) => new Promise((resolve, reject) => {
  fs.unlink(path, err => err ? reject(err) : resolve());
});
```
