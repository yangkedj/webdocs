#iOS API

###标签与别名 API (iOS)

####功能说明

> 温馨提示，设置标签别名请注意处理call back结果。只有call back 返回值为 0 才设置成功，才可以向目标推送。否则服务器 API 会返回1011错误

提供几个相关 API 用来设置别名（alias）与标签（tags）。

这几个 API 可以在 App 里任何地方调用。

##### 别名 alias

为安装了应用程序的用户，取个别名来标识。以后给该用户 Push 消息时，就可以用此别名来指定。

每个用户只能指定一个别名。

同一个应用程序内，对不同的用户，建议取不同的别名。这样，尽可能根据别名来唯一确定用户。

系统不限定一个别名只能指定一个用户。如果一个别名被指定到了多个用户，当给指定这个别名发消息时，服务器端API会同时给这多个用户发送消息。

举例：在一个用户要登录的游戏中，可能设置别名为 userid。游戏运营时，发现该用户 3 天没有玩游戏了，则根据 userid 调用服务器端API发通知到客户端提醒用户。

##### 标签 tag

为安装了应用程序的用户，打上标签。其目的主要是方便开发者根据标签，来批量下发 Push 消息。

可为每个用户打多个标签。

不同应用程序、不同的用户，可以打同样的标签。

举例： game, old_page,  women

#### Method - setTagsWithAlias (with Callback)


调用此 API 来同时设置别名与标签，支持回掉函数。

需要理解的是，这个接口是覆盖逻辑，而不是增量逻辑。即新的调用会覆盖之前的设置。

在之前调用过后，如果需要再次改变别名与标签，只需要重新调用此 API 即可。

##### 支持的版本

开始支持的版本：1.4.0

##### 接口定义

	+ (void)setTags:(NSSet *)tags alias:(NSString *)alias callbackSelector:(SEL)cbSelector object:(id)theTarget;
	
#####参数说明

+ alias
	+ nil 此次调用不设置此值。
	+ 空字符串 （@""）表示取消之前的设置。
	+ 每次调用设置有效的别名，覆盖之前的设置。
	+ 有效的别名组成：字母（区分大小写）、数字、下划线、汉字。
	+ 限制：alias 命名长度限制为 40 字节。（判断长度需采用UTF-8编码）

+ tags
	+ nil 此次调用不设置此值。
	+ 空集合（[NSSet set]）表示取消之前的设置。
	+ 每次调用至少设置一个 tag，覆盖之前的设置，不是新增。
	+ 有效的标签组成：字母（区分大小写）、数字、下划线、汉字。
	+ 限制：每个 tag 命名长度限制为 40 字节，最多支持设置 100 个 tag，但总长度不得超过1K字节。（判断长度需采用UTF-8编码）
	+ 单个设备最多支持设置 100 个 tag。App 全局 tag 数量无限制。
+ callbackSelector
	+ nil 此次调用不需要 Callback。
	+ 用于回掉返回对应的参数 alias, tags。并返回对应的状态码：0为成功，其他返回码请参考错误码定义。
	+ 回调函数请参考SDK 实现。
+ theTarget
	+ 参数值为实现了callbackSelector的实例对象。
	+ nil 此次调用不需要 Callback。

			- (void)tagsAliasCallback:(int)iResCode tags:(NSSet*)tags alias:(NSString*)alias {
			   		NSLog(@"rescode: %d, \ntags: %@, \nalias: %@\n", iResCode, tags , alias);
			}

##### Method - setTags

调用此 API 来设置标签，支持回掉函数。

该方法是 setTagsWithAlias (with Callback) 的简化版本，用于只变更标签的情况。

> 使用建议
> 
> 如果待设置的 alias / tags 是动态的，有可能在调用 setTagsWithAlias 时因为 alias / tags 无效而整调用失败。
> 
> 调用此方法只设置 tags，可以排除可能的无效的 alias 对本次调用的影响。

##### 支持的版本

开始支持的版本：1.4.0

##### 接口定义

	+ (void)setTags:(NSSet *)tags callbackSelector:(SEL)cbSelector object:(id)theTarget;
	
##### 参数说明

+ tags
	+ nil 此次调用不设置此值。
	+ 空集合（[NSSet set]）表示取消之前的设置。
	+ 每次调用至少设置一个 tag，覆盖之前的设置，不是新增。
	+ 有效的标签组成：字母（区分大小写）、数字、下划线、汉字。
	+ 限制：每个 tag 命名长度限制为 40 字节，最多支持设置 100 个 + callbackSelector
	+ nil 此次调用不需要 Callback。
	+ 用于回掉返回对应的参数 alias, tags。并返回对应的状态码：0为成功，其他返回码请参考错误码定义。
	+ 回调函数请参考SDK 实现。
+ callbackSelector
	+ nil 此次调用不需要 Callback。
	+ 用于回掉返回对应的参数 alias, tags。并返回对应的状态码：0为成功，其他返回码请参考错误码定义。
