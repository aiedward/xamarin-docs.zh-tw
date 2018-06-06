---
title: 使用 watchOS 當地語系化的 Xamarin
description: 本文件說明如何當地語系化 watchOS 使用 Xamarin 建置的應用程式。 其中也會討論監看式應用程式，監看式擴充功能，在程式碼中的字串文字、 測試以及多個分鏡腳本。
ms.prod: xamarin
ms.assetid: 55834877-757B-4860-AF2F-933A948BE38D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 4f158f1c8699ad5090eb7fade8af8918c8881d95
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790775"
---
# <a name="working-with-watchos-localization-in-xamarin"></a>使用 watchOS 當地語系化的 Xamarin

_調整多國語言的應用程式 watchOS_

![](localization-images/both-languages-sml.png "Apple Watch 顯示當地語系化的內容")

使用標準 iOS 方法當地語系化 watchOS 應用程式：

- 使用**當地語系化 ID**分鏡腳本的項目，
- **.strings**分鏡腳本，相關聯的檔案和
- **Localizable.strings**檔案的程式碼中使用的文字。

預設的分鏡腳本與資源位於**基底**目錄，和特定語言翻譯和其他資源會儲存在 **.lproj**目錄。
iOS 和 OS 監看式會自動使用使用者的語言選擇來載入正確的字串和資源。

由於 Apple Watch 應用程式有兩個部分-監看式應用程式以及 Watch 擴充功能-當地語系化字串資源需要在兩個地方，視使用的方式而定。

當地語系化的文字和資源會*不同*監看式應用程式和 watch 擴充功能中。

## <a name="watch-app"></a>監看式應用程式

監看式應用程式包含描述應用程式的使用者介面的分鏡腳本。 任何控制項 (例如`Label`和`Image`) 支援當地語系化有**當地語系化 ID**。

每個語言特有 **.lproj**目錄應包含 **.strings**使用翻譯檔案，每個項目 (使用**當地語系化 ID**)，以及映像分鏡腳本所參考。

## <a name="watch-extension"></a>Watch 擴充功能

監看式延伸模組是應用程式程式碼執行的所在。 從程式碼顯示給使用者的任何文字必須當地語系化延伸模組中，而不是在監看式應用程式。

延伸模組也應該包含特定語言 **.lproj**目錄，但 **.strings**檔案只需要翻譯用於程式碼中的文字。

## <a name="globalizing-the-watch-solution"></a>全球化 監看式方案

全球化是程序可確保應用程式當地語系化。
這表示設計具有記住的不同文字長度分鏡腳本的監看式應用程式，請確保每個螢幕的配置調整適當地顯示根據文字內容。 您也必須確定監看式延伸模組程式碼中參考的任何字串可使用翻譯`LocalizedString`方法。

### <a name="watch-app"></a>監看式應用程式

根據預設監看式應用程式未設定以進行當地語系化。 您必須移動預設分鏡腳本檔案，並建立其他翻譯目錄：

1. 建立**Base.lproj**目錄和移動**Interface.storyboard**到其中。

2. 建立 **<language>.lproj**目錄，針對每個您想要支援的語言。

3. **.Lproj**目錄應該包含**Interface.strings**文字檔案 （檔案名稱應該符合 storboard 名稱）。 您可以選擇性地放置任何需要這些目錄中的當地語系化的影像。

進行這些變更 （僅英文和西班牙文語言已加入的檔案） 之後，監看式應用程式專案看起來像這樣：

  ![](localization-images/watchapp-solution.png "監看式應用程式專案，以英文和西班牙文語言檔案")

#### <a name="storyboard-text"></a>分鏡腳本文字

當您編輯分鏡腳本時，選取每個項目，請注意**當地語系化 ID** ，會出現在**屬性**板：

  [![](localization-images/storyboard-sml.png "會出現在屬性板當地語系化 ID")](localization-images/storyboard.png#lightbox)

在**Base.lproj**資料夾中，建立索引鍵-值組，如下所示的機碼由所構成**當地語系化 ID**和屬性名稱的控制項上，加入接一個點 (`.`)。

```csharp
"AgC-eL-Hgc.title" = "WatchL10nEN"; // interface controller title
"0.text" = "Welcome to WatchL10n"; // Welcome
"1.text" = "Language settings are in Apple Watch App"; // How to change language
"2.title" = "Greetings"; // Greeting
"6.title" = "Detail";
"39.text" = "Second screen";
```

請注意，在此範例中，**當地語系化 ID**可以是簡單數字字串 （例如。 "0"、"1"，等) 或更複雜的字串 （例如"AgC-eL-Hgc")。 `Label` 控制項具有`Text`屬性和`Button`有`Title`屬性，會反映在當地語系化的值會設定位的方式請務必使用小寫的屬性名稱，如上述範例所示。

分鏡腳本上監看式轉譯時，正確的值，會自動擷取並顯示根據使用者所選取的語言。

#### <a name="storyboard-images"></a>分鏡腳本映像

範例解決方案也包含**gradient@2x.png**每個語言資料夾中的映像。 此映像 （例如可以針對每個語言不同 它內嵌文字需要轉譯，或使用當地語系化遙控器）。

只要設定 映像的**映像**分鏡腳本和正確的映像中的屬性會呈現上監看式，根據使用者所選取的語言。

![](localization-images/storyboard-image.png "設定影像影像 屬性中的分鏡腳本")

