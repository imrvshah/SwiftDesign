```
    func asyncPrintWithoutSemaphore(_ queue: DispatchQueue, _ symbol: String) {
        queue.async {
            for i in 0...10 {
                print(symbol, i)
            }
        }
    }
    
    func asyncPrintWithSemaphore(_ queue: DispatchQueue, _ symbol: String) {
        queue.async { [weak self] in
            print("\(symbol) waiting")
            self?.semaphore.wait()
            for i in 0...10 {
                print(symbol, i)
            }
            print("\(symbol) signal")
            self?.semaphore.signal()
        }
    }
    
    
```

```
 var controlSemaPhoreBuffer = DispatchSemaphore(value: 1) // controls buffer
    var controlSemaPhoreUnderFlow = DispatchSemaphore(value: 0) // controls buffer
    var controlSemaPhoreOverFlow = DispatchSemaphore(value: 100) // controls buffer
```
```
    func producer(_ value: Int) {
        self.controlSemaPhoreOverFlow.wait()
        self.controlSemaPhoreBuffer.wait()
        array.append(value)
        self.controlSemaPhoreBuffer.signal()
        self.controlSemaPhoreUnderFlow.signal()
    }
    
    func consumer() -> Int {
        self.controlSemaPhoreUnderFlow.wait()
        self.controlSemaPhoreBuffer.wait()
        let x = array.removeLast()
        self.controlSemaPhoreBuffer.signal()
        self.controlSemaPhoreOverFlow.signal()
        return x
    }
```


```
        DispatchQueue.global().async {
            DispatchQueue.concurrentPerform(iterations: 150) { index in
                // Do something computationally expensive here
    //            producer(index)
                print(self.consumer())
            }
        }

        
        for i in 1...5 {
            producer(i)
        }
```
