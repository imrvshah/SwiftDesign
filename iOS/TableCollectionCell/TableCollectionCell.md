```
//
//  ViewController.h
//  TableViewWIthCollectionViewCell
//
//  Created by Ravi Shah on 1/3/21.
//

#import <UIKit/UIKit.h>

@interface ViewController : UIViewController


@end
```

```
//
//  ViewController.m
//  TableViewWIthCollectionViewCell
//
//  Created by Ravi Shah on 1/3/21.
//

#import "ViewController.h"
#import "ListTableViewCell.h"
#import "TableCollectionViewCell.h"

//Number of Table view rows
const NSInteger numberOfTableViewRows = 10;

//Number of Collection view cells inside a table view row
const NSInteger numberOfCollectionViewCells = 15;

@interface ViewController ()<UITableViewDelegate, UITableViewDataSource, UICollectionViewDelegate, UICollectionViewDataSource>
@property(nonatomic, strong) UITableView *tableView;
@property (nonatomic, strong) NSArray *colorArray;
@property (nonatomic, strong) NSArray *reversedColorArray;
@property (nonatomic, strong) NSMutableDictionary *contentOffsetDictionary;
@property (nonatomic, strong) NSMutableArray *dataArray;


@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    [self customInit];
    // Do any additional setup after loading the view.
}

- (void)customInit
{
    self.tableView = [[UITableView alloc] initWithFrame:self.view.bounds style:UITableViewStylePlain];
    self.tableView.dataSource = self;
    self.tableView.delegate = self;
    self.tableView.translatesAutoresizingMaskIntoConstraints = NO;
    
    [self.view addSubview:self.tableView];
    
    [self.tableView.topAnchor constraintEqualToAnchor:self.view.topAnchor].active = YES;
    [self.tableView.bottomAnchor constraintEqualToAnchor:self.view.bottomAnchor].active = YES;
    [self.tableView.leftAnchor constraintEqualToAnchor:self.view.leftAnchor].active = YES;
    [self.tableView.rightAnchor constraintEqualToAnchor:self.view.rightAnchor].active = YES;
    
   
    [self.tableView registerClass:ListTableViewCell.class forCellReuseIdentifier:@"cellidentifier"];
    [self.tableView registerClass:ListTableViewCell.class forCellReuseIdentifier:@"secondcellidentifier"];
    //Create a random color Array.
    NSMutableArray *colorArray = [NSMutableArray arrayWithCapacity:numberOfCollectionViewCells];
    
    for (NSInteger collectionViewItem = 0; collectionViewItem < numberOfCollectionViewCells; collectionViewItem++)
    {
        
        CGFloat red = arc4random() % 255;
        CGFloat green = arc4random() % 255;
        CGFloat blue = arc4random() % 255;
        UIColor *color = [UIColor colorWithRed:red/255.0 green:green/255.0 blue:blue/255.0 alpha:1.0f];
        
        [colorArray addObject:color];
    }
    
    _colorArray = [NSArray arrayWithArray:colorArray];
    
    //Take random color array and reverse it before placing into the _reversedColorArray
    _reversedColorArray = [[_colorArray reverseObjectEnumerator] allObjects];
    
    _dataArray = [NSMutableArray arrayWithArray:_reversedColorArray];
    [_dataArray insertObject:_colorArray atIndex:0];
    [_dataArray insertObject:_colorArray atIndex:4];
    
    _contentOffsetDictionary = [NSMutableDictionary dictionary];
}


- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return self.dataArray.count;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    //By having two different cell identifiers, two different cell types can be created
    //without interfering with the way reusing of cells occur
    static NSString *cellidentifier = @"cellidentifier";
    static NSString *secondcellidentifier = @"secondcellidentifier";
    
    ListTableViewCell *cell = (ListTableViewCell *)[tableView dequeueReusableCellWithIdentifier:cellidentifier];
    ListTableViewCell *cell2 = (ListTableViewCell *)[tableView dequeueReusableCellWithIdentifier:secondcellidentifier];
    
    if ([[self.dataArray objectAtIndex:indexPath.row] isKindOfClass:[NSArray class]])
    {
        if (!cell)
        {
            cell = [[ListTableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:cellidentifier];
        }
        
        //No data in tableview except for the collectionview cells
        
        return cell;
    }
    else
    {
        if (!cell2)
        {
            cell2 = [[ListTableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:secondcellidentifier];
        }
        
        //No data in tableview except for the collectionview cells
        
        return cell2;
    }
}

//Cell Type determines the difference between horizontal and portrait cells
- (void)tableView:(UITableView *)tableView willDisplayCell:(ListTableViewCell *)cell forRowAtIndexPath:(NSIndexPath *)indexPath
{
    //Index 0 and 6 cater to the portrait collection view cell type
    if ([[self.dataArray objectAtIndex:indexPath.row] isKindOfClass:[NSArray class]])
    {
        [self insertDatasourceAndDelegateForCell:cell forRowAtIndexPath:indexPath andCellType:NO];
    }
    else
    {
        [self insertDatasourceAndDelegateForCell:cell forRowAtIndexPath:indexPath andCellType:YES];
    }
    
}

//Utility Method: cellType - NO means horizontal, YES means portrait
- (void) insertDatasourceAndDelegateForCell:(ListTableViewCell *)cell forRowAtIndexPath:(NSIndexPath *)indexPath andCellType:(BOOL)cellType
{
    [cell setCollectionViewDataSourceDelegate:self index:indexPath.row andCellType:cellType];
    NSInteger index = cell.collectionViewHorizontal.tag;
    
    CGFloat horizontalOffset = [self.contentOffsetDictionary[[@(index) stringValue]] floatValue];
    [cell.collectionViewHorizontal setContentOffset:CGPointMake(horizontalOffset, 0)];
}

#pragma mark - UITableViewDelegate Methods

- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath
{
    //Index 0 and 6 cater to the portrait collection view cell type
    if ([[self.dataArray objectAtIndex:indexPath.row] isKindOfClass:[NSArray class]])
    {
        return 270;
    }
    else
    {
        return 190;
    }
}

#pragma mark - UICollectionViewDataSource Methods

- (NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section
{
    //Index 0 and 6 cater to the portrait collection view cell type
    if ([[self.dataArray objectAtIndex:collectionView.tag] isKindOfClass:[NSArray class]])
    {
        return numberOfCollectionViewCells;
    }
    else
    {
        return numberOfCollectionViewCells;
    }
    
}

- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath
{
    TableCollectionViewCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:CollectionViewCellIdentifier forIndexPath:indexPath];
    
    //Index 0 and 6 cater to the portrait collection view cell type
    if ([[self.dataArray objectAtIndex:collectionView.tag] isKindOfClass:[NSArray class]])
    {
        //insert color
        cell.backgroundColor = _dataArray[collectionView.tag][indexPath.row];
    }
    else
    {
        //insert color
        NSLog(@"%ld", (long)collectionView.tag);
        cell.backgroundColor = _dataArray[collectionView.tag];
    }
    
    return cell;
    
}

#pragma mark - UIScrollViewDelegate Methods

- (void)scrollViewDidScroll:(UIScrollView *)scrollView
{
    if (![scrollView isKindOfClass:[UICollectionView class]]) return;
    
    CGFloat horizontalOffset = scrollView.contentOffset.x;
    
    UICollectionView *collectionView = (UICollectionView *)scrollView;
    NSInteger index = collectionView.tag;
    self.contentOffsetDictionary[[@(index) stringValue]] = @(horizontalOffset);
}




@end

```