+ theTarget
	+ 参数值为实现了callbackSelector的实例对象。
	+ nil 此次调用不需要 Callback。

			- (void)tagsAliasCallback:(int)iResCode tags:(NSSet*)tags alias:(NSString*)alias {
						NSLog(@"rescode: %d, \ntags: %@, \nalias: %@\n", iResCode, tags , alias);
			}


#### Method - setAlias

调用此 API 来设置别名，支持回掉函数。

该方法是 setTagsWithAlias (with Callback) 的简化版本，用于只变更别名的情况。

##### 支持的版本

开始支持的版本：1.4.0

##### 接口定义

		+ (void)setAlias:(NSString *)alias callbackSelector:(SEL)cbSelector object:(id)theTarget;
		
##### 参数说明

+ alias
	+ 空字符串 （@""）表示取消之前的设置。
	+ 每次调用设置有效的别名，覆盖之前的设置。
	+ 有效的别名组成：字母（区分大小写）、数字、下划线、汉字。
	+ 限制：alias 命名长度限制为 40 字节。（判断长度需采用UTF-8编码）
+ callbackSelector
	+ nil 此次调用不需要 Callback。
	+ 用于回掉返回对应的参数 alias, tags。并返回对应的状态码：0为成功，其他返回码请参考错误码定义。
	+ 回调函数请参考SDK 实现。
+ theTarget
	+ 参数值为实现了callbackSelector的实例对象。
	+ nil 此次调用不需要 Callback。
	  
	  
			- (void)tagsAliasCallback:(int)iResCode tags:(NSSet*)tags alias:(NSString*)alias {
					NSLog(@"rescode: %d, \ntags: %@, \nalias: %@\n", iResCode, tags , alias)		}
					
					
#### Method - filterValidTags

用于过滤出正确可用的 tags。

如果总数量超出最大限制则返回最大数量的靠前的可用tags。

> 使用建议
> 
> 设置 tags 时，如果其中一个 tag 无效，则整个设置过程失败。
> 
> 如果 App 的 tags 会在运行过程中动态设置，并且存在对 JPush SDK tag 规定的无效字符，则有可能一个 tag 无效导致这次调用里所有的 tags 更新失败。
> 
> 这时你可以调用本方法 filterValidTags 来过滤掉无效的 tags，得到有效的 tags，再调用 JPush SDK 的 set tags / alias 方法。


##### 支持的版本

开始支持的版本：1.4.0

##### 接口定义

	+ (NSSet*)filterValidTags:(NSSet*)tags;

##### 参数说明

+ tags
	+ 原 tag 集合。

##### 接口返回

有效的 tag 集合。


#### 错误码定义

Code|描述|详细解释
-|-|
6001|无效的设置，tag/alias 不应参数都为 null|
6002|设置超时	|建议重试
6003|alias 字符串不合法	|有效的别名、标签组成：字母（区分大小写）、数字、下划线、汉字。
6004|alias超长。最多 40个字节	|中文 UTF-8 是 3 个字节
6005|某一个 tag 字符串不合法	|有效的别名、标签组成：字母（区分大小写）、数字、下划线、汉字。
6006|某一个 tag 超长。一个 tag 最多 40个字节	|中文 UTF-8 是 3 个字节
6007|tags 数量超出限制。最多 100个	|这是一台设备的限制。一个应用全局的标签数量无限制。
6008|tag/alias 超出总长度限制。总长度最多 1K 字节	|


###获取 APNs 推送内容

#### 支持的版本

最初的版本。


#### 功能说明

iOS 设备收到一条推送（APNs），用户点击推送通知打开应用时，应用程序根据状态不同进行处理需在 AppDelegate 中的以下两个方法中添加代码以获取apn内容


+ 如果 App 状态为未运行，此函数将被调用，如果launchOptions包含UIApplicationLaunchOptionsLocalNotificationKey表示用户点击apn 通知导致app被启动运行；如果不含有对应键值则表示 App 不是因点击apn而被启动，可能为直接点击icon被启动或其他。

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions; 
		// apn 内容获取：NSDictionary *remoteNotification = [launchOptions objectForKey: UIApplicationLaunchOptionsRemoteNotificationKey]
		
+ 如果 App状态为正在前台或者后台运行，那么此函数将被调用，并且可通过AppDelegate的applicationState是否为UIApplicationStateActive判断程序是否在前台运行。此种情况在此函数中处理：

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo;
		// apn内容为userInfo
		
		
+ 如果是使用 iOS 7 的 Remote Notification 特性那么处理函数需要使用

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler;
		// apn内容为userInfo		



#### 示例代码

	- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
	    // 取得 APNs 标准信息内容
	    NSDictionary *aps = [userInfo valueForKey:@"aps"];
	    NSString *content = [aps valueForKey:@"alert"]; //推送显示的内容
	    NSInteger badge = [[aps valueForKey:@"badge"] integerValue]; //badge数量
	    NSString *sound = [aps valueForKey:@"sound"]; //播放的声音
	     
	    // 取得自定义字段内容
	    NSString *customizeField1 = [userInfo valueForKey:@"customizeField1"]; //自	定义参数，key是自己定义的
	    NSLog(@"content =[%@], badge=[%d], sound=[%@], customize field 	=[%@]",content,badge,sound,customizeField1);
	     
	    // Required
	    [APService handleRemoteNotification:userInfo];
	}
	//iOS 7 Remote Notification
	- (void)application:(UIApplication *)application didReceiveRemoteNotification:	(NSDictionary *)userInfo fetchCompletionHandler:(void (^)	(UIBackgroundFetchResult))completionHandler {
	 
	    NSLog(@"this is iOS7 Remote Notification");
	     
	    // Required
	   	 [APService handleRemoteNotification:userInfo];
	    completionHandler(UIBackgroundFetchResultNoData);
	}

