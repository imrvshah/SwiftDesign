```
//
//  AppDelegate.h
//  ImageDownloader
//
//  Created by Ravi Shah on 1/2/21.
//

#import <UIKit/UIKit.h>

@interface AppDelegate : UIResponder <UIApplicationDelegate>

@property (nonatomic, strong) UIWindow *window;
@end
```


```
//
//  AppDelegate.m
//  ImageDownloader
//
//  Created by Ravi Shah on 1/2/21.
//

#import "AppDelegate.h"
#import "ViewController.h"
@interface AppDelegate ()

@end

@implementation AppDelegate


- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.

    self.window = [[UIWindow alloc] initWithFrame:UIScreen.mainScreen.bounds];
    UIViewController *viewController = [[ViewController alloc] initWithNibName:nil bundle:nil];
    self.window.rootViewController = viewController;
    self.window.backgroundColor = [UIColor whiteColor];
    viewController.view.backgroundColor =  [UIColor whiteColor];
    [self.window makeKeyAndVisible];
    return YES;
}

@end
```



```
//
//  ViewController.h
//  ImageDownloader
//
//  Created by Ravi Shah on 1/2/21.
//

#import <UIKit/UIKit.h>

@interface ViewController : UIViewController<UITableViewDataSource, UITableViewDelegate>
@property (nonatomic, strong) IBOutlet UITableView * tableView;


@end

```



```
//
//  ViewController.m
//  ImageDownloader
//
//  Created by Ravi Shah on 1/2/21.
//

#import "ViewController.h"
#import "ListTableViewCell.h"

@interface ViewController ()
@property (nonatomic, strong) NSMutableArray<NSDictionary *>* data;

@end

@implementation ViewController

- (instancetype)initWithCoder:(NSCoder *)coder
{
    self = [super initWithCoder:coder];
    if (self)
    {
        [self customInit];
    }
    
    return self;
}
- (void)viewDidLoad {
    [super viewDidLoad];
    
    // Do any additional setup after loading the view.
    self.tableView = [[UITableView alloc] initWithFrame:self.view.bounds style:UITableViewStylePlain];
    
    // must set delegate & dataSource, otherwise the the table will be empty and not responsive
    self.tableView.delegate = self;
    self.tableView.dataSource = self;
    
    self.tableView.backgroundColor = [UIColor cyanColor];
    
    // add to canvas
    [self.view addSubview:self.tableView];
    
    self.tableView.translatesAutoresizingMaskIntoConstraints = NO;
    [self.tableView.topAnchor constraintEqualToAnchor:self.view.topAnchor].active = YES;
    [self.tableView.leftAnchor constraintEqualToAnchor:self.view.leftAnchor].active = YES;
    [self.tableView.bottomAnchor constraintEqualToAnchor:self.view.bottomAnchor].active = YES;
    [self.tableView.rightAnchor constraintEqualToAnchor:self.view.rightAnchor].active = YES;
    self.data = NSMutableArray.array;
    [self.tableView registerClass:ListTableViewCell.class forCellReuseIdentifier:@"listv"];
    [self fetchData];
    
}

- (void)customInit
{
    
}

- (void)fetchData
{
    //    __weak ViewController *weakSelf = self;
    //    [self.listViewModel getList:^(NSArray<List *> * _Nonnull lists, NSError * _Nonnull err) {
    //        dispatch_async(dispatch_get_main_queue(), ^{
    //            [weakSelf.tableView reloadData];
    //        });
    //    }];
    
    for (int i = 0; i < 100; i ++)
    {
        NSString *url = i % 2 == 0 ? @"https://i.imgur.com/w5rkSIj.jpg" : @"https://cdn.cocoacasts.com/cc00ceb0c6bff0d536f25454d50223875d5c79f1/above-the-clouds.jpg";
        NSDictionary *dict = @{@"url" : url,
                               @"label" : [NSString stringWithFormat:@"%d_hello", i]};
        [self.data addObject:dict];
                               
    }
    
    [self.tableView reloadData];
}

#pragma mark - Tableview methods

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return self.data.count;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    ListTableViewCell *tableViewCell = [tableView dequeueReusableCellWithIdentifier:@"listv"];
    
    if(!tableViewCell)
    {
        assert(false);
    }
    
    [tableViewCell setDisplay:[self.data objectAtIndex:indexPath.row]];
    return tableViewCell;
}



@end

```


```
//
//  ImageDownloader.h
//  ImageDownloader
//
//  Created by Ravi Shah on 1/2/21.
//

#import <Foundation/Foundation.h>

NS_ASSUME_NONNULL_BEGIN

@interface ImageDownloader : NSObject

+ (instancetype)sharedInstance;

- (void)downloadImage:(NSString *)url
          placeholder:(UIImage *)placeHolder
    completionHandler:(void (^) (UIImage *, BOOL))completionHandler;
@end

NS_ASSUME_NONNULL_END

```

