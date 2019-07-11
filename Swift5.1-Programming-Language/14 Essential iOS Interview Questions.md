# 14 Essential iOS Interview Questions

## Question 1
思考如下的UITableViewCell的构造函数：

    - (id)initWithStyle:(UITableViewCellStyle)style reuseIdentifier:(NSString *)reuseIdentifier

resuseIdentifier的作用是什么?给它设置非nil值可的好处是什么?

例如当cell有相同的样式,但是不同的内容.reuseIdentifier可以在UITableView中重用cell.
UITableView可以维护一个内部的UITableViewCell缓存,当调用dequeueReusableCellWithIdentifier:可以重用相同Identifier的cell.根据重用table cell可以提升滚动性能,因为新的cell不需要创建.

## Question 2
What are different ways that you can specify the layout of elements in a UIView?


## Question 3
atomic和nonatomic属性有什么不同?哪个是合成属性的默认值?什么时候应该选择用哪一个？

Atomic和non-atomic 是指setter/getter 是否原子操作的读和写值给属性.
当atomic关键值用在一个属性,任何访问属性将是同步的(线程安全).
因此,对getter的调用将保证是一个有效值,但这会带来性能的损失.
因此在一些情况,需要nonatomic来更快访问属性,但是会造成资源竞争.
如有两个线程访问同一个属性，会出现无法预料的结果.
默认是atomic.

## Question 4
解释copy和retain之间的区别?

Retain意味着引用计数加1.这意味着对象会保存在内存中,直到引用计数为0.这个属性会存储这个属性的引用，共享这个对象.

Copy意味着复制一个新对象,不与其他共享.


## Question 5
在Objective C中什么是swizzling方法?为什么去使用它?

Method swizzling 允许在运行时去替换一个类中已经存在的selector为不同的实现.

Method swizzling 允许你的代码执行在原始method之前或之后.

例如,

    #import "UIViewController+Log.h"
    @implementation UIViewController (Log)
        + (void)load {
            static dispatch_once_t once_token;
            dispatch_once(&once_token,  ^{
                SEL viewWillAppearSelector = @selector(viewDidAppear:);
                SEL viewWillAppearLoggerSelector = @selector(log_viewDidAppear:);
                Method originalMethod = class_getInstanceMethod(self, viewWillAppearSelector);
                Method extendedMethod = class_getInstanceMethod(self, viewWillAppearLoggerSelector);
                method_exchangeImplementations(originalMethod, extendedMethod);
            });
        }
        - (void) log_viewDidAppear:(BOOL)animated {
            [self log_viewDidAppear:animated];
            NSLog(@"viewDidAppear executed for %@", [self class]);
        }
    @end



## Question 6
What’s the difference between not-running, inactive, active, background and suspended execution states

* Not running: The app has not been launched or was running but was terminated by the system.

* Inactive: The app is running in the foreground but is currently not receiving events. (It may be executing other code though.) An app usually stays in this state only briefly as it transitions to a different state.

* Active: The app is running in the foreground and is receiving events. This is the normal mode for foreground apps.

* Background: The app is in the background and executing code. Most apps enter this state briefly on their way to being suspended. However, an app that requests extra execution time may remain in this state for a period of time. In addition, an app being launched directly into the background enters this state instead of the inactive state.

* Suspended: The app is in the background but is not executing code. The system moves apps to this state automatically and does not notify them before doing so. While suspended, an app remains in memory but does not execute any code. When a low-memory condition occurs, the system may purge suspended apps without notice to make more space for the foreground app.

## Question 7
What is a category and when is it used?

A category is a way of adding additional methods to a class without extending it. It is often used to add a collection of related methods. A common use case is to add additional methods to built in classes in the Cocoa frameworks. For example adding async download methods to the UIImage class.


## Question 8
Can you spot the bug in the following code and suggest how to fix it:

    @interface MyCustomController : UIViewController  

    @property (strong, nonatomic) UILabel *alert;  

    @end  

    @implementation MyCustomController  

    - (void)viewDidLoad {
        CGRect frame = CGRectMake(100, 100, 100, 50);
        self.alert = [[UILabel alloc] initWithFrame:frame];
        self.alert.text = @"Please wait...";
        [self.view addSubview:self.alert];
        dispatch_async(
            dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0),
            ^{
            sleep(10);
            self.alert.text = @"Waiting over";
            }
        ); 
    }  

    @end 

All UI updates must be done on the main thread. In the code above the update to the alert text may or may not happen on the main thread, since the global dispatch queue makes no guarantees . Therefore the code should be modified to always run the UI update on the main thread

    dispatch_async(		
        dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0),
        ^{
        sleep(10);
        dispatch_async(dispatch_get_main_queue(), ^{
            self.alert.text = @"Waiting over";
        });
    }); 

## Question 9
What is the difference between viewDidLoad and viewDidAppear?
Which should you use to load data from a remote server to display in the view?

viewDidLoad is called when the view is loaded, whether from a Xib file, storyboard or programmatically created in loadView. viewDidAppear is called every time the view is presented on the device. Which to use depends on the use case for your data. If the data is fairly static and not likely to change then it can be loaded in viewDidLoad and cached. However if the data changes regularly then using viewDidAppear to load it is better. In both situations, the data should be loaded asynchronously on a background thread to avoid blocking the UI.

## Question 10
What considerations do you need when writing a UITableViewController which shows images downloaded from a remote server?

This is a very common task in iOS and a good answer here can cover a whole host of knowledge. The important piece of information in the question is that the images are hosted remotely and they may take time to download, therefore when it asks for “considerations”, you should be talking about:

* Only download the image when the cell is scrolled into view, i.e. when cellForRowAtIndexPath is called.

