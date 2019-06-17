---
title: MacOS 使用者入門
description: 本文件說明如何開始使用.NET 內嵌 macOS。 它討論需求，而範例應用程式示範如何繫結的 managed 組件，並在 Xcode 專案中使用產生的輸出。
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 3de47aa57df29f52f71508977ae017dff009c282
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61181520"
---
# <a name="getting-started-with-macos"></a>MacOS 使用者入門

## <a name="what-you-will-need"></a>您將需要

* 遵循指示[Objective C 使用者入門](~/tools/dotnet-embedding/get-started/objective-c/index.md)指南。

## <a name="hello-world"></a>Hello World

首先，建置在 C# 中的簡單的 hello world 範例。

### <a name="create-c-sample"></a>建立 C# 範例

開啟 Visual Studio for Mac 中，建立新的 Mac 類別庫專案，名為**hello 從 csharp**，並將它儲存 **~/Projects/hello-from-csharp**。

中的程式碼取代**MyClass.cs**檔案使用下列程式碼片段：

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

建置專案。 產生的組件就會儲存成 **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**。

### <a name="bind-the-managed-assembly"></a>繫結的 managed 組件

Managed 組件之後，將它繫結叫用.NET 內嵌。

中所述[安裝](~/tools/dotnet-embedding/get-started/install/install.md)指南中，這可以做為建置後步驟，在專案中，使用自訂的 MSBuild 目標，或以手動方式：

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=macOS-modern --abi=x86_64 --outdir=output -c --debug
```

此架構將會置於 **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**。

### <a name="use-the-generated-output-in-an-xcode-project"></a>使用 Xcode 專案中產生的輸出

開啟 Xcode，然後建立新的 Cocoa 應用程式。 命名**hello 從 csharp** ，然後選取**OBJECTIVE-C**語言。

開啟 **~/Projects/hello-from-csharp/output**目錄中搜尋工具，選取**hello 從 csharp.framework**、 將它拖曳至 Xcode 專案，以及卸除它正上方**hello 從 csharp**專案中的資料夾。

![將拖放 framework](macos-images/hello-from-csharp-mac-drag-drop-framework.png)

請確定**複製的項目，視**簽入的對話方塊中出現，然後按一下**完成**。

![如有需要請將複製的項目](macos-images/hello-from-csharp-mac-copy-items-if-needed.png)

選取  **hello 從 csharp**專案，然後瀏覽至**hello 從 csharp**目標**一般** 索引標籤。在 **內嵌二進位**區段中，新增**hello 從 csharp.framework**。

![內嵌的二進位檔案](macos-images/hello-from-csharp-mac-embedded-binaries.png)

開啟**ViewController.m**，並取代具有內容：

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

最後，執行 Xcode 專案中，並如下所示將會顯示：

![從 C# 範例在模擬器中執行 hello](macos-images/hello-from-csharp-mac.png)

更完整且外觀較佳的範例[，請參閱這裡](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather)。
