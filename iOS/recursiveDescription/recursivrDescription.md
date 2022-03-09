
```
class Vertex<T> {
    let value: T
    var visited:Bool = false
    var adjacencyList: [Vertex] = []
    
    init(value : T) {
        self.value = value
    }
}

func dfs(from vertex:Vertex<String>) {
    vertex.visited = true
    print(vertex.value)
    for adj in vertex.adjacencyList {
        if !adj.visited {
            dfs(from:adj)
        }
    }
}
```


```
let v1 = Vertex<String>(value: "A")
let v2 = Vertex<String>(value: "B")
let v3 = Vertex<String>(value: "C")
v1.adjacencyList.append(v2)
v1.adjacencyList.append(v3)

let v4 = Vertex<String>(value: "D")
let v5 = Vertex<String>(value: "E")
v2.adjacencyList.append(v4)
v2.adjacencyList.append(v5)
let v6 = Vertex<String>(value: "F")
v3.adjacencyList.append(v6)

dfs(from: v1 )

```


```
A
B
D
E
C
F
```

```
import UIKit

extension UIView {
    /// Our version of recursiveDescription that prints out a views heirarchy
    func recursiveDescription() -> String {
        return recursiveDescriptionHelper(with: "")
    }
    
    /// Private helper to handle formatting of the heirarchy output
    private func recursiveDescriptionHelper(with prefix: String) -> String {
        guard !subviews.isEmpty else {
            return description
        }
        
        var text: String = description
        let nextPrefix: String = prefix + "|"
        for view in subviews {
            text.append("\n")
            text.append(nextPrefix)
            text.append(view.recursiveDescriptionHelper(with: nextPrefix))
        }
        
        return text
    }
}
```

```
// MARK: Example
let scrollView = UIScrollView()
let label1 = UILabel()
let label2 = UILabel()
scrollView.addSubview(label1)
scrollView.addSubview(label2)

let tableView = UITableView()
let imageView = UIImageView()
tableView.addSubview(imageView)

let view = UIView()
view.addSubview(scrollView)
view.addSubview(tableView)

print(view.recursiveDescription())
```

```
<UIView: 0x7fa3dba2c890; frame = (0 0; 0 0); layer = <CALayer: 0x7fa3dba2c150>>
|<UIScrollView: 0x7fa3da8a8c00; frame = (0 0; 0 0); clipsToBounds = YES; gestureRecognizers = <NSArray: 0x7fa3d9d6b4b0>; layer = <CALayer: 0x7fa3d9d8ae60>; contentOffset: {0, 0}; contentSize: {0, 0}; adjustedContentInset: {0, 0, 0, 0}>
||<UILabel: 0x7fa3d9e4cfc0; frame = (0 0; 0 0); userInteractionEnabled = NO; layer = <_UILabelLayer: 0x7fa3d9e4ddc0>>
||<UILabel: 0x7fa3d9c8cf60; frame = (0 0; 0 0); userInteractionEnabled = NO; layer = <_UILabelLayer: 0x7fa3d9c5cbc0>>
|<UITableView: 0x7fa3da848800; frame = (0 0; 0 0); clipsToBounds = YES; gestureRecognizers = <NSArray: 0x7fa3dba19da0>; layer = <CALayer: 0x7fa3dba13290>; contentOffset: {0, 0}; contentSize: {0, 0}; adjustedContentInset: {0, 0, 0, 0}; dataSource: (null)>
||<UIImageView: 0x7fa3d9c7c260; frame = (0 0; 0 0); userInteractionEnabled = NO; layer = <CALayer: 0x7fa3d9c94260>>

```
