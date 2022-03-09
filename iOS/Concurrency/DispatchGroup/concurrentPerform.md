```


var concurrentDispatchQueue = DispatchQueue(label:"blah", attributes: .concurrent)
```

```
func DispatchGroupConcurrentPerformExample() {
        let downloadGroup = DispatchGroup()
        let _ = DispatchQueue.global(qos: .userInitiated)
        DispatchQueue.concurrentPerform(iterations: 5) { index in
            downloadGroup.enter()
            testConcurrentQueue { result in
                switch result {
                case .success():
                    print("finish")
                case .failure(let error):
                    print(error)
                }
                downloadGroup.leave()
            }
        }
        
        downloadGroup.notify(queue: DispatchQueue.main) { [weak self] in
            self?.testSerialQueue()
        }
    }
```


```
 func testConcurrentQueue(completion : @escaping (Result<Void, Error>) -> Void) {
        sleep(3)
        concurrentDispatchQueue.async {
            for i in 0..<10 {
                print(i)
            }
            
            sleep(2)
            completion(.success(Void()))
        }
    }
```

```
func testSerialQueue() {
        DispatchQueue.global(qos: .background).sync {
//            sleep(2)
            for i in 0..<10 {
                print(i)
            }
        }
        
        print("testSerialQueue")
        
    }
```
