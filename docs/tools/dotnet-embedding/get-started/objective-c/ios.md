---
title: IOS 使用者入門
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 34afdd9e91ebfbe7ad57c7eec6ba7f05fff1a2aa
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="getting-started-with-ios"></a>IOS 使用者入門


## <a name="requirements"></a>需求

除了從需求我們[入門 OBJECTIVE-C](~/tools/dotnet-embedding/get-started/objective-c/index.md)指南也需要：

* [Xamarin.iOS 10.11](https://www.visualstudio.com/xamarin/)或更新版本

## <a name="hello-world"></a>Hello World

第一個讓我們來建置簡單的 hello world 範例中，在 C#。

### <a name="create-c-sample"></a>建立 C# 範例

開啟 Visual Studio for Mac、 建立新的 iOS 類別庫專案，其命名`hello-from-csharp`，並將它儲存`~/Projects/hello-from-csharp`。

中的程式碼取代`MyClass.cs`檔案使用下列程式碼片段：

```csharp
using UIKit;
public class MyUIView : UITextView
{
    public MyUIView ()
    {
        Text = "Hello from C#";
    }
}
```

建置專案，產生的組件會儲存為`~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll`。

### <a name="bind-the-managed-assembly"></a>繫結的 managed 組件

執行的 managed 組件建立原生架構 embeddinator:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

此架構將會放置於`~/Projects/hello-from-csharp/output/hello-from-csharp.framework`。

### <a name="use-the-generated-output-in-an-xcode-project"></a>在 Xcode 專案中使用產生的輸出

開啟 Xcode 並建立新的 iOS 單一檢視應用程式，其命名`hello-from-csharp`選取**OBJECTIVE-C**語言。

開啟`~/Projects/hello-from-csharp/output`目錄中尋找工具中，選取`hello-from-csharp.framework`、 將它拖曳到 Xcode 專案，以及卸除它正上方`hello-from-csharp`專案資料夾中的。

![拖曳和卸除 framework]Images/hello-from-csharp-ios-drag-drop-framework.png)

請確定`Copy items if needed`簽入顯的對話方塊中，按一下  `Finish`。

![如果需要，複製的項目](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

選取`hello-from-csharp`專案，並瀏覽至`hello-from-csharp`目標的**一般 索引標籤**。在**內嵌二進位**區段中，新增`hello-from-csharp.framework`。

![內嵌的二進位檔](ios-images/hello-from-csharp-ios-embedded-binaries.png)

開啟 ViewController.m，並取代具有內容：

```objective-c
#import "ViewController.h"
#include "hello-from-csharp/hello-from-csharp.h"

@interface ViewController ()
@end

@implementation ViewController
- (void)viewDidLoad {
    [super viewDidLoad];

    MyUIView *view = [[MyUIView alloc] init];
    view.frame = CGRectMake(0, 200, 200, 200);
    [self.view addSubview: view];
}
@end
```

最後執行 Xcode 專案，並像下面這樣將會顯示：

![在模擬器中執行的 C# 範例中的 hello](ios-images/hello-from-csharp-ios.png)