* Downloading the image asynchronously on a background thread so as not to block the UI so the user can keep scrolling.

* When the image has downloaded for a cell we need to check if that cell is still in the view or whether it has been re-used by another piece of data. If it’s been re-used then we should discard the image, otherwise we need to switch back to the main thread to change the image on the cell.

Other good answers will go on to talk about offline caching of the images, using placeholder images while the images are being downloaded.

## Question 11
What is a protocol, and how do you define your own and when is it used?

A protocol is similar to an interface from Java. It defines a list of required and optional methods that a class must/can implement if it adopts the protocol. Any class can implement a protocol and other classes can then send messages to that class based on the protocol methods without it knowing the type of the class.

    @protocol MyCustomDataSource
    - (NSUInteger)numberOfRecords;
    - (NSDictionary *)recordAtIndex:(NSUInteger)index;
    @optional
    - (NSString *)titleForRecordAtIndex:(NSUInteger)index;
    @end

A common use case is providing a DataSource for UITableView or UICollectionView.

## Question 11
What is KVC and KVO? Give an example of using KVC to set a value.

KVC stands for Key-Value Coding. It's a mechanism by which an object's properties can be accessed using string's at runtime rather than having to statically know the property names at development time. KVO stands for Key-Value Observing and allows a controller or class to observe changes to a property value.

Let's say there is a property name on a class:

    @property (nonatomic, copy) NSString *name;

We can access it using KVC:

    NSString *n = [object valueForKey:@"name"]

And we can modify it's value by sending it the message:

    [object setValue:@"Mary" forKey:@"name"]

## Question 12
What are blocks and how are they used?

Blocks are a way of defining a single task or unit of behavior without having to write an entire Objective-C class. Under the covers Blocks are still Objective C objects. They are a language level feature that allow programming techniques like lambdas and closures to be supported in Objective-C. Creating a block is done using the ^ { } syntax:

    myBlock = ^{
        NSLog(@"This is a block");
    }

It can be invoked like so:

    myBlock();

It is essentially a function pointer which also has a signature that can be used to enforce type safety at compile and runtime. For example you can pass a block with a specific signature to a method like so:

    - (void)callMyBlock:(void (^)(void))callbackBlock;

If you wanted the block to be given some data you can change the signature to include them:

    - (void)callMyBlock:(void (^)(double, double))block {
        ...
        block(3.0, 2.0);
    }

## Question 13
iOS提供了哪些机制去支持多线程?
* NSThread 可以创建一个低级别的线程, 当调用start启动线程.

        NSThread* myThread = [[NSThread alloc] initWithTarget:self
        selector:@selector(myThreadMainMethod:)
            object:nil];
        [myThread start]; 


* NSOperationQueue 允许创建一个线程池.用来并行执行NSOperations.NSOperations也可以通过NSOperationQueue请求mainQueue运行在主线程中.

        NSOperationQueue* myQueue = [[NSOperationQueue alloc] init];
        [myQueue addOperation:anOperation]; 
        [myQueue addOperationWithBlock:^{
        /* Do something. */
        }];

* GCD or Grand Central Dispatch 是提供丰富的api支持通用多线程任务.GCD可以为任务提供分发在主线程，并行队列,串行队列.

        dispatch_queue_t myQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
        dispatch_async(myQueue, ^{
            printf("Do some work here.\n");
        });

## Question 13
What is the Responder Chain?

When an event happens in a view, for example a touch event, the view will fire the event to a chain of UIResponder objects associated with the UIView. The first UIResponder is the UIView itself, if it does not handle the event then it continues up the chain to until UIResponder handles the event. The chain will include UIViewControllers, parent UIViews and their associated UIViewControllers, if none of those handle the event then the UIWindow is asked if it can handle it and finally if that doesn't handle the event then the UIApplicationDelegate is asked.

If you get the opportunity to draw this one out, it's worth doing to impress the interviewer:

![](https://process.filestackapi.com/cache=expiry:max/2RlPNYtPQPs56t7iPGQj)


## Question 14

What's the difference between using a delegate and notification?

Both are used for sending values and messages to interested parties. A delegate is for one-to-one communication and is a pattern promoted by Apple. In delegation the class raising events will have a property for the delegate and will typically expect it to implement some protocol. The delegating class can then call the _delegate_s protocol methods.

Notification allows a class to broadcast events across the entire application to any interested parties. The broadcasting class doesn't need to know anything about the listeners for this event, therefore notification is very useful in helping to decouple components in an application.

    [NSNotificationCenter defaultCenter] 
            postNotificationName:@"TestNotification" 
            object:self];





## Question 15

How would you securely store private user data offline on a device? What other security best practices should be taken?

Again there is no right answer to this, but it's a great way to see how much a person has dug into iOS security. If you're interviewing with a bank I'd almost definitely expect someone to know something about it, but all companies need to take security seriously, so here's the ideal list of topics I'd expect to hear in an answer:

* If the data is extremely sensitive then it should never be stored offline on the device because all devices are crackable.
* The keychain is one option for storing data securely. However it's encryption is based on the pin code of the device. User's are not forced to set a pin, so in some situations the data may not even be encrypted. In addition the users pin code may be easily hacked.
* A better solution is to use something like SQLCipher which is a fully encrypted SQLite database. The encryption key can be enforced by the application and separate from the user's pin code.

Other security best practices are:

* Only communicate with remote servers over SSL/HTTPS.
* If possible implement certificate pinning in the application to prevent man-in-the-middle attacks on public WiFi.
* Clear sensitive data out of memory by overwriting it.
* Ensure all validation of data being submitted is also run on the server side.











