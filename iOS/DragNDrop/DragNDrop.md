```
func dragViewRender() {
        timer = Timer(timeInterval: 0.5, target: self, selector: #selector(changeColor), userInfo: nil, repeats: true)
        dragView.backgroundColor = .systemRed
        RunLoop.main.add(timer, forMode: .default)
        self.view.addSubview(dragView)
        let panGesture = UIPanGestureRecognizer(target: self, action: #selector(dragged(_:)))
        dragView.addGestureRecognizer(panGesture)
        
    }
    
   @objc func dragged(_ recognizer: UIPanGestureRecognizer) {
    let translation = recognizer.translation(in: self.view)
        if let view = recognizer.view {
            view.center = CGPoint(x: view.center.x + translation.x, y: view.center.y + translation.y)
        }
        recognizer.setTranslation(CGPoint.zero, in: self.view)
    }
    
    @objc func changeColor() {
        dragView.backgroundColor = UIColor(red: .random(in: 0...1),
                               green: .random(in: 0...1),
                               blue: .random(in: 0...1),
                               alpha: 1.0)
    }
```



```
//
//  ViewController.m
//  DragNDrop
//
//  Created by Ravi Shah on 1/3/21.
//

#import "ViewController.h"

static NSInteger DRAG_AREA_PADDING = 5;

@interface ViewController () <UIGestureRecognizerDelegate>
@property(nonatomic, strong) UIView *dragAreaView;
@property(nonatomic, strong) UIView *dragView;
@property(nonatomic, strong) UIView *goalView;
@property(nonatomic, strong) NSLayoutConstraint *dragViewX;
@property(nonatomic, strong) NSLayoutConstraint *dragViewY;
@property(nonatomic, strong) UIPanGestureRecognizer *panGesture;
@property(nonatomic, assign) CGFloat initialDragViewY;
@property(nonatomic) BOOL isGoalReached;
@property(nonatomic, assign) CGRect lastBound;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
    self.lastBound = self.view.bounds;
    self.dragAreaView = [[UIView alloc] initWithFrame:CGRectMake(10, 101, 300, 456)];
   
    self.dragView = [[UIView alloc] initWithFrame:CGRectMake(10, 30, 100, 100)];
    self.goalView = [[UIView alloc] initWithFrame:CGRectMake(50, 216, 200, 200)];
    
    self.dragView.layer.cornerRadius = self.dragView.layer.bounds.size.height /2;
    [self.dragView setBackgroundColor:[UIColor redColor]];
    self.panGesture = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(panAction:)];
    self.panGesture.delegate = self;
    self.panGesture.enabled =YES;
    self.panGesture.minimumNumberOfTouches = 1;
    self.dragView.userInteractionEnabled =YES;
    
//    self.panGesture.delegate = self;
    [self.dragView addGestureRecognizer:self.panGesture];
    
    self.goalView.layer.cornerRadius = self.dragView.layer.bounds.size.height /2;
    self.goalView.layer.borderWidth = 4;
    
    
//    self.dragViewY = self.dragView.;
    
//    self.dragViewX = [NSLayoutConstraint constraintWithItem:self.dragView attribute:NSLayoutAttributeLeading relatedBy:NSLayoutRelationEqual toItem:self.view attribute:NSLayoutAttributeLeading multiplier:1.0 constant:5];
    
    self.initialDragViewY = self.dragViewY.constant;
    
   
    [self.dragAreaView addSubview: self.goalView];
    [self.dragAreaView addSubview: self.dragView];
    [self.view addSubview:self.dragAreaView];
    
    
    NSLayoutConstraint *height = [NSLayoutConstraint constraintWithItem:self.dragView
                                                              attribute:NSLayoutAttributeHeight
                                                              relatedBy:NSLayoutRelationEqual
                                                                 toItem:nil
                                                              attribute:NSLayoutAttributeNotAnAttribute
                                                             multiplier:1.0
                                                               constant:100];
    
    NSLayoutConstraint *width = [NSLayoutConstraint constraintWithItem:self.dragView
                                                              attribute:NSLayoutAttributeHeight
                                                              relatedBy:NSLayoutRelationEqual
                                                                 toItem:nil
                                                              attribute:NSLayoutAttributeNotAnAttribute
                                                             multiplier:1.0
                                                               constant:100];
    
    
    [self.dragView addConstraint:height];
    [self.dragView addConstraint:width];
    
    NSLayoutConstraint *heightg = [NSLayoutConstraint constraintWithItem:self.goalView
                                                              attribute:NSLayoutAttributeHeight
                                                              relatedBy:NSLayoutRelationEqual
                                                                 toItem:nil
                                                              attribute:NSLayoutAttributeNotAnAttribute
                                                             multiplier:1.0
                                                               constant:200];
    
    NSLayoutConstraint *widthg = [NSLayoutConstraint constraintWithItem:self.goalView
                                                              attribute:NSLayoutAttributeHeight
                                                              relatedBy:NSLayoutRelationEqual
                                                                 toItem:nil
                                                              attribute:NSLayoutAttributeNotAnAttribute
                                                             multiplier:1.0
                                                               constant:200];
    
    
    [self.goalView addConstraint:heightg];
    [self.goalView addConstraint:widthg];
    
    
//    [self.dragView addConstraints:[NSLayoutConstraint constraintsWithVisualFormat:@"H:[view(==100)]" options:0 metrics:nil views:NSDictionaryOfVariableBindings(self.dragAreaView)]];
//    [self.dragView addConstraints:[NSLayoutConstraint constraintsWithVisualFormat:@"W:[view(==100)]" options:0 metrics:nil views:NSDictionaryOfVariableBindings(self.dragAreaView)]];
//
//    [self.goalView addConstraints:[NSLayoutConstraint constraintsWithVisualFormat:@"H:[view(==200)]" options:0 metrics:nil views:NSDictionaryOfVariableBindings(self.dragAreaView)]];
//    [self.dragView addConstraints:[NSLayoutConstraint constraintsWithVisualFormat:@"W:[view(==200)]" options:0 metrics:nil views:NSDictionaryOfVariableBindings(self.dragAreaView)]];

    

    
    
//    self.view.translatesAutoresizingMaskIntoConstraints = NO;
//    self.dragAreaView.translatesAutoresizingMaskIntoConstraints = NO;
//    self.dragView.translatesAutoresizingMaskIntoConstraints = NO;
    
    self.dragViewY = [NSLayoutConstraint constraintWithItem:self.dragView
                                  attribute:NSLayoutAttributeTop
                                  relatedBy:NSLayoutRelationEqual
                                     toItem:self.dragAreaView
                                  attribute:NSLayoutAttributeTop
                                 multiplier:1.0
                                   constant:30];
    [self.dragViewY setActive:YES];
    
    
    self.dragViewX = [NSLayoutConstraint constraintWithItem:self.dragView
                                  attribute:NSLayoutAttributeLeading
                                  relatedBy:NSLayoutRelationEqual
                                     toItem:self.dragAreaView
                                  attribute:NSLayoutAttributeLeading
                                 multiplier:1.0
                                   constant:10];
    
    
    [self.dragViewX setActive:YES];
    
    self.initialDragViewY = 30;
    
  
    [self updateGoalView];
}

- (void)panAction:(UIGestureRecognizer *)gestureRecognizer
{
    NSLog(@"Hi");
    if (self.panGesture.state == UIGestureRecognizerStateChanged) {
        [self moveObject];
    }
    else if (self.panGesture.state == UIGestureRecognizerStateEnded) {
        if (self.isGoalReached) {
            NSLog(@"reched");
        }
        else {
            [self returnToStartLocationAnimated:YES];
        }
    }
}

#pragma mark - UI updates

- (void)moveObject {
    CGFloat minX = DRAG_AREA_PADDING;
    CGFloat maxX = self.dragAreaView.bounds.size.width - self.dragView.bounds.size.width - DRAG_AREA_PADDING;
    
    CGFloat minY = DRAG_AREA_PADDING;
    CGFloat maxY = self.dragAreaView.bounds.size.height - self.dragView.bounds.size.height - DRAG_AREA_PADDING;
    
    CGPoint translation = [self.panGesture translationInView:self.dragAreaView];
    
    CGFloat dragViewX = self.dragViewX.constant + translation.x;
    CGFloat dragViewY = self.dragViewY.constant + translation.y;
    
    if (dragViewX < minX) {
        dragViewX = minX;
        translation.x += self.dragViewX.constant - minX;
    }
    else if (dragViewX > maxX) {
        dragViewX = maxX;
        translation.x += self.dragViewX.constant - maxX;
    }
    else {
        translation.x = 0;
    }
    
    if (dragViewY < minY) {
        dragViewY = minY;
        translation.y += self.dragViewY.constant - minY;
    }
    else if (dragViewY > maxY) {
        dragViewY = maxY;
        translation.y += self.dragViewY.constant - maxY;
    }
    else {
        translation.y = 0;
    }
    
    self.dragViewX.constant = dragViewX;
    self.dragViewY.constant = dragViewY;
    
    [self.panGesture setTranslation:translation inView:self.dragAreaView];
    
    [UIView animateWithDuration:0.05 delay:0 options:UIViewAnimationOptionBeginFromCurrentState animations:^{
        [self.view layoutIfNeeded];
    } completion:nil];
    
    [self updateGoalView];
}

- (void)updateGoalView {
    UIColor *goalColor = self.isGoalReached ? [UIColor whiteColor] : [UIColor colorWithRed:174/255.0 green:0 blue:0 alpha:1];
    
    self.goalView.layer.borderColor = goalColor.CGColor;
    
    
}

- (void)returnToStartLocationAnimated:(BOOL)animated {
    self.dragViewX.constant = (self.dragAreaView.bounds.size.width - self.dragView.bounds.size.width) / 2;
    self.dragViewY.constant = self.initialDragViewY;

    if (animated) {
        [UIView animateWithDuration:0.3 delay:0 options:UIViewAnimationOptionBeginFromCurrentState animations:^{
            [self.view layoutIfNeeded];
        } completion:nil];
    }
}

#pragma mark - Getters

- (BOOL)isGoalReached {
    CGFloat distanceFromGoal = sqrt(pow(self.dragView.center.x - self.goalView.center.x, 2) + pow(self.dragView.center.y - self.goalView.center.y, 2));
    return distanceFromGoal < self.dragView.bounds.size.width / 2;
}
```

