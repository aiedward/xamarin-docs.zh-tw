---
title: 使用 Xamarin.Mac 繫結的主控台應用程式
description: 建立遠端控制的主控台應用程式來存取原生 macOS Api 使用 Xamarin.Mac。
ms.prod: xamarin
ms.assetid: 9E52B4CC-F530-4B3E-984A-7F5719A6D528
ms.technology: xamarin-mac
author: migueldeicaza
ms.author: miguel
ms.date: 07/27/2018
ms.openlocfilehash: 135ef06cd044235023c3acc970c8e7a33f144b47
ms.sourcegitcommit: d0da5ce4158239abd2b36f67550e9b475055766a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/27/2018
ms.locfileid: "39320821"
---
# <a name="xamarinmac-bindings-in-console-apps"></a>在主控台應用程式中的 Xamarin.Mac 繫結

某些情況下，您要使用 Apple 原生 Api 的一些 C# 中建立遠端控制的應用程式&ndash;沒有使用者介面的其中一個&ndash;使用 C#。

Mac 應用程式的專案範本包含呼叫`NSApplication.Init()`後面接著呼叫`NSApplication.Main(args)`，通常看起來像這樣：

```csharp
static class MainClass {
    static void Main (string [] args)
    {
        NSApplication.Init ();
        NSApplication.Main (args);
    }
}
```

在呼叫`Init`準備 Xamarin.Mac 執行階段呼叫`Main(args)`啟動 Cocoa 應用程式主迴圈，這會準備應用程式接收鍵盤和滑鼠事件，並顯示您的應用程式的主視窗。   在呼叫`Main`也會嘗試找出 Cocoa 資源，準備的最上層視窗，並預期應用程式套件組合的一部分的程式 (分散式目錄中的程式`.app`延伸模組和非常特殊的版面配置)。

遠端控制的應用程式不需要使用者介面，並不需要執行的應用程式套件組合的一部分。

## <a name="creating-the-console-app"></a>建立主控台應用程式

因此最好是以開頭為一般的.NET 主控台專案類型。

您需要執行一些作業：

- 建立空的專案。
- Xamarin.Mac.dll 程式庫的參考。
- 帶入您的專案中的非受控相依性。

在下面詳細說明這些步驟：

### <a name="create-an-empty-console-project"></a>建立空白的主控台專案

建立新的.NET 主控台專案，請確定它是.NET 並不是.NET Core，為 Xamarin.Mac.dll 不會執行.NET Core 執行階段下，它只會執行與 Mono 執行階段。

### <a name="reference-the-xamarinmac-library"></a>Xamarin.Mac 程式庫參考

若要編譯您的程式碼，您會想要參考`Xamarin.Mac.dll`從這個目錄的組件： `/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full`

若要這樣做，請移至的專案，選取 **.NET 組件**索引標籤，然後按一下**瀏覽**按鈕找出檔案系統上的檔案。  瀏覽至上面的路徑，然後選取**Xamarin.Mac.dll**從該目錄。

這會在編譯時期 Cocoa Api 讓您存取。   此時，您可以在其中加入`using AppKit`至您的檔案，並呼叫頂端`NSApplication.Init()`方法。   您可以執行您的應用程式之前，還有只能有一個步驟。

### <a name="bring-the-unmanaged-support-library-into-your-project"></a>未受管理的支援程式庫帶入您的專案

將執行應用程式之前，您必須將`Xamarin.Mac`支援程式庫到您的專案。   若要這樣做，將新檔案新增至您的專案 (專案選項中選取**新增**，然後**加入現有的檔案**)，並瀏覽到這個目錄：

`/Library/Frameworks/Xamarin.Mac.framework/Versions/Current/lib`

在這裡，選取 檔案**libxammac.dylib**。   您可以選擇複製、 連結或移動。   我個人要連結，但是複製同樣能夠運作。    您必須選取檔案，並在 [屬性] 面板中 (選取**檢視 > 板 > 屬性**如果看不到 [屬性] 面板)，請移至**建置**區段，並設定**複製到輸出目錄**設為**才複製**。

您現在可以執行您的 Xamarin.Mac 應用程式。

Bin 目錄中的結果會如下所示：

```
Xamarin.Mac.dll
Xamarin.Mac.pdb
consoleapp.exe
consoleapp.pdb
libxammac.dylib
```

若要執行此應用程式，您必須使用相同的目錄中的所有這些檔案。

## <a name="building-a-standalone-application-for-distribution"></a>建立發佈的獨立應用程式

您可能想要散發給使用者的單一可執行檔。  若要這樣做，您可以使用`mkbundle`工具來將各種檔案轉換成獨立的可執行檔。

首先，請確定您的應用程式編譯並執行。   一旦您滿意結果時，您可以從命令列來執行下列命令：

```
$ mkbundle --simple -o /tmp/consoleapp consoleapp.exe --library libxammac.dylib --config /Library/Frameworks/Mono.framework/Versions/Current/etc/mono/config --machine-config /Library/Frameworks/Mono.framework/Versions/Current//etc/mono/4.5/machine.config
[Output from the bundling tool]
$ _
```

上述命令列引動過程中，選擇`-o`是用來指定所產生的輸出，在此情況下，我們傳遞`/tmp/consoleapp`。   這現在是獨立應用程式可散發和 Mono 上 Xamarin.Mac 沒有外部相依性，它是完全獨立式可執行檔。

手動指定的命令列**machine.config**来使用檔案和全系統程式庫對應組態檔。   它們不是必要的所有應用程式，但因為它們會使用於當您使用.NET 的更多的功能很方便地組合，

## <a name="project-less-builds"></a>無專案的組建

您不需要完整的專案，來建立獨立的 Xamarin.Mac 應用程式，您也可以使用簡單的 Unix makefile，來完成工作。   下列範例會示範如何設定簡單的命令列應用程式的 makefile:

```
XAMMAC_PATH=/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full/
DYLD=/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib
MONODIR=/Library/Frameworks/Mono.framework/Versions/Current/etc/mono

all: consoleapp.exe

consoelapp.exe: consoleapp.cs Makefile
    mcs -g -r:$(XAMMAC_PATH)/Xamarin.Mac.dll consoleapp.cs
    
run: consoleapp.exe
    MONO_PATH=$(XAMMAC_PATH) DYLD_LIBRARY_PATH=$(DYLD) mono --debug consoleapp.exe $(COMMAND)


bundle: consoleapp.exe
    mkbundle --simple consoleapp.exe -o ncsharp -L $(XAMMAC_PATH) --library $(DYLD)/libxammac.dylib --config $(MONODIR)/config --machine-config $(MONODIR)/4.5/machine.config
```

上述`Makefile`提供三個目標：

- `make` 將會建置程式
- `make run` 建置並執行目前目錄中的程式
- `make bundle` 將會建立獨立的可執行檔
