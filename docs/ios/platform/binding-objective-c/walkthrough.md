---
title: 逐步解說：系結 iOS 目標-C 程式庫
description: 本文提供的實際操作逐步解說，是針對現有的 InfColorPicker （目標為 C）程式庫（）建立 Xamarin. iOS 系結。 它涵蓋了一些主題，例如編譯靜態的目標 C 程式庫、將其系結，以及在 Xamarin iOS 應用程式中使用系結。
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 82367d63d1cc7b52d69de52ee635e380cd68c382
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435655"
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>逐步解說：系結 iOS 目標-C 程式庫

> [!IMPORTANT]
> 我們目前正在調查 Xamarin 平臺上的自訂系結使用方式。 請填寫 [**這份問卷**](https://www.surveymonkey.com/r/KKBHNLT) ，以通知未來的開發工作。

_本文提供的實際操作逐步解說，是針對現有的 InfColorPicker （目標為 C）程式庫（）建立 Xamarin. iOS 系結。它涵蓋了一些主題，例如編譯靜態的目標 C 程式庫、將其系結，以及在 Xamarin iOS 應用程式中使用系結。_

在 iOS 上工作時，您可能會遇到需要使用協力廠商目標 C 程式庫的情況。 在這些情況下，您可以使用 Xamarin 的系結 _專案_ 來建立 [c #](~/cross-platform/macios/binding/overview.md) 系結，讓您能夠在您的 Xamarin iOS 應用程式中使用程式庫。

一般來說，在 iOS 生態系統中，您可以在3種類別中找到程式庫：

- 做為具有副檔名的先行編譯靜態程式庫 `.a` 檔案，其標頭 (s)  ( .h 檔案) 。 例如， [Google 的分析程式庫](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
- 作為先行編譯架構。 這只是一個資料夾，其中包含靜態程式庫、標頭，有時也會有擴充功能的其他資源 `.framework` 。 例如 [Google 的 AdMob 程式庫](https://developers.google.com/admob/ios/download)。
- 就像原始程式碼檔一樣。 例如，包含單純 `.m` 和目標 C 檔案的程式庫 `.h` 。

在第一和第二個案例中，已經有先行編譯 CocoaTouch 靜態程式庫，所以在本文中，我們將著重于第三個案例。 請記住，在開始建立系結之前，請一律檢查程式庫所提供的授權，以確保您可以隨意系結。

本文提供逐步解說，說明如何使用開放原始碼 [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) 目標 c 專案作為範例建立系結專案，不過，本指南中的所有資訊都可以調整以與任何協力廠商的目標 c 程式庫搭配使用。 InfColorPicker 程式庫提供可重複使用的視圖控制器，可讓使用者根據其 HSB 標記法來選取色彩，讓色彩選取更容易使用。

[![在 iOS 上執行的 InfColorPicker 程式庫範例](walkthrough-images/run01.png)](walkthrough-images/run01.png#lightbox)

在 Xamarin 中，我們將討論使用這個特定的目標 C API 所需的所有步驟：

- 首先，我們將使用 Xcode 建立目標 C 靜態程式庫。
- 然後，我們會將此靜態程式庫與 Xamarin 系結。
- 接下來，顯示目標 Sharpie 如何藉由自動產生某些 (，但不是所有需要的 API 定義的) ，來降低工作負載。
- 最後，我們將建立使用該系結的 Xamarin iOS 應用程式。

範例應用程式將示範如何使用強式委派，在 InfColorPicker API 和我們的 c # 程式碼之間進行通訊。 在瞭解如何使用強式委派之後，我們將討論如何使用弱式委派來執行相同的工作。

## <a name="requirements"></a>需求

本文假設您已熟悉 Xcode 和目標 C 語言，而且您已閱讀我們的系結 [目標-c](~/cross-platform/macios/binding/index.md) 檔。 此外，還需要下列事項才能完成所呈現的步驟：

- **Xcode 和 IOS SDK** -必須在開發人員的電腦上安裝和設定 Apple 的 Xcode 和最新的 iOS API。
- **[Xcode 命令列工具](#Installing_the_Xcode_Command_Line_Tools)** -必須針對目前安裝的 Xcode 版本安裝 Xcode 命令列工具 (的詳細資訊，請參閱下面的安裝詳細資料) 。
- **Visual Studio for Mac 或 Visual Studio** -必須在開發電腦上安裝並設定最新版本的 Visual Studio for Mac 或 Visual Studio。 需要 Apple Mac 才能開發 Xamarin iOS 應用程式，而在使用 Visual Studio 您必須連線到 [Xamarin 組建主機](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- **最新版本的目標 Sharpie** -目前從 [這裡](~/cross-platform/macios/binding/objective-sharpie/get-started.md)下載的目標 Sharpie 工具副本。 如果您已經安裝目標 Sharpie，您可以使用，將其更新為最新版本 `sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"></a>

## <a name="installing-the-xcode-command-line-tools"></a>安裝 Xcode 命令列工具

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

如上所述，我們將使用 Xcode 命令列工具 (具體來說 `make` ， `lipo` 在此逐步解說中) 。 此 `make` 命令是一個非常常見的 Unix 公用程式，它會使用 _makefile_ 指定程式的建立方式，將可執行程式和程式庫的編譯自動化。 此 `lipo` 命令是 OS X 命令列公用程式，可用來建立多架構檔案; 它會將多個檔案結合 `.a` 成一個檔案，以供所有硬體架構使用。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

如上所述，我們會在 **Mac 組建主機** 上使用 Xcode 命令列工具 (具體來說 `make` ， `lipo` 在此逐步解說中) 。 此 `make` 命令是一個非常常見的 Unix 公用程式，它會使用 _makefile_ 來指定如何建立程式，以自動化可執行程式和程式庫的編譯。 此 `lipo` 命令是 OS X 命令列公用程式，可用來建立多架構檔案; 它會將多個檔案結合 `.a` 成一個檔案，以供所有硬體架構使用。

-----

根據 Apple[從命令列建立的 XCODE 常見問題](https://developer.apple.com/library/ios/technotes/tn2339/_index.html)檔，在 OS X 10.9 和更新版本中，[Xcode**喜好**設定] 對話方塊的 [**下載**] 窗格不會再支援下載命令列工具。

您必須使用下列其中一種方法來安裝這些工具：

- **安裝 Xcode** -當您安裝 Xcode 時，它會隨附于所有命令列工具。 在) 中安裝的 OS X 10.9 填充碼 (中 `/usr/bin` ，可以將包含的任何工具 `/usr/bin` 對應至 Xcode 內對應的工具。 例如，命令可 `xcrun` 讓您從命令列尋找或執行 Xcode 內的任何工具。
- **終端機應用程式** -從終端機應用程式中，您可以藉由執行下列命令來安裝命令列工具 `xcode-select --install` ：
  - 啟動終端機應用程式。
  - 輸入 `xcode-select --install` ，然後按 **enter**鍵，例如：

  ```bash
  Europa:~ kmullins$ xcode-select --install
  ```

  - 系統會要求您安裝命令列工具，請按一下 [**安裝**] 按鈕： [ ![ 安裝命令列工具](walkthrough-images/xcode01.png)](walkthrough-images/xcode01.png#lightbox)

  - 將從 Apple 的伺服器下載並安裝這些工具： [ ![ 下載工具](walkthrough-images/xcode02.png)](walkthrough-images/xcode02.png#lightbox)

- **Apple 開發人員下載** -命令列工具套件可從 [apple 開發人員網頁下載](https://developer.apple.com/downloads/index.action) 。 使用您的 Apple ID 登入，然後搜尋並下載命令列工具： [ ![ 尋找命令列](walkthrough-images/xcode03.png)](walkthrough-images/xcode03.png#lightbox)工具

安裝命令列工具之後，我們就可以繼續進行逐步解說。

## <a name="walkthrough"></a>逐步解說

在本逐步解說中，我們將討論下列步驟：

- **[建立靜態程式庫](#Creating_A_Static_Library)** -此步驟牽涉到建立 **InfColorPicker** 目標 C 程式碼的靜態程式庫。 靜態程式庫會有 `.a` 副檔名，而且將內嵌至程式庫專案的 .net 元件。
- **[建立 xamarin. ios](#Create_a_Xamarin.iOS_Binding_Project)** 系結專案-一旦有靜態程式庫，我們將使用它來建立「xamarin」系結專案。 系結專案是由我們剛才建立的靜態程式庫和 c # 程式碼形式的中繼資料所組成，其會說明如何使用目標 C API。 此中繼資料通常稱為 API 定義。 我們將使用 **[目標 Sharpie](#Using_Objective_Sharpie)** 來協助我們建立 API 定義。
- **[將 API 定義](#Normalize_the_API_Definitions)** 正規化-目標 Sharpie 有很大的説明，但無法執行所有作業。 我們將討論一些我們需要對 API 定義進行的變更，然後才能使用它們。
- **[使用](#Using_the_Binding)** 系結程式庫-最後，我們將建立一個 Xamarin iOS 應用程式，以示範如何使用我們新建立的系結專案。

既然我們已瞭解所需的步驟，讓我們繼續進行本逐步解說的其餘部分。

<a name="Creating_A_Static_Library"></a>

## <a name="creating-a-static-library"></a>建立靜態程式庫

如果我們在 Github 中檢查 InfColorPicker 的程式碼：

[![檢查 Github 中 InfColorPicker 的程式碼](walkthrough-images/image02.png)](walkthrough-images/image02.png#lightbox)

我們可以在專案中看到下列三個目錄：

- **InfColorPicker** -此目錄包含專案的目標 C 程式碼。
- **PickerSamplePad** -此目錄包含範例 iPad 專案。
- **PickerSamplePhone** -此目錄包含範例 iPhone 專案。

讓我們從 [GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip) 下載 InfColorPicker 專案，並將它解壓縮到我們選擇的目錄中。 開啟專案的 Xcode 目標 `PickerSamplePhone` ，我們會在 Xcode Navigator 中看到下列專案結構：

[![Xcode Navigator 中的專案結構](walkthrough-images/image03.png)](walkthrough-images/image03.png#lightbox)

這個專案會將) 紅色方塊中的 InfColorPicker 原始程式碼 (直接新增到每個範例專案中，以達到程式碼重複使用。 範例專案的程式碼位於藍色方塊內。 因為這個特定的專案並不提供靜態程式庫，所以我們必須建立 Xcode 專案來編譯靜態程式庫。

第一個步驟是讓我們將 InfoColorPicker 原始程式碼新增至靜態程式庫中。 為了達成此目的，請執行下列動作：

1. 啟動 Xcode。
2. 從 [**檔案**] 功能表中選取 [**新增**  >  **專案**]：

    [![開始新專案](walkthrough-images/image04.png)](walkthrough-images/image04.png#lightbox)
3. 選取 [ **Framework & 程式庫**]、[ **Cocoa 觸控靜態程式庫** ] 範本，然後按 [ **下一步]** 按鈕：

    [![選取 Cocoa Touch 靜態程式庫範本](walkthrough-images/image05.png)](walkthrough-images/image05.png#lightbox)

4. 輸入 `InfColorPicker` **專案名稱** ，然後按 [ **下一步]** 按鈕：

    [![針對專案名稱輸入 InfColorPicker](walkthrough-images/image06.png)](walkthrough-images/image06.png#lightbox)
5. 選取要儲存專案的位置，然後按一下 [ **確定]** 按鈕。
6. 現在，我們需要將來源從 InfColorPicker 專案加入至靜態程式庫專案。 因為 **InfColorPicker .h** 檔案已存在於靜態程式庫 (預設) ，所以 Xcode 不會讓我們覆寫它。 在搜尋 **工具**中，流覽至原始專案中我們從 GitHub 解壓縮的 InfColorPicker 原始程式碼，複製所有 InfColorPicker 檔案，並將其貼到新的靜態程式庫專案中：

    [![複製所有 InfColorPicker 檔案](walkthrough-images/image12.png)](walkthrough-images/image12.png#lightbox)

7. 返回 Xcode，以滑鼠右鍵按一下 [ **InfColorPicker** ] 資料夾，然後選取 [ **將檔案新增至 "InfColorPicker ..."**：

    [![新增檔案](walkthrough-images/image08.png)](walkthrough-images/image08.png#lightbox)

8. 從 [新增檔案] 對話方塊中，流覽至剛剛複製的 InfColorPicker 原始程式碼檔案，選取 [全部]，然後按一下 [ **加入** ] 按鈕：

    [![全選，然後按一下 [新增] 按鈕](walkthrough-images/image09.png)](walkthrough-images/image09.png#lightbox)

9. 系統會將原始程式碼複製到我們的專案中：

    [![系統會將原始程式碼複製到專案中](walkthrough-images/image10.png)](walkthrough-images/image10.png#lightbox)

10. 從 Xcode Project Navigator 選取 **InfColorPicker** 檔，並將最後兩行加上批註， (因為寫入此程式庫的方式，所以不會使用此檔案) ：

    [![編輯 InfColorPicker 檔](walkthrough-images/image14.png)](walkthrough-images/image14.png#lightbox)

11. 我們現在需要檢查程式庫是否需要任何架構。 您可以在讀我檔案中找到此資訊，或開啟其中一個提供的範例專案。 此範例使用 `Foundation.framework` 、 `UIKit.framework` 和， `CoreGraphics.framework` 讓我們將其新增。

12. 選取 **InfColorPicker 目標 > 組建階段** ，並展開 [ **連結二進位檔與程式庫** ] 區段：

    [![展開 [連結二進位檔與程式庫] 區段](walkthrough-images/image16b.png)](walkthrough-images/image16b.png#lightbox)

13. 使用 **+** 按鈕來開啟對話方塊，讓您新增上列所需的框架架構：

    [![新增上面所列的必要框架架構](walkthrough-images/image16c.png)](walkthrough-images/image16c.png#lightbox)

14. **連結二進位檔與程式庫**區段現在看起來應該如下圖所示：

    [![連結二進位檔與程式庫區段](walkthrough-images/image16d.png)](walkthrough-images/image16d.png#lightbox)

到目前為止，我們已經結束了，不過我們還沒這麼做。 靜態程式庫已經建立，但我們需要建立它來建立一個 Fat 二進位檔，其中包含適用于 iOS 裝置和 iOS 模擬器的所有必要架構。

### <a name="creating-a-fat-binary"></a>建立 Fat 二進位檔

所有 iOS 裝置都有以 ARM 架構為基礎而開發的處理器。 每個新架構都新增了新的指示和其他改良功能，同時仍維持回溯相容性。 iOS 裝置具有 armv6、armv7、armv7s、arm64 指令集，但 [不會再使用 armv6](~/ios/deploy-test/compiling-for-different-devices.md)。 IOS 模擬器不是由 ARM 提供技術支援，而是 x86 和 x86_64 驅動的模擬器。 這表示必須為每個指令集提供程式庫。

Fat 程式庫是 `.a` 包含所有支援架構的檔案。

建立 fat 二進位檔是三個步驟的程式：

- 編譯 ARM 7 & ARM64 版本的靜態程式庫。
- 編譯 x86 和 x84_64 版本的靜態程式庫。
- 使用 `lipo` 命令列工具將兩個靜態程式庫合併成一個。

雖然這三個步驟很簡單，但在未來，當目標 C 程式庫收到更新或需要錯誤修正時，可能需要重複這些步驟。 如果您決定將這些步驟自動化，就可以簡化未來的 iOS 系結專案維護和支援。

有許多工具可將這類工作自動化，例如 shell 腳本、 [rake](https://rake.rubyforge.org/)、 [xbuild](https://www.mono-project.com/docs/tools+libraries/tools/xbuild/)和製作。 安裝 Xcode 命令列工具時， `make` 也會一併安裝，這是將用於此逐步解說的組建系統。 您可以使用 **Makefile** 來建立可在 iOS 裝置上使用的多架構共用程式庫，以及任何程式庫的模擬器：

<!--markdownlint-disable MD010 -->
```makefile
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
<!--markdownlint-enable MD010 -->

在您選擇的純文字編輯器中輸入 **Makefile** 命令，並 **使用您專案** 的名稱來更新區段。 此外，請務必確定您已完全貼上指示，並保留指令中的索引標籤。

將具有名稱 **Makefile** 的檔案儲存在與上面建立的 InfColorPicker Xcode 靜態程式庫相同的位置：

[![使用名稱 Makefile 儲存檔案](walkthrough-images/lib00.png)](walkthrough-images/lib00.png#lightbox)

在您的 Mac 上開啟終端機應用程式，並流覽至 Makefile 的位置。 在 `make` 終端機中輸入，然後按 **enter** 鍵，將會執行 **Makefile** ：

[![範例 makefile 輸出](walkthrough-images/lib01.png)](walkthrough-images/lib01.png#lightbox)

當您執行時，將會看到大量的文字滾動。 如果一切都正常運作，您會看到 **組建成功** ，而且 `libInfColorPicker-armv7.a` 會將 `libInfColorPicker-i386.a` 和檔案 `libInfColorPickerSDK.a` 複製到與 **Makefile**相同的位置：

[![LibInfColorPicker-armv7、libInfColorPicker-i386. a 和 libInfColorPickerSDK. Makefile 產生的檔案](walkthrough-images/lib02.png)](walkthrough-images/lib02.png#lightbox)

您可以使用下列命令來確認 Fat 二進位檔中的架構：

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

這應該會顯示下列內容：

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

至此，我們已完成 iOS 系結的第一個步驟，方法是使用 Xcode 和 Xcode 命令列工具和命令列工具來建立靜態程式庫 `make` `lipo` 。 讓我們移至下一個步驟，並使用 **Sharpie** 來自動建立適用于我們的 API 系結。

<a name="Create_a_Xamarin.iOS_Binding_Project"></a>

## <a name="create-a-xamarinios-binding-project"></a>建立 Xamarin iOS 系結專案

在使用 Sharpie 來自動化系結 **程式** 之前，我們必須先建立一個 Xamarin 的系結專案來存放 API 定義， (我們將使用 **Sharpie 的目標** ，以協助我們建立) 並為我們建立 c # 系結。

讓我們執行下列作業：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 開始 Visual Studio for Mac。
1. **在 [檔案**] 功能表中，選取 [**新增**  >  **方案 ...**]：

    ![開始新的解決方案](walkthrough-images/bind01.png)

1. 從 [新增方案] 對話方塊中，選取 [連結**庫**  >  **iOS**系結專案：

    ![選取 iOS 系結專案](walkthrough-images/bind02.png)

1. 按 [下一步] 按鈕。

1. 輸入 "InfColorPickerBinding" 作為 **專案名稱** ，然後按一下 [ **建立** ] 按鈕以建立方案：

    ![輸入 InfColorPickerBinding 做為專案名稱](walkthrough-images/bind02a.png)

將會建立方案，並包含兩個預設檔案：

![方案總管中的方案結構](walkthrough-images/bind03.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 啟動 Visual Studio。

1. **在 [檔案**] 功能表中，選取 [**新增**  >  **專案**]：

    ![開始新專案](walkthrough-images/bind01vs.png "開始新專案")

1. 從 [新增專案] 對話方塊中，選取 [ **Visual c # > iPhone & iPad > iOS 系結程式庫] (Xamarin) **：

    [![選取 iOS 系結程式庫](walkthrough-images/bind02.w157-sml.png)](walkthrough-images/bind02.w157.png#lightbox)

1. 輸入 "InfColorPickerBinding" 作為 **名稱** ，然後按一下 [ **確定]** 按鈕以建立解決方案。

將會建立方案，並包含兩個預設檔案：

![方案總管中的方案結構](walkthrough-images/bind03vs.png)

-----

- **ApiDefinition.cs** -此檔案將包含定義將如何以 c # 包裝目標 c API 的合約。
- **Structs.cs** -此檔案會保存介面和委派所需的任何結構或列舉值。

我們稍後將在本逐步解說中使用這兩個檔案。 首先，我們需要將 InfColorPicker 程式庫新增至系結專案。

### <a name="including-the-static-library-in-the-binding-project"></a>將靜態程式庫包含在系結專案中

現在我們已準備好基底系結專案，我們需要為 **InfColorPicker** 程式庫新增先前為其建立的 Fat 二進位程式庫。

請依照下列步驟新增程式庫：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 以滑鼠右鍵按一下 Solution Pad 中的 [ **原生參考** ] 資料夾，然後選取 [ **加入原生參考**：

    ![新增原生參考](walkthrough-images/bind04a.png)

1. 流覽至我們稍早在 () 所做的 Fat 二進位檔 `libInfColorPickerSDK.a` ，然後按 [ **開啟** ] 按鈕：

    ![選取 libInfColorPickerSDK 檔案。](walkthrough-images/bind05.png)
1. 檔案將包含在專案中：

    ![包含檔案](walkthrough-images/bind04.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. `libInfColorPickerSDK.a`從您的**Mac 組建主機**複製，並將其貼到您的系結專案中。

1. 以滑鼠右鍵按一下專案，然後選擇 [ **加入 > 現有專案 ...**：

    ![加入現有的檔案](walkthrough-images/bind04vs.png)

1. 流覽至， `libInfColorPickerSDK.a` 然後按下 [ **新增** ] 按鈕：

    ![新增 libInfColorPickerSDK。](walkthrough-images/bind05vs.png)

1. 檔案將包含在專案中。

-----

當檔案 **.a**新增至專案時，Xamarin 會自動將檔案的**組建動作**設為**ObjcBindingNativeLibrary**，並建立稱為的特殊檔案 `libInfColorPickerSDK.linkwith.cs` 。

此檔案包含的 `LinkWith` 屬性會告訴 Xamarin，如何處理我們剛剛新增的靜態程式庫。 下列程式碼片段顯示此檔案的內容：

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

`LinkWith`屬性會識別專案的靜態程式庫，以及一些重要的連結器旗標。

接下來我們需要建立 InfColorPicker 專案的 API 定義。 基於本逐步解說的目的，我們將使用目標 Sharpie 來產生 **ApiDefinition.cs**檔案。

<a name="Using_Objective_Sharpie"></a>

## <a name="using-objective-sharpie"></a>使用目標 Sharpie

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

目標 Sharpie 是 Xamarin)  (提供的命令列工具，可協助建立將協力廠商目標-C 程式庫系結至 c # 所需的定義。 在本節中，我們將使用目標 Sharpie 來建立 InfColorPicker 專案的初始 **ApiDefinition.cs** 。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

目標 Sharpie 是 Xamarin)  (提供的命令列工具，可協助建立將協力廠商目標-C 程式庫系結至 c # 所需的定義。 在本節中，我們將使用 **Mac 組建主機** 上的目標 Sharpie 來建立 InfColorPicker 專案的初始 **ApiDefinition.cs** 。

-----

若要開始使用，讓我們下載目標 Sharpie 安裝程式檔案，如本 [指南](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing)中所述。 執行安裝程式，並遵循安裝精靈中的所有工具提示，在我們的開發電腦上安裝目標 Sharpie。

成功安裝目標 Sharpie 之後，讓我們開始使用終端機應用程式，並輸入下列命令，以取得它所提供的所有工具協助系結的說明：

```bash
sharpie -help
```

如果我們執行上述命令，將會產生下列輸出：

```bash
Europa:Resources kmullins$ sharpie -help
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, --helpShow detailed help
  -v, --versionShow version information

Available Tools:
  xcode              Get information about Xcode installations and available SDKs.
  pod                Create a Xamarin C# binding to Objective-C CocoaPods
  bind               Create a Xamarin C# binding to Objective-C APIs
  update             Update to the latest release of Objective Sharpie
  verify-docs        Show cross reference documentation for [Verify] attributes
  docs               Open the Objective Sharpie online documentation
```

基於本逐步解說的目的，我們將使用下列目標 Sharpie 工具：

- **xcode** -這項工具會提供我們目前 xcode 安裝的相關資訊，以及我們所安裝之 IOS 和 Mac api 的版本。 稍後當我們產生系結時，我們將會使用此資訊。
- 系**結-我們**將使用此工具，將 InfColorPicker 專案中的 **.h**檔案剖析為初始**ApiDefinition.cs**和**StructsAndEnums.cs**檔案。

若要取得特定目標 Sharpie 工具的說明，請輸入工具的名稱和 `-help` 選項。 例如，會傳回 `sharpie xcode -help` 下列輸出：

```bash
Europa:Resources kmullins$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]+

Options:
  -h, -help           Show detailed help
  -v, -verbose        Be verbose with output

Xcode Options:
  -sdks               List all available Xcode SDKs. Pass -verbose for more
                        details.
  -sdkpath SDK        Output the path of the SDK
  -frameworks SDK     List all available framework directories in a given SDK.
```

在開始進行系結程式之前，我們必須在終端機中輸入下列命令，以取得目前已安裝之 Sdk 的相關資訊 `sharpie xcode -sdks` ：

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

從上述，我們可以看到我們的 `iphoneos9.3` 電腦上已安裝 SDK。 備妥這項資訊之後，我們就可以將 InfColorPicker 專案 `.h` 檔剖析成初始 **ApiDefinition.cs** 和 `StructsAndEnums.cs` InfColorPicker 專案。

在終端機應用程式中輸入下列命令：

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] -scope [full-path-to-project]/InfColorPicker/InfColorPicker [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

其中 `[full-path-to-project]` 是在電腦上 **InfColorPicker** Xcode 專案檔所在目錄的完整路徑，而 [iphone-os] 則是我們已安裝的 iOS SDK，如命令所述 `sharpie xcode -sdks` 。 請注意，在此範例中，我們已將 .h 以參數的形式傳遞，其中包含此目錄中的*所有*標頭檔** \* 。** 一般而言，您不應該這麼做，但要仔細閱讀標頭檔以尋找參考所有其他相關檔案的最上層 **.h**檔案，然後將其傳遞到目標 Sharpie。 

> [!TIP] 
> 針對 `-scope` 引數，傳入具有您要系結之標頭的資料夾。 如果沒有 `-scope` 引數，目標 Sharpie 會嘗試產生任何匯入之 IOS SDK 標頭的系結，例如，產生大量的定義檔，在編譯系結 `#import <UIKit.h>` 專案時可能會產生錯誤。 若使用 `-scope` 引數集，目標 Sharpie 將不會產生範圍資料夾外任何標頭的系結。 

系統會在終端機中產生下列 [輸出](walkthrough-images/os05.png) ：

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

**InfColorPicker.enums.cs**和**InfColorPicker.cs**檔案會建立在我們的目錄中：

[![InfColorPicker.enums.cs 和 InfColorPicker.cs 檔案](walkthrough-images/os06.png)](walkthrough-images/os06.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在先前建立的系結專案中開啟這兩個檔案。 複製 **InfColorPicker.cs** 檔案的內容，並將其貼入 **ApiDefinition.cs** 檔案，並將現有的程式 `namespace ...` 代碼區塊取代為 **InfColorPicker.cs** 檔案的內容， (讓 `using` 語句保持不變) ：

![InfColorPickerControllerDelegate 檔案](walkthrough-images/os07.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在先前建立的系結專案中開啟這兩個檔案。 從**Mac 組建主機**) 複製 (**InfColorPicker.cs**檔案的內容，並將其貼入**ApiDefinition.cs**檔案，並將現有的程式碼區塊取代為 InfColorPicker.cs 檔的 `namespace ...` 內容**InfColorPicker.cs** (讓語句保持不 `using` 變) 。

-----

<a name="Normalize_the_API_Definitions"></a>

## <a name="normalize-the-api-definitions"></a>將 API 定義標準化

目標 Sharpie 有時會轉譯問題 `Delegates` ，因此我們必須修改介面的定義， `InfColorPickerControllerDelegate` 並以下列程式碼取代 `[Protocol, Model]` 該行：

```csharp
[BaseType(typeof(NSObject))]
[Model]
```

如此一來，定義就會如下所示：

[![定義](walkthrough-images/os11.png)](walkthrough-images/os11.png#lightbox)

接下來，我們會對檔案的內容執行相同的動作 `InfColorPicker.enums.cs` ，並將其複製並貼到檔案中， `StructsAndEnums.cs` 讓 `using` 語句保持不變：

[![StructsAndEnums.cs 檔案的內容 ](walkthrough-images/os09.png)](walkthrough-images/os09.png#lightbox)

您也可能會發現目標 Sharpie 已標注具有屬性的系結 `[Verify]` 。 這些屬性工作表示您應該藉由比較系結與原始的 C/客觀-C 宣告來驗證目標 Sharpie 是否正確， (將會在系結宣告) 的上方批註中提供。 確認系結之後，您應該移除 [驗證] 屬性。 如需詳細資訊，請參閱 [驗證](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) 指南。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

至此，我們的系結專案應該已完成，而且可以建立。 讓我們建立系結專案，並確定我們最後沒有任何錯誤：

[建立系結專案，並確認沒有任何錯誤](walkthrough-images/os12.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

至此，我們的系結專案應該已完成，而且可以建立。 讓我們建立系結專案，並確定我們最後沒有任何錯誤。

-----

<a name="Using_the_Binding"></a>

## <a name="using-the-binding"></a>使用系結

遵循下列步驟來建立範例 iPhone 應用程式，以使用上面建立的 iOS 系結程式庫：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. **建立 [xamarin] 專案** -將名為 **InfColorPickerSample** 的新 Xamarin. ios 專案新增至方案，如下列螢幕擷取畫面所示：

    ![新增單一視圖應用程式](walkthrough-images/use01.png)

    ![設定識別碼](walkthrough-images/use01a.png)

1. **將參考加入至** 系結專案-更新 **InfColorPickerSample** 專案，使其具有 **InfColorPickerBinding** 專案的參考：

    ![加入系結專案的參考](walkthrough-images/use02.png)

1. **建立 iPhone 消費者介面**-按兩下**InfColorPickerSample**專案中的**MainStoryboard** ，以在 iOS 設計工具中進行編輯。 將 **按鈕** 新增至視圖並加以呼叫 `ChangeColorButton` ，如下列所示：

    ![將按鈕加入至視圖](walkthrough-images/use03.png)

1. **新增 InfColorPickerView. xib** -InfColorPicker 目標-C 程式庫包含 **xib** 檔案。 Xamarin 不會在系結專案中包含這個 **xib** ，這會在我們的範例應用程式中造成執行階段錯誤。 解決方法是將 **xib** 檔案新增至我們的 Xamarin 專案。 選取 [Xamarin] 專案，按一下滑鼠右鍵並選取 [ **新增 > 新增**檔案]，然後新增 **xib** 檔案，如下列螢幕擷取畫面所示：

    ![新增 InfColorPickerView. xib](walkthrough-images/use04.png)

1. 當系統詢問時，請將 **xib** 檔案複製到專案中。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. **建立 Xamarin Ios 專案**-使用**單一 View 應用程式**範本，新增名為**InfColorPickerSample**的新 Xamarin 專案：

    [![ (Xamarin) 專案的 iOS 應用程式](walkthrough-images/use01.w157-sml.png)](walkthrough-images/use01.w157.png#lightbox)

    [![選取範本](walkthrough-images/use01-2.w157-sml.png)](walkthrough-images/use01-2.w157.png#lightbox)

1. **將參考加入至** 系結專案-更新 **InfColorPickerSample** 專案，使其具有 **InfColorPickerBinding** 專案的參考：

    ![將參考加入至系結專案](walkthrough-images/use02vs.png)

1. **建立 iPhone 消費者介面**-按兩下**InfColorPickerSample**專案中的**MainStoryboard** ，以在 iOS 設計工具中進行編輯。 將 **按鈕** 新增至視圖並加以呼叫 `ChangeColorButton` ，如下列所示：

    ![建立 iPhone 消費者介面](walkthrough-images/use03vs.png)

1. **新增 InfColorPickerView. xib** -InfColorPicker 目標-C 程式庫包含 **xib** 檔案。 Xamarin 不會在系結專案中包含這個 **xib** ，這會在我們的範例應用程式中造成執行階段錯誤。 解決方法是將 **xib** 檔案從 **Mac 組建主機**新增至我們的 Xamarin 專案。 選取 [Xamarin] 專案，按一下滑鼠右鍵並選取 [**加入**  >  **現有專案**]，然後新增**xib**檔案。

-----

接下來，讓我們快速查看以 C 為目標的通訊協定，以及我們如何在系結和 c # 程式碼中處理它們。

### <a name="protocols-and-xamarinios"></a>通訊協定和 Xamarin

在目標 C 中，通訊協定會定義 (的方法，或可在特定情況下使用的訊息) 。 就概念而言，它們非常類似于 c # 中的介面。 目標 C 通訊協定與 c # 介面之間的一項主要差異在於，通訊協定可以有類別不需要執行的選擇性方法方法。 目標-C 使用 @optional 關鍵字來指出哪些方法是選擇性的。 如需有關通訊協定的詳細資訊，請參閱 [事件、通訊協定與委派](~/ios/app-fundamentals/delegates-protocols-and-events.md)。

**InfColorPickerController** 有一個這類通訊協定，如下列程式碼片段所示：

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

**InfColorPickerController**會使用此通訊協定來通知用戶端，表示使用者已挑選新的色彩，而且**InfColorPickerController**已完成。 目標 Sharpie 對應此通訊協定，如下列程式碼片段所示：

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

編譯系結程式庫時，Xamarin 會建立名為的抽象基類 `InfColorPickerControllerDelegate` ，此類別會使用虛擬方法來實作為此介面。

有兩種方式可以在 Xamarin iOS 應用程式中執行此介面：

- **強式委派** -使用強式委派包含建立子類別的 c # 類別 `InfColorPickerControllerDelegate` ，並覆寫適當的方法。 **InfColorPickerController** 會使用這個類別的實例來與其用戶端通訊。
- **弱式委派** -弱式委派是稍微不同的技巧，牽涉到在某個類別上建立公用方法 (例如 `InfColorPickerSampleViewController`) ，然後透過屬性將該方法公開給 `InfColorPickerDelegate` 通訊協定 `Export` 。

強式委派提供 Intellisense、型別安全和更好的封裝。 基於這些理由，您應該在您可以使用強式委派，而不是使用弱式委派。

在這個逐步解說中，我們將討論這兩種技術：先執行強式委派，然後說明如何實行弱式委派。

### <a name="implementing-a-strong-delegate"></a>執行強式委派

使用強式委派來回應訊息，以完成 Xamarin iOS 應用程式 `colorPickerControllerDidFinish:` ：

子**類別 InfColorPickerControllerDelegate** -將新類別新增至名為的專案 `ColorSelectedDelegate` 。 編輯類別，使其具有下列程式碼：

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

Xamarin 會建立名為的抽象基類，以系結目標 C 委派 `InfColorPickerControllerDelegate` 。 子類別化這個型別，並覆寫 `ColorPickerControllerDidFinish` 方法以存取的 `ResultColor` 屬性值 `InfColorPickerController` 。

**建立 ColorSelectedDelegate 的實例** 。我們的事件處理常式將需要 `ColorSelectedDelegate` 我們在上一個步驟中建立的型別實例。 編輯類別 `InfColorPickerSampleViewController` ，並將下列執行個體變數新增至類別：

```csharp
ColorSelectedDelegate selector;
```

將**ColorSelectedDelegate 變數初始化**，以確定 `selector` 為有效的實例，請更新中的方法， `ViewDidLoad` `ViewController` 以符合下列程式碼片段：

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();
  ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
  selector = new ColorSelectedDelegate (this);
}
```

**執行方法 HandleTouchUpInsideWithStrongDelegate** -下一步是在使用者接觸 **ColorChangeButton**時，執行事件處理常式。 編輯 `ViewController` ，然後新增下列方法：

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

我們首先會透過靜態方法取得的實例 `InfColorPickerController` ，並讓該實例透過屬性來感知我們的強式委派 `InfColorPickerController.Delegate` 。 這個屬性是由客觀 Sharpie 為我們自動產生的。 最後我們 `PresentModallyOverViewController` 會呼叫來顯示視圖， `InfColorPickerSampleViewController.xib` 讓使用者可以選取色彩。

**執行應用程式** -此時我們已完成所有程式碼。 如果您執行應用程式，您應該能夠變更的背景色彩， `InfColorColorPickerSampleView` 如下列螢幕擷取畫面所示：

[![執行應用程式](walkthrough-images/run01.png)](walkthrough-images/run01.png#lightbox)

恭喜！ 至此，您已成功建立並系結要在 Xamarin iOS 應用程式中使用的目標 C 程式庫。 接下來，讓我們來瞭解如何使用弱式委派。

### <a name="implementing-a-weak-delegate"></a>執行弱式委派

除了子類別化系結至特定委派之目標 C 通訊協定的類別之外，Xamarin 也可讓您在任何衍生自的類別中執行通訊協定方法 `NSObject` ，使用來裝飾您的方法， `ExportAttribute` 然後提供適當的選取器。 當您採用這種方法時，您會將類別的實例指派給 `WeakDelegate` 屬性，而非 `Delegate` 屬性。 弱式委派可讓您彈性地將委派類別帶到不同的繼承階層。 讓我們來看看如何在我們的 Xamarin iOS 應用程式中實施和使用弱式委派。

**建立 TouchUpInside 的事件處理常式** -讓我們為 [ `TouchUpInside` 變更背景色彩] 按鈕的事件建立新的事件處理常式。 此處理程式將會與在 `HandleTouchUpInsideWithStrongDelegate` 上一節中建立的處理常式填滿相同的角色，但會使用弱式委派，而不是強式委派。 編輯類別 `ViewController` ，然後新增下列方法：

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```

**更新 ViewDidLoad** -我們必須變更，才能 `ViewDidLoad` 使用剛才建立的事件處理常式。 編輯 `ViewController` 和變更 `ViewDidLoad` 為類似下列程式碼片段：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**處理 colorPickerControllerDidFinish： Message** -當完成時 `ViewController` ，iOS 會將訊息傳送 `colorPickerControllerDidFinish:` 至 `WeakDelegate` 。 我們需要建立可處理此訊息的 c # 方法。 若要這樣做，我們會建立 c # 方法，然後使用來裝飾它 `ExportAttribute` 。 編輯 `ViewController` ，並將下列方法新增至類別：

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

執行應用程式。 它現在的行為應該與之前完全相同，但它會使用弱式委派，而不是強式委派。 至此，您已成功完成本逐步解說。 您現在應該已經瞭解如何建立和使用 Xamarin 的系結專案。

## <a name="summary"></a>摘要

本文逐步解說建立和使用「Xamarin」系結專案的流程。 首先我們討論過如何將現有的目標 C 程式庫編譯成靜態程式庫。 接著，我們討論了如何建立 Xamarin 的系結專案，以及如何使用目標 Sharpie 來產生目標 C 程式庫的 API 定義。 我們討論了如何更新和調整產生的 API 定義，使其適用于公開取用。 在完成「Xamarin」系結專案之後，我們已移至在 Xamarin 應用程式中取用該系結，並著重于使用強式委派和弱式委派。

## <a name="related-links"></a>相關連結

- [繫結 Objective-C 程式庫](~/cross-platform/macios/binding/objective-c-libraries.md)
- [系結詳細資料](~/cross-platform/macios/binding/overview.md)
- [系結類型參考指南](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin for 目標-C 開發人員](~/ios/get-started/objective-c-developers/index.md)
- [架構設計指導方針](/dotnet/standard/design-guidelines/)