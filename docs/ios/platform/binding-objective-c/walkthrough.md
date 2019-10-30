---
title: 逐步解說：系結 iOS 目標-C 程式庫
description: 本文提供的實際操作逐步解說，是為現有的 InfColorPicker 的目標-C 程式庫建立 Xamarin. iOS 系結。 其中涵蓋的主題包括編譯靜態的目標 C 程式庫、將其系結，以及在 Xamarin iOS 應用程式中使用系結。
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: f5e5af7d9b4ec85832f2d6050f632d054ba089a2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032675"
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>逐步解說：系結 iOS 目標-C 程式庫

_本文提供的實際操作逐步解說，是為現有的 InfColorPicker 的目標-C 程式庫建立 Xamarin. iOS 系結。其中涵蓋的主題包括編譯靜態的目標 C 程式庫、將其系結，以及在 Xamarin iOS 應用程式中使用系結。_

在 iOS 上工作時，您可能會遇到需要使用協力廠商目標-C 程式庫的情況。 在這些情況下，您可以使用[ C# xamarin 的系](~/cross-platform/macios/binding/overview.md)結_專案_來建立系結，讓您在您的 Xamarin iOS 應用程式中使用程式庫。

一般來說，在 iOS 生態系統中，您可以找到3種類別的程式庫：

- 做為具有 `.a` 副檔名的先行編譯靜態程式庫檔案及其標頭（.h 檔）。 例如， [Google 的分析程式庫](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
- 做為先行編譯架構。 這只是一個資料夾，其中包含靜態程式庫、標頭，有時也是具有 `.framework` 延伸模組的其他資源。 例如， [Google 的 AdMob 程式庫](https://developers.google.com/admob/ios/download)。
- 就像原始程式碼檔案一樣。 例如，僅包含 `.m` 和 `.h` 目標 C 檔案的程式庫。

在第一個和第二個案例中，將會有預先編譯的 CocoaTouch 靜態程式庫，因此在本文中，我們將著重于第三個案例。 請記住，在您開始建立系結之前，請一律檢查程式庫所提供的授權，以確保您可以隨意系結它。

這篇文章提供逐步解說，說明如何使用開放原始碼[InfColorPicker](https://github.com/InfinitApps/InfColorPicker)目標-c 專案作為範例來建立系結專案，但本指南中的所有資訊都可以調整為與任何協力廠商目標-c 程式庫搭配使用. InfColorPicker 程式庫提供可重複使用的 view controller，可讓使用者根據其 HSB 標記法來選取色彩，讓色彩選擇更方便使用者使用。

[![](walkthrough-images/run01.png "Example of the InfColorPicker library running on iOS")](walkthrough-images/run01.png#lightbox)

我們將涵蓋在 Xamarin 中使用此特定目標-C API 的所有必要步驟。

- 首先，我們將使用 Xcode 建立目標-C 靜態程式庫。
- 然後，我們會將此靜態程式庫系結到 Xamarin. iOS。
- 接下來，顯示目標 Sharpie 如何藉由自動產生 Xamarin. iOS 系結所需的部分（而非全部）必要的 API 定義來減少工作負載。
- 最後，我們將建立使用該系結的 Xamarin iOS 應用程式。

範例應用程式將示範如何使用強式委派來進行 InfColorPicker API 與我們C#的程式碼之間的通訊。 在瞭解如何使用強式委派之後，我們將討論如何使用弱式委派來執行相同的工作。

## <a name="requirements"></a>需求

本文假設您已熟悉 Xcode 和目標 C 語言，而且您已閱讀我們的系結[目標-c](~/cross-platform/macios/binding/index.md)檔。 此外，若要完成顯示的步驟，需要下列各項：

- **Xcode 和 IOS SDK** -Apple 的 Xcode 和最新的 ios API 必須在開發人員的電腦上安裝及設定。
- **[Xcode 命令列工具](#Installing_the_Xcode_Command_Line_Tools)** -必須針對目前安裝的 Xcode 版本安裝 Xcode 命令列工具（如需安裝詳細資料，請參閱下文）。
- **Visual Studio for Mac 或 Visual Studio** -必須在開發電腦上安裝及設定最新版本的 Visual Studio for Mac 或 Visual Studio。 需要 Apple Mac 才能開發 Xamarin iOS 應用程式，而使用 Visual Studio 您必須連線到[Xamarin ios 組建主機](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- **目標 Sharpie 的最新版本**-從[這裡](~/cross-platform/macios/binding/objective-sharpie/get-started.md)下載的目標 Sharpie 工具的目前複本。 如果您已經安裝目標 Sharpie，可以使用 `sharpie update` 將其更新為最新版本

<a name="Installing_the_Xcode_Command_Line_Tools"/>

## <a name="installing-the-xcode-command-line-tools"></a>安裝 Xcode 命令列工具

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

如上所述，我們將在本逐步解說中使用 Xcode 命令列工具（特別是 `make` 和 `lipo`）。 `make` 命令是一個非常常見的 Unix 公用程式，它會使用指定如何建立程式的_makefile_ ，將可執行程式和程式庫的編譯作業自動化。 `lipo` 命令是用來建立多架構檔案的 OS X 命令列公用程式;它會將多個 `.a` 檔案結合成一個可供所有硬體架構使用的檔案。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

如上所述，我們將在此逐步解說中使用**Mac 組建主機**上的 Xcode 命令列工具（特別是 `make` 和 `lipo`）。 `make` 命令是一個非常常見的 Unix 公用程式，它會使用_makefile_指定建立程式的方式，將可執行程式和程式庫的編譯自動化。 `lipo` 命令是用來建立多架構檔案的 OS X 命令列公用程式;它會將多個 `.a` 檔案結合成一個可供所有硬體架構使用的檔案。

-----

根據 Apple[從命令列建立的 XCODE 常見問題](https://developer.apple.com/library/ios/technotes/tn2339/_index.html)檔，在 OS X 10.9 和更新版本中，[Xcode**喜好**設定] 對話方塊的 [**下載**] 窗格不會再支援下載的命令列工具。

您必須使用下列其中一種方法來安裝工具：

- **安裝 Xcode** -當您安裝 Xcode 時，它會隨附于您所有的命令列工具。 在 OS X 10.9 填充碼（安裝在 `/usr/bin`）中，可以將 `/usr/bin` 中包含的任何工具對應至 Xcode 內的對應工具。 例如，`xcrun` 命令，可讓您從命令列在 Xcode 內尋找或執行任何工具。
- **終端機應用程式**-從終端機應用程式，您可以藉由執行 `xcode-select --install` 命令來安裝命令列工具：
  - 啟動終端機應用程式。
  - 輸入 `xcode-select --install`，然後按**enter**，例如：

  ```bash
  Europa:~ kmullins$ xcode-select --install
  ```

  - 系統會要求您安裝命令列工具，請按一下 [**安裝**] 按鈕：[![](walkthrough-images/xcode01.png "安裝命令列工具")](walkthrough-images/xcode01.png#lightbox)

  - 系統會從 Apple 的伺服器下載並安裝這些工具：[![](walkthrough-images/xcode02.png "下載工具")](walkthrough-images/xcode02.png#lightbox)

- **Apple 開發人員下載**-命令列工具套件可從[Apple 開發人員下載網頁取得](https://developer.apple.com/downloads/index.action)。 使用您的 Apple ID 登入，然後搜尋並下載命令列工具：[![](walkthrough-images/xcode03.png "尋找命令列工具")](walkthrough-images/xcode03.png#lightbox)

安裝命令列工具之後，我們就可以繼續進行逐步解說。

## <a name="walkthrough"></a>逐步解說

在本逐步解說中，我們將涵蓋下列步驟：

- **[建立靜態程式庫](#Creating_A_Static_Library)** ：此步驟牽涉到建立**InfColorPicker**目標-C 程式碼的靜態程式庫。 靜態程式庫會有 `.a` 副檔名，而且會內嵌至程式庫專案的 .NET 元件中。
- **[建立 Xamarin ios](#Create_a_Xamarin.iOS_Binding_Project)** 系結專案-當我們有靜態程式庫之後，我們會使用它來建立 xamarin ios 系結專案。 系結專案包含我們剛才建立的靜態程式庫，以及說明如何使用「目標C# -C API」的程式碼形式的中繼資料。 此中繼資料通常稱為 API 定義。 我們將使用 **[目標 Sharpie](#Using_Objective_Sharpie)** 協助我們建立 API 定義。
- **[將 API 定義](#Normalize_the_API_Definitions)** 正規化-目標 Sharpie 會提供協助我們的絕佳工作，但無法完成所有動作。 我們將討論一些我們需要對 API 定義進行的變更，然後才能使用它們。
- **[使用](#Using_the_Binding)** 系結程式庫-最後，我們將建立一個 Xamarin iOS 應用程式，以示範如何使用新建立的系結專案。

既然我們已經瞭解所牽涉到的步驟，讓我們繼續進行逐步解說的其餘部分。

<a name="Creating_A_Static_Library"/>

## <a name="creating-a-static-library"></a>建立靜態程式庫

如果我們檢查 Github 中的 InfColorPicker 程式碼：

[![](walkthrough-images/image02.png "Inspect the code for InfColorPicker in Github")](walkthrough-images/image02.png#lightbox)

我們可以在專案中看到下列三個目錄：

- **InfColorPicker** -此目錄包含專案的目標-C 程式碼。
- **PickerSamplePad** -此目錄包含範例 iPad 專案。
- **PickerSamplePhone** -此目錄包含範例 iPhone 專案。

讓我們從[GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip)下載 InfColorPicker 專案，並將其解壓縮到我們選擇的目錄中。 開啟 `PickerSamplePhone` 專案的 Xcode 目標時，我們會在 Xcode Navigator 中看到下列專案結構：

[![](walkthrough-images/image03.png "The project structure in the Xcode Navigator")](walkthrough-images/image03.png#lightbox)

這個專案可以直接將 InfColorPicker 原始程式碼（在紅色方塊中）新增至每個範例專案，以重複使用程式碼。 範例專案的程式碼位於藍色方塊內。 因為這個特定專案不會提供靜態程式庫給我們，所以我們必須建立 Xcode 專案來編譯靜態程式庫。

第一個步驟是讓我們將 InfoColorPicker 原始程式碼新增至靜態程式庫。 為了達成此目的，我們將執行下列動作：

1. 啟動 Xcode。
2. 從 [**檔案**] 功能表中，選取 [**新增**] > **專案 ...** ：

    [![](walkthrough-images/image04.png "Starting a new project")](walkthrough-images/image04.png#lightbox)
3. 選取 [ **Framework & 程式庫**]、[ **Cocoa Touch 靜態程式庫**] 範本，然後按 [**下一步]** 按鈕：

    [![](walkthrough-images/image05.png "Select the Cocoa Touch Static Library template")](walkthrough-images/image05.png#lightbox)

4. 輸入**專案名稱**`InfColorPicker`，然後按 [**下一步]** 按鈕：

    [![](walkthrough-images/image06.png "Enter InfColorPicker for the Project Name")](walkthrough-images/image06.png#lightbox)
5. 選取要儲存專案的位置，然後按一下 [**確定]** 按鈕。
6. 現在，我們需要將來源從 InfColorPicker 專案新增至我們的靜態程式庫專案。 因為**InfColorPicker**檔案已經存在於靜態程式庫中（預設為），所以 Xcode 不會允許我們覆寫它。 從搜尋**工具**中，流覽至我們從 GitHub 解壓縮的原始專案中的 InfColorPicker 原始程式碼，複製所有 InfColorPicker 檔案，並貼到新的靜態程式庫專案中：

    [![](walkthrough-images/image12.png "Copy all of the InfColorPicker files")](walkthrough-images/image12.png#lightbox)

7. 返回 [Xcode]，以滑鼠右鍵按一下**InfColorPicker**資料夾，然後選取 [**將檔案新增至 "InfColorPicker ...]** ：

    [![](walkthrough-images/image08.png "Adding files")](walkthrough-images/image08.png#lightbox)

8. 從 [新增檔案] 對話方塊中，流覽至我們剛才複製的 InfColorPicker 原始程式碼檔案，選取 [全部]，然後按一下 [**新增**] 按鈕：

    [![](walkthrough-images/image09.png "Select all and click the Add button")](walkthrough-images/image09.png#lightbox)

9. 原始程式碼會複製到我們的專案中：

    [![](walkthrough-images/image10.png "The source code will be copied into the project")](walkthrough-images/image10.png#lightbox)

10. 從 [Xcode 專案導覽器] 中，選取 [ **InfColorPicker** ] 檔案並批註掉最後兩行（由於此程式庫的撰寫方式，不會使用此檔案）：

    [![](walkthrough-images/image14.png "Editing the InfColorPicker.m file")](walkthrough-images/image14.png#lightbox)

11. 我們現在需要檢查程式庫是否需要任何架構。 您可以在讀我檔案中找到這項資訊，或開啟其中一個提供的範例專案。 這個範例會使用 `Foundation.framework`、`UIKit.framework`和 `CoreGraphics.framework`，讓我們新增它們。

12. 選取**InfColorPicker 目標 > 組建階段**，並展開 [**連結二進位檔與程式庫**] 區段：

    [![](walkthrough-images/image16b.png "Expand the Link Binary With Libraries section")](walkthrough-images/image16b.png#lightbox)

13. 使用 [ **+** ] 按鈕可開啟對話方塊，讓您新增上述所列的必要框架架構：

    [![](walkthrough-images/image16c.png "Add the required frames frameworks listed above")](walkthrough-images/image16c.png#lightbox)

14. [**連結二進位**檔] 區段現在看起來應該如下圖所示：

    [![](walkthrough-images/image16d.png "The Link Binary With Libraries section")](walkthrough-images/image16d.png#lightbox)

此時我們已關閉，但我們並不是這麼做。 靜態程式庫已經建立，但我們需要建立它以建立 Fat 二進位檔，其中包含 iOS 裝置和 iOS 模擬器的所有必要架構。

### <a name="creating-a-fat-binary"></a>建立 Fat 二進位檔

所有 iOS 裝置都有由 ARM 架構提供的處理器，並已隨著時間發展。 每個新的架構都新增了新的指示和其他改良功能，同時仍維持回溯相容性。 iOS 裝置具有 armv6、armv7、armv7s、arm64 指令集–不過[armv6 未使用任何其他](~/ios/deploy-test/compiling-for-different-devices.md)。 IOS 模擬器不是由 ARM 提供技術支援，而是 x86 和 x86_64 供電的模擬器。 這表示必須為每個指令集提供程式庫。

Fat 程式庫是包含所有支援架構的 `.a` 檔案。

建立 fat 二進位檔是三個步驟的程式：

- 編譯 ARM 7 & ARM64 版本的靜態程式庫。
- 編譯 x86 和 x84_64 版本的靜態程式庫。
- 使用 `lipo` 命令列工具將兩個靜態程式庫結合成一個。

雖然這三個步驟相當簡單，但在未來，當目標 C 程式庫收到更新或需要錯誤修正時，可能需要重複這些步驟。 如果您決定將這些步驟自動化，它會簡化未來 iOS 系結專案的維護和支援。

有許多工具可用來自動化這類工作-shell 腳本、 [rake](https://rake.rubyforge.org/)、 [xbuild](https://www.mono-project.com/docs/tools+libraries/tools/xbuild/)和[進行](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/make.1.html)。 安裝 Xcode 命令列工具時，也會安裝 `make`，因此這就是將用於此逐步解說的組建系統。 以下是您可以用來建立多架構共用程式庫的**Makefile** ，其可在 iOS 裝置和任何程式庫的模擬器上工作：

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

在您選擇的純文字編輯器中輸入**Makefile**命令，並以**您**的專案名稱更新區段。 此外，請務必確定您已完全貼上上述指示，並保留指示中的索引標籤。

將具有名稱**Makefile**的檔案儲存至與我們先前建立的 InfColorPicker Xcode 靜態程式庫相同的位置：

[![](walkthrough-images/lib00.png "Save the file with the name Makefile")](walkthrough-images/lib00.png#lightbox)

在您的 Mac 上開啟終端機應用程式，並流覽至您的 Makefile 位置。 在終端機中輸入 `make`，按下**enter** ，就會執行**Makefile** ：

[![](walkthrough-images/lib01.png "Sample makefile output")](walkthrough-images/lib01.png#lightbox)

當您執行 [製作] 時，您會看到許多文字捲軸。 如果一切運作正常，您會看到 [**組建成功**] 和 [`libInfColorPicker-armv7.a`]、[`libInfColorPicker-i386.a`] 和 [`libInfColorPickerSDK.a`] 檔案會複製到與**Makefile**相同的位置：

[![](walkthrough-images/lib02.png "The libInfColorPicker-armv7.a, libInfColorPicker-i386.a and libInfColorPickerSDK.a files generated by the Makefile")](walkthrough-images/lib02.png#lightbox)

您可以使用下列命令來確認您的 Fat 二進位檔中的架構：

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

這應該會顯示下列內容：

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

此時，我們已完成 iOS 系結的第一個步驟，方法是使用 Xcode 建立靜態程式庫和 Xcode 命令列工具 `make` 和 `lipo`。 讓我們移至下一個步驟，並使用**Sharpie**自動為我們建立 API 系結。

<a name="Create_a_Xamarin.iOS_Binding_Project"/>

## <a name="create-a-xamarinios-binding-project"></a>建立 Xamarin iOS 系結專案

在我們可以使用**Sharpie**將系結程式自動化之前，我們必須先建立一個 Xamarin iOS 系結專案來存放 API 定義（我們將使用**Sharpie**來協助我們建立），並C#為我們建立系結。

讓我們執行下列動作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 啟動 Visual Studio for Mac。
1. 從 [**檔案**] 功能表中，選取 [**新增**] [ > **方案**]：

    ![](walkthrough-images/bind01.png "Starting a new solution")

1. 從 [新增解決方案] 對話方塊中，選取 [連結**庫**] > [iOS 系結**專案**]：

    ![](walkthrough-images/bind02.png "Select iOS Binding Project")

1. 按 [**下一步]** 按鈕。

1. 輸入 "InfColorPickerBinding" 作為**專案名稱**，然後按一下 [**建立**] 按鈕以建立解決方案：

    ![](walkthrough-images/bind02a.png "Enter InfColorPickerBinding as the Project Name")

將會建立解決方案，並會包含兩個預設檔案：

![](walkthrough-images/bind03.png "The solution structure in the Solution Explorer")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 啟動 Visual Studio。

1. 從 [**檔案**] 功能表中，選取 [**新增** > **專案 ...** ]：

    ![啟動新的專案](walkthrough-images/bind01vs.png "啟動新的專案")

1. 從 [新增專案] 對話方塊中，選取 [ **Visual C# > iPhone & iPad > iOS 系結程式庫（Xamarin）** ]：

    [![選取 iOS 系結程式庫](walkthrough-images/bind02.w157-sml.png)](walkthrough-images/bind02.w157.png#lightbox)

1. 輸入 "InfColorPickerBinding" 作為**名稱**，然後按一下 [**確定]** 按鈕以建立解決方案。

將會建立解決方案，並會包含兩個預設檔案：

![](walkthrough-images/bind03vs.png "The solution structure in the Solution Explorer")

-----

- **ApiDefinition.cs** -此檔案將包含定義將如何包裝目標-C API 的合約C#。
- **Structs.cs** -此檔案會保存介面和委派所需的任何結構或列舉值。

我們稍後會在逐步解說中使用這兩個檔案。 首先，我們需要將 InfColorPicker 程式庫新增至系結專案。

### <a name="including-the-static-library-in-the-binding-project"></a>在系結專案中包含靜態程式庫

現在我們已備妥基底系結專案，我們需要新增我們為**InfColorPicker**程式庫所建立的 Fat 二進位程式庫。

請遵循下列步驟來新增程式庫：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 以滑鼠右鍵按一下 Solution Pad 中的 [**原生參考**] 資料夾，然後選取 [**加入原生參考**]：

    ![](walkthrough-images/bind04a.png "Add Native References")

1. 流覽至我們稍早所做的 Fat 二進位檔（`libInfColorPickerSDK.a`），然後按下 [**開啟**] 按鈕：

    ![](walkthrough-images/bind05.png "Select the libInfColorPickerSDK.a file")
1. 檔案會包含在專案中：

    ![](walkthrough-images/bind04.png "Including a file")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 從您的**Mac 組建主機**複製 `libInfColorPickerSDK.a`，並將它貼入您的系結專案。

1. 以滑鼠右鍵按一下專案，然後選擇 [**加入 > 現有專案 ...** ]：

    ![](walkthrough-images/bind04vs.png "Adding an existing file")

1. 流覽至 `libInfColorPickerSDK.a`，然後按下 [**新增**] 按鈕：

    ![](walkthrough-images/bind05vs.png "Adding libInfColorPickerSDK.a")

1. 檔案會包含在專案中。

-----

當檔案新增至專案時，Xamarin 會自動將檔案的**組建動作**設定為**ObjcBindingNativeLibrary**，並建立稱為 `libInfColorPickerSDK.linkwith.cs`的特殊**檔案。**

此檔案包含的 `LinkWith` 屬性會告訴 Xamarin，如何處理我們剛才新增的靜態程式庫。 此檔案的內容會顯示在下列程式碼片段中：

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

`LinkWith` 屬性會識別專案的靜態程式庫和一些重要的連結器旗標。

接下來我們必須建立 InfColorPicker 專案的 API 定義。 基於本逐步解說的目的，我們將使用目標 Sharpie 來產生檔案**ApiDefinition.cs**。

<a name="Using_Objective_Sharpie"/>

## <a name="using-objective-sharpie"></a>使用目標 Sharpie

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

目標 Sharpie 是一種命令列工具（由 Xamarin 提供），可協助建立將協力廠商目標 C 程式庫系結至C#所需的定義。 在本節中，我們將使用目標 Sharpie 來建立 InfColorPicker 專案的初始**ApiDefinition.cs** 。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

目標 Sharpie 是一種命令列工具（由 Xamarin 提供），可協助建立將協力廠商目標 C 程式庫系結至C#所需的定義。 在本節中，我們將在**Mac 組建主機**上使用目標 Sharpie 來建立 InfColorPicker 專案的初始**ApiDefinition.cs** 。

-----

若要開始使用，讓我們下載目標 Sharpie 安裝程式檔案，如本[指南](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing)中所述。 執行安裝程式，並遵循安裝精靈中的所有工具提示，在開發電腦上安裝目標 Sharpie。

成功安裝目標 Sharpie 之後，讓我們啟動終端機應用程式，並輸入下列命令以取得其提供的所有工具的說明，以協助進行系結：

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

  xcode    Get information about Xcode installations and available SDKs.

  bind     Create a Xamarin C# binding to Objective-C APIs
Europa:Resources kmullins$
```

基於本逐步解說的目的，我們將使用下列目標 Sharpie 工具：

- **xcode** -這項工具可提供我們目前 xcode 安裝的相關資訊，以及我們已安裝的 IOS 和 Mac api 版本。 稍後當我們產生系結時，我們將會用到此資訊。
- **bind** -我們將使用此工具，將 InfColorPicker 專案中的 **.h**檔案剖析成初始**ApiDefinition.cs**和**StructsAndEnums.cs**檔案。

若要取得特定目標 Sharpie 工具的說明，請輸入工具的名稱和 `-help` 選項。 例如，`sharpie xcode -help` 會傳回下列輸出：

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

在開始進行系結程式之前，我們需要在終端機 `sharpie xcode -sdks`中輸入下列命令，以取得目前已安裝 Sdk 的相關資訊：

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

在上述程式中，我們可以看到我們的電腦上已安裝 `iphoneos9.3` SDK。 備妥這項資訊之後，即可將 InfColorPicker 專案 `.h` 檔案剖析成初始**ApiDefinition.cs** ，並 `StructsAndEnums.cs` InfColorPicker 專案。

在終端機應用程式中輸入下列命令：

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

其中 `[full-path-to-project]` 是電腦上**InfColorPicker** Xcode 專案檔所在目錄的完整路徑，而 [iphone-os] 是我們已安裝的 iOS SDK，如 `sharpie xcode -sdks` 命令所述。 請注意，在此範例中，我們已傳遞 **\*.h**做為參數，其中包含此目錄中的*所有*標頭檔-通常您不應該這麼做，但請仔細閱讀標頭檔以找出最上層的 **.h 檔案。** 參考所有其他相關檔案，然後將該檔案傳遞到目標 Sharpie。

系統會在終端機中產生下列[輸出](walkthrough-images/os05.png)：

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

而**InfColorPicker.enums.cs**和**InfColorPicker.cs**檔案將會建立在我們的目錄中：

[![](walkthrough-images/os06.png "The InfColorPicker.enums.cs and InfColorPicker.cs files")](walkthrough-images/os06.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

在上述所建立的系結專案中開啟這兩個檔案。 複製**InfColorPicker.cs**檔案的內容，並將它貼到**ApiDefinition.cs**檔案中，將現有的 `namespace ...` 程式碼區塊取代為**InfColorPicker.cs**檔案的內容（讓 `using` 的語句保持不變）：

![](walkthrough-images/os07.png "The InfColorPickerControllerDelegate file")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

在上述所建立的系結專案中開啟這兩個檔案。 複製**InfColorPicker.cs**檔案（從**Mac 組建主機**）的內容，並將它貼到**ApiDefinition.cs**檔案中，將現有的 `namespace ...` 程式碼區塊取代為**InfColorPicker.cs**檔案的內容（離開`using` 語句不變）。

-----

<a name="Normalize_the_API_Definitions"/>

## <a name="normalize-the-api-definitions"></a>標準化 API 定義

目標 Sharpie 有時候會有問題 `Delegates`轉譯，因此我們需要修改 `InfColorPickerControllerDelegate` 介面的定義，並以下列程式碼取代 `[Protocol, Model]` 行：

```csharp
[BaseType(typeof(NSObject))]
[Model]
```

如此一來，定義就會如下所示：

[![](walkthrough-images/os11.png "The definition")](walkthrough-images/os11.png#lightbox)

接下來，我們對 `InfColorPicker.enums.cs` 檔案的內容執行相同的動作，將它們複製並貼上 `StructsAndEnums.cs` 檔案，讓 `using` 的語句保持不變：

[![](walkthrough-images/os09.png "The contents the StructsAndEnums.cs file ")](walkthrough-images/os09.png#lightbox)

您也可能會發現目標 Sharpie 已標注具有 `[Verify]` 屬性的系結。 這些屬性工作表示您應該藉由比較系結與原始的 C/目標-C 宣告（將會在系結宣告上方的批註中提供），來驗證目標 Sharpie 是否執行正確的動作。 驗證系結之後，您應該移除驗證屬性。 如需詳細資訊，請參閱[驗證](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)指南。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

此時，我們的系結專案應該已經完成，而且可以開始建立。 讓我們建立系結專案，並確定我們最後沒有發生錯誤：

[建立系結專案，並確定沒有任何錯誤](walkthrough-images/os12.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

此時，我們的系結專案應該已經完成，而且可以開始建立。 讓我們來建立系結專案，並確定我們最後沒有發生錯誤。

-----

<a name="Using_the_Binding"/>

## <a name="using-the-binding"></a>使用系結

請遵循下列步驟來建立範例 iPhone 應用程式，以使用上面建立的 iOS 系結程式庫：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. **建立 Xamarin Ios 專案**-將名為**InfColorPickerSample**的新 Xamarin ios 專案新增至方案，如下列螢幕擷取畫面所示：

    ![](walkthrough-images/use01.png "Adding a Single View App")

    ![](walkthrough-images/use01a.png "Setting the Identifier")

1. **將參考加入至**系結專案-更新**InfColorPickerSample**專案，使其具有**InfColorPickerBinding**專案的參考：

    ![](walkthrough-images/use02.png "Adding Reference to the Binding Project")

1. **建立 IPhone 使用者介面**-按兩下**InfColorPickerSample**專案中的**Mainstoryboard.storyboard**檔案，以在 iOS 設計工具中進行編輯。 將**按鈕**新增至視圖，並 `ChangeColorButton`中呼叫，如下所示：

    ![](walkthrough-images/use03.png "Adding a Button to the view")

1. **新增 InfColorPickerView。 xib** -InfColorPicker 目標-C 程式庫包含**xib**檔案。 Xib 在系結專案中不會包含這個 **.** ，這會在我們的範例應用程式中造成執行階段錯誤。 這個方法的因應措施是將**xib**檔案新增至我們的 Xamarin iOS 專案。 選取 Xamarin 專案，按一下滑鼠右鍵並選取 **新增 > 新增**檔案，然後新增**xib**檔案，如下列螢幕擷取畫面所示：

    ![](walkthrough-images/use04.png "Add the InfColorPickerView.xib")

1. 當系統要求時，請將**xib**檔案複製到專案中。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. **建立 Xamarin Ios 專案**-使用**單一 View 應用程式**範本，新增名為**InfColorPickerSample**的新 Xamarin. ios 專案：

    [![iOS 應用程式（Xamarin）專案](walkthrough-images/use01.w157-sml.png)](walkthrough-images/use01.w157.png#lightbox)

    [![選取範本](walkthrough-images/use01-2.w157-sml.png)](walkthrough-images/use01-2.w157.png#lightbox)

1. **將參考加入至**系結專案-更新**InfColorPickerSample**專案，使其具有**InfColorPickerBinding**專案的參考：

    ![](walkthrough-images/use02vs.png "Add Reference to the Binding Project")

1. **建立 IPhone 使用者介面**-按兩下**InfColorPickerSample**專案中的**Mainstoryboard.storyboard**檔案，以在 iOS 設計工具中進行編輯。 將**按鈕**新增至視圖，並 `ChangeColorButton`中呼叫，如下所示：

    ![](walkthrough-images/use03vs.png "Create the iPhone User Interface")

1. **新增 InfColorPickerView。 xib** -InfColorPicker 目標-C 程式庫包含**xib**檔案。 Xib 在系結專案中不會包含這個 **.** ，這會在我們的範例應用程式中造成執行階段錯誤。 解決此問題的因應措施是從我們的**Mac 組建主機**，將**xib**檔案新增至我們的 Xamarin. iOS 專案。 選取 [Xamarin] 專案，按一下滑鼠右鍵並選取 [**新增** > **現有專案 ...** ]，然後新增**xib**檔案。

-----

接下來，讓我們快速查看目標-C 中的通訊協定，以及如何在系結和C#程式碼中處理它們。

### <a name="protocols-and-xamarinios"></a>通訊協定和 Xamarin iOS

在目標-C 中，通訊協定會定義可在特定情況下使用的方法（或訊息）。 在概念上，它們與中C#的介面非常類似。 目標 C 通訊協定和C#介面之間的一個主要差異在於，通訊協定可以有選擇性的方法-類別不需要執行的方法。 目標-C 會使用 @optional 關鍵字來指示哪些方法是選擇性的。 如需通訊協定的詳細資訊，請參閱[事件、通訊協定和委派](~/ios/app-fundamentals/delegates-protocols-and-events.md)。

**InfColorPickerController**有一個這種通訊協定，如下列程式碼片段所示：

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

**InfColorPickerController**會使用此通訊協定來通知用戶端，使用者已挑選新的色彩，而且**InfColorPickerController**已完成。 目標 Sharpie 會對應此通訊協定，如下列程式碼片段所示：

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

編譯系結程式庫時，Xamarin 會建立名為 `InfColorPickerControllerDelegate`的抽象基類，它會以虛擬方法來執行此介面。

有兩種方式可讓我們在 Xamarin iOS 應用程式中執行此介面：

- **強式委派**-使用強式委派時， C#需要建立子類別`InfColorPickerControllerDelegate`的類別，並覆寫適當的方法。 **InfColorPickerController**會使用此類別的實例來與其用戶端進行通訊。
- **弱式委派**-弱式委派是稍微不同的技術，牽涉到在某個類別上建立公用方法（例如 `InfColorPickerSampleViewController`），然後透過 `Export` 屬性將該方法公開至 `InfColorPickerDelegate` 通訊協定。

強式委派提供 Intellisense、型別安全和更好的封裝。 基於這些理由，您應該在可以使用強式委派的地方，而不是弱式委派。

在此逐步解說中，我們將討論這兩種技術：第一次執行強式委派，然後說明如何執行弱式委派。

### <a name="implementing-a-strong-delegate"></a>執行強式委派

使用強式委派來回應 `colorPickerControllerDidFinish:` 訊息，以完成 Xamarin iOS 應用程式：

子**類別 InfColorPickerControllerDelegate** -將新類別新增至名為 `ColorSelectedDelegate`的專案。 編輯類別，使其具有下列程式碼：

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

Xamarin 會藉由建立名為 `InfColorPickerControllerDelegate`的抽象基類，來系結目標-C 委派。 將此類型子類別化，並覆寫 `ColorPickerControllerDidFinish` 方法，以存取 `InfColorPickerController`的 `ResultColor` 屬性值。

**建立 ColorSelectedDelegate 的實例**-我們的事件處理常式將需要我們在上一個步驟中建立之 `ColorSelectedDelegate` 類型的實例。 編輯類別 `InfColorPickerSampleViewController`，並將下列執行個體變數新增至類別：

```csharp
ColorSelectedDelegate selector;
```

**初始化 ColorSelectedDelegate 變數**-若要確保 `selector` 是有效的實例，請更新 `ViewController` 中 `ViewDidLoad` 的方法，以符合下列程式碼片段：

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();
  ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
  selector = new ColorSelectedDelegate (this);
}
```

**執行方法 HandleTouchUpInsideWithStrongDelegate** -下一步會在使用者觸及**ColorChangeButton**時，執行的事件處理常式。 編輯 `ViewController`，並新增下列方法：

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

我們會先透過靜態方法取得 `InfColorPickerController` 的實例，並透過屬性 `InfColorPickerController.Delegate`，讓該實例知道我們的強式委派。 此屬性是由目標 Sharpie 為我們自動產生的。 最後，我們會呼叫 `PresentModallyOverViewController` 來顯示 `InfColorPickerSampleViewController.xib` 的 view，讓使用者可以選取色彩。

**執行應用程式**-至此，我們已完成所有程式碼。 如果您執行應用程式，您應該能夠變更 `InfColorColorPickerSampleView` 的背景色彩，如下列螢幕擷取畫面所示：

[![](walkthrough-images/run01.png "Running the Application")](walkthrough-images/run01.png#lightbox)

恭喜您！ 此時，您已成功建立並系結了要在 Xamarin iOS 應用程式中使用的目標-C 程式庫。 接下來，讓我們來瞭解如何使用弱式委派。

### <a name="implementing-a-weak-delegate"></a>執行弱式委派

Xamarin 不會將系結至特定委派之目標-C 通訊協定的類別子類別化，而是可讓您在任何衍生自 `NSObject`的類別中執行通訊協定方法、使用 `ExportAttribute`裝飾您的方法，然後提供適當的選取器。 當您採用這種方法時，您可以將類別的實例指派給 `WeakDelegate` 屬性，而不是 `Delegate` 屬性。 弱式委派可讓您彈性地將委派類別細分為不同的繼承階層。 讓我們看看如何在我們的 Xamarin iOS 應用程式中執行和使用弱式委派。

**建立 TouchUpInside 的事件處理常式**-讓我們為 [變更背景色彩] 按鈕的 `TouchUpInside` 事件建立新的事件處理常式。 這個處理常式將會填滿與我們在上一節中建立之 `HandleTouchUpInsideWithStrongDelegate` 處理常式相同的角色，但會使用弱式委派，而不是強式委派。 編輯類別 `ViewController`，並新增下列方法：

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```

**更新 ViewDidLoad** -我們必須變更 `ViewDidLoad`，讓它使用我們剛才建立的事件處理常式。 編輯 `ViewController`，並將 `ViewDidLoad` 變更為類似下列程式碼片段：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**處理 colorPickerControllerDidFinish： Message** -當 `ViewController` 完成時，iOS 會將訊息 `colorPickerControllerDidFinish:` 傳送至 `WeakDelegate`。 我們需要建立可處理C#此訊息的方法。 若要這樣做，我們會C#建立方法，然後使用`ExportAttribute`來裝飾它。 編輯 `ViewController`，並將下列方法新增至類別：

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

執行應用程式。 它現在的行為應該與之前完全相同，但它使用弱式委派，而不是強式委派。 此時，您已成功完成本逐步解說。 您現在應該已經瞭解如何建立和使用 Xamarin 的系結專案。

## <a name="summary"></a>總結

本文逐步解說建立和使用 Xamarin iOS 系結專案的程式。 首先，我們討論了如何將現有的目標 C 程式庫編譯成靜態程式庫。 接著，我們討論了如何建立 Xamarin iOS 系結專案，以及如何使用目標 Sharpie 來產生目標 C 程式庫的 API 定義。 我們已討論如何更新和調整產生的 API 定義，使其適用于公用。 在 Xamarin iOS 系結專案完成之後，我們已移至在 Xamarin iOS 應用程式中取用該系結，並著重于使用強式委派和弱式委派。

## <a name="related-links"></a>相關連結

- [系結範例（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/infcolorpicker)
- [繫結 Objective-C 程式庫](~/cross-platform/macios/binding/objective-c-libraries.md)
- [系結詳細資料](~/cross-platform/macios/binding/overview.md)
- [系結類型參考指南](~/cross-platform/macios/binding/binding-types-reference.md)
- [適用於 Objective-C 開發人員的 Xamarin](~/ios/get-started/objective-c-developers/index.md)
- [Framework 設計方針](https://msdn.microsoft.com/library/ms229042.aspx)
