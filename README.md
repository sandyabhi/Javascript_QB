## Polyfill

### Polyfill for the map function

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


### Polyfill for the filter function

```javascript
  let arr = [1, 2, 3, 4, 5, 6];
  
  Array.prototype.myFilter = function(cb) {
    let res = [];
    for(let i = 0; i < this.length; i++){
      if(cb(this[i], i, this)){
        res.push(this[i]);
      }
    }
    
    return res;
  }
  
  let ans = arr.myFilter((el, i, arr) => el <= 4);
  
  console.log(ans); // [1, 2, 3, 4]
```

### Polyfill for the reduce function

```javascript
    let arr = [1, 2, 3, 4, 5];
    // let ans = arr.reduce((acc, cur) => {
    //     return acc + cur
    // }, 0)
    
    Array.prototype.myReduce = function(cb , initialValue) {
        let acc = initialValue;
        for(let i = 0; i < this.length; i++){
            acc = acc ? cb(acc, this[i]) : this[i];
        }
        
        return acc;
    }
        
    console.log(arr.myReduce((acc, cur) => acc+cur));
    console.log(arr.myReduce((acc, cur) => acc+cur , 2));
```



