### NSOperation vs Dispatch_queue
- OperationQueue internally uses Grand Central Dispatch and on iOS.
- OperationQueue gives you a lot more control over how your operations are executed. 
You can define dependencies between individual operations for example, which isn't possible with plain GCD queues. 
It is also possible to cancel operations that have been enqueued in an OperationQueue (as far as the operations support it). 
When you enqueue a block in a GCD dispatch queue, it will definitely be executed at some point.
-  Whereas dispatch queues always execute tasks in first-in, first-out order, 
operation queues take other factors into account when determining the execution order of tasks. 
Primary among these factors is whether a given task depends on the completion of other tasks. 
You configure dependencies when defining your tasks and can use them to create complex execution-order graphs for your tasks.
- If you're just working with methods or chunks of code that need to be executed, GCD is
a fitting choice. If you're working with objects that need to encapsulate data and
functionality then you're more likely to utilize Operation
- . Some developers even go to the extreme of saying that you should always use Operations because it's built on top of
GCD, and Apple's guidance says to always use the highest level of abstraction provided.
-



### NSBlockOperation

```
NSBlockOperation* theOp = [NSBlockOperation blockOperationWithBlock: ^{
      NSLog(@"Beginning operation.\n");
      // Do some work.
   }];
```
After creating a block operation object, you can add more blocks to it using the addExecutionBlock: method. If you need to execute blocks serially, you must submit them directly to the desired dispatch queue.


### Defining a concurrent operation
```


@interface MyOperation : NSOperation {
    BOOL        executing;
    BOOL        finished;
}
- (void)completeOperation;
@end
 
@implementation MyOperation
- (id)init {
    self = [super init];
    if (self) {
        executing = NO;
        finished = NO;
    }
    return self;
}
 
- (BOOL)isConcurrent {
    return YES;
}
 
- (BOOL)isExecuting {
    return executing;
}
 
- (BOOL)isFinished {
    return finished;
}
@end

```


```
To create a queue, you allocate it in your application as you would any other object:

NSOperationQueue* aQueue = [[NSOperationQueue alloc] init];
To add operations to a queue, you use the addOperation: method. In OS X v10.6 and later, you can add groups of operations using the addOperations:waitUntilFinished: method, or you can add block objects directly to a queue (without a corresponding operation object) using the addOperationWithBlock: method. Each of these methods queues up an operation (or operations) and notifies the queue that it should begin processing them. In most cases, operations are executed shortly after being added to a queue, but the operation queue may delay execution of queued operations for any of several reasons. Specifically, execution may be delayed if queued operations are dependent on other operations that have not yet completed. Execution may also be delayed if the operation queue itself is suspended or is already executing its maximum number of concurrent operations. The following examples show the basic syntax for adding operations to a queue.

[aQueue addOperation:anOp]; // Add a single operation
[aQueue addOperations:anArrayOfOps waitUntilFinished:NO]; // Add multiple operations
[aQueue addOperationWithBlock:^{
   /* Do something. */
}];
```


### Dispatch queue

- Serial : Serial queues (also known as private dispatch queues) execute one task at a time in the order in which they are added to the queue. 
The currently executing task runs on a distinct thread (which can vary from task to task) that is managed by the dispatch queue. 
Serial queues are often used to synchronize access to a specific resource. 
You can create as many serial queues as you need, and each queue operates concurrently with respect to all other queues. 
In other words, if you create four serial queues, each queue executes only one task at a time but up to four tasks could still execute concurrently, 
one from each queue. For information on how to create serial queues,

- Concurrent: Concurrent queues (also known as a type of global dispatch queue) execute one or more tasks concurrently, 
but tasks are still started in the order in which they were added to the queue. 
The currently executing tasks run on distinct threads that are managed by the dispatch queue.
The exact number of tasks executing at any given point is variable and depends on system conditions.In iOS 5 and later, 
you can create concurrent dispatch queues yourself by specifying DISPATCH_QUEUE_CONCURRENT as the queue type. 
In addition, there are four predefined global concurrent queues for your application to use. 
For more information on how to get the global concurrent queues, see

