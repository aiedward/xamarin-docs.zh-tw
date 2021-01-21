---
title: 在 Xamarin 中進行當地語系化
description: 本檔說明 iOS 當地語系化功能，以及如何在 Xamarin iOS 應用程式中使用這些功能。 它會討論語言、地區設定、字串檔案、啟動影像等。
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/28/2017
ms.openlocfilehash: 6171066c8a6ceed7314cc50510ce4813d5ce1c0c
ms.sourcegitcommit: e27e29c14b783263e063baaa65d4eecb8dd31f57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98628978"
---
# <a name="localization-in-xamarinios"></a>在 Xamarin 中進行當地語系化

_本檔涵蓋 iOS SDK 的當地語系化功能，以及如何使用 Xamarin 來存取它們。_

請參閱 [國際化編碼](encodings.md) ，以取得在必須處理非 Unicode 資料的應用程式中包含字元集/字碼頁的指示。

## <a name="ios-platform-features"></a>iOS 平臺功能

本節說明 iOS 的一些當地語系化功能。 請跳到 [下一節](#localization-basics-in-ios) ，以查看特定的程式碼和範例。

### <a name="language"></a>語言

使用者會在 [ **設定** ] 應用程式中選擇其語言。 這項設定會影響作業系統和應用程式所顯示的語言字串和影像。

若要判斷應用程式中使用的語言，請取得第一個元素 `NSBundle.MainBundle.PreferredLocalizations` ：

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

此值將會是語言代碼，例如 `en` 英文、 `es` 西班牙文、 `ja` 日文等等。傳回的值會限制為應用程式支援的其中一個當地語系化， (使用 fallback 規則來判斷最符合的) 。

應用程式程式碼不一定需要檢查此值– Xamarin 和 iOS 都提供可協助自動為使用者的語言提供正確字串或資源的功能。 本檔的其餘部分將說明這些功能。

> [!NOTE]
> `NSLocale.PreferredLanguages`無論應用程式支援何種當地語系化，都可以使用來決定使用者的語言喜好設定。 這個方法所傳回的值會在 iOS 9 中變更;如需詳細資訊，請參閱 [技術提示 TN2418](https://developer.apple.com/library/content/technotes/tn2418/_index.html) 。

### <a name="locale"></a>Locale

使用者會在 [設定] 應用程式中選擇其地區 **設定** 。 此設定會影響日期、時間、數位和貨幣的格式化方式。

這可讓使用者選擇是否看到12小時或24小時的時間格式、其小數分隔符號是逗號或點，以及日期、月份和年份在日期顯示中的順序。

有了 Xamarin，您就可以存取 Apple 的 iOS 類別 (`NSNumberFormatter`) 以及在 system.string 中的 .net 類別。 開發人員應該評估哪些較適合其需求，因為每個都有不同的可用功能。 尤其是，如果您要使用 StoreKit 來抓取和顯示 In-App 購買價格，則應該使用 Apple 的格式設定類別來傳回所傳回的價格資訊。

您可以透過下列兩種方式之一來查詢目前的地區設定：

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

第一個值可以由作業系統快取，因此不一定會反映使用者目前選取的地區設定。 使用第二個值來取得目前選取的地區設定。

> [!NOTE]
> Mono (以 Xamarin 為基礎的 .NET 執行時間) ，且 Apple 的 iOS Api 不支援一組相同的語言/區域組合。
> 因此，您可以在 iOS [ **設定** ] 應用程式中選取未對應至 Mono 中有效值的語言/區域組合。 例如，將 iPhone 的語言設定為英文，並將其地區設定為西班牙，將會導致下列 Api 產生不同的值：
>
> - `CurrentThead.CurrentCulture`： en-us (Mono API) 
> - `CurrentThread.CurrentUICulture`： en-us (Mono API) 
> - `NSLocale.CurrentLocale.LocaleIdentifier`： en_ES (Apple API) 
>
> 由於 Mono 用 `CurrentThread.CurrentUICulture` 來選取資源以及 `CurrentThread.CurrentCulture` 格式化日期和貨幣，因此以 mono 為基礎的當地語系化 (例如，使用 .resx 檔) 可能不會產生這些語言/區域組合的預期結果。 在這些情況下，會依賴 Apple 的 Api，視需要進行當地語系化。

### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

`NSCurrentLocaleDidChangeNotification`當使用者更新其地區設定時，iOS 會產生。 應用程式在執行時可以接聽此通知，並且可以對 UI 進行適當的變更。

## <a name="localization-basics-in-ios"></a>IOS 中的當地語系化基本概念

您可以輕鬆地在 Xamarin 中利用 iOS 的下列功能，以提供當地語系化的資源來顯示給使用者。 請參閱 [TaskyL10n 範例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) ，以瞭解如何實行這些構想。

### <a name="specifying-default-and-supported-languages-in-infoplist"></a>在 Info. plist 中指定預設和支援的語言

在 [技術 Q&QA1828： Ios 如何判斷您應用程式的語言](https://developer.apple.com/library/content/qa/qa1828/_index.html)，Apple 說明 ios 如何選取要在應用程式中使用的語言。 下列因素會影響所顯示的語言：

- 使用者的慣用語言 (在 [ **設定** ] 應用程式中找到) 
- 與應用程式 ( >.lproj 資料夾配套的當地語系化) 
- `CFBundleDevelopmentRegion` (**Info. plist** 值，指定應用程式的預設語言) 
- `CFBundleLocalizations` (**Info. plist** 陣列，指定所有支援的當地語系化) 

如技術 Q&A 所示， `CFBundleDevelopmentRegion` 代表應用程式的預設區域和語言。 如果應用程式未明確支援任何使用者慣用的語言，則會使用此欄位所指定的語言。

> [!IMPORTANT]
> iOS 11 會比舊版的作業系統更嚴格地套用此語言選擇機制。 因此，任何未明確宣告其支援當地語系化的 iOS 11 應用程式（包括 >.lproj 資料夾或設定的值 `CFBundleLocalizations` ），可能會在 ios 11 中顯示與 ios 10 不同的語言。

如果 `CFBundleDevelopmentRegion` 未在 **plist** 檔案中指定，則 Xamarin build tools 目前會使用預設值 `en_US` 。 雖然這在未來的版本中可能會變更，但這表示預設語言為英文。

若要確保您的應用程式會選取預期的語言，請執行下列步驟：

- 指定預設語言。 開啟 **plist** ，並使用 **來源** 視圖來設定索引鍵的值 `CFBundleDevelopmentRegion` ; 在 XML 中，它看起來應該像下面這樣：

```xml
<key>CFBundleDevelopmentRegion</key>
<string>es</string>
```

此範例會使用 "es" 來指定不支援任何使用者的慣用語言時，預設為西班牙文。

- 宣告所有支援的當地語系化。 在 **plist** 中，使用 **來源** 視圖來設定索引鍵的陣列 `CFBundleLocalizations` ; 在 XML 中，它看起來應該像下面這樣：

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>es</string>
    ...
</array>
```

使用 .NET 機制（例如 .resx 檔）當地語系化的 Xamarin iOS 應用程式，也必須提供這些 **資訊。 plist** 值。

如需有關這些 **資訊 plist** 的詳細資訊，請參閱 Apple 的 [資訊內容清單索引鍵參考](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html)。

### <a name="getlocalizedstring-method"></a>GetLocalizedString 方法

`NSBundle.MainBundle.GetLocalizedString`方法會查閱已儲存在專案中之 **字串** 檔案的當地語系化文字。 這些檔案會依語言進行組織，在特殊命名的目錄中， **>.lproj** 尾碼 (請注意，此延伸模組的第一個字母是小寫的 "L" ) 。

#### <a name="strings-file-locations"></a>字串檔案位置

- **Base. >.lproj** 是包含預設語言資源的目錄。
  它通常位於專案根目錄 (但也可以放在 [ **資源** ] 資料夾中) 。
- 系統會為每個支援的語言（通常是在 **Resources** 資料夾）中建立 **&lt; &gt; >.lproj** 目錄。

每個語言目錄中可以有一些不同的 **字串** 檔案：

- 可 **當地語系化的. 字串**-當地語系化文字的主要清單。
- **InfoPlist：** 此檔案中允許某些特定的金鑰，以轉譯應用程式名稱之類的專案。
- **\<storyboard-name> 字串**–包含腳本中使用者介面元素翻譯的選擇性檔案。

這些檔案的 **組建動作** 應該是配套 **資源**。

#### <a name="strings-file-format"></a>字串檔案格式

當地語系化字串值的語法為：

```console
/* comment */
"key"="localized-value";
```

您應該在字串中將下列字元轉義：

- `\"` 報價
- `\\` 反 斜 杠
- `\n` 除

這是 **es/可當地語系化的範例。字串** (ie。範例中的西班牙文) 檔案：

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

### <a name="images"></a>映像

若要在 iOS 中將影像當地語系化：

1. 請參閱程式碼中的影像，例如：

    ```csharp
    UIImage.FromBundle("flag");
    ```

2. 將預設的影像檔案 **flag.png** 在 **>.lproj** (原生開發語言目錄) 。

3. 選擇性地將 **>.lproj** 資料夾中的影像當地語系化版本放在每個語言 (例如 **>.lproj**， **ja-jp >.lproj**) 。 在每個語言目錄中使用相同的檔案名 **flag.png** 。

如果特定語言沒有映射存在，則 iOS 會切換回預設的原生語言資料夾，並從該處載入映射。

#### <a name="launch-images"></a>啟動映射

針對 XIB 或適用于 iPhone 6 模型的或分鏡腳本，使用標準命名慣例的標準命名 (慣例) 將它們放在每個語言的 **>.lproj** 目錄中。

```console
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>應用程式名稱

將 **InfoPlist** 檔放在 **>.lproj** 目錄中，可讓您從應用程式的 **資訊** 中覆寫一些值，包括應用程式名稱：

```console
"CFBundleDisplayName" = "LeónTodo";
```

您可以用來將 [應用程式特定字串當地語系化](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21) 的其他索引鍵為：

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright

### <a name="dates-and-times"></a>日期和時間

雖然您可以使用內建的 .NET 日期和時間函式 (以及目前的 `CultureInfo`) 來格式化地區設定的日期和時間，但這會忽略地區設定特定的使用者設定 (可以與語言) 分開設定。

使用 iOS `NSDateFormatter` 來產生符合使用者地區設定喜好設定的輸出。 下列範例程式碼示範基本日期和時間格式化選項：

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

美國英文的結果：

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

如需詳細資訊，請參閱 Apple [日期](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html) 格式器檔。 測試區分地區設定的日期和時間格式時，請檢查 **IPhone 語言** 和 **地區** 設定。

<a name="rtl"></a>

### <a name="right-to-left-rtl-layout"></a>從右至左 (RTL) 版面配置

iOS 提供許多功能，可協助您建立 RTL 感知的應用程式：

- 使用 [自動設定] `leading` 和 [ `trailing` 屬性] 來控制對齊 (對應至 [英文] 的 [左] 和 [右]，但針對 RTL 語言，則會反轉) 。
  [`UIStackView`](~/ios/user-interface/controls/uistackview.md)控制項特別適用于配置可供 RTL 感知的控制項。
- `TextAlignment = UITextAlignment.Natural`適用于文字對齊 (這將會保留大部分的語言，但可用於 RTL) 。
- `UINavigationController` 自動翻轉 [上一頁] 按鈕並反轉滑動方向。

下列螢幕擷取畫面顯示阿拉伯文和希伯來文 (中的 [當地語系化 Tasky 範例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) ，雖然已在 [欄位]) 中輸入英文：

[![阿拉伯文的當地語系化](images/rtl-ar-sml.png)](images/rtl-ar.png#lightbox "阿拉伯文")

[![希伯來文中的當地語系化](images/rtl-he-sml.png)](images/rtl-he.png#lightbox "Hebrew")

iOS 會自動反轉 `UINavigationController` ，而其他控制項則會置於自動設定的內部 `UIStackView` 或對齊。
RTL 文字會使用與 LTR 文字相同的方式，使用 **字串** 檔案進行當地語系化。

<a name="code"></a>

## <a name="localizing-the-ui-in-code"></a>在程式碼中當地語系化 UI

[Tasky (當地語系化于程式碼) ](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)範例示範如何將使用者介面內建于程式碼 (而非 xib 或分鏡腳本) 的應用程式當地語系化。

### <a name="project-structure"></a>專案結構

![螢幕擷取畫面顯示範例的資源樹狀結構，其中包含可當地語系化字串的位置。](images/solution-code.png)

### <a name="localizablestrings-file"></a>可當地語系化的字串檔

如上所述，可當地語系化的 **字串** 檔案格式是由索引鍵/值組所組成。 此索引鍵會描述字串的意圖，而值則是要在應用程式中使用的已翻譯文字。

範例的西班牙文 (**es**) 當地語系化如下所示：

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

在應用程式程式碼中，無論使用者介面的顯示文字是在何處設定 (無論是標籤的文字，還是輸入的預留位置等) 程式碼都會使用 iOS 函式 `GetLocalizedString` 來取得正確的轉譯以顯示：

```csharp
var localizedString = NSBundle.MainBundle.GetLocalizedString ("key", "optional");
someControl.Text = localizedString;
```

<a name="storyboard"></a>

## <a name="localizing-storyboard-uis"></a>當地語系化分鏡腳本 Ui

範例 [Tasky (當地語系化 ](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard) 的分鏡腳本) 示範如何針對分鏡腳本中的控制項當地語系化文字。

### <a name="project-structure"></a>專案結構

**>.lproj** 目錄包含分鏡腳本，而且也應該包含應用程式中使用的任何影像。

其他語言目錄包含可 **當地語系化** 的字串資源，用於程式碼中所參考的任何字串資源，以及包含腳本中文字翻譯的 **MainStoryboard** 字串檔案。

![螢幕擷取畫面顯示範例的資源樹狀結構，其中包含 MainStoryboard 字串的位置。](images/solution-storyboard.png)

語言目錄應包含任何已當地語系化的影像複本，以覆寫 **>.lproj** 中的影像。

### <a name="object-id--localization-id"></a>物件識別碼/當地語系化識別碼

在腳本中建立和編輯控制項時，請選取每個控制項，並檢查用於當地語系化的識別碼：

- 在 Visual Studio for Mac 中，它位於 **Properties Pad** ，而且稱為 **當地語系化識別碼**。
- 在 Xcode 中，它稱為「 **物件識別碼**」。

此字串值的格式通常如下： "NF3-h8-xmR"，如下列螢幕擷取畫面所示：

![分鏡腳本當地語系化的 Xcode 視圖](images/xs-designer-localization-id.png)

此值會用於 **字串** 檔案，以自動將翻譯的文字指派給每個控制項。

### <a name="mainstoryboardstrings"></a>MainStoryboard

分鏡腳本轉譯檔案的格式類似于可 **當地語系化的字串** 檔案，不同之處在于左邊) 的 (索引鍵不能是使用者定義，而必須有非常特定的格式： `ObjectID.property` 。

在下面的範例 **Mainstoryboard** 中，您可以看到 `UITextField` s 有一個 `placeholder` 可以當地語系化的 text 屬性; `UILabel`具有 `text` 屬性， `UIButton` 預設文字則是使用 `normalTitle` 下列設定：

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
> 使用大小類別的分鏡腳本，可能會產生不會出現在應用程式中的翻譯。 [Apple 的 Xcode 版本](https://developer.apple.com/library/content/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html) 資訊指出如果有三個專案，腳本或 XIB 將不會正確地當地語系化：它會使用大小類別、基底當地語系化和組建目標設定為通用，而組建則以 iOS 7.0 為目標。 修正方法是將您的分鏡腳本字串檔案複製到兩個相同的檔案中： **MainStoryboard ~ iphone. 字串** 和 **MainStoryboard ~ ipad。字串**，如下列螢幕擷取畫面所示：
>
> ![字串檔案](images/xs-dup-strings.png)

<a name="appstore"></a>

## <a name="app-store-listing"></a>App Store 清單

遵循 [App Store 當地語系化](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization) 的 Apple 常見問題，以針對您的應用程式銷售的每個國家/地區輸入翻譯。 請注意，只有當您的應用程式也包含語言的當地語系化 **>.lproj** 目錄時，才會顯示翻譯的警告。

## <a name="summary"></a>[摘要]

本文涵蓋使用內建的資源處理和分鏡腳本功能，將 iOS 應用程式當地語系化的基本概念。

您可以在 [此跨平臺指南](~/cross-platform/app-fundamentals/localization.md)中，深入瞭解適用于 IOS、Android 和跨平臺應用程式的國際化和 L10n (包括 Xamarin) 。

## <a name="related-links"></a>相關連結

- [Tasky (當地語系化于程式碼)  (範例) ](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky (當地語系化的分鏡腳本)  (範例) ](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Apple 當地語系化指南](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [跨平臺當地語系化總覽](~/cross-platform/app-fundamentals/localization.md)
- [Xamarin.Forms 當地語系化](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Android 當地語系化](~/android/app-fundamentals/localization.md)
