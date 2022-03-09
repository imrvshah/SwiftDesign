
### Hit Test implementation 

![Algorithm](https://github.com/imrvshah/Swift-2020/blob/master/iOS/HitTest/HitTest_algorithm.png)
```
- (UIView *)hitTest:(CGPoint)point event:(UIEvent *)event
{
    if (!self.view.userInteractionEnabled || self.view.alpha <= 0.01 || self.view.isHidden)
    {
        return nil;
    }
    if ([self.view pointInside:point withEvent:event])
    {
        for (UIView *subview in [self.view.subviews reverseObjectEnumerator])
        {
            CGPoint convertPoint = [subview convertPoint:point toView:self.view];
            UIView *hitTestView = [subview hitTest:convertPoint withEvent:event];
            if (hitTestView)
            {
                return hitTestView;
            }
        }
        return self.view;
    }
    return nil;
}
```

```
override func hitTest(_ point: CGPoint, with event: UIEvent?) -> UIView? {
        if self.isUserInteractionEnabled || self.isHidden || self.alpha <= 0.01 {
            return nil
        }
        
        if self.point(inside: point, with: event) {
            for subview in self.subviews.reversed() {
                let convertPoint = subview.convert(point, to: self)
                let hitView = subview.hitTest(convertPoint, with: event)
                if hitView != nil {
                    return hitView
                }
                
            }
            
            return self
        }
        
        return nil
    }
```

***Common use cases for overriding hitTest:withEvent:***

### Increasing view touch area

![Increasing touch area](https://github.com/imrvshah/Swift-2020/blob/master/iOS/HitTest/IncreasingTOuchArea.png)
```
- (UIView *)hitTest:(CGPoint)point event:(UIEvent *)event
{
    if (self.view.isHidden || self.view.alpha <= 0.01 || !self.view.userInteractionEnabled)
    {
        return nil;
    }
    
    CGRect touchRect = CGRectInset(self.view.bounds, -10, -10);
    if(CGRectContainsPoint(touchRect, point))
    {
        for (UIView *subview in [self.view.subviews reverseObjectEnumerator])
        {
            CGPoint convertPoint = [subview convertPoint:point toView:self.view];
            UIView *hitView = [subview hitTest:convertPoint withEvent:event];
            if (hitView)
            {
                return hitView;
            }
        }
        
        return self.view;
    }
    
    return nil;
}

```

### Passing touch events through to views below

```
- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event
{
    UIView *hitTestView = [self.view hitTest:point withEvent:event];
    if (hitTestView == self.view) {
        hitTestView = nil;
    }
    return hitTestView;
}
```

### Passing touch events to subview

![Passing TOuch event through subviews](https://github.com/imrvshah/Swift-2020/blob/master/iOS/HitTest/PassingEventToSUbView.png)

```
- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event
{
    UIView *hitTestView = [super hitTest:point withEvent:event];
    if (hitTestView) {
        hitTestView = self.scrollView;
    }
    return hitTestView;
}
```
