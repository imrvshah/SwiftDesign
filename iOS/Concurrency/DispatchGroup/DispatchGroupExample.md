```
func DispatchGroupExample() {
        let downloadGroup = DispatchGroup()
        for _ in 0..<5 {
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
            for i in 0..<10 {
                print(i)
            }
        }
        
        print("testSerialQueue")
        
    }
```