```
//
//  TableCollectionViewCell.h
//  TableViewWIthCollectionViewCell
//
//  Created by Ravi Shah on 1/3/21.
//

#import <UIKit/UIKit.h>

NS_ASSUME_NONNULL_BEGIN

@interface TableCollectionViewCell : UICollectionViewCell

@end

NS_ASSUME_NONNULL_END

```


```
//
//  TableCollectionViewCell.m
//  TableViewWIthCollectionViewCell
//
//  Created by Ravi Shah on 1/3/21.
//

#import "TableCollectionViewCell.h"

@implementation TableCollectionViewCell

//Here can you add custom stuff to your cell if you like
- (instancetype)initWithFrame:(CGRect)frame
{
    self = [super initWithFrame:frame];
    if (self) {
       
    }
    return self;
}

- (void)setFrame:(CGRect)frame
{
    [super setFrame:frame];
//    [self setNeedsDisplay]; // force drawRect:
}


@end
```

```
//
//  ListTableViewCell.h
//  TableViewWIthCollectionViewCell
//
//  Created by Ravi Shah on 1/3/21.
//

#import <UIKit/UIKit.h>

NS_ASSUME_NONNULL_BEGIN
static NSString *CollectionViewCellIdentifier = @"CollectionViewCellIdentifier";

@interface ListTableViewCell : UITableViewCell
@property (nonatomic, strong) UICollectionViewFlowLayout *layout;
@property (nonatomic, strong) UICollectionView *collectionViewHorizontal;
@property (assign) BOOL cellType;

-(void)setCollectionViewDataSourceDelegate:(id<UICollectionViewDataSource,UICollectionViewDelegate>)dataSourceDelegate
                                     index:(NSInteger)index
                               andCellType: (BOOL) typeCell;

@end

NS_ASSUME_NONNULL_END

```

