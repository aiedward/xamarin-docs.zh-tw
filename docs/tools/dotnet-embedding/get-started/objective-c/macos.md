---
title: 開始使用 macOS
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 85510168f37e724563eae59dfca438177b4feffe
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="getting-started-with-macos"></a>開始使用 macOS

## <a name="what-you-will-need"></a>您必須

* 遵循指示[入門 OBJECTIVE-C](~/tools/dotnet-embedding/get-started/objective-c/index.md)指南。

## <a name="hello-world"></a>Hello World

首先，建立 C# 中的簡單的 hello world 範例。

### <a name="create-c-sample"></a>建立 C# 範例

開啟 Visual Studio for Mac、 建立新的 Mac 類別庫專案，名為**hello 從 csharp**，並將它儲存 **~/Projects/hello-from-csharp**。

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

建置專案。 產生的組件會儲存為 **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**。

### <a name="bind-the-managed-assembly"></a>繫結的 managed 組件

Managed 組件之後，將它繫結叫用.NET 內嵌。

中所述[安裝](~/tools/dotnet-embedding/get-started/install/install.md)指南中，如此可以做為建置後步驟，在專案中，以自訂的 MSBuild 目標，或以手動方式：

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=macOS-modern --abi=x86_64 --outdir=output -c --debug
```

此架構將會放置於 **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**。

### <a name="use-the-generated-output-in-an-xcode-project"></a>在 Xcode 專案中使用產生的輸出

開啟 Xcode，並建立新的 Cocoa 應用程式。 命名**hello 從 csharp**選取**OBJECTIVE-C**語言。

開啟 **~/Projects/hello-from-csharp/output**目錄中尋找工具中，選取**hello 從 csharp.framework**、 將它拖曳到 Xcode 專案，以及卸除它正上方**hello 從 csharp**專案資料夾中的。

![拖曳和卸除架構](macos-images/hello-from-csharp-mac-drag-drop-framework.png)

請確定**複製的項目，視**簽入顯的對話方塊中，按一下 **完成**。

![如果需要，複製的項目](macos-images/hello-from-csharp-mac-copy-items-if-needed.png)

選取**hello 從 csharp**專案，並瀏覽至**hello 從 csharp**目標的**一般** 索引標籤。在**內嵌二進位**區段中，新增**hello 從 csharp.framework**。

![內嵌的二進位檔](macos-images/hello-from-csharp-mac-embedded-binaries.png)

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

最後，執行 Xcode 專案，並像下面這樣將會顯示：

![在模擬器中執行的 C# 範例中的 hello](macos-images/hello-from-csharp-mac.png)

更完整且外觀較佳的範例[這裡會提供](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather)。
