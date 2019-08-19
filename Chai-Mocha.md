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
expect([]).to.be.an('array');

expect(object).to.have.property('property'); // Object.keys(something).includes('property')
```


## Basic structure for unit tests
```js
// imported module to test
const numberFuncs = {
  isInteger: number => number === parseInt(number, 10),
  getRandomNaturalNumber: (max = 1000) => Math.floor(Math.random() * (max - 1)) + 1,
}

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

before, beforeAll, after, afterAll
this (timeouts, skip)
