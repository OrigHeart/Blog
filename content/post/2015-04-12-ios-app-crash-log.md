+++
categories = ["杂七杂八"
]
tags = [
]
date = "2015-04-12T13:30:27+08:00"
title = "iOS捕获app crash日志的Api"

+++

定义uncaughtExceptionHandler方法:

~~~java
void uncaughtExceptionHandler( NSException   *exception) 
{
    // 异常的堆栈信息
    NSArray *stackArray = [exception callStackSymbols];
    // 出现异常的原因
    NSString *reason = [exception reason];
    // 异常名称
    NSString *name = [exception name];
    NSString *exceptionInfo = [NSString stringWithFormat:@"Exception reason：%@\nException name：%@\nException stack：%@",name, reason, stackArray];
    NSLog(@"%@", exceptionInfo);
}
~~~

调用方法的时机:

~~~java
- (BOOL) application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
{
   // 设置处理异常的Handler
    NSSetUncaughtExceptionHandler(&uncaughtExceptionHandler);
}
~~~

