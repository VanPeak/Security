#Android 四大组件安全设置



##Activity

###私有Activity
	1.不声明taskAffinity
	2.不声明LaunchMode
	3.设置exported属性为false
	4.保证Intent发送时的安全性，确定Intent是来自本应用程序
	5.启动Activity的时候不设置FLAG_ACTIVITY_NEW_TASK
	6.使用显式Intent加指定的类的方式来调用一个Activity
	7.敏感性西放置在Intent的extra中发送
	8.在onActivityResult的时候需要对返回的数据小心处理

###公共Activity
	1.AndroidManifest.xml中exported属性设置为true	
	2.收到Intent的时候判断所传内容的合法性
	3.Finish的时候不放置敏感信息在Intent内

###伙伴Activity
	1.不声明taskAffinity
	2.不声明LaunchMode
	3.不添加intent-fillter设置exported属性为true
	4.使用白名单机制验证对方包名和用户签名
	5.处理对方来的Intent时候判断所传内容的合法性
	6.只返回公开信息给对方
	PS：对方启动伙伴Activity
	1.不使用FLAG_ACTIVITY_NEW_TASK
	2.extra中只放置公开和不敏感的信息
	3.使用显式Intent，具体到包名，类名
	4.使用startActivityForResult启动伙伴Activity，返回时可以做进一步校验

###内部Activity
	1.定义Activity调用权限，并设置权限级别为signature
	2.不声明taskAffinity
	3.不声明LaunchMode
	4.不添加intent-fillter设置exported属性为true
	5.验证调用者签名
	6.对通过Intent传输的数据做加密处理

##Broadcast Receiver
	如果只限于程序内部广播的注册和发送，可以使用LocalBroadcastManager，有如下好处：
	1.发送的广播只会在自己App内传播，不会泄露给其他App，确保隐私数据不会泄露
	2.其他APP无法向本APP发送对应广播
	3.比系统全局广播效率更高

###私有Broadcast Receiver
	1.不添加Intent-fillter，设置exported属性为false
	2.收到Intent信息时候判断所传内容的合法性
	3.尽量减少通过Intent传播敏感信息，处理完毕后终止广播

###公共Broadcast Receiver
	1.设置exported属性为true
	2.收到Intent信息时候判断所传内容的合法性
	3.return result时候不放置敏感信息

###内部Broadcast Receiver
	1.定义Receiver调用权限，并设置权限级别为signature
	2.设置exported属性为true
	3.静态、动态广播注册时候都需要声明对应调用权限

##Service

###私有Service
	1.设置exported属性为false
	2.发送和接收Intent时候注意数据敏感性和来源正确性
	3.确保是同一个应用内的Intent才可以放置敏感信息

###公共Service
	1.设置exported属性为true
	2.发送和接收Intent时候注意数据敏感性和来源正确性
	3.return时候不放置敏感信息

###合作Service
	1.设置exported属性为true
	2.发送和接收Intent时候注意数据敏感性和来源正确性
	3.return时候不放置敏感信息，特别注意敏感信息的接收对象

###内部Service
	1.定义Service调用权限，并设置权限级别为signature
	2.不添加Intent-fillter，设置exported属性为true
	3.在不同的内部应用中使用相同的签名
	4.使用Intent发送返回数据时候注意敏感数据的处理

##Content Provider

###私有Content Provider
	1.Android2.2版本之前不要使用
	2.设置exported属性为false
	3.只能在应用内部进行敏感信息的收发

###公共Content Provider
	1.在返回result时候不放置敏感信息
	2.设置exported属性为true
	3.核对接收的输入条件的正确性

###合作Content Provider
	1.设置exported属性为true
	2.校验使用方的包名和签名
	3.接收处理敏感信息时候多加确认
	4.确认为可以公开的信息才返回给调用者

###内部Content Provider
	1.定义Provider调用权限，并设置权限级别为signature
	2.设置该provider需要权限
	3.设置exported属性为true
	4.检查传入的应用的包名和签名
	5.确认避免因传入和返回信息泄露敏感数据

###部分Content Provider
	1.Android2.2版本之前不要使用
	2.设置exported属性为false
	3.指定允许访问的URI权限授予临时路径
	4.校验收到的请求数据安全，即使已经获得临时授权
	5.只返回可公开的数据内容
	6.设置给予置顶URI的Intent临时访问权限


##附录：
	android:LaunchMode=["standard" | "singleTop" | "singleTask" | "singleInstance"]
	android:taskAffinity=["string"]
	android:enabled=["true" | "false"]  //能否被系统实例化，默认为true。

	android:exported=["true" | "false"]  //是否其它应用组件能调用这个组件或同它交互，默认为true，默认值依赖于是否包含过滤器。