---
title: 使用主控台應用程式的 Xamarin 系結
description: 使用 Xamarin 建立無周邊主控台應用程式來存取原生 macOS Api。
ms.prod: xamarin
ms.assetid: 9E52B4CC-F530-4B3E-984A-7F5719A6D528
ms.technology: xamarin-mac
author: migueldeicaza
ms.author: miguel
ms.date: 07/27/2018
ms.openlocfilehash: a38543d575f655a3b1b70ff94eece7fef1bf2d40
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769900"
---
# <a name="xamarinmac-bindings-in-console-apps"></a>主控台應用程式中的 Xamarin 系結

在某些C#情況下，您會想要使用中的一些 Apple 原生 api，來建立&ndash;無周邊應用程式，其中沒有使用&ndash; C#的使用者介面。

Mac 應用程式的專案範本包含呼叫`NSApplication.Init()` `NSApplication.Main(args)`，後面接著呼叫，其通常如下所示：

```csharp
static class MainClass {
    static void Main (string [] args)
    {
        NSApplication.Init ();
        NSApplication.Main (args);
    }
}
```

呼叫以`Init`準備 Xamarin 執行時間，呼叫以`Main(args)`啟動 Cocoa 應用程式主要迴圈，這可準備應用程式以接收鍵盤和滑鼠事件，並顯示應用程式的主視窗。   對的呼叫`Main`也會嘗試找出 Cocoa 資源、準備 toplevel 視窗，並預期程式會成為應用程式套件組合的一部分（以`.app`延伸模組和非常特定配置的目錄散發的程式）。

無周邊應用程式不需要使用者介面，也不需要在應用程式套件組合中執行。

## <a name="creating-the-console-app"></a>建立主控台應用程式

因此，最好是從一般 .NET 主控台專案類型開始。

您需要執行幾項工作：

- 建立空的專案。
- 參考 Xamarin. Mac 程式庫。
- 將非受控相依性帶入您的專案。

以下將更詳細說明這些步驟：

### <a name="create-an-empty-console-project"></a>建立空白的主控台專案

建立新的 .NET 主控台專案，請確定它是 .NET 而不是 .NET Core，因為 Xamarin. .dll 不會在 .NET Core 執行時間下執行，它只會與 Mono 執行時間一起執行。

### <a name="reference-the-xamarinmac-library"></a>參考 Xamarin. Mac 程式庫

若要編譯您的程式碼，您會想`Xamarin.Mac.dll`要參考此目錄中的元件：`/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full`

若要這麼做，請移至 [專案參考]，選取 [ **.Net 元件**] 索引標籤，然後按一下 [**流覽]** 按鈕，找出檔案系統上的檔案。  流覽至上述路徑，然後從該目錄選取 [ **Xamarin** ]。

這可讓您在編譯時期存取 Cocoa Api。   此時，您可以將新增`using AppKit`至檔案頂端，並`NSApplication.Init()`呼叫方法。   在執行應用程式之前，您只需要執行一個步驟即可。

### <a name="bring-the-unmanaged-support-library-into-your-project"></a>將非受控支援程式庫帶入您的專案中

在執行應用程式之前，您必須先將支援`Xamarin.Mac`程式庫帶入您的專案中。   若要這麼做，請將新檔案新增至您的專案（在 [專案選項] 中，選取 [**新增**]，然後按一下 [**新增現有**檔案]），然後流覽至此目錄：

`/Library/Frameworks/Xamarin.Mac.framework/Versions/Current/lib`

在這裡，選取**libxammac 檔案 dylib**。   系統會提供您複製、連結或移動的選擇。   我個人喜歡連結，但複製也可以運作。    接著，您必須選取檔案，並在屬性 pad 中（如果看不到屬性板，請選取 [ **View > pad > Properties** ）]，然後移至 [**組建**] 區段，並將 [**複製到輸出目錄**] 設定設為 [若**較新時複製**]。

您現在可以執行 Xamarin. Mac 應用程式。

您的 bin 目錄中的結果會如下所示：

```
Xamarin.Mac.dll
Xamarin.Mac.pdb
consoleapp.exe
consoleapp.pdb
libxammac.dylib
```

若要執行此應用程式，您將需要相同目錄中的所有檔案。

## <a name="building-a-standalone-application-for-distribution"></a>建立用於散發的獨立應用程式

您可能想要將單一可執行檔散發給您的使用者。  若要這樣做，您可以使用`mkbundle`工具將各種檔案轉換成獨立的可執行檔。

首先，請確定您的應用程式會編譯並執行。   當您對結果感到滿意之後，就可以從命令列執行下列命令：

```
$ mkbundle --simple -o /tmp/consoleapp consoleapp.exe --library libxammac.dylib --config /Library/Frameworks/Mono.framework/Versions/Current/etc/mono/config --machine-config /Library/Frameworks/Mono.framework/Versions/Current//etc/mono/4.5/machine.config
[Output from the bundling tool]
$ _
```

在上述命令列調用中，會使用`-o`選項來指定產生的輸出，在此案例中為已傳遞`/tmp/consoleapp`。   這現在是獨立的應用程式，您可以散發，而且在 Mono 或 Xamarin 上沒有外部相依性，它是完全獨立的可執行檔。

命令列會以手動方式指定要使用的**machine.config**檔案，以及整個系統的程式庫對應設定檔。   並非所有應用程式都需要它們，但是將它們組合起來很方便，因為當您使用 .NET 的更多功能時，會使用這些專案。

## <a name="project-less-builds"></a>專案-較少組建

您不需要完整的專案來建立獨立的 Xamarin. Mac 應用程式，也可以使用簡單的 Unix makefile 來完成工作。   下列範例顯示如何為簡單的命令列應用程式設定 makefile：

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

- `make`將建立程式
- `make run`會在目前的目錄中建立並執行程式
- `make bundle`會建立獨立的可執行檔
