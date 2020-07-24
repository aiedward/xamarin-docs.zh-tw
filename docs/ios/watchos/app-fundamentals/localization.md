---
title: 在 Xamarin 中使用 watchOS 當地語系化
description: 本檔說明如何將以 Xamarin 建立的 watchOS 應用程式當地語系化。 其中討論監看式應用程式、監看延伸模組、程式碼中的字串、分鏡腳本文字、測試等等。
ms.prod: xamarin
ms.assetid: 55834877-757B-4860-AF2F-933A948BE38D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 9e33f843d836ed5b66ed36397c9367dc671c51ed
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "86996249"
---
# <a name="working-with-watchos-localization-in-xamarin"></a>在 Xamarin 中使用 watchOS 當地語系化

_針對多種語言調整 watchOS 應用程式_

![顯示當地語系化內容 Apple Watch](localization-images/both-languages-sml.png)

watchOS 應用程式會使用標準的 iOS 方法進行當地語系化：

- 在分鏡腳本元素上使用**當地語系化識別碼**，
- **. 字串**與腳本相關聯的檔案，以及
- 適用于程式碼中所用文字的可**當地語系化字串**檔案。

預設的分鏡腳本和資源位於**基底**目錄中，而語言特定的翻譯和其他資源則會儲存在 **.lproj**目錄中。
iOS 和監看式作業系統會自動使用使用者的語言選擇來載入正確的字串和資源。

因為 Apple Watch 應用程式有兩個部分-監看式應用程式和監看延伸模組-在兩個地方都需要當地語系化的字串資源，視其使用方式而定。

[監看式] 應用程式和 [監看式] 延伸模組中的當地語系化文字和資源會*有所不同*。

## <a name="watch-app"></a>監看應用程式

監看式應用程式包含說明應用程式使用者介面的分鏡腳本。 支援當地語系化的任何控制項（例如 `Label` 和 `Image` ）都有**當地語系化識別碼**。

每個語言特定**的 .lproj**目錄都應該包含含有每個元素翻譯的**字串**檔案（使用**當地語系化識別碼**），以及分鏡腳本所參考的影像。

## <a name="watch-extension"></a>監看延伸模組

監看式延伸模組是您的應用程式程式碼執行所在的位置。 從程式碼對使用者顯示的任何文字，都必須在延伸模組中當地語系化，而不是在監看式應用程式中。

延伸模組也應該包含語言特定**的 .lproj**目錄，但是**字串**檔案只需要翻譯用於程式碼中的文字。

## <a name="globalizing-the-watch-solution"></a>全球化監看式解決方案

全球化是讓應用程式當地語系化的過程。
對於監看式應用程式，這表示以不同的文字長度設計分鏡腳本，以確保每個螢幕佈局會根據所顯示的文字，適當地調整。 您也必須確定可以使用方法來轉譯監看延伸模組程式碼中所參考的任何字串 `LocalizedString` 。

### <a name="watch-app"></a>監看應用程式

根據預設，監看式應用程式不會設定為當地語系化。 您需要移動預設分鏡腳本檔案，並為您的翻譯建立其他一些目錄：

1. 建立 **.lproj**目錄，並將**介面**移至其中。分鏡腳本。

2. 針對您想要支援的每種語言，建立** \<language> .lproj**目錄。

3. **.Lproj**目錄應該包含**介面字串**文字檔（檔案名應符合 storboard 的名稱）。 您可以選擇性地將需要當地語系化的任何影像放在這些目錄中。

進行這些變更之後，watch 應用程式專案看起來會像這樣（僅新增英文和西班牙文語言檔案）：

  ![具有英文和西班牙文語言檔案的監看式應用程式專案](localization-images/watchapp-solution.png)

#### <a name="storyboard-text"></a>分鏡腳本文字

