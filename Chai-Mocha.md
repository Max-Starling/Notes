## Get started

* Install packages  
```npm
npm install --save-dev chai
npm install --save-dev mocha
```
* Set up the test script in the package.json
  * --timeout <default it block timeout in ms> (number, default: 2000)
  * --recursive <path regexp to test files> (string, dafault: './test/*.js')
```JSON
"scripts": {
  "test": "mocha --timeout 10000 --recursive './test/**/*.spec.js'"
}
```
* Create `your-filename-here.spec.js` file in the test folder
* Start using chai сhai `expect` or chai `should`
```js
const expect = require('chai').expect;
// or
const chai = require('chai');
chai.should();
```

### "expect" and "should" syntax
```js
const getArticles = () => [/* ... */];

expect(getArticles()).to.be.an('array');

getArticles().should.be.an('array');
```
## Basic functions

```js
expect(something).to.equal('value'); // something === 'value'

expect(something).to.be.a('type'); // typeof something === 'value, but
expect([]).to.be.an('array'); // success

expect(object).to.have.property('property'); // Object.keys(object).includes('property');

const keys = [/* ... */];
expect(object).to.have.all.keys(...keys); // keys.every(key => Object.keys(object).includes(key));
expect(object).to.have.any.keys(...keys); // keys.some(key => Object.keys(object).includes(key));
```


## Basic structure for unit tests
Simple module:
```js
  module.exports = {
    isInteger: number => number === parseInt(number, 10),
    getRandomNaturalNumber: (max = 1000) => Math.floor(Math.random() * (max - 1)) + 1,
  };
```
Tests for the module:
```js
const expect = require('chai').expect;
const numberFuncs = require('/*...*/');

describe('numberFuncs', () => {
  describe('isInteger', () => {
    it('should always return a boolean', () => {
      expect(numberFuncs.isInteger('article')).to.be.a('boolean');
    });

    it('should return true', () => {
      expect(numberFuncs.isInteger(5)).to.equal(true);
    });

    it('should return false', () => {
      expect(numberFuncs.isInteger(7.5)).to.equal(false);
    });
  });

  describe('getRandomNaturalNumber', () => { 
    it('should return a natural number', () => {
      const natural = numberFuncs.getRandomNaturalNumber();
      expect(natural).to.be.an('number');
      expect(natural).to.be.above(0);
      expect(numberFuncs.isInteger(natural)).to.equal(true);
    });
  });
});
```

## Basic structure for API tests
Simple Express API:
```js
const Express = require('express');
const app = Express();

const checkCredentials = (username, password) => { /* ... */ };
const createAuthToken = (username) => { /* ... */ };

app.get('/login', async (req, res) => {
  const { username, password } = req.params;
  const user = await checkCredentials(username, password);
  if (user) {
    const token = await createAuthToken(username);
    res.status(200).send({ ...user, token });
  } else {
    res.status(400).send();
  }
});

const ARTICLES = [/* ... */];
const getAuthTokenFromHeaders = (headers) => { /* ... */ };
const checkAuthToken = (token) => { /* ... */ };

app.get('/articles', async (req, res) => {
  const authToken = getAuthTokenFromHeaders(req.headers);
  const isAuthorized = await checkAuthToken(authToken);
  if (isAuthorized)) {
    res.status(200).send(ARTICLES);
  } else {
    res.status(400).send();
  }
});
```
Tests for the API above:
```js
const axios = require('axios');
const expect = require('chai').expect;

const API = axios.create({ baseURL: 'http://localhost:3002' });
const POST = ({ endpoint, body }) => API.post(endpoint, body));
const GET = ({ endpoint, query, authToken }) => API.get(
  endpoint,
  { params: query },
  { headers: { 'Authorization': `Bearer ${authToken}` },
});

let authToken = '';

describe('POST /login', () => {
  const credentials = { username: 'admin', password: 'admin' };

  it('should return status 200 and object containing auth token', async () => {
    const response = await POST({ endpoint: '/login', body: credentials });
    expect(response).to.be.an('object');
    const { status, data } = response;
    expect(status).to.equal(200);
    expect(data).to.be.an('object');
    const { token } = data;
    expect(token).to.be.a('string');
    authToken = token;
  });
});

describe('GET /articles', () => {
  it('should return an array of objects containing title, text and username', async () => {
    const response = await GET({ endpoint: '/articles', authToken });
    expect(response).to.be.an('object');
    const { status, data } = response;
    expect(status).to.equal(200);
    expect(data).to.be.an('array');
    const article = data[0];
    expect(article).to.be.an('object');
    expect(article).to.have.all.keys('title', 'text', 'username');
  });
});


```
try - catch
before, beforeAll, after, afterAll
this (timeouts, skip)
