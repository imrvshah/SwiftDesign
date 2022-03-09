```
func getCommonSuperviews(with firstView:UIView, and secondView: UIView) -> UIView? {
    var currentView = firstView.superview
    var otherView = secondView.superview
    firstView.tag = -1  // -1 means this node is visited.
    
    while currentView != nil {
        firstView.superview?.tag = -1  // -1 means this node is visited.
        currentView = currentView?.superview
    }
    
    if secondView.tag == -1 { return secondView }
    
    while otherView != nil {
        // if view is visited before then return view
        guard otherView?.tag != -1 else { return otherView }
        otherView = otherView?.superview
    }
    
    return nil
}

```


```
static inline NSRange MyIntersectionRange(NSRange range1, NSRange range2)
{
    if (range1.location == NSNotFound || range1.length == NSNotFound ||
        range2.location == NSNotFound || range2.length == NSNotFound)
        return NSMakeRange(NSNotFound, NSNotFound);

    NSUInteger begin1 = range1.location;
    NSUInteger end1 = range1.location + range1.length;
    NSUInteger begin2 = range2.location;
    NSUInteger end2 = range2.location + range2.length;

    if (end1 <= begin2 || end2 <= begin1)
        return NSMakeRange(NSNotFound, NSNotFound);

    NSUInteger begin = MAX(begin1, begin2);
    NSUInteger end = MIN(end1, end2);

    return NSMakeRange(begin, end - begin);
}
```


```
@interface FBEnumerator : NSObject

@property (nonatomic) NSArray *internalArray;
@property (nonatomic) NSMutableArray *enumerators;

- (id)nextObject;
- (NSArray *)allObjects;

@end

@implementation FBEnumerator

//@[@1, @[@2, @[@3, @4]], @[ ], @5]

- (instancetype)initWithArray:(NSArray *)array
{
    if (self = [super init])
    {
        self.internalArray = array;
        self.enumerators = [NSMutableArray array];
        [self.enumerators addObject:[array objectEnumerator]];
    }
    return self;
}

- (id)nextObject
{
    NSEnumerator *lastEnumerator = [self.enumerators lastObject];
    id object = [lastEnumerator nextObject];

    if (!object)
    {
        [self.enumerators removeObject:lastEnumerator];
        return [self nextObject];
    }
    else
    {
        if ([object isKindOfClass:[NSNumber class]])
        {
            return object;
        }
        else if ([object isKindOfClass:[NSArray class]])
        {
            [self.enumerators addObject:[object objectEnumerator]];
            return [self nextObject];
        }
    }
    return nil;
}

- (NSArray *)allObjects
{
    NSMutableArray *allObjects = [NSMutableArray array];
    while (self.enumerators.count &gt; 0)
    {
        NSEnumerator *lastEnum = [self.enumerators lastObject];
        NSMutableArray *objects = nil;
        [self getAllObjectsInEnumerator:lastEnum intoArray:&amp;objects];
        [allObjects addObjectsFromArray:objects];
        [self.enumerators removeObject:lastEnum];
    }
    return allObjects;
}

- (void)getAllObjectsInEnumerator:(NSEnumerator *)enumerator intoArray:(NSMutableArray **)array
{
    if (*array == nil)
    {
        *array = [NSMutableArray array];
    }
    NSArray *objects = [enumerator allObjects];
    for (id object in objects)
    {
        if ([object isKindOfClass:[NSNumber class]])
        {
            [*array addObject:object];
        }
        else if ([object isKindOfClass:[NSArray class]])
        {
            [self getAllObjectsInEnumerator:[object objectEnumerator] intoArray:array];
        }
    }
}

@end


```

```
//
//  FBEnumator.m
//  DragNDrop
//
//  Created by Ravi Shah on 1/3/21.
//

#import "FBEnumator.h"

@interface FBEnumator()

@property (nonatomic) NSArray *internalArray;
@property (nonatomic) NSMutableArray *enumerators;

- (id)nextObject;
- (NSArray *)allObjects;

@end

@implementation FBEnumator

//@[@1, @[@2, @[@3, @4]], @[ ], @5]

- (instancetype)initWithArray:(NSArray *)array
{
    if (self = [super init])
    {
        self.internalArray = array;
        self.enumerators = [NSMutableArray array];
        [self.enumerators addObject:[array objectEnumerator]];
    }
    return self;
}

- (id)nextObject
{
    NSEnumerator *lastEnumerator = [self.enumerators lastObject];
    id object = [lastEnumerator nextObject];

    if (!object)
    {
        [self.enumerators removeObject:lastEnumerator];
        return [self nextObject];
    }
    else
    {
        if ([object isKindOfClass:[NSNumber class]])
        {
            return object;
        }
        else if ([object isKindOfClass:[NSArray class]])
        {
            [self.enumerators addObject:[object objectEnumerator]];
            return [self nextObject];
        }
    }
    return nil;
}

- (NSArray *)allObjects
{
    NSMutableArray *allObjects = [NSMutableArray array];
    while (self.enumerators.count > 0)
    {
        NSEnumerator *lastEnum = [self.enumerators lastObject];
        NSMutableArray *objects = nil;
        [self getAllObjectsInEnumerator:lastEnum intoArray:&objects];
        [allObjects addObjectsFromArray:objects];
        [self.enumerators removeObject:lastEnum];
    }
    return allObjects;
}

- (void)getAllObjectsInEnumerator:(NSEnumerator *)enumerator intoArray:(NSMutableArray **)array
{
    if (*array == nil)
    {
        *array = [NSMutableArray array];
    }
    NSArray *objects = [enumerator allObjects];
    for (id object in objects)
    {
        if ([object isKindOfClass:[NSNumber class]])
        {
            [*array addObject:object];
        }
        else if ([object isKindOfClass:[NSArray class]])
        {
            [self getAllObjectsInEnumerator:[object objectEnumerator] intoArray:array];
        }
    }
}

@end



```
