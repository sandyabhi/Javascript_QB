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
    // Syntax: arr.reduce(function(total, currentValue, currentIndex, arr), initialValue)

    Array.prototype.myReduce = function(cb , initialValue) {
        let acc = initialValue;
        for(let i = 0; i < this.length; i++){
            acc = acc ? cb(acc, this[i], i, this) : this[i];
        }
        
        return acc;
    }
        
    console.log(arr.myReduce((acc, cur) => acc+cur)); // 15
    console.log(arr.myReduce((acc, cur) => acc+cur , 2));  // 17
```

### Polyfill for the call function

___ The call() method takes arguments separately. fn.call(obj, "arg1", "arg2", "arg3") ___

```javascript
    const person1 = {name: "Sandeep"};
    const person2 = {name: "Abhishek"};
    
    function printSection(section){
        console.log(`${this.name} is in class 12-${section}.`);
    }
    
    Function.prototype.myCall = function(obj, ...args){
        if(typeof this !== 'function'){
            throw new Error("Not Callable");
        }
        
        obj.fn = this;
        
        obj.fn(args);
    } 
    
    printSection.myCall(person1, 'C');  // Sandeep is in class 12-C. 
    printSection.myCall(person2, 'A');  // Abhishek is in class 12-A.
```


### Polyfill for the apply function

___ The apply() method takes arguments as an array. fn.call(obj, ["arg1", "arg2", "arg3"]) ___

```javascript
    const person1 = {name: "Sandeep"};
    const person2 = {name: "Abhishek"};
    
    function printStudent(section, background){
        console.log(`${this.name} is in class 12-${section}, background = ${background}.`);
    }
    
    Function.prototype.myApply = function(obj, args){
        if(typeof this !== 'function'){
            throw new Error("Not Callable");
        }
        
        if (!Array.isArray(args)) {
            throw new Error("TypeError: CreateListFromArrayLike called on non-object");
        }

        
        obj.fn = this;
        
        obj.fn(...args);
    } 
    
    printStudent.myApply(person1, ['C', "Science"]);  // Sandeep is in class 12-C, background = Science.
    printStudent.myApply(person2, ['A', "Arts"]);  // Sandeep is in class 12-C, background = Science.
```

### Polyfill for the bind function

```javascript
    const person1 = {name: "Sandeep"};
    const person2 = {name: "Abhishek"};
    
    function printStudent(section, background){
        console.log(`${this.name} is in class 12-${section}, background = ${background}.`);
    }
    
    Function.prototype.myBind = function(obj, ...args){
        if(typeof this !== 'function'){
            throw new Error("Not Callable");
        }
        
        obj.fn = this;
        
        return function(...newArgs){
            return obj.fn(...args, ...newArgs);
        }
    } 
    
    const func = printStudent.myBind(person1, 'C');
    func("Science"); // Sandeep is in class 12-C, background = Science. 
    
    const func2 = printStudent.myBind(person2, 'A', "Arts");
    func2(); // Abhishek is in class 12-A, background = Arts.
```


### Polyfill for the Promise

```javascript
    function myPromise(executor){
        let onResolve, onReject, isFullfilled = false, isRejected = false, isCalled = false, value;
        
        function resolve(val){
            isFullfilled = true;
            value = val;
            
            if(typeof onResolve === 'function'){
                onResolve(val);
                isCalled = true;
            };
        }
        
        function reject(val){
            isRejected = true;
            value = val;
            
            if(typeof onResolve === 'function'){
                onReject(val);
                isCalled = true;
            };
        }
        
        this.then = function(cb){
            onResolve = cb;
            
            if(isFullfilled && !isCalled){
                isCalled = true;
                onResolve(value)
            }
            
            return this;
        }
        
        this.catch = function(cb){
            onReject = cb;
            
            if(isRejected && !isCalled){
                isCalled = true;
                onReject(value)
            }
            
            return this;
        }
        
        try{
            executor(resolve, reject)   
        }catch(error){
            reject(error)
        }
    }
    
    const promise = new myPromise((resolve, reject) => {
        resolve(5)
    })
    
    promise.then((res) => {
        console.log(res);
    })
    .catch((err) => console.log(err));
```

### Polyfill for the Promise.all()

```javascript
  function func1(){
        return new Promise((resolve, reject) => {
            resolve("Hello");
        })
    }
    function func2(){
        return new Promise((resolve, reject) => {
            resolve("World");
        })
    }
    function func3(val){
        return new Promise((resolve, reject) => {
            resolve(val);
        })
    }
    
    Promise.promiseAll = (promises) => {
        return new Promise((resolve, reject) => {
            const result = [];
            
            if(!promises.length){
                resolve(result);
                return;
            }
            
            let pending = promises.length;
            
            promises.forEach((promise, idx) => {
                Promise.resolve(promise).then((res) => {
                    result[idx] = res;
                    
                    pending--;
                    
                    if(pending === 0){
                        resolve(result);
                    }
                }, reject)
            })
        })
    } 
    
    Promise.promiseAll([
        func1(), func2(), func3("Sandeep")
    ]).then((res) => console.log(res))
```



### Polyfill for the Promise.all()

```javascript
     let arr = [1, 2, [3, [4, 5]]];
    // console.log(arr.flat(2)) // [ 1, 2, 3, 4, 5 ]
    
    Array.prototype.myFlat = function(depth){
        let res = [];
        if(!Array.isArray(this)){
            throw new Error(`${this}.myFlat is not a function`);
        }
        
        this.forEach((el) => {
            if(Array.isArray(el) && depth > 0){
                res.push(...el.myFlat(depth-1));
            }else{
                res.push(el);
            }
        })
        
        return res;
    }
    
    console.log(arr.myFlat(2)); // [ 1, 2, 3, 4, 5 ]
```



