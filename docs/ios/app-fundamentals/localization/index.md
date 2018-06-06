---
title: 當地語系化的 Xamarin.iOS
description: 本文件說明 iOS 當地語系化功能以及如何在 Xamarin.iOS 應用程式中使用這些功能。 它討論語言、 地區設定中，字串的檔案、 啟動映像，等等。
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/28/2017
ms.openlocfilehash: 06758fd8fac62a63c309b173738a8ee889716143
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34785262"
---
# <a name="localization-in-xamarinios"></a>當地語系化的 Xamarin.iOS

_本文件涵蓋 iOS SDK 的當地語系化功能以及如何使用 Xamarin 存取它們。_

請參閱[國際化編碼](encodings.md)如需包括字元組/字碼頁必須處理的非 Unicode 資料的應用程式中的指示。

## <a name="ios-platform-features"></a>iOS 平台功能

本章節描述一些在 iOS 中的當地語系化功能。 請跳至[下一節](#basics)若要查看特定程式碼和範例。

### <a name="language"></a>語言

使用者選擇其語言**設定**應用程式。 這個設定會影響的語言字串和顯示依作業系統和應用程式中的影像。 

若要判斷應用程式中所使用的語言，取得的第一個項目`NSBundle.MainBundle.PreferredLocalizations`:

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

這個值是語言代碼的例如`en`英文`es`西班牙文，`ja`日文等等。傳回的值僅限於應用程式 （使用後援規則決定最符合項目） 所支援的當地語系化資源的其中一個。

應用程式程式碼不一定需要檢查此值 – Xamarin 並 iOS 這兩個提供的功能是為了自動提供使用者的語言正確的字串或資源。 這份文件的其餘部分將詳述這些功能。

> [!NOTE]
> 使用`NSLocale.PreferredLanguages`來判斷使用者的語言喜好設定，不論應用程式支援當地語系化資源。 在 iOS 9; 中變更這個方法所傳回的值請參閱[技術附註 TN2418](https://developer.apple.com/library/content/technotes/tn2418/_index.html)如需詳細資訊。

### <a name="locale"></a>地區設定

使用者選擇其地區設定中的**設定**應用程式。 這項設定會影響格式化日期、 時間、 數字和貨幣的方式。

這可讓使用者選擇他們是否看到 12 小時制或 24 小時制的時間格式，其小數分隔符號是逗號或點，以及一天、 月份和年份的日期顯示中的順序。

使用 Xamarin，您可以存取這兩個 Apple iOS 類別 (`NSNumberFormatter`) 以及在 System.Globalization 將.NET 類別。 開發人員應評估這是更適合他們的需求，因為在每個不同的功能。 特別是，如果您會擷取並顯示在應用程式內購買的價格使用 StoreKit 應該用於 Apple 的格式類別傳回的價格資訊。

兩個方式可以查詢目前的地區設定：

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

第一個值可以快取的作業系統，因此可能不一定會反映使用者的目前選取的地區設定。 您可以使用第二個值，取得目前選取的地區設定。

### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

iOS 會產生`NSCurrentLocaleDidChangeNotification`當使用者更新其地區設定。 雖然它們正在執行，並可以對 UI 進行適當的變更，應用程式可以接聽此通知。

<a name="basics" />

## <a name="localization-basics-in-ios"></a>在 iOS 中的當地語系化基本概念

在 Xamarin 顯示當地語系化的資源提供給使用者，可輕鬆利用 iOS 的下列功能。 請參閱[TaskyL10n 範例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)以了解如何實作這些概念。

### <a name="specifying-default-and-supported-languages-in-infoplist"></a>指定的預設值而且 Info.plist 中支援的語言

在[技術問與答 QA1828: iOS 如何決定語言的應用程式](https://developer.apple.com/library/content/qa/qa1828/_index.html)，Apple 描述 iOS 如何選取要在應用程式中使用的語言。 下列因素會影響在顯示的語言：

- 使用者偏好的語言 (位於**設定**應用程式)
- 當地語系化資源隨同應用程式 （.lproj 資料夾）
- `CFBundleDevelopmentRegion` (**Info.plist**值，指定應用程式的預設語言)
- `CFBundleLocalizations` (**Info.plist**陣列，指定所有支援的當地語系化資源)

如同在技術問與答、`CFBundleDevelopmentRegion`代表應用程式的預設地區和語言。 如果應用程式明確不支援任何使用者的慣用語言，則會使用此欄位所指定的語言。 

> [!IMPORTANT]
> iOS 11 會比更舊版作業系統的嚴格套用此語言工作階段機制。 因為這個緣故，未明確宣告其支援的當地語系化資源 – 依包括.lproj 資料夾，或設定值的任何 iOS 11 應用程式`CFBundleLocalizations`– 與之前在 iOS 10 中，可能會顯示不同的語言中 iOS 11。

如果`CFBundleDevelopmentRegion`中尚未指定**Info.plist**檔案，Xamarin.iOS 建置工具目前使用的預設值為`en_US`。 雖然這可能會在未來版本中變更，這表示預設語言是英文。

若要確保您的應用程式選取預期的語言，請採取下列步驟：

- 指定預設語言。 開啟**Info.plist**並用**來源**檢視設定的值`CFBundleDevelopmentRegion`金鑰，在 XML 中，它看起來應該類似下列：

```xml
<key>CFBundleDevelopmentRegion</key>
<string>es</string>
```

這個範例會使用"es"來指定當沒有任何使用者的慣用語言都支援，預設為西班牙文。

- 宣告所有支援的當地語系化資源。 在**Info.plist**，使用**來源**設定陣列，以便檢視`CFBundleLocalizations`金鑰，在 XML 中，它看起來應該類似下列：

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>es</string>
    ...
</array>
```

Xamarin.iOS 應用程式使用.NET 機制，例如.resx 檔必須提供這些當地語系化的**Info.plist**以及值。

如需有關這些**Info.plist**索引鍵，看看 Apple[資訊屬性清單索引鍵參考](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html)。

### <a name="localizedstring-method"></a>LocalizedString 方法

`NSBundle.MainBundle.LocalizedString`方法會查詢中所儲存的當地語系化文字 **.strings**專案中的檔案。 這些檔案會依使用的特殊命名目錄中的語言， **.lproj**後置詞。

#### <a name="strings-file-locations"></a>.strings 檔案位置

- **Base.lproj**是包含預設語言資源的目錄。
  它通常位於專案根目錄中 (但也可以放在**資源**資料夾)。
- **<language>.lproj**來建立目錄的每個支援的語言，通常**資源**資料夾。

可以是不同的數字 **.strings**每個語言目錄中的檔案：

- **Localizable.strings** – 當地語系化文字的主要清單。
- **InfoPlist.strings** – 某些特定金鑰允許這個檔案中轉譯項目，例如應用程式名稱。
- **< 分鏡腳本名稱 >.strings** – 選擇性檔案，其中包含使用者介面項目的分鏡腳本中的翻譯。

**建置動作**這些檔案應該是**配套資源**。

#### <a name="strings-file-format"></a>.strings 檔案格式

當地語系化的字串值的語法是：

```console
/* comment */
"key"="localized-value";
```

您應該逸出字串中的下列字元：

* `\"`  引號
* `\\`  反斜線
* `\n`  新行字元

這是範例**es/Localizable.strings** （即西班牙文） 檔案的範例：

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

若要當地語系化 iOS 中的映像：

1. 請參閱映像中的程式碼，例如：

  ```csharp
  UIImage.FromBundle("flag");
  ```

2. 預設映像檔案的放置**flag.png**中**Base.lproj** （原生開發語言目錄）。

3. （選擇性） 將放在映像的當地語系化的版本 **.lproj**資料夾 （例如每個語言 **es.lproj**， **ja.lproj**)。 使用相同的檔名**flag.png**每個語言目錄中。

如果不存在特定語言的映像，iOS 會改為使用預設的原生語言資料夾，並從中載入的影像。


#### <a name="launch-images"></a>啟動映像

使用標準命名慣例啟動映像 （和 XIB 或分鏡腳本 iPhone 6 機型） 放置在時 **.lproj**每種語言的目錄。

```console
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>應用程式名稱

放置**InfoPlist.strings**檔案 **.lproj**目錄可讓您覆寫從應用程式的某些值**Info.plist**，包括應用程式名稱：

```console
"CFBundleDisplayName" = "LeónTodo";
```

您可以使用其他金鑰[當地語系化應用程式特定字串](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21)是：

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright

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

```console
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

西班牙文，西班牙中的結果：

```console
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

請參閱 Apple[日期格式器](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html)文件的詳細資訊。 測試時區分地區設定日期和時間格式，同時檢查**iPhone 語言**和**區域**設定。

<a name="rtl" />

### <a name="right-to-left-rtl-layout"></a>由右至左 (RTL) 配置

iOS 提供許多功能，協助您建立從右至左感知應用程式：

* 使用自動配置的`leading`和`trailing`控制項 aligment （其對應至 left 和 right 的英文，但已反轉 RTL 語言） 的屬性。
  [ `UIStackView` ](~/ios/user-interface/controls/uistackview.md)控制項特別適用於 RTL 注意控制項的版面配置。
* 使用`TextAlignment = UITextAlignment.Natural`（這會保留對大多數語言而言，但適合從右至左） 的文字對齊方式。
* `UINavigationController` 會自動翻轉上一頁按鈕及反轉撥動方向。

下列螢幕擷取畫面顯示[當地語系化 Tasky 範例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)阿拉伯文和希伯來文 （雖然已在欄位中輸入英文版）：

[![](images/rtl-ar-sml.png "阿拉伯當地語系化")](images/rtl-ar.png#lightbox "Arabic") 

[![](images/rtl-he-sml.png "當地語系化希伯來文")](images/rtl-he.png#lightbox "Hebrew")

iOS 會自動反轉`UINavigationController`，和其他控制項放置於`UIStackView`或使用自動配置對齊。
使用當地語系化從右至左文字 **.strings**方式與從左至右文字相同的檔案。

<a name="code"/>

## <a name="localizing-the-ui-in-code"></a>當地語系化的 UI 程式碼中

[Tasky （當地語系化程式碼中）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)範例示範如何將應用程式當地語系化使用者介面建立程式碼 （而非 XIBs 或分鏡腳本） 中的位置。

### <a name="project-structure"></a>專案結構

![](images/solution-code.png "樹狀目錄中的資源")

### <a name="localizablestrings-file"></a>Localizable.strings 檔案

如上所述， **Localizable.strings**檔案格式是由索引鍵-值組所組成。 索引鍵描述字串的意圖，而且值是要用於應用程式翻譯的文字。

西班牙文 (**es**) 範例的當地語系化資源如下所示：

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

* 在 Visual Studio for Mac 位於**屬性板**稱為**當地語系化 ID**。
* 在 Xcode 中，它會呼叫**物件識別碼**。

此字串值通常會有的形式，例如，"NF3-h 8-xmR"，如下列螢幕擷取畫面所示：

![](images/xs-designer-localization-id.png "分鏡腳本當地語系化的 [Xcode] 檢視")

此值用於 **.strings**檔案將自動指派給每個控制項的翻譯的文字。

### <a name="mainstoryboardstrings"></a>MainStoryboard.strings

分鏡腳本轉譯檔案的格式會類似於**Localizable.strings**檔案，不同之處在於 （左值） 的索引鍵不能是使用者定義，但是改為必須有非常特定的格式： `ObjectID.property`。

在範例**Mainstoryboard.strings**以下您可以看見`UITextField`有`placeholder`可當地語系化; text 屬性`UILabel`有`text`屬性; 和`UIButton`s 預設文字使用設定`normalTitle`:

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
> 使用分鏡腳本以大小的類別，可能會導致不會出現在應用程式的翻譯。 [Apple 的 Xcode 版本資訊](https://developer.apple.com/library/content/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html)表示的分鏡腳本或 XIB 將未當地語系化正確是否三件事是，則為 true： 它會使用大小的類別、 基底的當地語系化和 「 建置 」 目標會設定為萬用、 而建置的目標 iOS 7.0。 修正為您分鏡腳本字串檔案複製到兩個相同的檔案： **MainStoryboard~iphone.strings**和**MainStoryboard~ipad.strings**，如下列螢幕擷取畫面所示：
> 
> ![](images/xs-dup-strings.png "字串的檔案")

<a name="appstore" />

## <a name="app-store-listing"></a>應用程式市集清單

遵循 Apple 的常見問題集的[應用程式存放區當地語系化](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization)輸入您的應用程式會在銷售每個國家/地區的翻譯。 請注意如果您的應用程式也包含當地語系化翻譯只會顯示其警告 **.lproj**目錄的語言。

## <a name="summary"></a>總結

本文涵蓋當地語系化使用內建資源的處理和分鏡腳本功能的 iOS 應用程式基本的概念。

您可以了解更多關於 i18n 和 L10n iOS、 Android 和跨平台應用程式 （包括 Xamarin.Forms） 中[本跨平台指南](~/cross-platform/app-fundamentals/localization.md)。

## <a name="related-links"></a>相關連結

- [Tasky （當地語系化程式碼中） （範例）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky （當地語系化分鏡腳本） （範例）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Apple 當地語系化指南](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [跨平台當地語系化概觀](~/cross-platform/app-fundamentals/localization.md)
- [Xamarin.Forms 當地語系化](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Android 的當地語系化](~/android/app-fundamentals/localization.md)
