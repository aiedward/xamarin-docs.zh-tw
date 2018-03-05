---
title: "逐步解說： 繫結 iOS Objective C 程式庫"
description: "本文章提供逐步解說動手建立現有的 Objective C 程式庫，InfColorPicker Xamarin.iOS 繫結。 它涵蓋了主題，例如編譯靜態 Objective C 程式庫、 繫結，以及 Xamarin.iOS 應用程式中使用的繫結。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 210a6b45c144de3a0663658d8b33132e39c75ae7
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>逐步解說： 繫結 iOS Objective C 程式庫

_本文章提供逐步解說動手建立現有的 Objective C 程式庫，InfColorPicker Xamarin.iOS 繫結。它涵蓋了主題，例如編譯靜態 Objective C 程式庫、 繫結，以及 Xamarin.iOS 應用程式中使用的繫結。_

當工作在 iOS 上，您可能會遇到情況下，您要使用協力廠商 Objective C 程式庫。 在這些情況下，您可以使用 Xamarin.iOS_繫結專案_建立[C# 繫結](~/cross-platform/macios/binding/overview.md)，可讓您使用 Xamarin.iOS 應用程式中的程式庫。

通常 iOS 生態系統中您可以將程式庫找到 3 類別：

* 為使用先行編譯靜態程式庫檔案`.a`連同其標頭 （.h 檔案） 的延伸模組。 例如， [Google Analytics 程式庫](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
* 做為先行編譯的架構。 這是僅包含靜態程式庫標頭，有時候其他資源的資料夾`.framework`延伸模組。 例如， [Google AdMob 程式庫](https://developers.google.com/admob/ios/download)。
* 是原始程式碼檔。 例如，文件庫只包含`.m`和`.h`Objective C 的檔案。

在第一個和第二個案例中會已經有先行編譯的 CocoaTouch 靜態程式庫，本文章中我們將著重在第三個案例。 請記住，您開始之前建立的繫結，一定要檢查以確保您可自行將它繫結的程式庫提供的授權。

本文章提供建立繫結專案使用開放原始碼的逐步解說[InfColorPicker](https://github.com/InfinitApps/InfColorPicker) Objective C 專案為例，不過本指南中的所有資訊可以都為任何適用於第三方 Objective C 程式庫。 InfColorPicker 程式庫提供可重複使用的檢視控制站，可讓使用者選取根據其 HSB 表示法，讓更方便使用的色彩選擇的色彩。

[ ![](walkthrough-images/run01.png "在 iOS 上執行的 InfColorPicker 程式庫的範例")](walkthrough-images/run01.png)

我們會使用這個特定 Objective C 中的 API Xamarin.iOS 所有必要的步驟：

- 首先，我們將建立使用 Xcode OBJECTIVE-C 靜態程式庫。
- 然後，我們將繫結與 Xamarin.iOS 此靜態程式庫。
- 接下來，顯示 目標 Sharpie 如何降低工作負載自動產生一些 （但非全部） Xamarin.iOS 繫結所需的必要應用程式開發介面定義。
- 最後，我們將建立 Xamarin.iOS 應用程式使用之繫結。

範例應用程式將示範如何使用強式委派 InfColorPicker API 和 C# 程式碼之間的通訊。 我們已看到如何使用強式的委派之後，我們將討論如何使用弱式的委派來執行相同的工作。

## <a name="requirements"></a>需求

本文假設您已安裝 Xcode 和 Objective C 語言熟悉，而且您已閱讀我們[繫結 OBJECTIVE-C](~/cross-platform/macios/binding/index.md)文件。 此外，以下是要求完成呈現的步驟：

-  **Xcode 和 iOS SDK** -Apple 安裝 Xcode 和最新的 iOS 應用程式開發介面需要安裝和設定開發人員的電腦上。
-  **[Xcode 命令列工具](#Installing_the_Xcode_Command_Line_Tools)** -Xcode 命令列工具必須安裝目前已安裝 Xcode （請參閱下方的安裝詳細資料） 的版本。
-  **Visual Studio for Mac 或 Visual Studio** -最新版的 Visual Studio for Mac 或 Visual Studio 應該安裝和設定開發電腦上。 Apple Mac 做是為了開發 Xamarin.iOS 應用程式，而且使用 Visual Studio 時您必須連接到[Xamarin.iOS 組建主機](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
-  **最新版的目標 Sharpie** -從目前的目標 Sharpie 工具複本下載[這裡](~/cross-platform/macios/binding/objective-sharpie/get-started.md)。 如果您已經安裝的目標 Sharpie，您可以更新它的最新版本使用 `sharpie update`

## <a name="installing-the-xcode-command-line-tools"></a>安裝 Xcode 命令列工具

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)


如前所述，我們將使用 Xcode 命令列工具 (特別是`make`和`lipo`) 在此逐步解說。 `make`命令是很常見的 Unix 公用程式，可使用自動化的可執行程式和程式庫編譯_makefile_指定建置程式的方式。 `lipo`命令是 OS X 命令列公用程式來建立多重架構檔案; 它會結合多個`.a`成可供所有的硬體架構的一個檔案的檔案。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


如前所述，我們將使用 Xcode 命令列工具上**Mac 組建主機**(特別是`make`和`lipo`) 在此逐步解說。 `make`命令是很常見的 Unix 公用程式，可使用自動化的可執行程式和程式庫編譯_makefile_來指定如何建置程式。 `lipo`命令是 OS X 命令列公用程式來建立多重架構檔案; 它會結合多個`.a`成可供所有的硬體架構的一個檔案的檔案。


-----

根據 Apple[從命令列使用 Xcode 常見問題集建置](https://developer.apple.com/library/ios/technotes/tn2339/_index.html)文件，為 OS X 10.9 和更新版本、**下載**窗格 Xcode**喜好設定**對話方塊不不再支援下載的命令列工具。

您必須使用下列方法之一來安裝工具：

- **安裝 Xcode** -當您安裝 Xcode，就會自動收到配套您所有的命令列工具。 在 OS X 10.9 填充碼 (安裝在`/usr/bin`)，可以將對應中包含的任何工具`/usr/bin`Xcode 內對應的工具。 例如，`xcrun`命令，可讓您尋找，或從命令列執行在 Xcode 內的任何工具。
- **終端機應用程式**-從終端機應用程式中，您可以安裝命令列工具執行`xcode-select --install`命令：
    - 啟動終端機應用程式。
    - 型別`xcode-select --install`按**Enter**，例如：

    ```bash
    Europa:~ kmullins$ xcode-select --install
    ```

    - 系統會詢問安裝命令列工具，請按一下 **安裝**按鈕： [ ![ ](walkthrough-images/xcode01.png "安裝命令列工具")](walkthrough-images/xcode01.png)

    - 下載並安裝從 Apple 伺服器工具： [ ![ ](walkthrough-images/xcode02.png "下載工具")](walkthrough-images/xcode02.png)

- **下載適用於 Apple 開發人員**-命令列工具封裝可[Apple 開發人員下載]()網頁。 登入您的 Apple ID，然後搜尋和下載命令列工具： [ ![ ](walkthrough-images/xcode03.png "尋找命令列工具")](walkthrough-images/xcode03.png)

安裝程式命令列工具，我們就可以繼續在此逐步解說。

## <a name="walkthrough"></a>逐步解說

在此逐步解說中，我們將涵蓋下列步驟：

- **[建立靜態程式庫](#Creating_A_Static_Library)** -這個步驟包含建立靜態程式庫的**InfColorPicker** Objective C 程式碼。 靜態程式庫會有`.a`副檔名，且會內嵌至程式庫專案的.NET 組件。
- **[建立 Xamarin.iOS 繫結專案](#Create_a_Xamarin.iOS_Binding_Project)** -一旦靜態程式庫，我們將使用它來建立 Xamarin.iOS 繫結專案。 繫結專案包含剛才所建立的靜態程式庫和 C# 程式碼說明如何使用 Objective C 應用程式開發介面來形式的中繼資料。 此中繼資料通常稱為 「 應用程式開發介面定義。 我們將使用**[目標 Sharpie](#Using_Objective_Sharpie)** 來幫助我們與建立應用程式開發介面定義。
- **[將標準化的 API 定義](#Normalize_the_API_Definitions)** -目標 Sharpie 的絕佳的幫助我們，但它無法進行的所有項目。 我們將討論一些變更，我們需要進行的應用程式開發介面定義，才能使用。
- **[使用繫結庫](#Using_the_Binding)** -最後，我們將建立的 Xamarin.iOS 應用程式，示範如何使用我們的新建立之繫結專案。

既然我們了解哪些步驟，讓我們移至本逐步解說的其餘部分。

## <a name="creating-a-static-library"></a>建立靜態程式庫

如果我們在 Github 中 InfColorPicker 檢查程式碼：

[ ![](walkthrough-images/image02.png "檢查針對 InfColorPicker Github 中的程式碼")](walkthrough-images/image02.png)

我們可以看到在專案中的下列三個目錄：

- **InfColorPicker** -此目錄包含 Objective C 程式碼專案。
- **PickerSamplePad** -此目錄包含範例 iPad 專案。
- **PickerSamplePhone** -此目錄包含範例 iPhone 專案。

讓我們 InfColorPicker 從下載專案[GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip)並將它解壓縮我們選擇的目錄中。 開啟 Xcode 目標`PickerSamplePhone`專案中，我們看到 Xcode 導覽中的下列專案結構：

[ ![](walkthrough-images/image03.png "在 Xcode 導覽中的專案結構")](walkthrough-images/image03.png)

這個專案會進行重複使用程式碼直接加入到每個範例專案的 InfColorPicker 原始碼 （以紅色方塊中）。 這個範例專案的程式碼是在藍色方塊內。 因為這個特定的專案不提供我們的靜態程式庫，則需要我們建立 Xcode 專案，以編譯靜態程式庫。

第一個步驟是讓我們 InfoColorPicker 將原始程式碼加入到靜態程式庫。 為了讓我們來達成此目的，執行下列動作：

1. 啟動 Xcode。
2. 從**檔案**功能表中選取**新增** > **專案...**:

    [ ![](walkthrough-images/image04.png "在新專案起始")](walkthrough-images/image04.png)
3. 選取**架構和程式庫**、 **Cocoa 觸控靜態程式庫**範本，然後按一下**下一步**按鈕：

    [ ![](walkthrough-images/image05.png "選取 Cocoa 觸控靜態程式庫範本")](walkthrough-images/image05.png)
4. 輸入`InfColorPicker`如**專案名稱**按一下**下一步**按鈕：

    [ ![](walkthrough-images/image06.png "針對專案名稱輸入 InfColorPicker")](walkthrough-images/image06.png)
5. 選取要儲存專案，然後按一下位置**確定** 按鈕。
6. 現在我們需要將來源從 InfColorPicker 專案加入至靜態程式庫專案。 因為**InfColorPicker.h**檔案已經在我們的靜態程式庫 （依預設）、 Xcode 將不允許我們覆寫它。 從**Finder**，InfColorPicker 原始碼的原始專案中，我們將工具解壓縮從 GitHub 瀏覽、 複製所有 InfColorPicker 檔案並將它們貼至我們新的靜態程式庫專案：

    [ ![](walkthrough-images/image12.png "複製所有 InfColorPicker 檔案")](walkthrough-images/image12.png)

7. 返回 Xcode，請以滑鼠右鍵按一下**InfColorPicker**資料夾，然後選取**將檔案新增至 「 InfColorPicker...」**:

    [ ![](walkthrough-images/image08.png "新增檔案")](walkthrough-images/image08.png)

8. 從 [新增檔案] 對話方塊中，瀏覽至 InfColorPicker 原始程式碼檔案我們剛才複製全部選取，按一下**新增**按鈕：

    [ ![](walkthrough-images/image09.png "所有選取，然後按一下 [新增] 按鈕")](walkthrough-images/image09.png)

9. 原始程式碼會複製到專案：

    [ ![](walkthrough-images/image10.png "原始程式碼會複製到專案")](walkthrough-images/image10.png)

10. 從 Xcode 專案導覽器 中，選取**InfColorPicker.m**檔案，並標記為註解的最後兩行 （因為寫入此媒體櫃，不會使用這個檔案的方式）：

    [ ![](walkthrough-images/image14.png "編輯 InfColorPicker.m 檔案")](walkthrough-images/image14.png)

11. 我們現在需要檢查是否有任何程式庫所需的架構。 讀我檔案，或透過開啟其中一個提供的範例專案，您可以找到這項資訊。 這個範例會使用`Foundation.framework`， `UIKit.framework`，和`CoreGraphics.framework`讓我們將其加入。

12. 選取**InfColorPicker 目標 > 建置階段**展開**連結二進位的程式庫**> 一節：

    [ ![](walkthrough-images/image16b.png "展開連結二進位的程式庫區段")](walkthrough-images/image16b.png)

13. 使用 **+** 按鈕，即可開啟對話方塊，可讓您將新增所需的框架架構上面所列：

    [ ![](walkthrough-images/image16c.png "加入上面所列的必要的框架架構")](walkthrough-images/image16c.png)

14. **連結二進位的程式庫**區段現在看起來應該像下面的影像：

    [ ![](walkthrough-images/image16d.png "連結二進位的程式庫 > 一節")](walkthrough-images/image16d.png)

此時我們關閉，但我們不正確完成。 建立靜態程式庫之後，但我們需要建立，以便建立 Fat 二進位，其中包含所有 iOS 裝置和 iOS 模擬器需要的架構。

### <a name="creating-a-fat-binary"></a>建立 Fat 二進位檔

所有 iOS 裝置都沒有由 ARM 架構的處理器都所開發的一段時間。 每個新的架構新增新的指示和其他增強功能，同時仍維持回溯相容性。 在 iOS 裝置上我們有 armv6、 armv7、 armv7s、 arm64 指令集 – 雖然[我們無法再使用 armv6](~/ios/deploy-test/compiling-for-different-devices.md)。 IOS 模擬器不會由 ARM 和 x86 和 x86_64 電源模擬器是 istead。 我們表示我們是我們必須在每個指令上提供文件庫設定。

Fat 程式庫是`.a`檔案包含所有支援的架構。

建立二進位 fat 是三個步驟的程序：

- 編譯靜態程式庫的 ARM 7 和 ARM64 版本。
- 編譯靜態程式庫的 x86 和 x84_64 版本。
- 使用`lipo`命令列工具，可將兩個靜態程式庫結合成一個。

雖然這三個步驟是相當容易，且可能需要重複步驟以後當 Objective C 程式庫接收更新，或如果我們需要 bug 修正。 如果您決定讓這些步驟自動化，它會簡化未來維護和支援的 iOS 繫結專案。

有許多工具可用來自動化這類的殼層指令碼[耙](http://rake.rubyforge.org/)， [xbuild](http://www.mono-project.com/Microsoft.Build)，和[進行](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/make.1.html)。 當我們安裝 Xcode 命令列工具時，我們也會安裝產生，因此也就是將用於此逐步解說的建置系統。 以下是**Makefile**可讓您建立能夠在 iOS 裝置和模擬器的任何文件庫的多重架構共用程式庫：

```bash
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=./YOUR-PROJECT-NAME
PROJECT=$(PROJECT_ROOT)/YOUR-PROJECT-NAME.xcodeproj
TARGET=YOUR-PROJECT-NAME

all: lib$(TARGET).a

lib$(TARGET)-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

lib$(TARGET)-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET)-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET).a: lib$(TARGET)-i386.a lib$(TARGET)-armv7.a lib$(TARGET)-arm64.a
    xcrun -sdk iphoneos lipo -create -output $@ $^

clean:
    -rm -f *.a *.dll
```

輸入**Makefile**您所選擇的純文字編輯器中的命令，並更新具有區段**您專案名稱**與您專案的名稱。 也很重要，確保我們貼上上述指示中的索引標籤已被保留的指示。

名稱儲存檔案**Makefile**至 InfColorPicker Xcode 靜態程式庫前面所建立的相同位置：


[ ![](walkthrough-images/lib00.png "Makefile 的名稱儲存檔案")](walkthrough-images/lib00.png)

開啟 Mac 上 Terminal 應用程式，並瀏覽至您的 Makefile 的位置。 型別`make`到終端機中，按**Enter**和**Makefile**將會執行：

[ ![](walkthrough-images/lib01.png "範例 makefile 輸出")](walkthrough-images/lib01.png)

當您執行建立時，您會看到大量捲動文字。 如果一切正常運作，您會看到文字**建置成功**和`libInfColorPicker-armv7.a`，`libInfColorPicker-i386.a`和`libInfColorPickerSDK.a`檔案會複製到相同的位置**Makefile**:

[ ![](walkthrough-images/lib02.png "LibInfColorPicker armv7.a、 libInfColorPicker i386.a 和 libInfColorPickerSDK.a 檔案產生的 Makefile")](walkthrough-images/lib02.png)

您可以使用下列命令，您 Fat 的二進位檔中確認架構：

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

這應會顯示下列：

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

此時，我們已經完成我們的 iOS 繫結的第一個步驟，建立靜態程式庫使用 Xcode 和 Xcode 命令列工具`make`和`lipo`。 讓我們移至下一個步驟，並使用**目標 Sharpie**自動為我們的 API 繫結的建立。

## <a name="create-a-xamarinios-binding-project"></a>建立繫結專案 Xamarin.iOS

我們可以使用之前**目標 Sharpie**自動繫結程序，我們需要建立 Xamarin.iOS 繫結專案以包含應用程式開發介面定義 (我們將使用**目標 Sharpie**來幫助我們建置），並建立為我們的 C# 繫結。

讓我們執行下列作業：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 啟動 Visual Studio for mac。
1. 從**檔案**功能表上，選取**新增** > **方案...**:

    ![](walkthrough-images/bind01.png "啟動新的解決方案")

1. 從新方案 對話方塊中，選取**文件庫** > **iOS 繫結專案**:

    ![](walkthrough-images/bind02.png "選取 iOS 繫結的專案")

1. 按一下**下一步** 按鈕。

1. 輸入 「 InfColorPickerBinding"做為**專案名稱**按一下**建立**按鈕以建立方案：

    ![](walkthrough-images/bind02a.png "做為專案名稱輸入 InfColorPickerBinding")

將建立的方案，而且兩個預設的檔案：

![](walkthrough-images/bind03.png "在 [方案總管] 中的方案結構")


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


1. 啟動 Visual Studio。

1. 從**檔案**功能表上，選取**新增** > **專案...**:

    ![](walkthrough-images/bind01vs.png "在新專案起始")

1. 從 新增專案 對話方塊中，選取  **iOS** > **繫結的程式庫**:

    ![](walkthrough-images/bind02vs.png "選取 iOS 繫結文件庫")

1. 輸入 「 InfColorPickerBinding"做為**名稱**按一下**確定**按鈕以建立方案。

將建立的方案，而且兩個預設的檔案：

![](walkthrough-images/bind03vs.png "在 [方案總管] 中的方案結構")

-----



- **ApiDefinition.cs** -這個檔案會包含定義如何 OBJECTIVE-C API 會包裝在 C# 中的合約。
- **Structs.cs** -此檔案會保留任何結構或列舉值所需的介面和委派。

我們會使用這兩個檔案，稍後在本逐步解說。 首先，我們需要將 InfColorPicker 程式庫加入至繫結專案。

### <a name="including-the-static-library-in-the-binding-project"></a>繫結專案中包含靜態程式庫

現在我們有基底繫結專案準備好時，我們需要加入 Fat 二進位的程式庫前面所建立的**InfColorPicker**程式庫。

請遵循下列步驟來新增程式庫：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 以滑鼠右鍵按一下**原生參考**方案板然後選取資料夾**加入原生參考**:

    ![](walkthrough-images/bind04a.png "加入原生參考")

1. 瀏覽至 Fat 二進位我們稍早組成 (`libInfColorPickerSDK.a`) 按**開啟**按鈕：

    ![](walkthrough-images/bind05.png "選取 libInfColorPickerSDK.a 檔案")
1. 檔案會包含在專案中：

    ![](walkthrough-images/bind04.png "其中包括的檔案")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 複製`libInfColorPickerSDK.a`從您**Mac 組建主機**並貼到您的繫結專案。

1. 以滑鼠右鍵按一下專案，然後選擇 **新增 > 現有項目...**:

    ![](walkthrough-images/bind04vs.png "加入現有的檔案")

1. 瀏覽至`libInfColorPickerSDK.a`按**新增**按鈕：

    ![](walkthrough-images/bind05vs.png "加入 libInfColorPickerSDK.a")

1. 檔案將會包含在專案中。

-----


當.a 檔案加入至專案時，將會自動設定 Xamarin.iOS**建置動作**要檔案**ObjcBindingNativeLibrary**，並建立名為的特殊檔案`libInfColorPickerSDK.linkwith.cs`。


這個檔案包含`LinkWith`告訴 Xamarin.iOS 如何控制代碼靜態程式庫我們剛才加入的屬性。 此檔案的內容是以下列程式碼片段所示：

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

`LinkWith`屬性會識別在靜態程式庫專案和一些重要的連結器旗標。


我們必須進行下一件事是建立 InfColorPicker 專案的應用程式開發介面定義。 基於本逐步解說的目的，我們將使用目標 Sharpie 產生檔**ApiDefinition.cs**。

## <a name="using-objective-sharpie"></a>使用目標 Sharpie

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)


目標 Sharpie 是命令列工具 （由 Xamarin 提供），可以協助建立繫結至 C# 的第 3 個合作對象 Objective C 程式庫所需的定義。 在本節中，我們將使用目標 Sharpie 建立初始**ApiDefinition.cs** InfColorPicker 專案。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


目標 Sharpie 是命令列工具 （由 Xamarin 提供），可以協助建立繫結至 C# 的第 3 個合作對象 Objective C 程式庫所需的定義。 在本節中，我們將使用目標 Sharpie 上我們**Mac 組建主機**建立初始**ApiDefinition.cs** InfColorPicker 專案。


-----

若要開始使用，讓我們來下載目標 Sharpie installer 檔案中所述[指南](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing)。 執行安裝程式並從安裝精靈，在我們的開發電腦上安裝目標 Sharpie 遵循所有畫面上的提示。

我們已成功完成目標 Sharpie 之後安裝，讓我們來啟動終端機應用程式並輸入下列命令，以取得所有的工具，它會提供給協助繫結中的說明：

```bash
sharpie -help
```

如果我們執行上述命令，則會產生下列輸出：

```bash
Europa:Resources kmullins$ sharpie -help
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, --helpShow detailed help
  -v, --versionShow version information

Available Tools:

  xcode    Get information about Xcode installations and available SDKs.

  bind     Create a Xamarin C# binding to Objective-C APIs
Europa:Resources kmullins$
```

針對這個逐步解說中，我們將使用下列目標 Sharpie 工具：

- **xcode** -這個工具可讓我們 Xcode 我們目前的安裝和 iOS 和 Mac Api，我們已安裝的版本資訊。 我們將使用這項資訊稍後當我們產生我們的繫結。
- **繫結**-我們將使用此工具來剖析**.h**至初始 InfColorPicker 專案中的檔案**ApiDefinition.cs**和**StructsAndEnums.cs**檔案。

若要取得特定的目標 Sharpie 工具的說明，請輸入工具的名稱和`-help`選項。 例如，`sharpie xcode -help`會傳回下列輸出：

```bash
Europa:Resources kmullins$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]+

Options:
  -h, --help                 Show detailed help
  -v, --verbose              Be verbose with output
      --sdks                 List all available Xcode SDKs. Pass -verbose for
                               more details.
Europa:Resources kmullins$
```

我們可以開始繫結程序之前，我們需要終端機中輸入下列命令來取得我們目前已安裝的 Sdk 的相關資訊`sharpie xcode -sdks`:

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

從上述項目，我們可以看到我們有`iphoneos8.1`我們的機器上安裝 SDK。 這項資訊的位置中，我們已準備好剖析 InfColorPicker 專案`.h`檔案至初始**ApiDefinition.cs**和`StructsAndEnums.cs`InfColorPicker 專案。

輸入下列命令中的的 Terminal 應用程式：

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

其中`[full-path-to-project]`是目錄的完整路徑位置**InfColorPicker** Xcode 專案檔位於我們的電腦，且 [iphone os] iOS SDK，我們已安裝時，所註明`sharpie xcode -sdks`命令。 請注意，在此範例中我們已經傳遞 **\*.h**做為參數，其中包括*所有*標頭檔，此目錄位在您通常應該這樣做，但改為仔細閱讀若要尋找最上層的標頭檔**.h**參考的所有其他相關檔案，並只將其傳遞至目標 Sharpie 檔案。

下列[輸出](walkthrough-images/os05.png)將在終端機中產生：

```bash
Europa:Resources kmullins$ sharpie bind -output InfColorPicker -namespace InfColorPicker -sdk iphoneos8.1 /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h -unified
Compiler configuration:
    -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk -miphoneos-version-min=8.1 -resource-dir /Library/Frameworks/ObjectiveSharpie.framework/Versions/1.1.1/clang-resources -arch armv7 -ObjC

[  0%] parsing /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h
In file included from /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h:60:
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* sourceColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* resultColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
4 warnings generated.
[100%] parsing complete
[bind] InfColorPicker.cs
Europa:Resources kmullins$
```

和**InfColorPicker.enums.cs**和**InfColorPicker.cs**我們目錄中建立檔案：

[ ![](walkthrough-images/os06.png "InfColorPicker.enums.cs 和 InfColorPicker.cs 檔案")](walkthrough-images/os06.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)


開啟兩個檔案中前面所建立的繫結專案。 複製的內容**InfColorPicker.cs**檔案，並將它貼入**ApiDefinition.cs**檔案，以取代現有`namespace ...`程式碼區塊的內容**InfColorPicker.cs**檔案 (離開`using`保持不變的陳述式):

![](walkthrough-images/os07.png "InfColorPickerControllerDelegate 檔案")


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


開啟兩個檔案中前面所建立的繫結專案。 複製的內容**InfColorPicker.cs**檔案 (從**Mac 組建主機**) 並將它貼入**ApiDefinition.cs**檔案，以取代現有`namespace ...`程式碼區塊的內容**InfColorPicker.cs**檔案 (離開`using`保持不變的陳述式)。


-----

## <a name="normalize-the-api-definitions"></a>將標準化的 API 定義

目標 Sharpie 有時會有問題轉譯`Delegates`，因此我們必須將修改的定義`InfColorPickerControllerDelegate`介面，並取代`[Protocol, Model]`為下列行：

```csharp
[BaseType(typeof(NSObject))]
[Model]
```
因此，定義如下：

[ ![](walkthrough-images/os11.png "定義")](walkthrough-images/os11.png)

接下來，我們的內容同樣的步驟`InfColorPicker.enums.cs`檔案，複製並貼上在`StructsAndEnums.cs`檔案離開`using`保持不變的陳述式：

[ ![](walkthrough-images/os09.png "內容 StructsAndEnums.cs 檔案 ")](walkthrough-images/os09.png)

您也可能會發現目標 Sharpie 已標註的繫結`[Verify]`屬性。 這些屬性會指出您應該確認目標 Sharpie 未正確的做法，藉由比較原始 C/Objective C 宣告 （這會提供繫結的宣告上方的註解） 的繫結。 一旦確認繫結，您應該移除驗證屬性。 如需詳細資訊，請參閱[確認](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)指南。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)


此時，我們的繫結專案應完整且準備好要建置。 讓我們來建立繫結專案，並確定，我們最後會有任何錯誤：

[建立繫結專案，並確定沒有任何錯誤](walkthrough-images/os12.png)


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


此時，我們的繫結專案應完整且準備好要建置。 讓我們來建立繫結專案，並確定，我們最後會有任何錯誤。


-----

## <a name="using-the-binding"></a>使用繫結

請遵循下列步驟來建立使用的 iOS 上面所建立的繫結的程式庫的範例 iPhone 應用程式：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. **建立 Xamarin.iOS 專案**-加入新的 Xamarin.iOS 專案呼叫**InfColorPickerSample**加入方案中，如下列螢幕擷取畫面所示：

    ![](walkthrough-images/use01.png "加入單一檢視應用程式")

    ![](walkthrough-images/use01a.png "設定識別項")

1. **繫結的專案參考加入**-更新**InfColorPickerSample**專案，使其具有參考**InfColorPickerBinding**專案：

    ![](walkthrough-images/use02.png "將參考加入至繫結專案")

1. **建立使用者介面在 iPhone** -按兩下**MainStoryboard.storyboard**檔案**InfColorPickerSample**編輯 iOS 設計工具中的專案。 新增**按鈕**檢視並呼叫它`ChangeColorButton`，如下列所示：

    ![](walkthrough-images/use03.png "將按鈕加入至檢視")
1. **新增 InfColorPickerView.xib** -InfColorPicker Objective C 程式庫包含**.xib**檔案。 Xamarin.iOS 將不會包含這**.xib**在繫結的專案中，這會導致在我們的範例應用程式的執行階段錯誤。 因應措施是新增**.xib** Xamarin.iOS 在專案的檔案。 選取 Xamarin.iOS 專案，以滑鼠右鍵按一下並選取**新增 > 新增檔案**，並加入**.xib**檔案中下列螢幕擷取畫面所示：

    ![](walkthrough-images/use04.png "新增 InfColorPickerView.xib")

1. 當系統詢問，請複製**.xib**將檔案貼入專案。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


1. **建立 Xamarin.iOS 專案**-加入新的 Xamarin.iOS 專案呼叫**InfColorPickerSample**加入方案中，如下列螢幕擷取畫面所示：

    ![](walkthrough-images/use01vs.png "建立 Xamarin.iOS 專案")

1. **繫結的專案參考加入**-更新**InfColorPickerSample**專案，使其具有參考**InfColorPickerBinding**專案：

    ![](walkthrough-images/use02vs.png "加入繫結專案參考")

1. **建立使用者介面在 iPhone** -按兩下**MainStoryboard.storyboard**檔案**InfColorPickerSample**編輯 iOS 設計工具中的專案。 新增**按鈕**檢視並呼叫它`ChangeColorButton`，如下列所示：

    ![](walkthrough-images/use03vs.png "建立使用者介面在 iPhone")

1. **新增 InfColorPickerView.xib** -InfColorPicker Objective C 程式庫包含**.xib**檔案。 Xamarin.iOS 將不會包含這**.xib**在繫結的專案中，這會導致在我們的範例應用程式的執行階段錯誤。 因應措施是新增**.xib**檔案，以從我們 Xamarin.iOS 專案我們**Mac 組建主機**。 選取 Xamarin.iOS 專案，以滑鼠右鍵按一下並選取**新增** > **現有項目...**，並加入**.xib**檔案。


-----



接下來，讓我們快速查看 Objective C 以及如何我們處理它們的繫結和 C# 程式碼中的通訊協定。

### <a name="protocols-and-xamarinios"></a>通訊協定和 Xamarin.iOS

Objective C，在通訊協定會定義方法 （或訊息），可用在某些情況下。 就概念而言，它們是非常類似於 C# 中的介面。 Objective C 通訊協定和 C# 介面之間的主要差異是通訊協定可以有選擇性的方法-類別沒有實作的方法。 Objective C 使用@optional關鍵字用來表示的方法是選擇性的。 如需通訊協定的詳細資訊請參閱[事件、 通訊協定與委派](~/ios/app-fundamentals/delegates-protocols-and-events.md)。

**InfColorPickerController**有一個這類通訊協定，下列程式碼片段所示：

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

此通訊協定由**InfColorPickerController**通知用戶端為使用者已選取新的色彩， **InfColorPickerController**已完成。 目標 Sharpie 對應此通訊協定，如下列程式碼片段所示：

```csharp
[BaseType(typeof(NSObject))]
[Model]
public partial interface InfColorPickerControllerDelegate {

    [Export ("colorPickerControllerDidFinish:")]
    void ColorPickerControllerDidFinish (InfColorPickerController controller);

    [Export ("colorPickerControllerDidChangeColor:")]
    void ColorPickerControllerDidChangeColor (InfColorPickerController controller);
}

```

Xamarin.iOS 繫結的程式庫編譯時，會建立稱為的抽象基底類別`InfColorPickerControllerDelegate`，它會實作這個介面使用虛擬方法。

有兩種方式，我們可以 Xamarin.iOS 應用程式中實作這個介面：

- **強式委派**-使用強式的委派包含建立 C# 類別的子類別`InfColorPickerControllerDelegate`並覆寫適當的方法。 **InfColorPickerController**將用於此類別的執行個體和它的用戶端通訊。
- **弱式委派**-弱式委派是一種稍有不同的技術，包含一些類別上建立公用方法 (例如`InfColorPickerSampleViewController`)，然後將該方法來公開`InfColorPickerDelegate`通訊協定透過`Export`屬性。

強式的委派提供 Intellisense、 型別安全和較佳的封裝。 基於這些理由，您應該使用強式委派您可以在其中，而不是弱式委派。

在本逐步解說中，我們將討論這兩種技術： 第一次實作強式委派，並接著說明如何實作弱式委派。

### <a name="implementing-a-strong-delegate"></a>實作強式的委派

若要回應使用強式委派來完成 Xamarin.iOS 應用程式`colorPickerControllerDidFinish:`訊息：

**子類別 InfColorPickerControllerDelegate** -將新類別加入至專案，稱為`ColorSelectedDelegate`。 編輯類別，使其具有下列程式碼：

```csharp
using InfColorPickerBinding;
using UIKit;

namespace InfColorPickerSample
{
    public class ColorSelectedDelegate:InfColorPickerControllerDelegate
    {
        readonly UIViewController parent;

        public ColorSelectedDelegate (UIViewController parent)
        {
            this.parent = parent;
        }

        public override void ColorPickerControllerDidFinish (InfColorPickerController controller)
        {
            parent.View.BackgroundColor = controller.ResultColor;
            parent.DismissViewController (false, null);
        }
    }
}
```

Xamarin.iOS 會藉由建立呼叫抽象基底類別項目繫結 OBJECTIVE-C 委派`InfColorPickerControllerDelegate`。 子類別化這個型別和覆寫`ColorPickerControllerDidFinish`方法來存取值`ResultColor`屬性`InfColorPickerController`。

**建立的執行個體 ColorSelectedDelegate** -我們事件處理常式需要的執行個體`ColorSelectedDelegate`我們在上一個步驟建立的類型。 編輯類別`InfColorPickerSampleViewController`並將下列執行個體變數加入至類別：

```csharp
ColorSelectedDelegate selector;
```

**初始化 ColorSelectedDelegate 變數**-若要確保`selector`有效執行個體，請更新方法`ViewDidLoad`中`ViewController`以符合下列程式碼片段：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
    selector = new ColorSelectedDelegate (this);
}
```
**實作方法 HandleTouchUpInsideWithStrongDelegate** -接下來實作的事件處理常式時使用者碰觸**ColorChangeButton**。 編輯`ViewController`，並加入下列方法：

```csharp
using InfColorPicker;
...

private void HandleTouchUpInsideWithStrongDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.Delegate = selector;
    picker.PresentModallyOverViewController (this);
}

```

我們先取得的執行個體`InfColorPickerController`透過靜態方法，並讓該執行個體知道我們透過屬性的強式委派`InfColorPickerController.Delegate`。 這個屬性是自動為我們產生的目標 Sharpie。 最後呼叫`PresentModallyOverViewController`顯示檢視`InfColorPickerSampleViewController.xib`，讓使用者可以選取色彩。

**執行應用程式**-此時我們完成所有的程式碼。 如果您執行應用程式，您應該能夠變更的背景色彩`InfColorColorPickerSampleView`中的下列螢幕擷取畫面所示：

[ ![](walkthrough-images/run01.png "執行應用程式")](walkthrough-images/run01.png)

恭喜您！ 此時您已成功建立並繫結 Xamarin.iOS 應用程式中使用 Objective C 程式庫。 接下來，讓我們了解如何使用弱式委派。

### <a name="implementing-a-weak-delegate"></a>實作弱式的委派

而不是子類別化特定的委派繫結至 OBJECTIVE-C 通訊協定的類別，Xamarin.iOS 也可讓您在任何衍生自的類別中實作的通訊協定方法`NSObject`，而將您的方法與`ExportAttribute`，然後按一下提供適當的選取器。 當您採用這個方法時，您指派至類別的執行個體`WeakDelegate`屬性而非`Delegate`屬性。 弱式委派，可讓您彈性地使用不同的繼承階層中向您類別的委派。 我們來看看如何實作及使用我們的 Xamarin.iOS 應用程式中的弱式委派。

**建立事件處理常式，如 TouchUpInside** -讓我們來建立新的事件處理常式，如`TouchUpInside`變更背景色彩 按鈕的事件。 這個處理常式將會填滿該相同角色`HandleTouchUpInsideWithStrongDelegate`我們在上一節中建立，但會使用弱式委派，而不是強式的委派處理常式。 編輯類別`ViewController`，並加入下列方法：

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```
**更新 ViewDidLoad** -我們必須變更`ViewDidLoad`以便讓它使用我們剛才建立的事件處理常式。 編輯`ViewController`並變更`ViewDidLoad`類似下列的程式碼片段：


```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**處理 colorPickerControllerDidFinish： 訊息**-當`ViewController`已完成之後，iOS 會將訊息傳送`colorPickerControllerDidFinish:`至`WeakDelegate`。 我們要建立 C# 方法可以處理此訊息。 若要這樣做，我們建立 C# 方法，然後將它與裝飾`ExportAttribute`。 編輯`ViewController`，並將下列方法加入至類別：

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

執行應用程式。 它現在應作用完全相同，但是它會使用弱式委派，而不強式委派。 此時您已順利完成這個逐步解說。 您現在應該了解如何建立和使用 Xamarin.iOS 繫結專案。

## <a name="summary"></a>總結

這篇文章會逐步完成建立和使用 Xamarin.iOS 繫結專案的程序。 首先，我們會討論如何編譯靜態程式庫中現有的 Objective C 程式庫。 然後我們涵蓋如何建立 Xamarin.iOS 繫結專案，以及如何使用目標 Sharpie 產生 Objective C 程式庫的應用程式開發介面定義。 我們將討論如何更新，並調整產生應用程式開發介面定義，使其適用於公用耗用量。 Xamarin.iOS 繫結專案完成之後，我們移到目前耗用 Xamarin.iOS 應用程式，而且著重於使用強式的委派和弱式的委派中該繫結。

## <a name="related-links"></a>相關連結

- [繫結範例 （範例）](https://developer.xamarin.com/samples/monotouch/InfColorPicker/)
- [繫結 Objective-C 程式庫](~/cross-platform/macios/binding/objective-c-libraries.md)
- [繫結詳細資料](~/cross-platform/macios/binding/overview.md)
- [繫結型別參考指南](~/cross-platform/macios/binding/binding-types-reference.md)
- [適用於 Objective-C 開發人員的 Xamarin](~/ios/get-started/objective-c-developers/index.md)
- [Framework 設計方針](http://msdn.microsoft.com/en-us/library/ms229042.aspx)
