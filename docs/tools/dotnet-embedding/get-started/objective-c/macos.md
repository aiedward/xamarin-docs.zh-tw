---
title: 開始使用 macOS
description: 本檔說明如何開始使用 .NET 內嵌搭配 macOS。 它會討論需求，並提供範例應用程式，以示範如何系結 managed 元件，並在 Xcode 專案中使用產生的輸出。
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: f0e2128bca5d2965395647353cd5a95a4030439f
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292710"
---
# <a name="getting-started-with-macos"></a>開始使用 macOS

## <a name="what-you-will-need"></a>您將需要

* 遵循[開始使用目標-C](~/tools/dotnet-embedding/get-started/objective-c/index.md)指南中的指示。

## <a name="hello-world"></a>Hello world

首先，在中C#建立簡單的 hello world 範例。

### <a name="create-c-sample"></a>建立C#範例

開啟 Visual Studio for Mac，建立名為**hello from csharp**的新 Mac 類別庫專案，並將其儲存至 **~/Projects/hello-from-csharp**。

將**MyClass.cs**檔案中的程式碼取代為下列程式碼片段：

```csharp
using AppKit;
public class MyNSView : NSTextView
{
    public MyNSView ()
    {
        Value = "Hello from C#";
    }
}
```

建置專案。 產生的元件將會儲存為 **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**。

### <a name="bind-the-managed-assembly"></a>系結 managed 元件

一旦您擁有 managed 元件，請叫用 .NET 內嵌來系結它。

如[安裝](~/tools/dotnet-embedding/get-started/install/install.md)指南中所述，您可以在專案中使用自訂的 MSBuild 目標，或以手動方式完成這項作業：

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=macOS-modern --abi=x86_64 --outdir=output -c --debug
```

架構會放在 **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**中。

### <a name="use-the-generated-output-in-an-xcode-project"></a>在 Xcode 專案中使用產生的輸出

開啟 Xcode，並建立新的 Cocoa 應用程式。 將它命名為**hello-csharp** ，然後選取 [**目標-C** ] 語言。

在搜尋工具中開啟 **~/Projects/hello-from-csharp/output**目錄，選取 [ **hello-from**]，將其拖曳至 [Xcode] 專案，並放在專案中的 [ **hello from csharp** ] 資料夾的正上方。

![拖放架構](macos-images/hello-from-csharp-mac-drag-drop-framework.png)

請確定已在彈出的對話方塊中核取 [**需要時複製專案**]，然後按一下 **[完成**]。

![視需要複製專案](macos-images/hello-from-csharp-mac-copy-items-if-needed.png)

選取 [ **hello from csharp** ] 專案，並流覽至 [ **hello from csharp** ] 目標的 **[一般**] 索引標籤。在 [**內嵌二進位**] 區段中，新增**hello-csharp. framework**。

![內嵌的二進位檔](macos-images/hello-from-csharp-mac-embedded-binaries.png)

開啟**ViewController**，並將內容取代為：

```objc
#import "ViewController.h"

#include "hello-from-csharp/hello-from-csharp.h"

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    MyNSView *view = [[MyNSView alloc] init];
    view.frame = CGRectMake(0, 200, 200, 200);
    [self.view addSubview: view];
}

@end
```

最後，執行 Xcode 專案，就會顯示如下所示的內容：

![在模擬器C#中執行的範例 Hello](macos-images/hello-from-csharp-mac.png)
