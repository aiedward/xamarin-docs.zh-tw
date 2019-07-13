---
title: 使用 watchOS 在 Xamarin 中的當地語系化
description: 本文件說明如何使用 Xamarin 建置的 watchOS 應用程式的當地語系化。 它討論 watch 應用程式，監看式延伸模組，在程式碼中的字串文字、 測試及更多分鏡腳本。
ms.prod: xamarin
ms.assetid: 55834877-757B-4860-AF2F-933A948BE38D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: bed8180c513eefd5765be767a5dca7cecefa6101
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865955"
---
# <a name="working-with-watchos-localization-in-xamarin"></a>使用 watchOS 在 Xamarin 中的當地語系化

_調整您的 watchOS 應用程式，針對多種語言_

![](localization-images/both-languages-sml.png "Apple Watch 顯示當地語系化的內容")

watchOS 應用程式當地語系化使用標準的 iOS 方法：

- 使用**當地語系化 ID**在分鏡腳本的項目、
- **.strings**分鏡腳本，相關聯的檔案和
- **Localizable.strings**檔案的程式碼中使用的文字。

預設的分鏡腳本與資源位於**基底**目錄中，和特定語言翻譯和其他資源會儲存在 **.lproj**目錄。
iOS 和監看式作業系統會自動使用使用者的語言選擇來載入正確的字串和資源。

因為 Apple Watch 應用程式有兩個部分-監控應用程式與監控擴充功能-在兩個位置，根據使用方式需要資源的當地語系化字串。

當地語系化的文字和資源都*不同*中監控應用程式與監控擴充功能。

## <a name="watch-app"></a>Watch 應用程式

Watch 應用程式包含分鏡腳本，描述應用程式的使用者介面。 任何控制項 (例如`Label`並`Image`) 支援當地語系化有**當地語系化 ID**。

每個語言特有 **.lproj**目錄應該包含 **.strings**含翻譯檔案，每個項目 (使用**當地語系化 ID**)，以及映像分鏡腳本所參考。

## <a name="watch-extension"></a>監看式延伸模組

監看式延伸模組是執行您的應用程式程式碼。 從程式碼顯示給使用者的任何文字，就必須在延伸模組，而不是在 watch 應用程式當地語系化。

擴充功能也應該包含特定語言 **.lproj**目錄，但 **.strings**檔案只需要翻譯會在您的程式碼的文字。

## <a name="globalizing-the-watch-solution"></a>全球化 監看式解決方案

全球化是讓應用程式的可當地語系化的程序。
這表示設計具有記住的不同文字長度分鏡腳本的監看式應用程式，確保每個畫面版面配置調整適當地顯示根據文字內容。 您也必須確定監看式延伸模組程式碼中參考的任何字串可以使用轉譯`LocalizedString`方法。

### <a name="watch-app"></a>Watch 應用程式

根據預設 watch 應用程式未設定進行當地語系化。 您需要移動預設分鏡腳本檔案並建立一些其他目錄的翻譯：

1. 建立**Base.lproj**目錄和移動**Interface.storyboard**到其中。

2. 建立 **<language>.lproj**針對您想要支援每種語言的目錄。

3. **.Lproj**目錄應該包含**Interface.strings**文字檔案 （檔案名稱應該符合 storboard 的名稱）。 您可以選擇性地放置任何需要當地語系化這些目錄中的映像。

進行這些變更 （僅英文和西班牙文語言已新增檔案） 之後，在監看式應用程式的專案看起來像這樣：

  ![](localization-images/watchapp-solution.png "監看式應用程式專案，具有英文和西班牙文語言檔案")

#### <a name="storyboard-text"></a>分鏡腳本的文字

當您編輯分鏡腳本時，選取每個項目，請注意**當地語系化 ID**出現在**屬性**板：

  [![](localization-images/storyboard-sml.png "在 [屬性] 面板會顯示當地語系化 ID")](localization-images/storyboard.png#lightbox)

在  **Base.lproj**資料夾中，建立索引鍵 / 值組，如下所示，其中索引鍵由形成**當地語系化 ID**和屬性名稱的控制項上，加入以點 (`.`)。

```csharp
"AgC-eL-Hgc.title" = "WatchL10nEN"; // interface controller title
"0.text" = "Welcome to WatchL10n"; // Welcome
"1.text" = "Language settings are in Apple Watch App"; // How to change language
"2.title" = "Greetings"; // Greeting
"6.title" = "Detail";
"39.text" = "Second screen";
```

請注意，在此範例中，**當地語系化 ID**可以是簡單數字字串 （例如。 "0"、"1"，等等) 或更複雜的字串 （例如"AgC-eL-Hgc")。 `Label` 控制項具有`Text`屬性和`Button`有`Title`屬性，這會反映在當地語系化的值會設定-的方式請務必使用小寫字母的屬性名稱，如上述範例所示。

在 監看式轉譯分鏡腳本時，正確的值，會自動擷取並顯示根據使用者所選取的語言。

#### <a name="storyboard-images"></a>分鏡腳本映像

範例解決方案也會包含 **gradient@2x.png** 每個語言資料夾中的映像。 此映像 （例如可能會針對每種語言不同。 它也可能會內嵌文字需要翻譯，或使用本地化遙控器）。

只要將設定的映像**映像**分鏡腳本和正確的映像中的屬性將會呈現在監看式，根據使用者所選取的語言。

![](localization-images/storyboard-image.png "在分鏡腳本中設定的映像映像屬性")

