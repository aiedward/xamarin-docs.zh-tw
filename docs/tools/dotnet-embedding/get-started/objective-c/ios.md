---
title: 開始使用 iOS
description: 本檔說明如何開始使用搭配 iOS 的 .NET 內嵌。 它會討論需求並提供範例應用程式，以示範如何系結 managed 元件，並使用 Xcode 專案中的輸出。
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: d5bde89ed90e55724fbc25fc473e265affa9ce2f
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292935"
---
# <a name="getting-started-with-ios"></a>開始使用 iOS

## <a name="requirements"></a>需求

除了[開始使用《目標-C](~/tools/dotnet-embedding/get-started/objective-c/index.md)指南》的需求之外，您還需要：

* [Xamarin. iOS 10.11](https://visualstudio.microsoft.com/xamarin/)或更新版本

## <a name="hello-world"></a>Hello World

首先，在中C#建立簡單的 hello world 範例。

### <a name="create-c-sample"></a>建立C#範例

開啟 Visual Studio for Mac，建立新的 iOS 類別庫專案，將它命名為**hello-csharp**，並將其儲存至 **~/Projects/hello-from-csharp**。

將**MyClass.cs**檔案中的程式碼取代為下列程式碼片段：

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

建立專案，產生的元件將會儲存為 **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**。

### <a name="bind-the-managed-assembly"></a>系結 managed 元件

一旦您擁有 managed 元件，請叫用 .NET 內嵌來系結它。

如[安裝](~/tools/dotnet-embedding/get-started/install/install.md)指南中所述，您可以在專案中使用自訂的 MSBuild 目標，或以手動方式完成這項作業：

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

架構會放在 **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**中。

### <a name="use-the-generated-output-in-an-xcode-project"></a>在 Xcode 專案中使用產生的輸出

開啟 Xcode，建立新的 iOS 單一視圖應用程式，將它命名**為 hello-csharp**，然後選取 [**目標-C** ] 語言。

在搜尋工具中開啟 **~/Projects/hello-from-csharp/output**目錄，選取 [ **hello-from**]，將其拖曳至 [Xcode] 專案，並放在專案中的 [ **hello from csharp** ] 資料夾的正上方。

![拖放架構](ios-images/hello-from-csharp-ios-drag-drop-framework.png)

請確定已在彈出的對話方塊中核取 [**需要時複製專案**]，然後按一下 **[完成**]。

![視需要複製專案](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

選取 [ **hello from csharp** ] 專案，並流覽至 [ **hello from csharp** ] 目標的 **[一般]** 索引標籤。在 [**內嵌二進位**] 區段中，新增**hello-csharp. framework**。

![內嵌的二進位檔](ios-images/hello-from-csharp-ios-embedded-binaries.png)

開啟**ViewController**，並將內容取代為：

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

.NET 內嵌目前不支援 iOS 上的 bitcode，其已針對某些 Xcode 專案範本啟用。 

在您的專案設定中將它停用：

![Bitcode 選項](../../images/ios-bitcode-option.png)

最後，執行 Xcode 專案，就會顯示如下所示的內容：

![在模擬器C#中執行的範例 Hello](ios-images/hello-from-csharp-ios.png)