注意： 因為所有 Apple 監看 Retina 顯示器只有**@2x**所需的映像的版本。 您不需要指定**@2x**腳本中。

### <a name="watch-extension"></a>Watch 擴充功能

監看式延伸模組需要類似的目錄結構來支援當地語系化，但是沒有任何分鏡腳本。 中的延伸模組的當地語系化的字串只是 C# 程式碼所參考。

![](localization-images/watchextension-solution.png "監看式延伸目錄結構來支援當地語系化")

#### <a name="strings-in-code"></a>在程式碼中的字串

**Localizable.strings**檔案有稍微不同的結構是否分鏡腳本與相關聯。 在此情況下，我們可以選擇任何 「 索引鍵 」 字串。Apple 的建議是使用索引鍵，以反映實際的預設語言中會顯示的文字：

```csharp
"Breakfast time" = "Breakfast time!"; // morning
"Lunch time" = "Lunch time!"; // midday
"Dinner time" = "Dinner time!"; // evening
"Bed time" = "Bed time!"; // night
```

`NSBundle.MainBundle.LocalizedString`方法用來解析成轉譯的對應字串，如下列程式碼所示。

```csharp
var display = "Breakfast time";
var localizedDisplay =
  NSBundle.MainBundle.LocalizedString (display, comment:"greeting");
displayText.SetText (localizedDisplay);
```

#### <a name="images-in-code"></a>在程式碼中的映像

程式碼會填入的映像可以設定兩種方式。

1. 您可以變更`Image`控制其值設定為影像的字串名稱，已存在於 監看式應用程式時，例如

  ```csharp
  displayImage.SetImage("gradient"); // image in Watch App (as shown above)
  ```

2. 您也可以將影像從延伸模組中，移到監看式使用`FromBundle`和應用程式會自動選擇使用者的語言選擇的正確影像。 在範例方案中沒有映像**language@2x.png**中每一種語言資料夾，然後它會顯示在`DetailController`使用下列程式碼：

  ```csharp
  using (var image = UIImage.FromBundle ("language")) {
    displayImage.SetImage (image);
  }
  ```

  請注意，您不需要指定**@2x**時參照映像的檔案名稱。

第二種方法也是適用於您從遠端伺服器上監看式; 呈現下載映像不過在此情況下您應該確定您所下載的映像已正確當地語系化根據使用者的喜好設定。

## <a name="localization"></a>當地語系化

一旦您已設定您的方案，轉譯器需要處理您 **.strings**檔案和您想要支援每種語言的映像。

您可以建立多個 **.lproj**目錄，就需要 （一個用於每個支援的語言）。 名稱使用的語言代碼，例如**en**， **es**， **de**， **ja**， **PT-BR**，（適用於英文等西班牙文、 德文、 日文及葡萄牙文 （巴西） 分別)。

「 附加 」 範例會使用 （電腦所產生） 翻譯示範如何當地語系化 watchOS 應用程式。

### <a name="watch-app"></a>監看式應用程式

這些值可用來轉譯使用者介面中監看式應用程式的分鏡腳本所定義。 *金鑰*值是每個分鏡腳本控制項的組合**當地語系化 ID**和翻譯的屬性。

建議您加入註解，以便在轉譯器可讓您知道轉譯應該是包含檔案的原始文字。

#### <a name="eslprojinterfacestrings"></a>es.lproj/Interface.strings

分鏡腳本 （機器翻譯） 西班牙文語言字串如下所示。 最好將註解加入至每一行，因為很難知道**當地語系化 ID**否則參考：

```csharp
"AgC-eL-Hgc.title" = "Spanish"; // app screen heading
"0.text" = "Bienvenido a WatchL10n"; // Welcome to WatchL10n
"1.text" = "Ajustes de idioma están en Apple Watch App"; // Change the language in the Apple Watch App
"2.title" = "Saludos"; // Greetings
"6.title" = "2nd"; // second screen heading
"39.text" = "Segunda pantalla"; // second screen
```

### <a name="watch-extension"></a>Watch 擴充功能

這些值會轉譯資訊才會顯示給使用者程式碼中使用。 *金鑰*雖然它們撰寫程式碼中，選取由開發人員，而且通常包含要轉譯實際的字串。

#### <a name="eslprojlocalizablestrings-file"></a>es.lproj/Localizable.strings 檔案

（機器翻譯） Spansish 語言字串：

```csharp
"Breakfast time" = "la hora del desayuno"; // morning
"Lunch time" = "hora de comer"; // midday
"Dinner time" = "hora de la cena"; // evening
"Bed time" = "la hora de dormir"; // night
```

## <a name="testing"></a>測試

若要變更語言喜好設定的方法不同模擬器和實體裝置。

### <a name="simulator"></a>模擬器

在模擬器中，選取要使用 iOS 進行測試的語言**設定**應用程式 （在模擬器首頁畫面中的灰色齒輪圖示）。

  ![](localization-images/sim-settings-sml.png "IOS 設定應用程式當地語系化設定")

### <a name="watch-device"></a>監看式裝置

使用 監看式測試，變更 監看式語言**Apple Watch**配對的 iPhone 上的應用程式。

  ![](localization-images/phone-settings-sml.png "變更 Apple Watch 應用程式，在配對的 iPhone 上監看式的語言")



## <a name="related-links"></a>相關連結

- [WatchLocalization （範例）](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchLocalization/)
