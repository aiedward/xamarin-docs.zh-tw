---
title: 在 Xamarin 中的當地語系化
description: 本檔說明 iOS 當地語系化功能，以及如何在 Xamarin iOS 應用程式中使用這些功能。 其中討論語言、地區設定、字串檔案、啟動影像等。
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/28/2017
ms.openlocfilehash: c42b41f9b853fba58ef70b8bd2f8ab20a3369647
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84569233"
---
# <a name="localization-in-xamarinios"></a>在 Xamarin 中的當地語系化

_本檔涵蓋 iOS SDK 的當地語系化功能，以及如何使用 Xamarin 來存取它們。_

如需在必須處理非 Unicode 資料的應用程式中包含字元集/字碼頁的指示，請參閱[國際化編碼](encodings.md)。

## <a name="ios-platform-features"></a>iOS 平臺功能

本節說明 iOS 中的一些當地語系化功能。 請跳到[下一節](#localization-basics-in-ios)，以查看特定的程式碼和範例。

### <a name="language"></a>語言

使用者在 [**設定**] 應用程式中選擇其語言。 此設定會影響作業系統和應用程式所顯示的語言字串和影像。

若要判斷應用程式中所使用的語言，請取得的第一個元素 `NSBundle.MainBundle.PreferredLocalizations` ：

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

這個值會是語言代碼 `en` ，例如英文、西班牙文 `es` 、日文等等 `ja` 。傳回的值會限制為應用程式支援的其中一個當地語系化（使用回溯規則來判斷最符合的項）。

應用程式代碼不一定需要檢查此值– Xamarin 和 iOS 兩者都提供一些功能，可協助自動為使用者的語言提供正確的字串或資源。 本檔的其餘部分將說明這些功能。

> [!NOTE]
> 使用 `NSLocale.PreferredLanguages` 來判斷使用者的語言喜好設定，而不論應用程式支援的當地語系化為何。 這個方法所傳回的值會在 iOS 9 中變更;如需詳細資訊，請參閱[技術提示 TN2418](https://developer.apple.com/library/content/technotes/tn2418/_index.html) 。

### <a name="locale"></a>地區設定

使用者在 [設定] 應用程式中選擇其地區**設定**。 此設定會影響日期、時間、數位和貨幣格式化的方式。

這可讓使用者選擇是否要看到12小時或24小時的時間格式、其小數分隔符號是否為逗號或點，以及日期顯示的日、月和年的順序。

有了 Xamarin，您可以同時存取 Apple 的 iOS 類別（ `NSNumberFormatter` ）以及 System. 全球化中的 .net 類別。 開發人員應該評估哪個版本較適合其需求，因為每個都有不同的功能。 特別是，如果您要使用 StoreKit 來抓取和顯示應用程式內購買價格，您應該使用 Apple 的格式設定類別來取得傳回的價格資訊。

您可以使用下列兩種方式的其中一種來查詢目前的地區設定：

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

第一個值可由作業系統快取，因此不一定會反映使用者目前選取的地區設定。 使用第二個值來取得目前選取的地區設定。

> [!NOTE]
> Mono （以 Xamarin 為基礎的 .NET 執行時間），而 Apple 的 iOS Api 不支援相同的語言/區域組合集合。
> 因此，您可以在 iOS [**設定**] 應用程式中選取未對應到 Mono 中有效值的語言/區域組合。 例如，將 iPhone 的語言設為英文，而其地區設定為西班牙，會導致下列 Api 產生不同的值：
>
> - `CurrentThead.CurrentCulture`： en-us （Mono API）
> - `CurrentThread.CurrentUICulture`： en-us （Mono API）
> - `NSLocale.CurrentLocale.LocaleIdentifier`： en_ES （Apple API）
>
> 因為 Mono `CurrentThread.CurrentUICulture` 會使用來選取資源，並 `CurrentThread.CurrentCulture` 格式化日期和貨幣，所以以 Mono 為基礎的當地語系化（例如，使用 .resx 檔案）可能不會產生這些語言/區域組合的預期結果。 在這些情況下，視需要依賴 Apple 的 Api 來進行當地語系化。

### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

`NSCurrentLocaleDidChangeNotification`當使用者更新其地區設定時，iOS 會產生。 應用程式可以在執行時接聽此通知，並且可以對 UI 進行適當的變更。

## <a name="localization-basics-in-ios"></a>IOS 中的當地語系化基本概念

您可以輕鬆地在 Xamarin 中運用 iOS 的下列功能來提供當地語系化的資源，以便向使用者顯示。 請參閱[TaskyL10n 範例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)，以瞭解如何執行這些概念。

### <a name="specifying-default-and-supported-languages-in-infoplist"></a>在 Info. plist 中指定預設和支援的語言

在[技術問答中&QA1828： Ios 如何判斷您應用程式的語言](https://developer.apple.com/library/content/qa/qa1828/_index.html)，Apple 會說明 ios 如何選取要在應用程式中使用的語言。 下列因素會影響所顯示的語言：

- 使用者的慣用語言（在 [**設定**] 應用程式中找到）
- 與應用程式配套的當地語系化（. .lproj 資料夾）
- `CFBundleDevelopmentRegion`（**Info. plist**值，指定應用程式的預設語言）
- `CFBundleLocalizations`（**Info. plist**陣列，指定所有支援的當地語系化）

如技術 Q&A 所示， `CFBundleDevelopmentRegion` 代表應用程式的預設區域和語言。 如果應用程式未明確支援任何使用者的慣用語言，則會使用此欄位所指定的語言。

> [!IMPORTANT]
> iOS 11 套用此語言選擇機制，比舊版作業系統更嚴格。 因此，任何未明確宣告其支援當地語系化的 iOS 11 應用程式（藉由包含 .lproj 資料夾或設定的值 `CFBundleLocalizations` ），在 ios 11 中可能會顯示不同的語言，而不是在 ios 10 中。

如果 `CFBundleDevelopmentRegion` 未在**plist**檔案中指定，則 Xamarin. iOS build tools 目前會使用預設值 `en_US` 。 雖然這在未來的版本中可能會變更，但這表示預設語言是英文。

若要確保您的應用程式會選取預期的語言，請採取下列步驟：

- 指定預設語言。 開啟**plist** ，並使用**來源**視圖來設定索引鍵的值 `CFBundleDevelopmentRegion` ; 在 XML 中，它看起來應該如下所示：

```xml
<key>CFBundleDevelopmentRegion</key>
<string>es</string>
```

這個範例會使用 "es" 來指定不支援任何使用者的慣用語言時，預設為西班牙文。

- 宣告所有支援的當地語系化。 在**plist**中，使用**來源**視圖來設定索引鍵的陣列 `CFBundleLocalizations` ; 在 XML 中，它看起來應該如下所示：

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>es</string>
    ...
</array>
```

已使用 .NET 機制（例如 .resx 檔案）當地語系化的 Xamarin iOS 應用程式，也必須提供這些**資訊。 plist**值。

如需有關這些**plist**金鑰的詳細資訊，請參閱 Apple 的[資訊屬性清單索引鍵參考](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html)。

### <a name="getlocalizedstring-method"></a>GetLocalizedString 方法

`NSBundle.MainBundle.GetLocalizedString`方法會查詢已儲存在專案中之**string**檔案中的當地語系化文字。 這些檔案是依語言，在特殊命名的目錄中，以 **. .lproj**尾碼（請注意，延伸模組的第一個字母是小寫的 "L"）來組織。

#### <a name="strings-file-locations"></a>字串檔案位置

- **.Lproj**是包含預設語言資源的目錄。
  它通常位於專案根目錄中（但也可以放在**Resources**資料夾中）。
- 會針對每個支援的語言建立** &lt; &gt; .lproj**目錄，通常是在**Resources**資料夾中。

每個語言目錄中可以有數個不同的**字串**檔案：

- 可**當地語系化的字串**–當地語系化文字的主要清單。
- **Infoplist.strings** –此檔案中允許某些特定的索引鍵來轉譯應用程式名稱之類的專案。
- ** \<storyboard-name> string** –選擇性檔案，其中包含分鏡腳本中使用者介面元素的翻譯。

這些檔案的**組建動作**應為 [組合**資源**]。

#### <a name="strings-file-format"></a>字串檔案格式

當地語系化字串值的語法為：

```console
/* comment */
"key"="localized-value";
```

您應該在字串中換用下列字元：

- `\"`右
- `\\`字元
- `\n`換

這是一個範例**es/可當地語系化的字串**（即西班牙文）檔案，來自範例：

```console
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="images"></a>影像

若要在 iOS 中將映射當地語系化：

1. 請參閱程式碼中的映射，例如：

    ```csharp
    UIImage.FromBundle("flag");
    ```

2. 將預設影像檔案**旗**標放在 **.lproj** （原生開發語言目錄）中。

3. 選擇性地將影像的當地語系化版本放在 **.lproj**資料夾中，以取得每種語言（例如 **es .lproj**， **ja-jp .lproj**）。 在每個語言目錄中使用相同的檔案名**旗標 .png** 。

如果特定語言的映射不存在，iOS 會切換回預設的原生語言資料夾，並從該處載入影像。

#### <a name="launch-images"></a>啟動映射

將標準命名慣例用於啟動映射（以及適用于 iPhone 6 模型的 XIB 或分鏡腳本），並將它們放在每種語言的 **.lproj**目錄中。

```console
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>應用程式名稱

將**infoplist.strings**檔案放在 **.lproj**目錄中，可讓您覆寫應用程式**資訊 plist**中的一些值，包括應用程式名稱：

```console
"CFBundleDisplayName" = "LeónTodo";
```

您可以用來將[應用程式特定的字串當地語系化](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21)的其他金鑰如下：

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright

### <a name="dates-and-times"></a>日期和時間

雖然您可以使用內建的 .NET 日期和時間函式（以及目前的 `CultureInfo` ）來格式化地區設定的日期和時間，但這會忽略地區設定特定的使用者設定（可以與語言分開設定）。

使用 iOS `NSDateFormatter` 來產生符合使用者地區設定喜好設定的輸出。 下列範例程式碼示範基本日期和時間格式選項：

```csharp
var date = NSDate.Now;
var df = new NSDateFormatter ();
df.DateStyle = NSDateFormatterStyle.Full;
df.TimeStyle = NSDateFormatterStyle.Long;
Debug.WriteLine ("Full,Long: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Short;
df.TimeStyle = NSDateFormatterStyle.Short;
Debug.WriteLine ("Short,Short: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Medium;
df.TimeStyle = NSDateFormatterStyle.None;
Debug.WriteLine ("Medium,None: " + df.StringFor(date));
```

美國地區的英文結果：

```console
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

西班牙西班牙文的結果：

```console
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

如需詳細資訊，請參閱 Apple[日期](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html)格式器檔。 測試區分地區設定的日期和時間格式時，請同時檢查**IPhone 語言**和**地區**設定。

<a name="rtl"></a>

### <a name="right-to-left-rtl-layout"></a>由右至左（RTL）版面配置

iOS 提供一些功能，可協助您建立 RTL 感知應用程式：

- 針對控制項對齊，使用自動設定的 `leading` 和 `trailing` 屬性（對應至英文的 left 和 right），而對 RTL 語言則是相反的。
  [`UIStackView`](~/ios/user-interface/controls/uistackview.md)控制項特別適合用來配置要進行 RTL 感知的控制項。
- 用於 `TextAlignment = UITextAlignment.Natural` 文字對齊（最適用于大部分語言，但對 RTL 而言則為 right）。
- `UINavigationController`自動翻轉 [上一頁] 按鈕並反轉滑動方向。

下列螢幕擷取畫面顯示阿拉伯文和希伯來文中的[當地語系化 Tasky 範例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)（雖然已在欄位中輸入英文）：

[![](images/rtl-ar-sml.png "Localization in Arabic")](images/rtl-ar.png#lightbox "Arabic")

[![](images/rtl-he-sml.png "Localization in Hebrew")](images/rtl-he.png#lightbox "Hebrew")

iOS 會自動反轉 `UINavigationController` ，而其他控制項則會置於自動設定內部 `UIStackView` 或對齊。
RTL 文字是使用 **. 字串**檔案進行當地語系化，其方式與 LTR 文字相同。

<a name="code"></a>

## <a name="localizing-the-ui-in-code"></a>在程式碼中將 UI 當地語系化

[Tasky （在程式碼中當地語系化）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)範例示範如何將使用者介面內建的應用程式當地語系化（而不是 xib 或分鏡腳本）。

### <a name="project-structure"></a>專案結構

![](images/solution-code.png "Resources tree")

### <a name="localizablestrings-file"></a>可當地語系化的字串檔案

如上所述，可當地語系化的**字串**檔案格式是由索引鍵/值組所組成。 此索引鍵會描述字串的意圖，而值則是要在應用程式中使用的翻譯文字。

範例的西班牙文（**es**）當地語系化如下所示：

```console
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="performing-the-localization"></a>執行當地語系化

在應用程式代碼中，無論使用者介面的顯示文字是在何處設定（不論是標籤的文字或輸入的預留位置等等），程式碼都會使用 iOS 函式 `GetLocalizedString` 來抓取要顯示的正確轉譯：

```csharp
var localizedString = NSBundle.MainBundle.GetLocalizedString ("key", "optional");
someControl.Text = localizedString;
```

<a name="storyboard"></a>

## <a name="localizing-storyboard-uis"></a>當地語系化分鏡腳本 Ui

範例[Tasky （當地語系化](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)的腳本）顯示如何將分鏡腳本中控制項的文字當地語系化。

### <a name="project-structure"></a>專案結構

**.Lproj**目錄包含分鏡腳本，而且也應該包含應用程式中使用的任何影像。

其他語言目錄包含程式碼中所參考之任何字串資源的可**當地語系化字串**，以及包含分鏡腳本中文字翻譯的**mainstoryboard.storyboard**字串檔案。

![](images/solution-storyboard.png "Resources tree")

語言目錄應包含任何已當地語系化之影像的複本，以覆寫 **.lproj**中的現有映射。

### <a name="object-id--localization-id"></a>物件識別碼/當地語系化識別碼

在分鏡腳本中建立和編輯控制項時，請選取每個控制項，並檢查要用於當地語系化的識別碼：

- 在 Visual Studio for Mac 中，它位於**Properties Pad** ，而且稱為「**當地語系化識別碼**」。
- 在 Xcode 中，它稱為「**物件識別碼**」。

此字串值的格式通常為 "NF3-h8-xmR"，如下列螢幕擷取畫面所示：

![](images/xs-designer-localization-id.png "Xcode view of Storyboard localization")

這個值會在**字串**檔案中用來將翻譯的文字自動指派給每個控制項。

### <a name="mainstoryboardstrings"></a>Mainstoryboard.storyboard 字串

分鏡腳本轉譯檔案的格式與可當地語系化的**字串**檔案相似，不同之處在于索引鍵（左邊的值）不能是使用者定義的，而是必須具有非常特定的格式： `ObjectID.property` 。

在下面的**範例中，您**可以看到 `UITextField` 有一個 `placeholder` 可以當地語系化的文字屬性。 `UILabel`具有 `text` 屬性，且 `UIButton` 預設文字是使用 `normalTitle` 下列設定：

```console
"SXg-TT-IwM.placeholder" = "nombre de la tarea";
"Pqa-aa-ury.placeholder"= "otra información de tarea";
"zwR-D9-hM1.text" = "Detalles de la tarea";
"bAM-2j-Rzw.text" = "Notas";           /* Notes */
"NF3-h8-xmR.text" = "Completo";        /* Done */
"MWt-Ya-pMf.normalTitle" = "Guardar";  /* Save */
"IGr-pR-05L.normalTitle" = "Eliminar"; /* Delete */
```

> [!IMPORTANT]
> 使用具有大小類別的分鏡腳本可能會導致翻譯不會出現在應用程式中。 [Apple 的 Xcode 版本](https://developer.apple.com/library/content/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html)資訊表示如果有三個專案，則腳本或 XIB 將無法正確當地語系化：它使用大小類別，基底當地語系化和組建目標設定為通用，而組建則以 iOS 7.0 為目標。 修正方法是將您的分鏡腳本字串檔案複製到兩個相同的檔案： **mainstoryboard.storyboard ~ iphone. string**和**mainstoryboard.storyboard ~ ipad. string**，如下列螢幕擷取畫面所示：
>
> ![](images/xs-dup-strings.png "Strings files")

<a name="appstore"></a>

## <a name="app-store-listing"></a>App Store 清單

遵循 Apple 在[App Store 當地語系化](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization)上的常見問題，為您的應用程式銷售所在的每個國家/地區輸入翻譯。 請注意，只有當您的應用程式也包含語言的當地語系化 **.lproj**目錄時，才會顯示翻譯的警告。

## <a name="summary"></a>總結

本文涵蓋使用內建的資源處理和分鏡腳本功能來當地語系化 iOS 應用程式的基本概念。

您可以在[此跨平臺指南](~/cross-platform/app-fundamentals/localization.md)中深入瞭解適用于 IOS、Android 和跨平臺應用程式的 I18n 和 L10n （包括 Xamarin）。

## <a name="related-links"></a>相關連結

- [Tasky （在程式碼中當地語系化）（範例）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky （當地語系化的分鏡腳本）（範例）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Apple 當地語系化指南](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [跨平臺當地語系化總覽](~/cross-platform/app-fundamentals/localization.md)
- [Xamarin.Forms 當地語系化](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Android 當地語系化](~/android/app-fundamentals/localization.md)
