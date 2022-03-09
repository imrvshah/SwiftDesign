```
    var concurrentDispatchQueue = DispatchQueue(label:"blah", attributes: .concurrent)

```
```
func cancelDispatchBlock() {
        let downloadGroup = DispatchGroup()
        var blocks = [DispatchWorkItem]()
        
        for _ in 0..<5 {
            downloadGroup.enter()
            let block = DispatchWorkItem(flags: .inheritQoS) { [weak self] in
                self?.testConcurrentQueue { result in
                    switch result {
                    case .success():
                        print("finish")
                    case .failure(let error):
                        print(error)
                    }
                    downloadGroup.leave()
                }
            }
            blocks.append(block)
            DispatchQueue.main.async(execute: block)
            
        }
        
        for i in 0..<3 {
            let cancel = Bool.random()
            if cancel {
                blocks[i].cancel()
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
