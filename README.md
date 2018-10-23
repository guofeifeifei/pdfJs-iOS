![pdf.jpg](https://upload-images.jianshu.io/upload_images/6694822-a2a15bbfd7b8ab49.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
加载pdf文件一般我们会使用UIWebView、WKWebView来加载pdf，但是当pdf内含有签名以及电子章的时候，电子签章和签名将无法显示出来。原因是：在PDF文件中看到的签名是签名字段小部件的外观。字段小部件是特定的PDF注释。iOS中的默认PDF呈现引擎（由UIDocumentInteractionController，QuickLook框架等使用）仅显示主页面内容，它不显示PDF页面上的注释。这时候就需要我们使用其他第三方PDF渲染引擎了。

pdf.js在 HTML5 平台上展示 PDF 文档。支持web、iOS、Android端(android 手机微信端不支持)。
1.创建iOS工程，将minified复制到工程目录中。
![minified复制到工程目录.png](https://upload-images.jianshu.io/upload_images/6694822-8d47005d26d43bd4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.拖拽复制的文件目录到工程中，选择folder references（亲测选择groups没有效果，原因未知）：

![拖拽复制的文件目录到工程中.png](https://upload-images.jianshu.io/upload_images/6694822-af8d5335ac87cbda.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.新建PdfLoadWebView继承自UIWebView，添加如下代码：
```
- (void)loadPDFFile:(NSString*)filePath {
    _filePath = filePath;
  
    NSString *viwerPath = [[NSBundle mainBundle] pathForResource:@"viewer" ofType:@"html" inDirectory:@"minified/web"];
    NSString *urlStr = [NSString stringWithFormat:@"%@?file=%@#page=1",viwerPath,filePath];
    urlStr = [urlStr stringByAddingPercentEncodingWithAllowedCharacters:[NSCharacterSet URLQueryAllowedCharacterSet]];
    NSURLRequest *request = [NSURLRequest requestWithURL:[NSURL URLWithString:urlStr]];
    [self loadRequest:request];
}
```
在控制器里添加如下代码：
```
  webView = [[PdfLoadWebView alloc] initWithFrame:self.view.bounds];
    [self.view addSubview:webView];
    NSString *pdfFilePath = [[NSBundle mainBundle] pathForResource:@"git搭建" ofType:@"pdf"];
    [webView loadPDFFile:pdfFilePath];
```
运行后效果如图：
![运行后效果.png](https://upload-images.jianshu.io/upload_images/6694822-c4e7c757ae756e6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注释：
1.因为项目中我只需要展示pdf，所以就将对pdf的操作按钮隐藏掉了。如果需要按钮将minified->web->viewer.html 中
```
<div class="toolbar" style="display: none;">
```
代码修改成
```
<div class="toolbar">

```
![viewer.png](https://upload-images.jianshu.io/upload_images/6694822-bfc6e441a582caa9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.项目中需要用到pdf滚到到底部或者头部，所以我在viewer.html写了两个js方法以供oc进行调用。
viewer.html文件中
```
<script type="text/javascript">
        function lastPage() {
            document.getElementById("lastPage").click();
        }
    function firstPage() {
        document.getElementById("firstPage").click();
    }
```
在PdfLoadWebView.m中进行oc调用js方法
```
        
[self stringByEvaluatingJavaScriptFromString:@"lastPage()"];

```
因为我实在页面加载完成后立即进行页面跳转。所以加了一个延时操作

现在加载的是本地pdf，加载网络pdf还需要继续研究。
[DEMO](https://github.com/guofeifeifei/pdfJs-iOS/tree/0232ea847193e135993c4bfed7de54f7e70b1f56)
[iOS展示pdf签名时遇到的问题及解决办法](https://link.jianshu.com/?t=https%3A%2F%2Fblog.csdn.net%2FlYcHeeMMM%2Farticle%2Fdetails%2F78783487)
[iOS使用pdf.js打开PDF文件](https://www.jianshu.com/p/ded81b392d4d)
[iOS 加载PDF问题无法显示电子章问题](https://www.jianshu.com/p/fd5f248a8158)
