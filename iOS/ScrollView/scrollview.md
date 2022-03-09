It looks as though the view has moved down by 100 points, and this is in fact true in relation to its own coordinate system. The view’s actual position on the screen (or in its superview, to put it more accurately) remains fixed, however, as that is determined by its frame, which has not changed:

The frame rectangle … describes the view’s location and size in its superview’s coordinate system.

Since the view’s position is fixed (from its own perspective), think of the coordinate system plane as a piece of transparent film we can drag around, and of the view as a fixed window we are looking through. Adjusting the bounds’s origin is equivalent to moving the transparent film such that another part of it becomes visible through the view:


And this is exactly what UIScrollView does when it scrolls. Note that from the perspective of the user it appears as though the view’s subviews are moving, although their positions in terms of the view’s coordinate system (in other words, their frames) remain unchanged.

Let’s Build UIScrollView
A scroll view does not need to constantly update the coordinates of its subviews to make them scroll. All it has to do is adjust the origin of its bounds. With that knowledge, implementing a very simple scroll view is trivial. We set up a gesture recognizer to detect the user’s pan gestures, and in response to a gesture we translate the view’s bounds by the dragged amount:

```
//
//  RSCustomScrollView.h
//  RSScrollView
//
//  Created by Ravi Shah on 12/30/20.
//

#import <UIKit/UIKit.h>

NS_ASSUME_NONNULL_BEGIN

@interface RSCustomScrollView : UIView
@property (nonatomic) CGSize contentSize;
@property (nonatomic) BOOL scrollVertical;
@property (nonatomic) BOOL scrollHorizontal;
@end

NS_ASSUME_NONNULL_END

```

```
//
//  RSCustomScrollView.m
//  RSScrollView
//
//  Created by Ravi Shah on 12/30/20.
//

#import "RSCustomScrollView.h"

@implementation RSCustomScrollView

- (id)initWithFrame:(CGRect)frame
{
    self = [super initWithFrame:frame];
    if (self == nil) {
        return nil;
    }
    UIPanGestureRecognizer *gestureRecognizer = [[UIPanGestureRecognizer alloc]
        initWithTarget:self action:@selector(handlePanGesture:)];
    [self addGestureRecognizer:gestureRecognizer];
    return self;
}

- (void)handlePanGesture:(UIPanGestureRecognizer *)gestureRecognizer
{
    CGPoint translation = [gestureRecognizer translationInView:self];
    CGRect bounds = self.bounds;

    // Translate the view's bounds, but do not permit values that would violate contentSize
    CGFloat newBoundsOriginX = bounds.origin.x - translation.x;
    CGFloat minBoundsOriginX = 0.0;
    CGFloat maxBoundsOriginX = self.contentSize.width - bounds.size.width;
    bounds.origin.x = fmax(minBoundsOriginX, fmin(newBoundsOriginX, maxBoundsOriginX));

    CGFloat newBoundsOriginY = bounds.origin.y - translation.y;
    CGFloat minBoundsOriginY = 0.0;
    CGFloat maxBoundsOriginY = self.contentSize.height - bounds.size.height;
    bounds.origin.y = fmax(minBoundsOriginY, fmin(newBoundsOriginY, maxBoundsOriginY));

    self.bounds = bounds;
    [gestureRecognizer setTranslation:CGPointZero inView:self];
}

@end

```


```
//
//  ViewController.m
//  RSScrollView
//
//  Created by Ravi Shah on 12/30/20.
//

#import "ViewController.h"
#import "RSCustomScrollView.h"
@interface ViewController ()

@property (nonatomic) RSCustomScrollView *customScrollView;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
    
    self.customScrollView = [[RSCustomScrollView alloc] initWithFrame:self.view.bounds];
    self.customScrollView.contentSize = CGSizeMake(self.view.bounds.size.width, 1000);
    self.customScrollView.scrollHorizontal = NO;
    
    UIView *redView = [[UIView alloc] initWithFrame:CGRectMake(20, 20, 100, 100)];
    UIView *greenView = [[UIView alloc] initWithFrame:CGRectMake(150, 160, 150, 200)];
    UIView *blueView = [[UIView alloc] initWithFrame:CGRectMake(40, 400, 200, 150)];
    UIView *yellowView = [[UIView alloc] initWithFrame:CGRectMake(100, 600, 180, 150)];
    
    redView.backgroundColor = [UIColor colorWithRed:0.815 green:0.007 blue:0.105 alpha:1];
    greenView.backgroundColor = [UIColor colorWithRed:0.494 green:0.827 blue:0.129 alpha:1];
    blueView.backgroundColor = [UIColor colorWithRed:0.29 green:0.564 blue:0.886 alpha:1];
    yellowView.backgroundColor = [UIColor colorWithRed:0.972 green:0.905 blue:0.109 alpha:1];
    
    [self.customScrollView addSubview:redView];
    [self.customScrollView addSubview:greenView];
    [self.customScrollView addSubview:blueView];
    [self.customScrollView addSubview:yellowView];
    
    
    
    UIView *redView1 = [[UIView alloc] initWithFrame:CGRectMake(20, 500+20, 100, 100)];
    UIView *greenView1 = [[UIView alloc] initWithFrame:CGRectMake(150, 500+160, 150, 200)];
    UIView *blueView1 = [[UIView alloc] initWithFrame:CGRectMake(40, 500+400, 200, 150)];
    UIView *yellowView1 = [[UIView alloc] initWithFrame:CGRectMake(100, 500+600, 180, 150)];
    
    redView1.backgroundColor = [UIColor purpleColor];
    greenView1.backgroundColor = [UIColor redColor];
    blueView1.backgroundColor = [UIColor grayColor];
    yellowView1.backgroundColor = [UIColor blackColor];
    
    [self.customScrollView addSubview:redView1];
    [self.customScrollView addSubview:greenView1];
    [self.customScrollView addSubview:blueView1];
    [self.customScrollView addSubview:yellowView1];
    
    
    [self.view addSubview:self.customScrollView];
}


@end

```
