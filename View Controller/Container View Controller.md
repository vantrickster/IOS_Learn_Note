### 以编程的形式添加一个子ViewController

***

1. 调用容器ViewController的`addChildViewController:`方法(引发子ViewController自动调用`willMoveToParentViewController:`)
2. 把子ViewController的根视图添加到容器ViewController的视图体系中(记住要设置子ViewController根视图的**frame**)
3. 添加你对于子ViewController根视图想要添加的任何约束
4. 调用子ViewController的`didMoveToParentViewController:`方法

```
- (void) displayContentController: (UIViewController*) content {
   [self addChildViewController:content];
   content.view.frame = [self frameForContentController];
   [self.view addSubview:self.currentClientView];
   [content didMoveToParentViewController:self];
}
```

### 以编程的形式移除一个子ViewController

***

1. 调用子ViewController的`willMoveToParentViewController:`，参数为**nil**
2. 移除子ViewController根视图上的任何约束
3. 从容器ViewController的视图体系中移除子ViewController的根视图
4. 调用子ViewController的`removeFromParentViewController`方法

```
- (void) hideContentController: (UIViewController*) content {
   [content willMoveToParentViewController:nil];
   [content.view removeFromSuperview];
   [content removeFromParentViewController];
}
```

### 管理子ViewController的外观更新(Appearance Updates)

***

向容器ViewController添加了子ViewController后，容器会自动转接(forwards)外观相关的信息给孩子。然后，有时候默认行为可能按照某种顺序发送这些事件而不能满足你的要求。比如，当多个孩子同时改变视图状态时，你可能想让这些外观回调能以一种更有逻辑的顺序同时发生。
如果想接管外观回调，需要重写容器ViewController的`shouldAutomaticallyForwardAppearanceMethods`方法，让它返回**NO**，让**UIKit**知道在孩子外观发生变化时，容器会通知它们。
当外观转变(appearance transition)发生时，适时调用子ViewController的`beginAppearanceTransition:animated:`或者`endAppearanceTransition`方法。

```
// 当容器出现或者消失时转发外观消息
-(void) viewWillAppear:(BOOL)animated {
    [self.child beginAppearanceTransition: YES animated: animated];
}
 
-(void) viewDidAppear:(BOOL)animated {
    [self.child endAppearanceTransition];
}
 
-(void) viewWillDisappear:(BOOL)animated {
    [self.child beginAppearanceTransition: NO animated: animated];
}

-(void) viewDidDisappear:(BOOL)animated {
    [self.child endAppearanceTransition];
}
```








