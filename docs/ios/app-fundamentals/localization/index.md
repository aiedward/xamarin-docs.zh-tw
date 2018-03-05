---
title: "iOS 當地語系化"
description: "本文件涵蓋 iOS SDK 的當地語系化功能以及如何使用 Xamarin 存取它們。"
ms.topic: article
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/28/2017
ms.openlocfilehash: ea91dbcf7148651cb5d10acae4ada8bb6758c39e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="ios-localization"></a>iOS 當地語系化

_本文件涵蓋 iOS SDK 的當地語系化功能以及如何使用 Xamarin 存取它們。_

請參閱[國際化編碼](encodings.md)如需包括字元組/字碼頁必須處理的非 Unicode 資料的應用程式中的指示。

## <a name="ios-platform-features"></a>iOS 平台功能

本章節描述一些在 iOS 中的當地語系化功能。 請跳至[下一節](#basics)若要查看特定程式碼和範例。

### <a name="language"></a>語言

使用者選擇其語言**設定**應用程式。 這個設定會影響的語言字串和作業系統，以及偵測語言設定的應用程式所顯示的影像。

這是使用者會決定是否要顯示英文、 西班牙文、 日文、 法文或其他應用程式中顯示的語言。

實際的 iOS 7 中支援的語言清單： 英文 （美國）、 英文 （英國）、 中文 （簡體）、 中文 （繁體）、 法文、 德文、 義大利文、 日文、 韓文、 西班牙文、 阿拉伯文、 卡達隆尼亞、 克羅埃西亞文、 捷克文、 丹麥文、 荷蘭文、 芬蘭文、 希臘文、 希伯來文匈牙利文、 印尼文、 馬來文、 挪威文、 波蘭文、 葡萄牙文、 葡萄牙文 （巴西）、 羅馬尼亞文、 俄文、 斯洛伐克文、 瑞典文、 泰文、 土耳其文、 烏克蘭文、 越南文、 英文 （澳大利亞）、 西班牙文 （墨西哥）。

目前的語言可以查詢所存取的第一個元素`PreferredLanguages`陣列：

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

這個值是語言代碼的例如`en`英文`es`西班牙文，`ja`日文等等。_傳回的值僅限於應用程式 （使用後援規則決定最符合項目） 所支援的當地語系化資源的其中一個。_

應用程式程式碼不一定需要檢查此值-Xamarin 並 iOS 這兩個提供的功能是為了自動提供使用者的語言正確的字串或資源。 這份文件的其餘部分將詳述這些功能。

> [!NOTE]
> **注意：** iOS 9 之前, 是建議的程式碼`var lang = NSLocale.PreferredLanguages [0];`。
>
> 在 iOS 9-變更這個程式碼所傳回的結果看到[技術附註 TN2418](https://developer.apple.com/library/content/technotes/tn2418/_index.html)如需詳細資訊。
>
> 您可以繼續使用`NSLocale.PreferredLanguages [0]`來判斷使用者所選的實際值 （不論當地語系化您的應用程式支援）。

### <a name="locale"></a>地區設定

使用者選擇其地區設定中的**設定**應用程式。 這項設定會影響格式化日期、 時間、 數字和貨幣的方式。

這可讓使用者選擇他們是否看到 12 小時或 24 小時的時間格式，其小數分隔符號是逗號或點，以及一天、 月份和年份的日期顯示中的順序。

使用 Xamarin，您可以存取這兩個 Apple iOS 類別 (`NSNumberFormatter`) 以及在 System.Globalization 將.NET 類別。 開發人員應評估這是更適合他們的需求，因為在每個不同的功能。 特別的是，如果您會擷取並顯示在應用程式內購買的價格使用 StoreKit 應該明確地用於 Apple 的格式類別傳回的價格資訊。

您可以透過其中一種查詢目前的地區設定：

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

第一個值可以快取的作業系統，因此可能不一定會反映使用者的目前選取的地區設定。 您可以使用第二個值，取得目前選取的地區設定。


### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

iOS 會產生`NSCurrentLocaleDidChangeNotification`當使用者更新其地區設定。 雖然它們正在執行，以及對 UI 進行適當的變更，應用程式可以接聽此通知。

<a name="basics" />

## <a name="localization-basics-in-ios"></a>在 iOS 中的當地語系化基本概念

在 Xamarin 顯示當地語系化的資源提供給使用者，可輕鬆利用 iOS 的下列功能。 請參閱[TaskyL10n 範例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)以了解如何實作這些概念。

### <a name="infoplist"></a>Info.plist

在開始之前，設定**Info.plist**檔案具有下列索引鍵：

- `CFBundleDevelopmentRegion` -應用程式 （通常的語言，說出由開發人員，以及使用分鏡腳本與字串與影像資源） 的預設語言。 在下列範例中， **en**指定 （適用於英文）。
- `CFBundleLocalizations` -其他的應用程式，同時也使用的語言代碼，例如支援的當地語系化資源的陣列**es** （西班牙文） 和**PT-PT** （葡萄牙說出葡萄牙文）。

```xml
<key>CFBundleDevelopmentRegion</key>
<string>en</string>
<key>CFBundleLocalizations</key>
<array>
  <string>de</string>
  <string>es</string>
  <string>ja</string>
  ...
</array>
```

### <a name="localizedstring-method"></a>LocalizedString 方法

`NSBundle.MainBundle.LocalizedString`方法會查詢中所儲存的當地語系化文字**.strings**專案中的檔案。 這些檔案會依使用的特殊命名目錄中的語言， **.lproj**後置詞。

#### <a name="strings-file-locations"></a>.strings 檔案位置

- **Base.lproj**是包含預設語言資源的目錄。
  它通常位於專案根目錄中 (但也可以放在**資源**資料夾)。
- **<language>.lproj**來建立目錄的每個支援的語言，通常**資源**資料夾。

可以是不同的數字**.strings**每個語言目錄中的檔案：

- **Localizable.strings** -當地語系化文字的主要清單。
- **InfoPlist.strings** -某些特定金鑰允許在這個檔案中轉譯之類的應用程式名稱。
- **< 分鏡腳本名稱 >.strings** -選用的檔案，其中包含使用者介面項目的分鏡腳本中的翻譯。

**建置動作**這些檔案應該是**配套資源**。

#### <a name="strings-file-format"></a>.strings 檔案格式

當地語系化的字串值的語法是：

```csharp
/* comment */
"key"="localized-value";
```

您應該逸出字串中的下列字元：

* `\"`  引號
* `\\`  反斜線
* `\n`  新行字元

這是範例**es/Localizable.strings** （即西班牙文） 檔案的範例：

```csharp
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

若要當地語系化 iOS 中的映像：

1. 請參閱映像中的程式碼，例如：

  ```csharp
  UIImage.FromBundle("flag");
  ```

2. 預設映像檔案的放置**flag.png**中**Base.lproj** （原生開發語言目錄）。

3. （選擇性） 將放在映像的當地語系化的版本**.lproj**資料夾 （例如每個語言 **es.lproj**， **ja.lproj**)。 使用相同的檔名**flag.png**每個語言目錄中。

如果不存在特定語言的映像，iOS 會改為使用預設的原生語言資料夾，並從中載入的影像。


#### <a name="launch-images"></a>啟動映像

使用標準命名慣例啟動映像 （和 XIB 或分鏡腳本 iPhone 6 機型） 放置在時**.lproj**每種語言的目錄。

```csharp
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>應用程式名稱

放置**InfoPlist.strings**檔案**.lproj**目錄可讓您覆寫從應用程式的某些值**Info.plist**，包括應用程式名稱：

```csharp
"CFBundleDisplayName" = "LeónTodo";
```

您可以使用其他金鑰[當地語系化應用程式特定字串](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21)是：

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright


<!--
## App icon

Does not seem to be possible (although it definitely used to be!).
-->

### <a name="localization-native-development-region"></a>當地語系化原生開發區域

預設字串 (位於**Base.lproj**資料夾) 會假設為 '後援' 語言。 這表示，如果轉譯要求的程式碼中，並且找不到目前的語言， **Base.lproj**資料夾將會搜尋使用的預設字串 （如果找到相符項目，轉譯的識別項字串本身是顯示）。

開發人員可以選擇不同的語言，以進行後援設定 plist 機碼`CFBundleDevelopmentRegionKey`。 它的值應設為原生開發語言的語言代碼。 這個螢幕擷取畫面顯示 plist Xamarin Studio 中使用原生開發區域設定為西班牙文 (es):

![](images/cfbundledevelopmentregion.png "InfoPList.strings 屬性")

如果使用在分鏡腳本，並在整個程式碼的預設語言不是英文，設定此值以反映在整個應用程式程式碼所使用的原生語言。

### <a name="dates-and-times"></a>日期和時間

雖然可以使用內建的.NET 日期和時間函數 (以及目前`CultureInfo`) 若要格式化的地區設定日期和時間，這會忽略地區設定特定使用者設定 （其中可以設定個別語言）。

使用 iOS`NSDateFormatter`可產生使用者的地區設定喜好設定相符的輸出。 下列程式碼範例示範基本的日期和時間格式選項：

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

在美國英文的結果：

```csharp
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

西班牙文，西班牙中的結果：

```csharp
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

請參閱 Apple[日期格式器](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html)文件的詳細資訊。 測試時區分地區設定日期和時間格式，同時檢查**iPhone 語言**和**區域**設定。

<a name="rtl" />

### <a name="right-to-left-rtl-layout"></a>由右至左 (RTL) 配置

iOS 提供許多功能，協助您建立從右至左感知應用程式：

* 使用**自動配置**`leading`和`trailing`控制項 aligment 屬性 (對應於*左*和*右*英文，比方說，但卻是反轉 RTL 語言）。
  [ `UIStackView` ](~/ios/user-interface/controls/uistackview.md)控制項特別適用於 RTL 注意控制項的版面配置。
* 使用`TextAlignment = UITextAlignment.Natural`文字對齊方式 (這將成為*左*大部分語言但*右*為從右至左)。
* `UINavigationController` 會自動翻轉上一頁按鈕及反轉撥動方向。

下列螢幕擷取畫面顯示[當地語系化**Tasky**範例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)阿拉伯文和希伯來文 （雖然已在欄位中輸入英文版）：

[ ![](images/rtl-ar-sml.png "阿拉伯當地語系化")](images/rtl-ar.png "Arabic") 

[ ![](images/rtl-he-sml.png "當地語系化希伯來文")](images/rtl-he.png "Hebrew")

iOS 會自動反轉`UINavigationController`，和其他控制項放置於`UIStackView`或使用自動配置對齊。
使用當地語系化從右至左文字**.strings**方式與從左至右文字相同的檔案。


<a name="code"/>

## <a name="localizing-the-ui-in-code"></a>當地語系化的 UI 程式碼中

[Tasky （當地語系化程式碼中）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)範例示範如何將應用程式當地語系化使用者介面建立程式碼 （而非 XIBs 或分鏡腳本） 中的位置。

### <a name="project-structure"></a>專案結構



![](images/solution-code.png "樹狀目錄中的資源")

### <a name="localizablestrings-file"></a>Localizable.strings 檔案

如上所述， **Localizable.strings**組成索引鍵 / 值組，其中索引鍵是使用者選取的字串表示的檔案格式

西班牙文 (**es**) 範例的當地語系化資源如下所示：

```csharp
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

### <a name="performing-the-localization"></a>執行 wordpress

在應用程式程式碼中，只要使用者介面的顯示文字 設定 （無論是在標籤文字，或輸入預留位置等等） 的程式碼使用 iOS`LocalizedString`函式來擷取要顯示正確的翻譯：

```csharp
var localizedString = NSBundle.MainBundle.LocalizedString ("key", "optional");
someControl.Text = localizedString;
```

<a name="storyboard"/>

## <a name="localizing-storyboard-uis"></a>當地語系化的 Ui 分鏡腳本

此範例[Tasky （當地語系化分鏡腳本）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)示範如何在分鏡腳本的控制項上的文字當地語系化。

### <a name="project-structure"></a>專案結構

**Base.lproj**目錄包含分鏡腳本，而且也應包含任何應用程式中使用的影像。

其他語言目錄包含**Localizable.strings**的任何程式碼中參考的字串資源的檔案，以及**MainStoryboard.strings**包含翻譯的文字中的檔案分鏡腳本。

![](images/solution-storyboard.png "樹狀目錄中的資源")

語言目錄應該包含任何映像已當地語系化的若要覆寫中的一個副本**Base.lproj**。

### <a name="object-id--localization-id"></a>物件識別碼 / 當地語系化 ID

當建立和編輯控制項中的分鏡腳本中，選取每個控制項，並檢查使用當地語系化的識別碼：

* 在 Visual Studio for Mac，它具有屬性輸入板中，呼叫**當地語系化 ID**。
* 在 Xcode 中，它會呼叫**物件識別碼**。

它是字串值，通常會有的表單**"NF3-h 8-xmR"**:

![](images/xs-designer-localization-id.png "分鏡腳本當地語系化的 [Xcode] 檢視")

此值用於**.strings**檔案將自動指派給每個控制項的翻譯的文字。

### <a name="mainstoryboardstrings"></a>MainStoryboard.strings

分鏡腳本轉譯檔案的格式會類似於**Localizable.strings**檔案，不同之處在於 （左值） 的索引鍵不能是使用者定義，但是改為必須有非常特定的格式： `ObjectID.property`。

在範例**Mainstoryboard.strings**以下您可以看見`UITextField`有`placeholder`可當地語系化; text 屬性`UILabel`有`text`屬性; 和`UIButton`s 預設文字使用設定`normalTitle`:

```csharp
"SXg-TT-IwM.placeholder" = "nombre de la tarea";
"Pqa-aa-ury.placeholder"= "otra información de tarea";
"zwR-D9-hM1.text" = "Detalles de la tarea";
"bAM-2j-Rzw.text" = "Notas";           /* Notes */
"NF3-h8-xmR.text" = "Completo";        /* Done */
"MWt-Ya-pMf.normalTitle" = "Guardar";  /* Save */
"IGr-pR-05L.normalTitle" = "Eliminar"; /* Delete */
```

> [!IMPORTANT]
> **使用分鏡腳本以大小的類別**可能會導致未顯示的翻譯。 這可能與[此問題](http://stackoverflow.com/questions/24989208/xcode-6-does-not-localize-interface-builder)其中 Apple 文件的指示： 當地語系化的分鏡腳本或 XIB 將未當地語系化正確所有下列三種情況時： 分鏡腳本或 XIB 使用大小類別。 基底的當地語系化和 「 建置 」 目標會設定為通用。 建置目標 iOS 7.0。
修正為您分鏡腳本字串檔案複製到兩個相同的檔案： **MainStoryboard~iphone.strings**和**MainStoryboard~ipad.strings**:

> ![](images/xs-dup-strings.png "字串的檔案")


<!--
# Native Formatting

Xamarin.iOS applications can take advantage of the .NET framework's formatting options for localizing
numbers and dates.

### Date string formatting

https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html#//apple_ref/doc/uid/TP40002369-SW1


NSDateFormatter formatter = new NSDateFormatter ();
formatter.DateFormat = "MMMM/dd/yyyy";
NSString dateString = new NSString (formatter.ToString (d));


### Number formatting

https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfNumberFormatting10_4.html#//apple_ref/doc/uid/TP40002368-SW1

-->

<a name="appstore" />

## <a name="app-store-listing"></a>應用程式市集清單

遵循 Apple 的常見問題集的[應用程式存放區當地語系化](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization)輸入您的應用程式會在銷售每個國家/地區的翻譯。 請注意如果您的應用程式也包含當地語系化翻譯只會顯示其警告**.lproj**目錄的語言。

<!--

Once you’ve entered your application into iTunes Connect the default language
metadata and screenshots will appear as shown:

![]( "itunes connect 1")

Use the language list on the right to select other languages to provide
translated application name, description, search keywords, URLs and screenshots.
The complete list of languages is shown in this screenshot:

![]( "itunes connect 2")
-->

## <a name="summary"></a>總結

本文涵蓋當地語系化使用內建資源的處理和分鏡腳本功能的 iOS 應用程式基本的概念。

您可以了解更多關於 i18n 和 L10n iOS、 Android 和跨平台應用程式 （包括 Xamarin.Forms） 中[本跨平台指南](~/cross-platform/app-fundamentals/localization.md)。


## <a name="related-links"></a>相關連結

- [Tasky （當地語系化程式碼中） （範例）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky （當地語系化分鏡腳本） （範例）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Apple 當地語系化指南](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [跨平台當地語系化概觀](~/cross-platform/app-fundamentals/localization.md)
- [Xamarin.Forms Localization](~/xamarin-forms/app-fundamentals/localization.md)
- [Android 的當地語系化](~/android/app-fundamentals/localization.md)
