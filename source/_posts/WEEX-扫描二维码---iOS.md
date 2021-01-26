---
title: "WEEX 扫描二维码 - iOS"
categories: [ "JS" ]
tags: [ "qrcode","weex","ios" ]
draft: false
slug: "weex-scan-twodimensional-code-ios"
date: "2017-09-09 13:23:00"
---

## Weex *.vue 文件中的调用方法

```
scan = weex.requireModule('jd-scan')

scan.scanCode(res=>{
	if(res.result){
		console.log(res.data)
	}
})

```


<!--more-->


## iOS 的原生实现

JDScan.h

```
#ifndef JDScan_h
#define JDScan_h

#import <WeexSDK/WeexSDK.h>

typedef void (^CallBlock)(NSDictionary*); // 定义回调函数
@interface JDScanModule :NSObject<WXModuleProtocol>
@end

	
#endif /* JDScan_h */

```

JDScan.m

```
#import "JDScan.h"
#import "JDScanViewController.h"

@implementation JDScanModule
@synthesize weexInstance; 

WX_EXPORT_METHOD(@selector(scanCode:))

-(void)scanCode:(CallBlock)callback{
    // 下面这个是我定义的扫描二维码的原生类
    JDScanViewController *scanVC = [[JDScanViewController alloc]init];
    // 将回调函数传递给scanVC 视图控制器,如果扫描到二维码将值传递给此闭包函数
    scanVC.callback = callback;
    [self.weexInstance.viewController.navigationController     presentViewController:scanVC animated:true completion:nil];
}
@end

```

JDScanViewController.h

```
#import <UIKit/UIKit.h>
#import <AVFoundation/AVFoundation.h>
#import <WeexSDK/WeexSDK.h>

@interface JDScanViewController : UIViewController <AVCaptureMetadataOutputObjectsDelegate>

@property(nonatomic,copy)WXModuleCallback callback;

@end

```

JDScanViewController.m

```
#import "JDScanViewController.h"
#import <AVFoundation/AVFoundation.h>

@interface JDScanViewController ()     <AVCaptureMetadataOutputObjectsDelegate>
@property (nonatomic, strong) AVCaptureSession * session;
@property (nonatomic, strong) AVCaptureVideoPreviewLayer *captureLayer;
@property (nonatomic, strong) UIView *sanFrameView;
@end

@implementation JDScanViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    [self setup];
}

-(void)setup{
#if !(TARGET_IPHONE_SIMULATOR)
    self.session = [[AVCaptureSession alloc]init];
    [_session setSessionPreset:AVCaptureSessionPresetHigh];
    AVCaptureDevice * device = [AVCaptureDevice defaultDeviceWithMediaType:AVMediaTypeVideo];
    AVCaptureDeviceInput * input = [AVCaptureDeviceInput deviceInputWithDevice:device error:nil];
    AVCaptureMetadataOutput * output = [[AVCaptureMetadataOutput alloc]init];
    if (output && input && device) {
        [output setMetadataObjectsDelegate:self queue:dispatch_get_main_queue()];
        [_session addInput:input];
        [_session addOutput:output];
        output.metadataObjectTypes=@[AVMetadataObjectTypeQRCode,AVMetadataObjectTypeEAN13Code, AVMetadataObjectTypeEAN8Code, AVMetadataObjectTypeCode128Code];
    }
    
    _captureLayer = [AVCaptureVideoPreviewLayer layerWithSession:_session];
    _captureLayer.videoGravity=AVLayerVideoGravityResizeAspectFill;
    _captureLayer.frame=self.view.layer.bounds;
#endif
    
    [self.view.layer addSublayer:_captureLayer];
    UIButton *button = [[UIButton alloc]initWithFrame:CGRectMake(self.view.bounds.size.width/2-25, self.view.bounds.size.height-100, 50, 50)];
    //[button setBackgroundImage:[UIImage imageNamed:@"eye.png"] forState:UIControlStateNormal];
    [button setTitle:@"取消" forState:UIControlStateNormal];
    [button addTarget:self action:@selector(back) forControlEvents:UIControlEventTouchUpInside];
    [self.view addSubview:button];
}

-(void)back{
    [self dismissViewControllerAnimated:true completion:nil];
}
- (void)captureOutput:(AVCaptureOutput *)captureOutput     didOutputMetadataObjects:(NSArray *)metadataObjects     fromConnection:(AVCaptureConnection *)connection
{
    [_captureLayer removeFromSuperlayer];
    [_session stopRunning];
    AudioServicesPlaySystemSound(kSystemSoundID_Vibrate);
    if (metadataObjects.count > 0) {
        AVMetadataMachineReadableCodeObject * metadataObject = [metadataObjects objectAtIndex : 0 ];
        self.callback(@{@"result":@"true",@"data":metadataObject.stringValue});
        [self dismissViewControllerAnimated:true completion:nil];
    }
}
-(void)viewWillAppear:(BOOL)animated{
    [_session startRunning];
}
- (void)dealloc {
    [_captureLayer removeFromSuperlayer];
}

@end

```

AppDelegate.m

```
[WXSDKEngine registerModule:@"jd-scan" withClass:[JDScanModule class]];
```