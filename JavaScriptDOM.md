
# Обработка событий пользователя

## Обработка данных формы
```html
<form name="valform" onsubmit="handleSubmit(event)">
  Name: <input type="text" name="name">
  <br/>
  Card Type:
  <select name="cardType">
    <option value="visa">Visa</option>
    <option value="mastercard">MasterCard</option>
    <option value="discover">Discover</option>
    <option value="amex">Amex</option>
    <option value="diners">Diners Club</option>
  </select>
  <br/>
  <button type="submit">Submit</button>
</form>
```
```js
function handleSubmit(e) {
  e.preventDefault();
  const formData = new FormData(e.target);
  const values = Object.fromEntries(formData);
  console.log(values); // { name: '...', cardType: '...' }
}
```
![image](https://user-images.githubusercontent.com/22237384/155226176-9895af8e-86c9-4ae9-a19e-7a4a5cdca933.png)
![image](https://user-images.githubusercontent.com/22237384/155226480-9a4ef714-8a1a-4051-90e7-0e8ce224588b.png)


