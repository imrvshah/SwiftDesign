```
var concurrentDispatchQueue = DispatchQueue(label:"blah", attributes: .concurrent)
```

```
func DispatchGroupExampleWithOrder() {
        let downloadGroup = DispatchGroup()
        for _ in 0..<5 {
            downloadGroup.enter()
            sendOrder {
                print("succ")
//                downloadGroup.leave()
            } failure: {
                print("fail")
//                downloadGroup.leave()
            }

        }
        
        concurrentDispatchQueue.async {
        downloadGroup.wait()
        }
        
        downloadGroup.notify(queue: DispatchQueue.main) { [weak self] in
            self?.testSerialQueue()
        }
    }
```

```
// Simulates sending order request to RH backend
    func sendOrder(success: @escaping () -> Void, failure: @escaping () -> Void) {
        print("sendingOrder:")
 
        DispatchQueue.main.asyncAfter(deadline: DispatchTime.now() + 0.1) {
            let rand = Int.random(in: 0...1)
            if rand == 0 {
                print("success: ")
                success()
            } else {
                print("failure:")
                failure()
            }
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
