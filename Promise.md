# promise

语法：

```javascript
new Promise( (resolve, reject) => {
    //这里放异步代码
    //成功的时候
    resolve(data) //这里的data会传入then中函数里面
    //失败的时候
    reject('err')
}).then( (data) => {
    //这里放处理代码
} ).catch( (err) => {
    console.log(err);
} )
```

Promise需要传入一个函数，这个函数需要有两个参数，resolve和reject 而他们两个也都是函数

遇到resolve，就会执行下面then里面的函数

例子： 每隔1s打印一行文字

```javascript
new Promise( (resolve, reject) => {
    setTimeout( () => {
        resolve('Hello VueJs')
    }, 1000)
}).then( (data) => {
    console.log(data);
    return new Promise((resolve, reject) => {
        setTimeout( () => {
            resolve('Hello world')
        }, 1000)
    })
}).then( (data) => {
    console.log(data);
    return new Promise((resolve, reject) => {
        setTimeout( () => {
            resolve('To every coder')
        }, 1000)
    })
}).then( (data) => {
    console.log(data);
})
```

## promise 的三种状态

![Promise的三种状态](../img/Promise1.png)

补充一个，我们也可以把err写入then里面的函数中

```javascript
new Promise(((resolve, reject) => {
    reject('err message')
})).then(data => {

},err => {
    console.log(err);
})
```

```javascript
new Promise(resolve => {
    setTimeout(() => {
        resolve('1')
    }, 1000)
}).then(res => {
    return new Promise(resolve => {
        resolve(res +  '2')
    })
}).then(res => {
    console.log(res + '3')
})
```

我们可以直接用 ```Promise.resolve(res)``` 来简化上面的代码

```javascript
new Promise(resolve => {
    setTimeout(() => {
        resolve('1')
    }, 1000)
}).then(res => {
    return Promise.resolve(res + 2)
}).then(res => {
    console.log(res + 3)
})
```

 我们继续简写，直接将 ```Promise.resolve``` 去掉

```javascript
new Promise(resolve => {
    setTimeout(() => {
        resolve('1')
    }, 1000)
}).then(res => {
    return res + 2
}).then(res => {
    console.log(res + 3)
})
```


## Promise.all

当多个promise都是resolve状态时， 我们就可以用 ```promise.all```

```javascript
Promise.all([new Promise(resolve => {
    setTimeout(() => {
        resolve('result1')
    }, 1000)
}), new Promise(resolve => {
    resolve('result2')
})]).then(result => {
    //这里的result是一个数组，它存着两个promise的resolve传回来的值
    console.log(result[0])
    console.log(result[1])
})
```




















