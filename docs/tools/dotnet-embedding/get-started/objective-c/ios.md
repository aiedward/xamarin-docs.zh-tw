---
title: 開始使用 iOS
description: 本文件說明如何開始使用.NET 內嵌 iOS。 它討論需求，並提供範例應用程式示範如何繫結 managed 組件，並在 Xcode 專案使用的輸出。
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 009772ac88ad57bab53fb71c9705b71f0f8acc8b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61318676"
---
# <a name="getting-started-with-ios"></a>開始使用 iOS

## <a name="requirements"></a>需求

除了從需求我們[Objective C 使用者入門](~/tools/dotnet-embedding/get-started/objective-c/index.md)指南中，您也需要：

* [Xamarin.iOS 10.11](https://visualstudio.microsoft.com/xamarin/)或更新版本

## <a name="hello-world"></a>Hello World

首先，建置在 C# 中的簡單的 hello world 範例。

### <a name="create-c-sample"></a>建立 C# 範例

開啟 Visual Studio for Mac 中，建立新的 iOS 類別庫專案，將其命名**hello 從 csharp**，並將它儲存 **~/Projects/hello-from-csharp**。

中的程式碼取代**MyClass.cs**檔案使用下列程式碼片段：

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

建置專案，並產生的組件就會儲存成 **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**。

### <a name="bind-the-managed-assembly"></a>繫結的 managed 組件

Managed 組件之後，將它繫結叫用.NET 內嵌。

中所述[安裝](~/tools/dotnet-embedding/get-started/install/install.md)指南中，這可以做為建置後步驟，在專案中，使用自訂的 MSBuild 目標，或以手動方式：

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

此架構將會置於 **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**。

### <a name="use-the-generated-output-in-an-xcode-project"></a>使用 Xcode 專案中產生的輸出

開啟 Xcode 中，建立新的 iOS 單一檢視應用程式，將其命名**hello 從 csharp**，然後選取**OBJECTIVE-C**語言。

開啟 **~/Projects/hello-from-csharp/output**目錄中搜尋工具，選取**hello 從 csharp.framework**、 將它拖曳至 Xcode 專案，以及卸除它正上方**hello 從 csharp**專案中的資料夾。

![將拖放 framework](ios-images/hello-from-csharp-ios-drag-drop-framework.png)

請確定**複製的項目，視**簽入的對話方塊中出現，然後按一下**完成**。

![如有需要請將複製的項目](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

選取  **hello 從 csharp**專案，然後瀏覽至**hello 從 csharp**目標**一般 索引標籤**。在 **內嵌二進位**區段中，新增**hello 從 csharp.framework**。

![內嵌的二進位檔案](ios-images/hello-from-csharp-ios-embedded-binaries.png)

開啟**ViewController.m**，並取代具有內容：

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

.NET 內嵌目前不支援 bitcode 在 iOS 上，啟用一些 Xcode 專案範本。 

停用它在您的專案設定：

![Bitcode 選項](../../images/ios-bitcode-option.png)

最後，執行 Xcode 專案中，並如下所示將會顯示：

![從 C# 範例在模擬器中執行 hello](ios-images/hello-from-csharp-ios.png)