注意： 因為所有的 Apple Watch Retina 顯示器，只有 **@2x** 所需的映像的版本。 您不需要指定 **@2x** 在分鏡腳本。

### <a name="watch-extension"></a>監看式延伸模組

監看式延伸模組需要類似的目錄結構，以支援當地語系化，但是沒有任何分鏡腳本。 中的延伸模組的當地語系化的字串是只藉由參考C#程式碼。

![](localization-images/watchextension-solution.png "監看式延伸模組目錄結構，以支援當地語系化")

#### <a name="strings-in-code"></a>在程式碼中的字串

**Localizable.strings**檔案已經稍有不同的結構是否分鏡腳本與相關聯。 在此情況下，我們可以選擇任何 「 金鑰 」 的字串;Apple 的建議是使用索引鍵，以反映實際會以預設語言顯示的文字：

```csharp
"Breakfast time" = "Breakfast time!"; // morning
"Lunch time" = "Lunch time!"; // midday
"Dinner time" = "Dinner time!"; // evening
"Bed time" = "Bed time!"; // night
```

`NSBundle.MainBundle.LocalizedString`方法用來解析與其已翻譯字串，如下列程式碼所示。

```csharp
var display = "Breakfast time";
var localizedDisplay =
  NSBundle.MainBundle.LocalizedString (display, comment:"greeting");
displayText.SetText (localizedDisplay);
```

#### <a name="images-in-code"></a>在程式碼中的映像

兩種方式，就可以設定程式碼會填入的映像。

1. 您可以變更`Image`控制項，其值設定為影像的字串名稱，已經存在於 Watch 應用程式，例如

    ```csharp
    displayImage.SetImage("gradient"); // image in Watch App (as shown above)
    ```

2. 您也可以將映像從延伸模組中，移至監看式使用`FromBundle`，應用程式會自動選擇的使用者的語言選擇正確的映像。 在範例解決方案中沒有映像 **language@2x.png** 兩種語言資料夾，並會顯示在`DetailController`使用下列程式碼：

    ```csharp
    using (var image = UIImage.FromBundle ("language")) {
        displayImage.SetImage (image);
    }
    ```

    請注意，您不需要指定 **@2x** 參照映像的檔案名稱時。

第二種方法也是適用於您從遠端伺服器上監看式; 呈現下載映像不過在此情況下您應該確定您所下載的映像已正確當地語系化根據使用者的喜好設定。

## <a name="localization"></a>當地語系化

一旦您已設定您的解決方案，轉譯器需要處理您 **.strings**檔案和您想要支援每種語言的映像。

您可以建立多個 **.lproj**目錄，您需要 （一個用於每種支援語言）。 使用語言代碼，例如，所以稱為**en**， **es**， **de**，**日本**， **PT-BR**，等等 （適用於英文西班牙文、 德文、 日文和葡萄牙文 （巴西） 分別)。

附隨的範例會示範如何將當地語系化的 watchOS 應用程式使用 （機器產生） 的翻譯。

### <a name="watch-app"></a>Watch 應用程式

這些值用來轉譯在監看式應用程式的分鏡腳本中定義的使用者介面。 *金鑰*值是每個分鏡腳本控制項的組合**當地語系化 ID**和翻譯的屬性。

建議您將使轉譯器可讓您知道轉譯應該是包含檔案的原始文字的註解。

#### <a name="eslprojinterfacestrings"></a>es.lproj/Interface.strings

分鏡腳本 （機器翻譯） 西班牙文語言字串如下所示。 這是將註解新增至每一行，很有幫助，因為很難知道**當地語系化 ID**否則參考：

```csharp
"AgC-eL-Hgc.title" = "Spanish"; // app screen heading
"0.text" = "Bienvenido a WatchL10n"; // Welcome to WatchL10n
"1.text" = "Ajustes de idioma están en Apple Watch App"; // Change the language in the Apple Watch App
"2.title" = "Saludos"; // Greetings
"6.title" = "2nd"; // second screen heading
"39.text" = "Segunda pantalla"; // second screen
```

### <a name="watch-extension"></a>監看式延伸模組

在程式碼中使用這些值來轉譯才會顯示給使用者的資訊。 *金鑰*時它們撰寫程式碼，所選取開發人員，而且通常包含要轉譯的實際字串。

#### <a name="eslprojlocalizablestrings-file"></a>es.lproj/Localizable.strings 檔案

（機器翻譯） Spansish 語言字串：

```csharp
"Breakfast time" = "la hora del desayuno"; // morning
"Lunch time" = "hora de comer"; // midday
"Dinner time" = "hora de la cena"; // evening
"Bed time" = "la hora de dormir"; // night
```

## <a name="testing"></a>測試

方法可以變更語言喜好設定不同的模擬器和實體裝置。

### <a name="simulator"></a>模擬器

在模擬器中，選取要使用 iOS 進行測試的語言**設定**應用程式 （在模擬器首頁畫面的灰色齒輪圖示）。

  ![](localization-images/sim-settings-sml.png "IOS 設定應用程式當地語系化設定")

### <a name="watch-device"></a>監看式裝置

當以監看式測試，變更 監看式的語言**Apple Watch**配對的 iPhone 上的應用程式。

  ![](localization-images/phone-settings-sml.png "變更在配對的 iPhone 上的 Apple Watch 應用程式中的監看式的語言")



## <a name="related-links"></a>相關連結

- [WatchLocalization （範例）](https://developer.xamarin.com//samples/monotouch/watchOS/WatchLocalization/)
