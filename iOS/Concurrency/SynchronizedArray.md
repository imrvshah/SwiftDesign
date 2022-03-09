```
public class SynchronizedArray<T> {
    private var array : [T] = []
    private let accessQueue = DispatchQueue(label: "com.rs.hh", attributes: .concurrent)
    
    public func append(newElement: T) {
        self.accessQueue.async(flags: .barrier) {
            self.array.append(newElement)
        }
    }
    
    public func removeAtindex(index: Int) {
        if index > self.array.count || index < self.array.count {
            return
        }
        self.accessQueue.async(flags: .barrier) {
            self.array.remove(at: index)
        }
    }
    
    public func count() -> Int {
        var count = 0
        self.accessQueue.sync {
            count = self.array.count
        }
        return count
    }
    
    public func firstElement() -> T? {
        var element: T?
        self.accessQueue.async {
            element = self.array.removeFirst()
        }
        
        return element
    }
    
    
}
```