- Main dispatch queue:The main dispatch queue is a globally available serial queue that executes tasks on the application’s main thread. 
This queue works with the application’s run loop (if one is present) to interleave the execution of queued tasks with the execution of other event sources attached to the run loop. 
Because it runs on your application’s main thread, the main queue is often used as a key synchronization point for an application.
Although you do not need to create the main dispatch queue, you do need to make sure your application drains it appropriately. 
For more information on how this queue is managed, see Performing Tasks on the Main Thread.



### Queue-Related Technologies
In addition to dispatch queues, Grand Central Dispatch provides several technologies that use queues to help manage your code. Table 3-2 lists these technologies and provides links to where you can find out more information about them.

Dispatch groups : A dispatch group is a way to monitor a set of block objects for completion. (You can monitor the blocks synchronously or asynchronously depending on your needs.) Groups provide a useful synchronization mechanism for code that depends on the completion of other tasks. 
For more information about using groups, 

Dispatch semaphores : A dispatch semaphore is similar to a traditional semaphore but is generally more efficient. Dispatch semaphores call down to the kernel only when the calling thread needs to be blocked because the semaphore is unavailable. If the semaphore is available, no kernel call is made. For an example of how to use dispatch semaphores,

Dispatch sources: A dispatch source generates notifications in response to specific types of system events. You can use dispatch sources to monitor events such as process notifications, signals, and descriptor events among others. When an event occurs, the dispatch source submits your task code asynchronously to the specified dispatch queue for processing.

### Quality of service

If you just need a concurrent queue but don't want to manage your own, you can use
the global class method on DispatchQueue to get one of the pre-defined global queues:
```
let queue = DispatchQueue.global(qos: .userInteractive)
```

As mentioned above, Apple offers six quality of service classes:

- .userInteractive
The .userInteractive QoS is recommended for tasks that the user directly interacts
with. UI-updating calculations, animations or anything needed to keep the UI
responsive and fast. If the work doesn't happen quickly, things may appear to freeze.
Tasks submitted to this queue should complete virtually instantaneously.
- .userInitiated
The .userInitiated queue should be used when the user kicks off a task from the UI
that needs to happen immediately, but can be done asynchronously. For example, you
may need to open a document or read from a local database. If the user clicked a
button, this is probably the queue you want. Tasks performed in this queue should take
a few seconds or less to complete.
- .utility
You'll want to use the .utility dispatch queue for tasks that would typically include a
progress indicator such as long-running computations, I/O, networking or continuous
data feeds. The system tries to balance responsiveness and performance with energy
efficiency. Tasks can take a few seconds to a few minutes in this queue.

- .background
For tasks that the user is not directly aware of you should use the .background queue.
They don't require user interaction and aren't time sensitive. Prefetching, database
maintenance, synchronizing remote servers and performing backups are all great
examples. The OS will focus on energy efficiency instead of speed. You'll want to use
this queue for work that will take significant time, on the order of minutes or more.
- .default and .unspecified
There are two other possible choices that exist, but you should not use explicitly.
There's a .default option, which falls between .userInitiated and .utility and is the
default value of the qos argument. It's not intended for you to directly use. The second
option is .unspecified, and exists to support legacy APIs that may opt the thread out of
a quality of service. It's good to know they exist, but if you're using them, you're almost
certainly doing something wrong.


```
@property(nonatomic, strong) dispatch_queue_t cacheQueue;
@property(nonatomic, strong) dispatch_queue_t downloadTaskQueue;

 _cacheQueue = dispatch_queue_create("com.cached", DISPATCH_QUEUE_CONCURRENT);
 _downloadTaskQueue = dispatch_queue_create("com.cached", DISPATCH_QUEUE_CONCURRENT);
 
 dispatch_async(dispatch_get_main_queue(), ^ {
                        completionHandler(image, NO);
                    });
                    
 dispatch_sync([[ImageDownloader sharedInstance] downloadTaskQueue], ^{
 dispatch_barrier_async([[ImageDownloader sharedInstance] downloadTaskQueue], ^{
```

![Thread](https://github.com/imrvshah/Swift-2020/blob/master/iOS/Concurrency/Screen%20Shot%202021-01-03%20at%2012.15.18%20PM.png)