参考文档：[Handling Local and Remote Notifications](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/IPhoneOSClientImp.html#//apple_ref/doc/uid/TP40008194-CH103-SW4)
		
### 获取应用内推送信息

#### 支持的版本

r1.2.5 以后。

#### 功能说明

从jpush服务器获取用户推送的内容和标题。

#### 实现方法

获取iOS的推送内容需要在delegate类中注册通知并实现回调方法。

1. 在方法- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *) launchOptions 加入下面的代码：

		NSNotificationCenter *defaultCenter = [NSNotificationCenter defaultCenter];
		[defaultCenter addObserver:self selector:@selector(networkDidReceiveMessage:) name:kAPNetworkDidReceiveMessageNotification object:nil];


2. 实现回调方法 networkDidReceiveMessage

		- (void)networkDidReceiveMessage:(NSNotification *)notification {
		    NSDictionary * userInfo = [notification userInfo];
		    NSString *content = [userInfo valueForKey:@"content"];
		    NSString *extras = [userInfo valueForKey:@"extras"]; 
		    NSString *customizeField1 = [extras valueForKey:@"customizeField1"]; //自定义参数，key是自己定义的
		 
		}
		
		
#####参数描述：

content：获取推送的内容

extras：获取用户自定义参数

customizeField1：根据自定义key获取自定义的value

更多实现参考 SDK下载压缩包中的 demo。


### 获取 RegistrationID

#### 支持的版本

r1.7.0 开始支持。

#### 功能说明

##### RegistrationID 定义

集成了 JPush SDK 的应用程序在第一次成功注册到 JPush 服务器时，JPush 服务器会给客户端返回一个唯一的该设备的标识 - RegistrationID。JPush SDK 会以广播的形式发送 RegistrationID 到应用程序。

应用程序可以把此 RegistrationID 保存以自己的应用服务器上，然后就可以根据 RegistrationID 来向设备推送消息或者通知。

#### API - getRegistrationID

调用此 API 来取得应用程序对应的 RegistrationID。 只有当应用程序成功注册到 JPush 的服务器时才返回对应的值，否则返回空字符串。

##### 支持的版本

开始支持的版本：1.7.0。


##### 接口定义

	+ (NSString *)registrationID

> 开发者在自定义的监听kAPNetworkDidRegisterNotification时也可以通过"RegistrationID"这个key值来获取对应的 RegistrationID。
		
#### 附加说明

#####通过 RegistrationID 推送消息和通知

可以通过 RegistrationID 来推送消息和通知， 参考文档 Push API v2， 当 receiver_type = 5 并且设置 receiver_value 为 RegistrationID 时候即可根据 RegistrationID 推送。

> 注：要使用此功能，客户端 App 一定要集成有 r1.7.0 及以上版本的 JPush IOS SDK


### 页面的统计

#### 支持的版本

r1.7.0 版本开始。

#### 功能说明

本 API 用于“用户指定页面使用时长”的统计，并上报到服务器，在 Portal 上展示给开发者。页面统计集成正确，才能够获取正确的页面访问路径、访问深度（PV）的数据。

#### API

##### 接口定义

	+ (void)startLogPageView:(NSString*)pageName;
	+ (void)stopLogPageView:(NSString*)pageName;
	+ (void)beginLogPageView:(NSString*)pageName duration:(int)seconds;
	
##### 参数说明

+ pageName 需要统计页面自定义名称

+ duration 自定义的页面时间

##### 调用说明

 应在所有的需要统计得页面得 viewWillAppear 和 viewWillDisappear 加入 startLogPageView 和 stopLogPageView 来统计当前页面的停留时间。

> 或者直接使用 beginLogPageView 来自定义加入页面和时间信息。

#### 代码示例

	- (void)viewWillAppear:(BOOL)animated
	    {
	        [super viewWillAppear:animated];
	        [APService startLogPageView:@"PageOne"];
	    }
	- (void)viewWillDisappear:(BOOL)animated 
	    {
	        [super viewWillDisappear:animated];
	        [APService stopLogPageView:@"PageOne"];
	    }   
	－(void)trackView
	   {
	       [APService beginLogPageView:@"PageTwo" duration:10];
	   }


### 获取 OpenUDID

Apple 现在对 Device UDID 限制使用，我们使用 [OpenUDID](https://github.com/ylechelle/OpenUDID) 的方案来定义 UDID。

注：我们根据需要，对具体生成出来的内容有适当的调整。

	+ (NSString *)openUDID;
	