當您編輯腳本時，請選取每個元素，並注意出現在**Properties** pad 中的 [**當地語系化識別碼**]：

  [![出現在 Properties pad 中的當地語系化識別碼](localization-images/storyboard-sml.png)](localization-images/storyboard.png#lightbox)

在 **.lproj**資料夾中，建立如下所示的索引鍵/值組，其中的索引鍵是由**當地語系化識別碼**和控制項上的屬性名稱所組成，並以點（ `.` ）聯結。

```csharp
"AgC-eL-Hgc.title" = "WatchL10nEN"; // interface controller title
"0.text" = "Welcome to WatchL10n"; // Welcome
"1.text" = "Language settings are in Apple Watch App"; // How to change language
"2.title" = "Greetings"; // Greeting
"6.title" = "Detail";
"39.text" = "Second screen";
```

請注意，在此範例中，**當地語系化識別碼**可以是簡單的數位字串（例如 "0"、"1" 等）或更複雜的字串（例如 "AgC-eL-Hgc"）。 `Label`控制項具有 `Text` 屬性，而且的 `Button` `Title` 屬性會反映其當地語系化值的設定方式，請務必使用小寫屬性名稱，如上述範例所示。

當腳本在 watch 上轉譯時，將會根據使用者選取的語言，自動解壓縮並顯示正確的值。

#### <a name="storyboard-images"></a>分鏡腳本影像

範例解決方案也會 **gradient@2x.png** 在每個語言資料夾中包含一個影像。 每種語言的此映射可能不同（例如 它可能會有內嵌文字，需要翻譯或使用當地語系化的圖示）。

只要在分鏡腳本中設定影像的**image**屬性，就會根據使用者選取的語言，在監看式上轉譯正確的影像。

![在分鏡腳本中設定 images 影像屬性](localization-images/storyboard-image.png)

注意：因為所有 Apple watch 都有 Retina 顯示，所以只 **@2x** 需要影像的版本。 您不需要 **@2x** 在腳本中指定。

### <a name="watch-extension"></a>監看延伸模組

監看式延伸模組需要類似的目錄結構來支援當地語系化，但沒有分鏡腳本。 延伸模組中的當地語系化字串只是 c # 程式碼所參考的字串。

![支援當地語系化的監看式延伸目錄結構](localization-images/watchextension-solution.png)

#### <a name="strings-in-code"></a>程式碼中的字串

可**當地語系化的字串**檔案有稍微不同于與分鏡腳本相關聯的結構。 在此情況下，我們可以選擇任何「索引鍵」字串;Apple 的建議是使用索引鍵來反映會以預設語言顯示的實際文字：

```csharp
"Breakfast time" = "Breakfast time!"; // morning
"Lunch time" = "Lunch time!"; // midday
"Dinner time" = "Dinner time!"; // evening
"Bed time" = "Bed time!"; // night
```

`NSBundle.MainBundle.LocalizedString`方法可用來將字串解析成其已轉譯的對應專案，如下列程式碼所示。

```csharp
var display = "Breakfast time";
var localizedDisplay =
  NSBundle.MainBundle.LocalizedString (display, comment:"greeting");
displayText.SetText (localizedDisplay);
```

#### <a name="images-in-code"></a>程式碼中的影像

程式碼所填入的影像可以透過兩種方式來設定。

1. 您可以變更 `Image` 控制項，其方式是將其值設定為監看式應用程式中已存在之映射的字串名稱，例如

    ```csharp
    displayImage.SetImage("gradient"); // image in Watch App (as shown above)
    ```

2. 您可以使用將影像從延伸模組移至監看式 `FromBundle` ，應用程式將會自動選擇正確的影像以用於使用者的語言選擇。 在範例方案中 **language@2x.png** ，每個語言資料夾中都會有一個影像，並 `DetailController` 使用下列程式碼顯示：

    ```csharp
    using (var image = UIImage.FromBundle ("language")) {
        displayImage.SetImage (image);
    }
    ```

    請注意，在 **@2x** 參照影像的檔案名時，您不需要指定。

如果您從遠端伺服器下載影像以在監看式上轉譯，第二種方法也適用。不過在此情況下，您應該確定您下載的影像已根據使用者的喜好設定正確當地語系化。

## <a name="localization"></a>當地語系化

一旦您設定好解決方案，轉譯程式將需要針對您想要支援的每種語言，處理您的**字串**檔案和影像。

您可以視需要建立任意數量的 **.lproj**目錄（每個支援的語言各一個）。 它們是使用語言代碼（例如**en**、 **es**、 **de**、 **ja**、 **pt-BR**等等）來命名（分別適用于英文、西班牙文、德文、日文和葡萄牙文（巴西））。

附加的範例會使用（機器產生的）翻譯來示範如何將 watchOS 應用程式當地語系化。

### <a name="watch-app"></a>監看應用程式

這些值可用來轉譯監看式應用程式的分鏡腳本中定義的使用者介面。 索引*鍵值*是每個分鏡腳本控制項的**當地語系化識別碼**和要轉譯之屬性的組合。

建議將包含原始文字的批註新增至檔案，讓轉譯者知道轉譯的內容。

#### <a name="eslprojinterfacestrings"></a>es. .lproj/Interface. 字串

分鏡腳本的（機器翻譯的）西班牙文語言字串如下所示。 將批註新增至每一行會很有説明，因為很難以得知**當地語系化識別碼**的參考，否則：

```csharp
"AgC-eL-Hgc.title" = "Spanish"; // app screen heading
"0.text" = "Bienvenido a WatchL10n"; // Welcome to WatchL10n
"1.text" = "Ajustes de idioma están en Apple Watch App"; // Change the language in the Apple Watch App
"2.title" = "Saludos"; // Greetings
"6.title" = "2nd"; // second screen heading
"39.text" = "Segunda pantalla"; // second screen
```

### <a name="watch-extension"></a>監看延伸模組

這些值會在程式碼中用來轉譯資訊，然後才向使用者顯示。 開發人員在撰寫程式碼時選取了*金鑰*，而且通常會包含要轉譯的實際字串。

#### <a name="eslprojlocalizablestrings-file"></a>es. .lproj/可當地語系化的字串檔案

（機器翻譯） Spansish 語言字串：

```csharp
"Breakfast time" = "la hora del desayuno"; // morning
"Lunch time" = "hora de comer"; // midday
"Dinner time" = "hora de la cena"; // evening
"Bed time" = "la hora de dormir"; // night
```

## <a name="testing"></a>測試

變更語言喜好設定的方法在模擬器與實體裝置之間有所不同。

### <a name="simulator"></a>模擬器

在模擬器上，選取要使用 iOS**設定**應用程式測試的語言（模擬器主畫面中的灰色齒輪圖示）。

  ![IOS 設定應用程式當地語系化設定](localization-images/sim-settings-sml.png)

### <a name="watch-device"></a>監看裝置

使用 watch 進行測試時，請在配對的 iPhone 上變更**Apple Watch**應用程式中的監看式語言。

  ![在配對的 iPhone 上變更 Apple Watch 應用程式中的監看式語言](localization-images/phone-settings-sml.png)

## <a name="related-links"></a>相關連結

- [WatchLocalization （範例）](https://developer.xamarin.com//samples/monotouch/watchOS/WatchLocalization/)
