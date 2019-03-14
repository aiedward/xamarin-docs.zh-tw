---
title: 逐步解說：繫結 iOS OBJECTIVE-C 程式庫
description: 本文章提供實際操作的逐步解說中，建立現有的 OBJECTIVE-C 程式庫，InfColorPicker 的 Xamarin.iOS 繫結。 它涵蓋的主題，例如編譯靜態的 Objective C 程式庫、 繫結，以及在 Xamarin.iOS 應用程式中使用的繫結。
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: fcf4e6d9b281eaac4be888c499e537f7397528a0
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669267"
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>逐步解說：繫結 iOS OBJECTIVE-C 程式庫

_本文章提供實際操作的逐步解說中，建立現有的 OBJECTIVE-C 程式庫，InfColorPicker 的 Xamarin.iOS 繫結。它涵蓋的主題，例如編譯靜態的 Objective C 程式庫、 繫結，以及在 Xamarin.iOS 應用程式中使用的繫結。_

IOS 上工作時，您可能會遇到您要使用第三方 Objective C 程式庫的情況。 在這些情況下，您可以使用 Xamarin.iOS_繫結專案_來建立[C#繫結](~/cross-platform/macios/binding/overview.md)，可讓您的程式庫，在您的 Xamarin.iOS 應用程式。

通常在 iOS 生態系統中，您可以找到程式庫 3 個類別：

* 為使用先行編譯的靜態程式庫檔案`.a`連同其標頭 （.h 檔案） 的延伸模組。 比方說， [Google Analytics 程式庫](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
* 做為先行編譯的架構。 這是只包含靜態程式庫標頭，有時候其他資源的資料夾`.framework`延伸模組。 例如， [Google AdMob 文件庫](https://developers.google.com/admob/ios/download)。
* 是原始程式碼檔。 例如，只包含的媒體櫃`.m`和`.h`Objective C 檔案。

在第一個和第二個案例中會已經有先行編譯的產品 CocoaTouch 靜態程式庫以便在這篇文章中，我們將著重於第三個案例。 請記住，然後再開始建立繫結，一定要檢查以確保您可以自由地將它繫結程式庫所提供的授權。

這篇文章提供建立繫結專案使用開放原始碼的逐步解說[InfColorPicker](https://github.com/InfinitApps/InfColorPicker)做為範例，不過，本指南中的所有資訊都可以與任何適用於專案的 Objective C第三方 Objective C 程式庫。 InfColorPicker 程式庫提供可重複使用檢視控制器，可讓使用者選取根據其 HSB 表示法，讓使用者更容易使用的色彩選取範圍的色彩。

[![](walkthrough-images/run01.png "在 iOS 上執行的 InfColorPicker 程式庫的範例")](walkthrough-images/run01.png#lightbox)

我們將討論所有使用此特定的 Objective C API，在 Xamarin.iOS 中的必要步驟：

- 首先，我們將建立使用 Xcode 的 OBJECTIVE-C 靜態程式庫。
- 然後，我們將繫結使用 Xamarin.iOS 此靜態程式庫。
- 接下來，顯示 目標 Sharpie 如何藉由自動產生一些 （但非全部） 減少工作負載的 Xamarin.iOS 繫結所需的必要 API 定義。
- 最後，我們將建立使用此繫結的 Xamarin.iOS 應用程式。

範例應用程式將示範如何使用強式委派 InfColorPicker API 之間的通訊和我們C#程式碼。 我們已了解如何使用強式的委派之後，我們將討論如何使用弱式的委派來執行相同的工作。

## <a name="requirements"></a>需求

本文假設您有一定熟悉 Xcode 和 Objective C 語言，而且您已經讀過我們[繫結 OBJECTIVE-C](~/cross-platform/macios/binding/index.md)文件。 此外，以下是完成的步驟：

-  **Xcode 和 iOS SDK** -Apple 的 Xcode 和最新的 iOS API 需要安裝並設定開發人員電腦上。
-  **[Xcode 命令列工具](#Installing_the_Xcode_Command_Line_Tools)** -目前安裝的 Xcode （請參閱下方的安裝詳細資料） 的版本必須安裝 Xcode 命令列工具。
-  **Visual Studio for Mac 或 Visual Studio** -最新版本的 Visual Studio for Mac 或 Visual Studio 應該安裝並設定開發電腦上。 Apple Mac 做是為了開發 Xamarin.iOS 應用程式，並使用 Visual Studio 時您必須連線到[Xamarin.iOS 組建主機](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
-  **最新版的目標 Sharpie** -從下載目前的目標 Sharpie 工具複本[這裡](~/cross-platform/macios/binding/objective-sharpie/get-started.md)。 如果您已安裝的目標 Sharpie，您可以更新它的最新版本使用 `sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"/>

## <a name="installing-the-xcode-command-line-tools"></a>安裝 Xcode 命令列工具

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)


如上所述，我們將使用 Xcode 命令列工具 (特別`make`和`lipo`) 在此逐步解說。 `make`命令是很常見的 Unix 公用程式，可使用自動化的可執行程式和程式庫編譯_makefile_ ，指定應該如何建置程式。 `lipo`命令是 OS X 命令列公用程式來建立多重架構檔案，它會結合多個`.a`成可供所有的硬體架構的一個檔案的檔案。


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


如上所述，我們將使用 Xcode 命令列工具上**Mac 組建主機**(特別`make`和`lipo`) 在此逐步解說。 `make`命令是很常見的 Unix 公用程式，可使用自動化的可執行程式和程式庫編譯_makefile_來指定如何建置程式。 `lipo`命令是 OS X 命令列公用程式來建立多重架構檔案，它會結合多個`.a`成可供所有的硬體架構的一個檔案的檔案。


-----

根據 Apple[從 Xcode 常見問題集的命令列建置](https://developer.apple.com/library/ios/technotes/tn2339/_index.html)文件，在 OS X 10.9 和更新版本，則**下載**窗格中的 Xcode**喜好設定**對話方塊不不再支援下載的命令列工具。

您必須使用下列方法之一來安裝工具：

- **安裝 Xcode** -當您安裝 Xcode，隨附您所有的命令列工具。 在 OS X 10.9 填充碼 (安裝在`/usr/bin`)，可以將對應中包含的任何工具`/usr/bin`Xcode 內的對應工具。 比方說，`xcrun`命令，可讓您尋找，或從命令列執行 Xcode 內的任何工具。
- **終端機應用程式**-從終端機應用程式中，您可以安裝的命令列工具執行`xcode-select --install`命令：
    - 啟動終端機應用程式。
    - 型別`xcode-select --install`按下**Enter**，例如：

    ```bash
    Europa:~ kmullins$ xcode-select --install
    ```

    - 您需要安裝的命令列工具，請按一下**安裝**按鈕： [![](walkthrough-images/xcode01.png "安裝程式命令列工具")](walkthrough-images/xcode01.png#lightbox)

    - 工具會下載並安裝來自 Apple 的伺服器： [![](walkthrough-images/xcode02.png "下載工具")](walkthrough-images/xcode02.png#lightbox)

- **Apple 開發人員下載**-命令列工具封裝可[Apple 開發人員下載](https://developer.apple.com/downloads/index.action)網頁。 登入您的 Apple ID，然後搜尋和下載命令列工具：[![](walkthrough-images/xcode03.png "尋找命令列工具")](walkthrough-images/xcode03.png#lightbox)

使用安裝命令列工具，我們準備好繼續進行本逐步解說。

## <a name="walkthrough"></a>逐步解說

在此逐步解說中，我們將討論下列步驟：

- **[建立靜態程式庫](#Creating_A_Static_Library)** -這個步驟包含建立的靜態程式庫**InfColorPicker** OBJECTIVE-C 程式碼。 靜態程式庫必須`.a`副檔名，且會內嵌至程式庫專案的.NET 組件。
- **[建立 Xamarin.iOS 繫結專案](#Create_a_Xamarin.iOS_Binding_Project)** -一旦我們擁有的靜態程式庫，我們將使用它來建立 Xamarin.iOS 繫結專案。 繫結專案包含我們剛剛建立的靜態程式庫和中繼資料的形式C#說明如何使用 OBJECTIVE-C API 來的程式碼。 此中繼資料通常稱為 API 定義。 我們將使用 **[目標 Sharpie](#Using_Objective_Sharpie)** 來幫助我們使用建立 API 定義。
- **[標準化的 API 定義](#Normalize_the_API_Definitions)** -目標 Sharpie 直接挑明解協助我們，但無法執行的所有項目。 我們將討論一些變更，我們需要進行的 API 定義，才能使用。
- **[使用繫結程式庫](#Using_the_Binding)** -最後，我們將在其中建立 Xamarin.iOS 應用程式示範如何使用我們新建立的繫結的專案。

現在我們了解涉及的步驟，讓我們繼續進行本逐步解說的其餘部分。

<a name="Creating_A_Static_Library"/>

## <a name="creating-a-static-library"></a>建立靜態程式庫

如果我們的 Github 中的 InfColorPicker 檢查程式碼：

[![](walkthrough-images/image02.png "檢查 InfColorPicker 在 Github 中的程式碼")](walkthrough-images/image02.png#lightbox)

我們可以看到專案中的下列三個目錄：

- **InfColorPicker** -此目錄包含專案的 OBJECTIVE-C 程式碼。
- **PickerSamplePad** -此目錄包含範例 iPad 專案。
- **PickerSamplePhone** -此目錄包含範例的 iPhone 專案。

讓我們下載 InfColorPicker 專案[GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip)並將它解壓縮我們選擇的目錄中。 開啟 Xcode 目標`PickerSamplePhone`專案中，我們會看到下列專案結構在 Xcode 導覽：

[![](walkthrough-images/image03.png "Xcode 導覽器中的專案結構")](walkthrough-images/image03.png#lightbox)

此專案可重複使用程式碼達到藉由直接新增到每個範例專案的 InfColorPicker 原始碼 （以紅色方塊）。 範例專案的程式碼是在藍色方塊內。 因為這個特定的專案不會提供我們的靜態程式庫，則需要我們建立的 Xcode 專案，以編譯靜態程式庫。

第一個步驟是讓我們將 InfoColorPicker 來源程式碼新增至靜態程式庫。 若要這麼做讓我們執行下列動作：

1. 啟動 Xcode。
2. 從**檔案**功能表中，選取**新增** > **專案...**:

    [![](walkthrough-images/image04.png "開始新的專案")](walkthrough-images/image04.png#lightbox)
3. 選取 [**架構與程式庫**，則**Cocoa Touch 靜態程式庫**範本，然後按一下**下一步]** 按鈕：

    [![](walkthrough-images/image05.png "選取 Cocoa Touch 靜態程式庫範本")](walkthrough-images/image05.png#lightbox)

4. 請輸入`InfColorPicker`for**專案名稱**，按一下 [**下一步]** 按鈕：

    [![](walkthrough-images/image06.png "針對專案名稱輸入 InfColorPicker")](walkthrough-images/image06.png#lightbox)
5. 選取要儲存專案，然後按一下的位置**確定** 按鈕。
6. 現在我們需要將來源從 InfColorPicker 專案新增至我們的靜態程式庫專案。 因為**InfColorPicker.h**檔案已存在於我們的靜態程式庫 （預設）、 Xcode 無法讓我們加以覆寫。 從**Finder**、 瀏覽至原始專案中，我們從 GitHub 解壓縮 InfColorPicker 原始碼、 將所有 InfColorPicker 檔案複製和貼到我們新的靜態程式庫專案：

    [![](walkthrough-images/image12.png "將所有 InfColorPicker 檔案複製")](walkthrough-images/image12.png#lightbox)

7. 返回 Xcode，以滑鼠右鍵按一下**InfColorPicker**資料夾，然後選取**將檔案新增至 「 InfColorPicker...」**:

    [![](walkthrough-images/image08.png "新增檔案")](walkthrough-images/image08.png#lightbox)

8. 從 新增檔案 對話方塊中，瀏覽至我們剛剛複製的 InfColorPicker 來源的程式碼檔案，它們全部選取，按一下 **新增**按鈕：

    [![](walkthrough-images/image09.png "全部選取，然後按一下 [新增] 按鈕")](walkthrough-images/image09.png#lightbox)

9. 原始程式碼會複製到我們的專案中：

    [![](walkthrough-images/image10.png "原始程式碼會複製到專案")](walkthrough-images/image10.png#lightbox)

10. 從 Xcode 專案導覽器 中，選取**InfColorPicker.m**檔案，並標記為註解的最後兩行 （因為撰寫此程式庫，不會使用此檔案的方式）：

    [![](walkthrough-images/image14.png "編輯 InfColorPicker.m 檔案")](walkthrough-images/image14.png#lightbox)

11. 我們現在需要檢查是否有任何所需的程式庫的架構。 讀我檔案，或藉由開啟其中一個提供的範例專案，您可以找到這項資訊。 這個範例會使用`Foundation.framework`， `UIKit.framework`，和`CoreGraphics.framework`讓我們加入它們。

12. 選取  **InfColorPicker 目標 > 建置階段**並展開**連結二進位與程式庫**區段：

    [![](walkthrough-images/image16b.png "展開 [連結二進位與程式庫] 區段")](walkthrough-images/image16b.png#lightbox)

13. 使用 **+**  按鈕以開啟對話方塊，讓您將新增所需的畫面格架構上面所列：

    [![](walkthrough-images/image16c.png "加入上面所列的必要的框架架構")](walkthrough-images/image16c.png#lightbox)

14. **連結二進位與程式庫**區段應該看起來像下面的影像：

    [![](walkthrough-images/image16d.png "[連結二進位與程式庫] 區段")](walkthrough-images/image16d.png#lightbox)

此時我們已關閉，但我們尚未完成。 建立靜態程式庫之後，但我們要建置它來建立 Fat 二進位，包含所有的 iOS 裝置和 iOS 模擬器需要的架構。

### <a name="creating-a-fat-binary"></a>建立 Fat 二進位檔

所有 iOS 裝置都必須由 ARM 架構的處理器都所開發的一段時間。 每個新的架構會新增新的指示和其他改進功能，同時仍維持回溯相容性。 在 iOS 裝置上我們 armv6、 armv7、 armv7s、 arm64 指令集 – 雖然[我們不會再使用 armv6](~/ios/deploy-test/compiling-for-different-devices.md)。 IOS 模擬器不由 ARM 和 x86 及 x86_64 提供模擬器是 istead。 我們對我們的意義是，我們必須提供程式庫在每個指令上設定。

Fat 程式庫是`.a`檔案，其中包含所有支援的架構。

建立 fat 二進位是三個步驟的程序：

- 編譯靜態程式庫的 ARM 7 & ARM64 版本。
- 編譯靜態程式庫的 x86 和 x84_64 版本。
- 使用`lipo`命令列工具，可將兩個靜態程式庫結合成一個。

雖然這三個步驟是非常直接了當，並且可能需要重複執行它們未來當 OBJECTIVE-C 程式庫接收更新，或如果我們所需的 bug 修正。 如果您決定將這些步驟自動化，它會簡化未來的維護和支援的 iOS 繫結專案。

有許多工具可用來自動化這類工作-殼層指令碼[rake](http://rake.rubyforge.org/)， [xbuild](https://www.mono-project.com/docs/tools+libraries/tools/xbuild/)，並[讓](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/make.1.html)。 當我們安裝 Xcode 命令列工具時，我們也會安裝進行，因此也就是建置系統，將會用於本逐步解說。 以下是**Makefile**可供您建立能夠在 iOS 裝置和任何文件庫的模擬器的多架構共用程式庫：

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

輸入**Makefile**在純文字編輯器中，您所選擇的命令，並更新具有區段**您的專案名稱**與您專案的名稱。 也很重要，確保我們貼上上述指示中的索引標籤都已被保留的指示。

儲存檔案同名**Makefile**至 InfColorPicker Xcode 靜態程式庫前面所建立的相同位置：

[![](walkthrough-images/lib00.png "Makefile 的名稱儲存檔案")](walkthrough-images/lib00.png#lightbox)

開啟終端機應用程式，在 Mac 上，並瀏覽至您的 Makefile 的位置。 型別`make`到終端機中，按下**Enter**並**Makefile**就會執行：

[![](walkthrough-images/lib01.png "範例 makefile 輸出")](walkthrough-images/lib01.png#lightbox)

當您執行進行時，您會看到內含大量文字的捲動。 如果一切正常運作，您會看到文字**建置成功**並`libInfColorPicker-armv7.a`，`libInfColorPicker-i386.a`並`libInfColorPickerSDK.a`檔案會複製到相同的位置**Makefile**:

[![](walkthrough-images/lib02.png "產生 Makefile libInfColorPicker armv7.a、 libInfColorPicker i386.a 和 libInfColorPickerSDK.a 檔案")](walkthrough-images/lib02.png#lightbox)

您可以使用下列命令，在您的 Fat 二進位檔內確認架構：

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

這應該顯示下列項目：

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

到目前為止，我們已經完成我們的 iOS 繫結的第一個步驟，藉由建立靜態程式庫使用 Xcode 和 Xcode 命令列工具`make`和`lipo`。 讓我們移至下一個步驟，並使用**目標 Sharpie**來自動建立我們的 API 繫結。

<a name="Create_a_Xamarin.iOS_Binding_Project"/>

## <a name="create-a-xamarinios-binding-project"></a>建立 Xamarin.iOS，繫結專案

我們可以使用之前**目標 Sharpie**繫結程序自動化，我們需要建立 Xamarin.iOS 繫結專案來裝載 API 定義 (我們將使用**目標 Sharpie**來幫助我們[組建]），並建立C#讓我們繫結。

讓我們執行下列作業：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 啟動 Visual Studio for mac。
1. 從**檔案**功能表上，選取**新增** > **方案...**:

    ![](walkthrough-images/bind01.png "啟動新的解決方案")

1. 從新的解決方案 對話方塊中，選取**程式庫** > **iOS 繫結專案**:

    ![](walkthrough-images/bind02.png "選取 iOS 繫結專案")

1. 按一下 **下一步**  按鈕。

1. 輸入 「 InfColorPickerBinding"作為**專案名稱**，按一下 **建立**按鈕，以建立方案：

    ![](walkthrough-images/bind02a.png "做為專案名稱輸入 InfColorPickerBinding")

將建立方案和兩個預設檔案將會包含：

![](walkthrough-images/bind03.png "在 [方案總管] 中的方案結構")


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


1. 啟動 Visual Studio。

1. 從**檔案**功能表上，選取**新增** > **專案...**:

    ![開始新的專案](walkthrough-images/bind01vs.png "開始新的專案")

1. 從 新增專案 對話方塊中，選取  **Visual C# > iPhone 與 iPad > iOS 繫結程式庫 (Xamarin)**:

    [![選取 iOS 繫結程式庫](walkthrough-images/bind02.w157-sml.png)](walkthrough-images/bind02.w157.png#lightbox)

1. 輸入 「 InfColorPickerBinding"做為**名稱**，按一下 **確定**按鈕，以建立解決方案。

將建立方案和兩個預設檔案將會包含：

![](walkthrough-images/bind03vs.png "在 [方案總管] 中的方案結構")

-----

- **ApiDefinition.cs** -這個檔案會包含合約，以定義如何 Objective C API 會包裝在C#。
- **Structs.cs** -這個檔案會保留任何結構或列舉值所需的介面和委派。

我們將使用這兩個檔案，稍後在本逐步解說。 首先，我們需要將 InfColorPicker 程式庫新增至繫結專案。

### <a name="including-the-static-library-in-the-binding-project"></a>靜態程式庫納入繫結專案

現在我們又發現我們基底繫結的專案就緒，我們需要新增前面所建立的 Fat 二進位的程式庫**InfColorPicker**程式庫。

請遵循下列步驟來新增程式庫：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 以滑鼠右鍵按一下**原生參考**Solution Pad 中選取的資料夾**新增原生參考**:

    ![](walkthrough-images/bind04a.png "新增原生參考")

1. 瀏覽至 Fat 二進位我們稍早 (`libInfColorPickerSDK.a`) 然後按**開啟**按鈕：

    ![](walkthrough-images/bind05.png "選取 libInfColorPickerSDK.a 檔案")
1. 檔案將會包含在專案中：

    ![](walkthrough-images/bind04.png "其中包括的檔案")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 複製`libInfColorPickerSDK.a`從您**Mac 組建主機**並將它貼到您的繫結專案。

1. 以滑鼠右鍵按一下專案，然後選擇 **新增 > 現有項目...**:

    ![](walkthrough-images/bind04vs.png "加入現有的檔案")

1. 瀏覽至`libInfColorPickerSDK.a`按下**新增**按鈕：

    ![](walkthrough-images/bind05vs.png "新增 libInfColorPickerSDK.a")

1. 檔案將包含在專案中。

-----

當 **.a**檔案新增至專案，將會自動設定 Xamarin.iOS**建置動作**要檔案**ObjcBindingNativeLibrary**，並建立特殊的檔案呼叫`libInfColorPickerSDK.linkwith.cs`。


此檔案包含`LinkWith`告訴 Xamarin.iOS 如何控制代碼靜態程式庫我們剛才加入的屬性。 此檔案的內容是以下列程式碼片段所示：

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

`LinkWith`屬性識別的靜態程式庫專案和一些重要的連結器旗標。


接下來我們要做為建立 InfColorPicker 專案的 API 定義。 基於本逐步解說的目的，我們將產生的檔案使用目標 Sharpie **ApiDefinition.cs**。

<a name="Using_Objective_Sharpie"/>

## <a name="using-objective-sharpie"></a>使用目標 Sharpie

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)


目標 Sharpie 是命令列工具 （由 Xamarin 提供），可以協助建立繫結第 3 的合作對象 Objective C 程式庫，以所需的定義C#。 在本節中，我們將使用目標 Sharpie，以建立初始**ApiDefinition.cs** InfColorPicker 專案。


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


目標 Sharpie 是命令列工具 （由 Xamarin 提供），可以協助建立繫結第 3 的合作對象 Objective C 程式庫，以所需的定義C#。 在本節中，我們將使用目標 Sharpie 上我們**Mac 組建主機**以建立初始**ApiDefinition.cs** InfColorPicker 專案。


-----

若要開始使用，讓我們下載目標 Sharpie installer 檔案，在此所述[指南](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing)。 執行安裝程式，並遵循安裝精靈來安裝目標 Sharpie，我們的開發電腦上所有畫面上的提示。

我們已成功完成目標 Sharpie 之後安裝，讓我們啟動終端機應用程式並輸入下列命令來取得所有的工具，它提供可協助在繫結中的說明：

```bash
sharpie -help
```

如果我們執行上述命令時，就會產生下列輸出：

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

- **xcode** -此工具會提供我們目前的 Xcode 安裝] 和 [iOS 與 Mac Api，我們已安裝版本的相關資訊。 我們將使用這項資訊稍後當我們產生我們的繫結。
- **繫結**-我們將使用此工具來剖析 **.h**至初始 InfColorPicker 專案中的檔案**ApiDefinition.cs**並**StructsAndEnums.cs**檔案。

若要取得特定的目標 Sharpie 工具的說明，請輸入工具的名稱和`-help`選項。 比方說，`sharpie xcode -help`傳回下列輸出：

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

我們可以開始繫結程序之前，我們需要在終端機中輸入下列命令來取得我們目前已安裝的 Sdk 的相關資訊`sharpie xcode -sdks`:

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

從上述項目，我們可以看到，我們有`iphoneos9.3`SDK 安裝在電腦上。 在利用此資訊的地方，我們已準備好剖析 InfColorPicker 專案`.h`檔案載入初始**ApiDefinition.cs**和`StructsAndEnums.cs`InfColorPicker 專案。

在終端機應用程式中，輸入下列命令：

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

何處`[full-path-to-project]`是目錄的完整路徑位置**InfColorPicker** Xcode 專案檔是在我們的電腦上，而且 [iphone os] 已 iOS SDK，我們已安裝，如下所註`sharpie xcode -sdks`命令。 請注意，在此範例中我們已經傳遞 **\*.h**做為參數，其中包括*所有*標頭檔，在這個目錄中-通常您應該不這麼做，但改為仔細閱讀若要尋找最上層的標頭檔 **.h**參考所有其他相關檔案，並只將它傳遞至目標 Sharpie 的檔案。

下列[輸出](walkthrough-images/os05.png)就會產生在終端機：

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

而**InfColorPicker.enums.cs**並**InfColorPicker.cs**將我們的目錄中建立檔案：

[![](walkthrough-images/os06.png "InfColorPicker.enums.cs 和 InfColorPicker.cs 檔案")](walkthrough-images/os06.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)


開啟兩個前面所建立的繫結專案中。 複製的內容**InfColorPicker.cs**檔案，並將它貼到**ApiDefinition.cs**檔案，並取代現有`namespace ...`程式碼區塊的內容**InfColorPicker.cs**檔案 (離開`using`保持不變的陳述式):

![](walkthrough-images/os07.png "InfColorPickerControllerDelegate 檔案")


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


開啟兩個前面所建立的繫結專案中。 複製的內容**InfColorPicker.cs**檔案 (從**Mac 組建主機**) 並將它貼至**ApiDefinition.cs**檔案，並取代現有`namespace ...`程式碼區塊的內容**InfColorPicker.cs**檔案 (離開`using`保持不變的陳述式)。


-----

<a name="Normalize_the_API_Definitions"/>

## <a name="normalize-the-api-definitions"></a>標準化的 API 定義

目標 Sharpie 有時會有問題的轉譯`Delegates`，因此我們需要修改的定義`InfColorPickerControllerDelegate`介面，並取代`[Protocol, Model]`取代為下列一行：

```csharp
[BaseType(typeof(NSObject))]
[Model]
```
如此定義看起來像：

[![](walkthrough-images/os11.png "定義")](walkthrough-images/os11.png#lightbox)

接下來，我們是一樣的內容`InfColorPicker.enums.cs`檔案，複製並貼上它們`StructsAndEnums.cs`離開檔案`using`保持不變的陳述式：

[![](walkthrough-images/os09.png "內容 StructsAndEnums.cs 檔案 ")](walkthrough-images/os09.png#lightbox)

您還可以找到目標 Sharpie 已標註的繫結`[Verify]`屬性。 這些屬性會指出您應該確認目標 Sharpie 未正確的動作，藉由比較原始的 C/Objective C 宣告 （這會在繫結宣告上方的註解中提供） 的繫結。 一旦確認繫結，您應該移除驗證屬性。 如需詳細資訊，請參閱[確認](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)指南。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)


到目前為止，我們繫結的專案應該完成並準備好建置。 讓我們建立我們繫結的專案，並確定，我們最終會有任何錯誤：

[建立繫結專案，並確定沒有任何錯誤](walkthrough-images/os12.png)


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


到目前為止，我們繫結的專案應該完成並準備好建置。 讓我們建立我們繫結的專案，並確定，我們最終會有任何錯誤。


-----

<a name="Using_the_Binding"/>

## <a name="using-the-binding"></a>使用繫結

請遵循下列步驟來建立範例 iPhone 應用程式，使用先前建立的繫結程式庫的 iOS:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. **建立 Xamarin.iOS 專案**-加入新的 Xamarin.iOS 專案，稱為**InfColorPickerSample**加入方案中，如下列螢幕擷取畫面所示：

    ![](walkthrough-images/use01.png "新增單一檢視應用程式")

    ![](walkthrough-images/use01a.png "設定識別碼")

1. **將參考加入至繫結專案**-Update **InfColorPickerSample**專案，使其具有參考**InfColorPickerBinding**專案：

    ![](walkthrough-images/use02.png "將參考新增至繫結專案")

1. **建立使用者介面在 iPhone** -按兩下 **MainStoryboard.storyboard** 中的檔案 **InfColorPickerSample** iOS 設計工具中編輯的專案。 新增** 按鈕**檢視，並為它`ChangeColorButton`，如下列所示：

    ![](walkthrough-images/use03.png "將按鈕新增至檢視")

1. **新增 InfColorPickerView.xib** -InfColorPicker Objective C 程式庫包含 **.xib**檔案。 Xamarin.iOS 將不會包含這 **.xib**在繫結專案中，這會在我們的範例應用程式的執行階段錯誤。 因應措施是將 **.xib** Xamarin.iOS 專案的檔案。 選取的 Xamarin.iOS 專案，以滑鼠右鍵按一下並選取**新增 > 新增檔案**，並新增 **.xib**檔案，如下列螢幕擷取畫面所示：

    ![](walkthrough-images/use04.png "新增 InfColorPickerView.xib")

1. 當系統要求，請將複製 **.xib**到專案的檔案。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. **建立 Xamarin.iOS 專案**-加入新的 Xamarin.iOS 專案，名為**InfColorPickerSample**使用**單一檢視應用程式**範本：

    [![iOS 應用程式 (Xamarin) 專案](walkthrough-images/use01.w157-sml.png)](walkthrough-images/use01.w157.png#lightbox)

    [![選取範本](walkthrough-images/use01-2.w157-sml.png)](walkthrough-images/use01-2.w157.png#lightbox)

1. **將參考加入至繫結專案**-Update **InfColorPickerSample**專案，使其具有參考**InfColorPickerBinding**專案：

    ![](walkthrough-images/use02vs.png "將參考加入至繫結專案")

1. **建立使用者介面在 iPhone** -按兩下 **MainStoryboard.storyboard** 中的檔案 **InfColorPickerSample** iOS 設計工具中編輯的專案。 新增** 按鈕**檢視，並為它`ChangeColorButton`，如下列所示：

    ![](walkthrough-images/use03vs.png "建立 iPhone 使用者介面")

1. **新增 InfColorPickerView.xib** -InfColorPicker Objective C 程式庫包含 **.xib**檔案。 Xamarin.iOS 將不會包含這 **.xib**在繫結專案中，這會在我們的範例應用程式的執行階段錯誤。 因應措施是將 **.xib**檔案，以從我們的 Xamarin.iOS 專案我們**Mac 組建主機**。 選取的 Xamarin.iOS 專案，以滑鼠右鍵按一下並選取**新增** > **現有項目...**，並新增 **.xib**檔案。

-----

接下來，讓我們看一下在 OBJECTIVE-C 與我們如何處理這些繫結中的通訊協定和C#程式碼。

### <a name="protocols-and-xamarinios"></a>通訊協定和 Xamarin.iOS

在 OBJECTIVE-C、 通訊協定會定義方法 （或訊息），可用在某些情況下。 就概念而言，它們是非常類似於介面，在C#。 OBJECTIVE-C 通訊協定之間的主要差異，C#介面是通訊協定可以有選擇性的方法-類別沒有實作的方法。 OBJECTIVE-C 使用@optional關鍵字用以指出哪一個方法是選擇性的。 如需通訊協定的詳細資訊請參閱 <<c0> [ 事件、 通訊協定與委派](~/ios/app-fundamentals/delegates-protocols-and-events.md)。

**InfColorPickerController**都有一個這類通訊協定，下列程式碼片段所示：

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

使用此通訊協定**InfColorPickerController**通知用戶端使用者已選取新的色彩，而且**InfColorPickerController**完成。 目標 Sharpie 對應此通訊協定，如下列程式碼片段所示：

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

Xamarin.iOS 繫結程式庫編譯時，會建立抽象的基底類別，稱為`InfColorPickerControllerDelegate`，以實作此介面與虛擬方法。

有兩種方式，我們可以在 Xamarin.iOS 應用程式中實作此介面：

- **強式委派**-使用強式的委派牽涉到建立C#類別子類別化`InfColorPickerControllerDelegate`並覆寫適當的方法。 **InfColorPickerController**將使用此類別的執行個體與用戶端進行通訊。
- **弱式的委派**-弱委派是一種稍有不同的技術，包含一些類別上建立的公用方法 (例如`InfColorPickerSampleViewController`)，然後公開該方法，以`InfColorPickerDelegate`通訊協定透過`Export`屬性。

強式的委派提供 Intellisense、 型別安全和更佳的封裝。 基於這些理由，您應該使用強式的委派，即可，而不是弱式的委派。

在本逐步解說中，我們將討論這兩種技術： 第一次實作強式的委派，並接著說明如何實作弱式的委派。

### <a name="implementing-a-strong-delegate"></a>實作強式的委派

完成 Xamarin.iOS 應用程式使用強式的委派來回應`colorPickerControllerDidFinish:`訊息：

**子類別 InfColorPickerControllerDelegate** -將新類別加入專案，稱為`ColorSelectedDelegate`。 編輯類別，使其具有下列的程式碼：

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

Xamarin.iOS 會藉由呼叫抽象基底類別項目繫結 Objective C 委派`InfColorPickerControllerDelegate`。 子類別化這個型別和覆寫`ColorPickerControllerDidFinish`方法來存取的值`ResultColor`屬性`InfColorPickerController`。

**建立的執行個體 ColorSelectedDelegate** -我們的事件處理常式將會需要的執行個體`ColorSelectedDelegate`我們在上一個步驟中建立的型別。 編輯類別`InfColorPickerSampleViewController`並將下列執行個體變數新增至類別：

```csharp
ColorSelectedDelegate selector;
```

**初始化 ColorSelectedDelegate 變數**-以確保`selector`有效執行個體，請更新方法`ViewDidLoad`在`ViewController`以符合下列程式碼片段：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
    selector = new ColorSelectedDelegate (this);
}
```
**實作方法 HandleTouchUpInsideWithStrongDelegate** -接下來實作使用者的接觸到的事件處理常式**ColorChangeButton**。 編輯`ViewController`，並新增下列方法：

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

我們先取得的執行個體`InfColorPickerController`透過靜態方法，並讓該執行個體知道我們透過屬性的強式委派`InfColorPickerController.Delegate`。 這個屬性是自動為我們產生的目標 Sharpie。 最後我們會呼叫`PresentModallyOverViewController`若要顯示的檢視`InfColorPickerSampleViewController.xib`，讓使用者可以選取色彩。

**執行應用程式**-在此我們所有的程式碼完成時的時間點。 如果您執行應用程式時，您應該能夠變更的背景色彩`InfColorColorPickerSampleView`如下列螢幕擷取畫面所示：

[![](walkthrough-images/run01.png "執行應用程式")](walkthrough-images/run01.png#lightbox)

恭喜您！ 現在您已成功建立並繫結 Objective C 程式庫，以用於 Xamarin.iOS 應用程式。 接下來，讓我們了解如何使用弱式的委派。

### <a name="implementing-a-weak-delegate"></a>實作弱委派

而不是子類別化特定的委派繫結到 OBJECTIVE-C 通訊協定的類別，Xamarin.iOS 還可讓您在任何衍生自的類別中實作的通訊協定方法`NSObject`，修飾您的方法與`ExportAttribute`，然後提供適當的選取器。 當您採用這個方法時，您指派至您類別的執行個體`WeakDelegate`屬性，而非`Delegate`屬性。 弱式的委派會提供您彈性地採取不同的繼承階層中向下委派類別。 我們來看看如何實作及使用 Xamarin.iOS 應用程式中的弱式委派。

**建立事件處理常式，如 TouchUpInside** -讓我們來建立新的事件處理常式，如`TouchUpInside`變更背景色彩 按鈕的事件。 這個處理常式將會填滿的相同角色`HandleTouchUpInsideWithStrongDelegate`我們在上一節中建立，但會使用弱式委派，而不是強式的委派處理常式。 編輯類別`ViewController`，並新增下列方法：

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

**處理 colorPickerControllerDidFinish:訊息**-當`ViewController`已完成之後，iOS 會將訊息傳送`colorPickerControllerDidFinish:`至`WeakDelegate`。 我們需要建立C#可以處理此訊息的方法。 若要這樣做，我們建立C#方法，然後將它與裝飾`ExportAttribute`。 編輯`ViewController`，並將下列方法新增至類別：

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

執行應用程式。 其現在行為完全相同，但是它使用弱式委派，而不強式的委派。 現在您已成功完成此逐步解說。 您現在應該有了解如何建立及使用 Xamarin.iOS 繫結專案。

## <a name="summary"></a>總結

本文章會逐步完成建立及使用 Xamarin.iOS 繫結專案的程序。 首先，我們會討論如何編譯靜態程式庫中現有的 OBJECTIVE-C 程式庫。 然後我們會探討如何建立 Xamarin.iOS 繫結專案，以及如何使用目標 Sharpie 產生 OBJECTIVE-C 程式庫的 API 定義。 我們討論過如何更新，並調整產生的 API 定義，使其適合供公眾使用。 Xamarin.iOS 繫結專案完成之後，我們移取用該繫結中的 Xamarin.iOS 應用程式，並著重於使用強式的委派和弱式的委派。

## <a name="related-links"></a>相關連結

- [繫結範例 （範例）](https://developer.xamarin.com/samples/monotouch/InfColorPicker/)
- [繫結 Objective-C 程式庫](~/cross-platform/macios/binding/objective-c-libraries.md)
- [繫結詳細資料](~/cross-platform/macios/binding/overview.md)
- [繫結型別參考指南](~/cross-platform/macios/binding/binding-types-reference.md)
- [適用於 Objective-C 開發人員的 Xamarin](~/ios/get-started/objective-c-developers/index.md)
- [Framework 設計方針](https://msdn.microsoft.com/library/ms229042.aspx)
- [Xamarin University 課程：建置 OBJECTIVE-C 繫結程式庫](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University 課程：建置目標 Sharpie OBJECTIVE-C 繫結程式庫](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
