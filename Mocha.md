[<< Cypress](Cypress) ‧ [Essentials >>](Essentials)

https://mochajs.org/#getting-started
### Simple Functions
```js
describe('Array', function () {
  describe('#indexOf()', function () {
    it('should return -1 when the value is not present', function () {
      assert.equal([1, 2, 3].indexOf(4), -1);
    });
  });
});
```

### Assert w/ Tolerance


### Timing a Test
```js
const handleTiming = function(time){

   before(function(){
       time.start = Date.now();
   });

   after(function(){
      const totalTimeMillis = Date.now() - time.start;
    });

}
describe('time this bitch', function(){
  let time  = {start: null};
  handleTiming(time);
  
  // ...

});

describe('time this bitch', function(){
  let time  = {start: null};
  handleTiming(time);
  
  // ...

});

```
`--diff`
`--slow [x]`
`--reporter list`
## Chai

https://www.chaijs.com/api/bdd/

```js
```
