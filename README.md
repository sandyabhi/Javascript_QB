## Polyfill

### Polyfill for map function

```javascript
  let arr = [1, 2, 3 ,4, 5];
  arr.map((e,i) => e*i);

  Array.prototype.myMap = function (cb) {
    let res = [];
    for(let i=0; i<this.length; i++){
      // map(element, index, array)
      res.push(cb(this[i], i, this));
    }
  
    return res;
  }
  
  
  let newArr = arr.myMap((e,i) =>  e*i);
  console.log(newArr);   // [ 0, 2, 6, 12, 20 ]
```