```
//
//  ListTableViewCell.m
//  TableViewWIthCollectionViewCell
//
//  Created by Ravi Shah on 1/3/21.
//

#import "ListTableViewCell.h"
#import "TableCollectionViewCell.h"

@implementation ListTableViewCell

- (void)awakeFromNib {
    [super awakeFromNib];
    // Initialization code
   
}

- (instancetype)initWithStyle:(UITableViewCellStyle)style reuseIdentifier:(NSString *)reuseIdentifier
{
    if (self = [super initWithStyle:style reuseIdentifier:reuseIdentifier])
    {
        [self setFlowLayouts];
    }
    return self;
}

- (void)setSelected:(BOOL)selected animated:(BOOL)animated {
    [super setSelected:selected animated:animated];

    // Configure the view for the selected state
}

//Happens after setCollectionViewDataSourceDelegate
-(void)layoutSubviews
{
    [super layoutSubviews];
    
    if (self.cellType)
    {
        _layout.itemSize = CGSizeMake(300, 169); //horizontal
        _layout.sectionInset = UIEdgeInsetsMake(10, 10, 10, 10);
    }
    else
    {
        
        _layout.itemSize = CGSizeMake(170, 249); //portrait
        _layout.sectionInset = UIEdgeInsetsMake(10, 10, 10, 10);
    }

    self.collectionViewHorizontal.frame = self.contentView.bounds;
}

- (void)prepareForReuse
{
    [super prepareForReuse];
}

#pragma mark - Private Methods
-(void)setCollectionViewDataSourceDelegate:(id<UICollectionViewDataSource, UICollectionViewDelegate>)dataSourceDelegate index:(NSInteger)index andCellType: (BOOL) typeCell
{
    self.collectionViewHorizontal.dataSource = dataSourceDelegate;
    self.collectionViewHorizontal.delegate = dataSourceDelegate;
    self.collectionViewHorizontal.tag = index;
    
    self.cellType = typeCell;
    
    [self.collectionViewHorizontal reloadData];
}

- (void)setFlowLayouts
{
    _layout = [[UICollectionViewFlowLayout alloc] init];
    _layout.sectionInset = UIEdgeInsetsMake(10, 10, 10, 10);
    _layout.itemSize = CGSizeMake(200, 270);
    _layout.scrollDirection = UICollectionViewScrollDirectionHorizontal;
    
    self.collectionViewHorizontal = [[UICollectionView alloc] initWithFrame:CGRectZero collectionViewLayout:_layout];
    [self.collectionViewHorizontal registerClass:[TableCollectionViewCell class] forCellWithReuseIdentifier:CollectionViewCellIdentifier];
    self.collectionViewHorizontal.backgroundColor = [UIColor clearColor];
    self.collectionViewHorizontal.showsHorizontalScrollIndicator = NO;
    [self.contentView addSubview:self.collectionViewHorizontal];
}

@end

```
