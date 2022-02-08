***Requirements:*** 
- Who can view my photos?
- Does user need authenticated to use the service for uploading photos?
- Can user follow other person to view their photos?
- Is there a feed for users to see the photos around the world?
- Does it support functionality offline? 

***Feature list*** 

1. User can see the feeds from other users 
2. user can scroll photo vertically (albums) and horizontally (more photos in that albums)
3. user can upload photo
4. user can share photos 

***Wire-frame***
![Wire-frame](https://github.com/imrvshah/Swift-2020/blob/master/Design/PhotoStream_wire-frame.png)


***API Design / High level desing***
- `getFeeds(_ userId : String, _ paginationCount: Int, _ startDate : TimeInterval, _ endDate : TimeInterval) -> [Feeds]`
- `postPhotos(_ userId: String, _ image : UIImage, _ imageName : String, _ albumID: String) -> Bool`
- `getAllPhotos(_ userId: String) -> [PHPhoto]`
- `getPhotosInAlbum(_ userId: String, _ albumId: String) -> [PHPhoto]`

***ERD*** 

![High level Client-server design](https://github.com/imrvshah/Swift-2020/blob/master/Design/PhotoStream/ERD.png)

***High level Client-server design*** 

![ERD](https://github.com/imrvshah/Swift-2020/blob/master/Design/PhotoStream/High_level_design.png)

- ****Maintainability**** - As you can see in the diagram, we fully decoupled data from receiving part or consuming part. So, it doesn't matter from which place the data come from persistane store or from network request. It helps to organize and reuse the code without impacting the others. 

- ****Scalability/Performance**** -  We have one subscriber (long poll running) so whenever we get the update from the long poll we trigger our presentation layer to prepare the UI and render it. So to add new subscription is easy and independent.

-  ****Testability**** - State is immutable and at the one place, which will help to avoid mistakes in simultaneous changes

- ****Avaiilability**** - It is available for all iOS devices with iOS 12 and higher

- ****Security**** - We need to make sure that we are not logging any PII or EUII. moreover when requesting the newsfeed or any other APIs are threat proof. I mean to say we should have encrypted communication between them. 

- ****Reliability**** - We should add enough logging / telemetry. Logging will help to debug any issue and scenario markers will be useful to nonitor reliability percentage automatically when user is using it. THe purpose of logging is to fire an alert whenever reliability goes down so we can escalate the issue before customer starts complaining about it. BI telelemtry would be useful for user experience and A/B testing. 

***Low level Client design*** 

![Low level Client design](https://github.com/imrvshah/Swift-2020/blob/master/Design/PhotoStream/Low_level_client_design.png)

***Detailed design of some modules News feeds***

- ***Poll mechanism***: we will use poll which has an active subscription with the web socket server so whenever we get the new images/updates we can send it to client to update the UI.
- ***Pagination*** : We need a pagination here to avoid large chunk of data in to the memory even though user is not looking at all the feeds at the same time, If user keeps scrolling we can fetch more data along the way with page and pagelimit.  Download the images asynchronusly and save it to cache/core data.
- ***Storage***: If the user load more images then we might have number of images in the app, where to store them as we are offering offline support. One way is to store in cache and another way to store in files. Cache is a temporary memory we can set a limit and number of items. These cache imgaes generally available at the run time so we can easily access it. As a fallback we can save in to document directory cache folder where it will automatically clean up when memory pressure. You can also choose to save in files as a persistent storage but starting from iOS 11 apple notified that user only save user related data into documents which can be visible by users. 
- ***Battery***: We need to make sure that battery and/or energy use would be minimal. Based on Apple's energy efficiency guide, it is better to download items in bulk compared to download one by one. But, there is a tread-off here. In my opinion, performance has the highest priority here compared to battery consumption. Another consideration is stop any video playing if the app goes background or stop longpoll etc to save battery,
-  ***Memory***: If we are displaying in UITableview or collectionView we should reuse the cell which will help to reduce the memory usage. Another things to take care about n number of qperations in queue which sometime make UI unresponsive to the springboard, That results in to the BOOM and/or FOOM. To solve that we need to limit the number of concurrnet operations allowed in the queue.
- ***Offline support*** - We will use coredata to save information about photos so when user try to load the data we will update it from the database first and load it with the new fresh data. if there is no internet connection then we will show currently available images.


