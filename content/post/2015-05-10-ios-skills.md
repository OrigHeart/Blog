+++
categories = ["iOS能工巧匠集"
]
tags = ["小技巧"
]
date = "2015-05-10T15:30:27+08:00"
title = "iOS常用小技巧（经常更新）"

+++


### 循环效果:

~~~java
self.count++;
self.count %= 4;
~~~

可以实现0～4的循环效果，替换了

~~~java
if(self.count==4) self.count = 0;
~~~

### 计算动画当前移动的值：（相似三角形）
开始值 + 当前帧数 * (结束值 - 开始值) / (帧频 * 动画时长)

### iOS7下不让view渗透到NavigationBar或TabBar的下面：

~~~java
if ([self respondsToSelector:@selector(edgesForExtendedLayout)]) {
        self.edgesForExtendedLayout = UIRectEdgeNone;
}
~~~

### 自定义控件
* 创建一个继承UIView的一个类
* 创建一个空的Xib名称根类名称一样
* 往xib里面拖一个UIView把UIView的Class修改为自定义的类型
* 往这个View里面拖进各种控件
* 把里面的控件根代码进行关联
* 在用到的地方用[NSBundle mainBundle]来加载控件 切记不要用initWithFrame的方法来加载

~~~java
TRHeaderView *hv = [[[NSBundle mainBundle]loadNibNamed:@"TRHeaderView" owner:self options:Nil]lastObject];
    hv.wb = wb;
    self.tableView.tableHeaderView = hv;
~~~

### 在scrollView里面整齐的放入表情，类似于QQ的表情放置方法：

~~~java
- (void)viewDidLoad
{
    [super viewDidLoad];
 
    self.faces = [NSArray arrayWithContentsOfFile:@"/Users/liukang/Main/Origheart/face/emoticons.plist"];
    
    for (int i=0; i<self.faces.count; i++) {
        NSDictionary *faceDic = self.faces[i];
        NSString *imageName = [faceDic objectForKey:@"png"];
        UIButton *btn = [[UIButton alloc]initWithFrame:CGRectMake(i%8*40, i/8*40, 40, 40)];
        btn.tag = i;
        [btn setImage:[UIImage imageNamed:imageName] forState:UIControlStateNormal];
        [btn addTarget:self action:@selector(clicked:) forControlEvents:UIControlEventTouchUpInside];
        [self.mySV addSubview:btn];
        int row = self.faces.count%8==0?self.faces.count/8 :self.faces.count/8+1;
        [self.mySV setContentSize:CGSizeMake(0, row*40)];
    }
}
-(void)clicked:(UIButton *)btn{
    NSDictionary *faceDic = self.faces[btn.tag];
    NSString *text = [faceDic objectForKey:@"chs"];
    
    self.myTF.text = [self.myTF.text stringByAppendingString:text];
}
~~~

### 用storyboard，当启动次数(记录在Userdefaults中)是偶数的时候将根视图控制器设置为其他VC

~~~java
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    NSUserDefaults *ud = [NSUserDefaults standardUserDefaults];
    int runTimes = [ud integerForKey:@"runTimes"];
    
    if (runTimes%2==0) {
        UIViewController *vc = [self.window.rootViewController.storyboard instantiateViewControllerWithIdentifier:@"greenvc"];
        
        self.window.rootViewController = vc;

    }
    [ud setInteger:++runTimes forKey:@"runTimes"];
    [ud synchronize];
    return YES;
}
~~~

### present多个view controller后，回到第一个view controller的方法：
present: A->B->C dismiss:C->A,不显示出B：
在B的viewDidLoad里面加入收听通知，实现响应方法，并在适当的地方移除收听：

~~~java
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(back:) name:@"TestComplete" object:nil];
- (IBAction)back:(id)sender {
    [self dismissViewControllerAnimated:YES completion:nil];
}
- (void)dealloc
{
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}
~~~

在C dismiss的时候发送通知：

~~~java
[[NSNotificationCenter defaultCenter] postNotificationName:@"TestComplete" object:nil];
~~~

