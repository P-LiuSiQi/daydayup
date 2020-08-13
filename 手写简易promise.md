## 2020-08-13
### 简易版promise
```
  const PENDING = 'pending'
  const RESOLVED = 'resolved'
  const REJECTED = 'rejected'

  function MyPromise(fn) {
    const that = this
    that.state = PENDING
    that.value = null
    this.resolvedCallbacks = []
    this.rejectedCallbacks = []

    function resolve(value) {
      if (that.state === PENDING) {
        that.state = RESOLVED
        that.value = value
        that.resolvedCallbacks.map(cb => cb(that.value))
      }
    }

    function reject(value) {
      if (that.state === PENDING) {
        that.state = REJECTED
        that.value = value
        that.rejectedCallbacks.map(cb => cb(that.value))
      }
    }

    try {
      fn(resolve, reject)
    } catch(e) {
      reject(e)
    }
  }

  MyPromise.prototype.then = function(onFulfilled, onRejected) {
    const that = this
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v
    onRejected = typeof onRejected === 'function' ? onRejected : r => { throw r }
    if (that.state === PENDING) {
      that.resolvedCallbacks.push(onFulfilled)
      that.rejectedCallbacks.push(onRejected)
    }
    if (that.state === RESOLVED) {
      onFulfilled(that.value)
    }
    if (that.state === REJECTED) {
      onRejected(that.value)
    }
  }
```

### 20行代码实现promise
```
  function Promise(excutor) {
    this.callbacks = []

    function resolve(value) {
      setTimeout(() => {
        this.data = value
        this.callbacks.forEach(callback => callback(value))
      })
    }
    excutor(resolve.bind(this))
  }

  Promise.prototype.then = function (onResolved) {
    return new Promise(resolve => {
      this.callbacks.push(() => {
        var result = onResolved(this.data)
        if (result instanceof Promise) {
          result.then(resolve)
        } else {
          resolve(result)
        }
      })
    })
  }
```