```
//
//  ImageDownloader.m
//  ImageDownloader
//
//  Created by Ravi Shah on 1/2/21.
//

#import <UIKit/UIKit.h>
#import "ImageDownloader.h"

@interface ImageDownloader()

@property(nonatomic, strong) NSMutableDictionary *cachedImages;
@property(nonatomic, strong) NSMutableDictionary *imageDownloadTasks;
@property(nonatomic, strong) dispatch_queue_t cacheQueue;
@property(nonatomic, strong) dispatch_queue_t downloadTaskQueue;
@property(nonatomic, strong) NSCache *imagecache;

@end
@implementation ImageDownloader

+ (instancetype)sharedInstance
{
    static ImageDownloader* instance = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        instance = [[ImageDownloader alloc] init];
    });
    return instance;
}

- (void)downloadImage:(NSString *)url
          placeholder:(UIImage *)placeHolder
    completionHandler:(void (^) (UIImage *, BOOL))completionHandler
{
    if (url == nil || [url isKindOfClass:NSNull.class] || [url length] == 0)
    {
        if (completionHandler)
        {
            completionHandler(nil, YES);
            return;
        }
    }
    
    UIImage *cachedImage = [[[ImageDownloader sharedInstance] imagecache] objectForKey:url];
    if (cachedImage)
    {
        if (completionHandler) {
            dispatch_async(dispatch_get_main_queue(), ^ {
            completionHandler(cachedImage, YES);
            });
            return;
        }
    }
    else
    {
        NSURL *stringURL = [NSURL URLWithString:url];
        if (!stringURL)
        {
            if (completionHandler)
            {
                completionHandler(nil, YES);
                return;
            }
        }
        else
        {
            dispatch_sync([[ImageDownloader sharedInstance] downloadTaskQueue], ^{
//                NSURLSessionDataTask *task = [[[ImageDownloader sharedInstance] imageDownloadTasks] valueForKey:url];
//                if (task)
//                {
//                    return;
//                }
                
                NSURLSessionDataTask *task =  [[NSURLSession sharedSession] dataTaskWithURL:stringURL completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {
                    if (!data || error)
                    {
                        if (completionHandler)
                        {
                            completionHandler(nil, YES);
                        }
                        return;
                    }
                    
                    if (error)
                    {
                        if (completionHandler)
                        {
                            dispatch_async(dispatch_get_main_queue(), ^{
                                completionHandler(placeHolder, YES);
                            });
                            
                        }
                        return;
                    }
                    
                    UIImage *image = [UIImage imageWithData:data];
                    [[[ImageDownloader sharedInstance] imagecache] setObject:image forKey:url];
//                    dispatch_barrier_async([[ImageDownloader sharedInstance] cacheQueue], ^{
//
//                    });
                    
//                    dispatch_barrier_async([[ImageDownloader sharedInstance] downloadTaskQueue], ^{
//                        [[[ImageDownloader sharedInstance] imageDownloadTasks] removeObjectForKey:url];
//
//                    });
                    
                    
                    dispatch_async(dispatch_get_main_queue(), ^ {
                        completionHandler(image, NO);
                    });
                }];
//                dispatch_barrier_async([[ImageDownloader sharedInstance] downloadTaskQueue], ^{
//                    [[[ImageDownloader sharedInstance] imageDownloadTasks] setValue:task forKey:url];
//                });
                
                [task resume];
            });
            
            
           
        }
    }
}

- (id)init
{
    if (self = [super init])
    {
        _imageDownloadTasks = NSMutableDictionary.dictionary;
        _cachedImages = NSMutableDictionary.dictionary;
        _cacheQueue = dispatch_queue_create("com.cached", DISPATCH_QUEUE_CONCURRENT);
        _downloadTaskQueue = dispatch_queue_create("com.cached", DISPATCH_QUEUE_CONCURRENT);
        _imagecache = [[NSCache alloc] init];
        _imagecache.countLimit = 1;
    }
    
    return self;
}





@end

```

```
//
//  ListTableViewCell.h
//  TestMVVM
//
//  Created by Ravi Shah on 11/17/20.
//

#import <UIKit/UIKit.h>

NS_ASSUME_NONNULL_BEGIN

@interface ListTableViewCell : UITableViewCell
//@property (nonatomic, strong) IBOutlet UILabel * titleLabel;
//@property (nonatomic, strong) IBOutlet UILabel * ratings;
//@property (nonatomic, strong) IBOutlet UIImageView * moviewImageView;
@property (nonatomic, strong) NSString *previousURL;
- (void)setDisplay:(NSDictionary *)dict;
@end

NS_ASSUME_NONNULL_END

```

```
//
//  ListTableViewCell.m
//  TestMVVM
//
//  Created by Ravi Shah on 11/17/20.
//

#import "ListTableViewCell.h"
#import "ImageDownloader.h"

@implementation ListTableViewCell

- (void)awakeFromNib {
    [super awakeFromNib];
    // Initialization code
}

- (void)setSelected:(BOOL)selected animated:(BOOL)animated {
    [super setSelected:selected animated:animated];

    // Configure the view for the selected state
}

- (void)setDisplay:(NSDictionary *)dict {
    [self.textLabel setText:[dict valueForKey:@"label"]];
    [[ImageDownloader sharedInstance] downloadImage:[dict valueForKey:@"url"] placeholder:[UIImage new] completionHandler:^(UIImage * _Nonnull image, BOOL success) {
        if (success || [self.previousURL isEqualToString:[dict valueForKey:@"url"]])
        {
            [self.imageView setImage:image];
            [self setNeedsLayout];
            
        }
        
    }];
    
    self.previousURL = [dict valueForKey:@"url"];
//    [self.ratings setText:[NSString stringWithFormat:@"%d", list.ratings.intValue]];
}


@end

```
