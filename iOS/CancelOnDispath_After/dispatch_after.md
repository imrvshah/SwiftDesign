```
#import "ViewController.h"
#import "BoolFlag.h"

@interface ViewController ()
@property (weak) BoolFlag *cancelState;
@end

@implementation ViewController

- (IBAction)showAllertTouche:(UIButton *)sender {
    if (self.cancelState) {
        self.cancelState.boolValue = YES;
    }
    
    self.cancelState = [self cancelableDispatchAfter:dispatch_time(DISPATCH_TIME_NOW, (int64_t)(3 * NSEC_PER_SEC))
                                             inQueue:dispatch_get_main_queue()
                                           withBlock:^{
        UIAlertController * alert = [UIAlertController alertControllerWithTitle:@"Testing dispatch" message:@"Showing alert to test dispatch after" preferredStyle:UIAlertControllerStyleAlert];
        UIAlertAction *alertAction = [UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:^(UIAlertAction * _Nonnull action) {  NSLog(@"Ok pressed"); }];
        [alert addAction:alertAction];
        [self presentViewController:alert animated:YES completion:nil];
    }];
    
    // Cancel after creating
    if (self.cancelState) {
        self.cancelState.boolValue = YES;
    }
}

- (BoolFlag *)cancelableDispatchAfter:(dispatch_time_t)when
                              inQueue:(dispatch_queue_t)queue
                            withBlock:(void (^)())block{
    BoolFlag *  cancelState = [[BoolFlag alloc] init];
    void(^newBlock) () = ^void(){
        if(cancelState.boolValue == NO){
            block();
        }
    };
    dispatch_after(when, queue, newBlock);
    return cancelState;
}

```

```
//
//  BoolFlag.h
//  CancelableDispatchAfter
//
//  Created by David A Cespedes R on 30/05/17.
//  Copyright © 2017 LSR Marketing Service. All rights reserved.
//

#import <Foundation/Foundation.h>

@interface BoolFlag : NSObject

@property (readwrite) bool boolValue;

@end

```

```
//
//  BoolFlag.m
//  CancelableDispatchAfter
//
//  Created by David A Cespedes R on 30/05/17.
//  Copyright © 2017 LSR Marketing Service. All rights reserved.
//

#import "BoolFlag.h"

@implementation BoolFlag

@end

```
