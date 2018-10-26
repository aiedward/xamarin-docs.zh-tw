---
title: 字串及影像當地語系化
description: Xamarin.Forms 應用程式可以使用.NET 資源檔來當地語系化。
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: 09fe3587e4e435383822e50bd12616747b807f82
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108453"
---
# <a name="localization"></a>當地語系化

_Xamarin.Forms 應用程式可以使用.NET 資源檔來當地語系化。_

## <a name="overview"></a>總覽

當地語系化 .NET 應用程式使用的內建機制[RESX 檔案](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx)類別和類別`System.Resources`和`System.Globalization`命名空間。 包含已翻譯的字串的 RESX 檔案內嵌在 Xamarin.Forms 組件，以及編譯器所產生的類別，提供翻譯的強型別存取。 然後可以在程式碼中擷取翻譯的文字。

### <a name="sample-code"></a>程式碼範例

有兩個與這份文件相關聯的範例：

* [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization)是非常簡單的示範，說明的概念。 如下所示的程式碼片段會從這個範例。
* [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized)會使用這些當地語系化技術的基本工作應用程式。

#### <a name="shared-projects-are-not-recommended"></a>不建議使用共用的專案

TodoLocalized 範例包括[共用專案示範](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/)不過，由於建置系統的限制資源檔不會收到 **。 designer.cs**所產生的檔案會中斷存取的能力強型別程式碼中的已翻譯的字串。

這份文件的其餘部分與使用 Xamarin.Forms.NET Standard 程式庫範本的專案產生關聯。

## <a name="globalizing-xamarinforms-code"></a>全球化 Xamarin.Forms 程式碼

**全球化**應用程式是程序讓 「 世界就緒 」。 這表示撰寫能夠顯示不同語言的程式碼。

全球化應用程式的主要部分的其中一個建置使用者介面，使其沒有任何*硬式編碼*文字。 相反地，從一組均已轉譯到其所選的語言的字串應該擷取向使用者顯示的任何項目。

在這份文件中，我們將檢驗如何使用 RESX 檔案來儲存這些字串，並顯示根據使用者的喜好設定擷取它們。

這些範例是以英文、 法文、 西班牙文、 德文、 中文、 日文、 俄文和巴西葡萄牙文的語言為目標。 應用程式，可以轉譯為所需的最少或多個語言。

> [!NOTE]
> 通用 Windows 平台上，則應該使用推播通知的當地語系化，而不是 RESX 檔案的 RESW 檔案。 如需詳細資訊，請參閱 < [UWP 當地語系化](/windows/uwp/design/globalizing/globalizing-portal/)。

### <a name="adding-resources"></a>新增資源

全球化 Xamarin.Forms.NET 標準程式庫應用程式的第一個步驟加入將用來儲存所使用的所有文字，應用程式中的 RESX 資源檔。 我們需要新增包含預設的文字，RESX 檔案，然後新增額外的 RESX 檔案，讓我們想要支援每種語言。

#### <a name="base-language-resource"></a>基底的語言資源

基底資源 (RESX) 檔案會包含預設語言字串 （這些範例會假設英文是預設的語言）。 將檔案新增至 Xamarin.Forms 的一般程式碼專案的專案上按一下滑鼠右鍵，然後選擇**新增 > 新增檔案...**.

選擇有意義的名稱，例如**AppResources**然後按**確定**。

[![加入資源檔](text-images/resx-new-file-sml.png "新的檔案對話方塊")](text-images/resx-new-file.png#lightbox "新增檔案 對話方塊")

兩個檔案會加入至專案：

* **AppResources.resx**可翻譯的字串中的 XML 格式儲存的檔案。
* **AppResources.designer.cs**宣告部分類別以包含 RESX XML 檔案中所建立的所有項目參考的檔案。

方案的樹狀結構會顯示相關的檔案。 RESX 檔案*應該*加以編輯以便新增新的翻譯字串; **。 designer.cs**檔案應該*不*進行編輯。

![](text-images/appresources-tree.png "AppResources.resx 檔案")

##### <a name="string-visibility"></a>字串的可見性

根據預設當產生強型別字串的參考時，將其`internal`組件。 這是因為 RESX 檔案的預設建置工具會產生 **。 designer.cs**檔案中使用`internal`屬性。

選取  **AppResources.resx**檔案，並顯示**屬性**板以查看此建置工具的設定。 下列螢幕擷取畫面**自訂工具： ResXFileCodeGenerator**。


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-internal-sml.png "AppResources.Resx 的 [屬性] 視窗")](text-images/vs-resx-internal.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "AppResources.Resx 的 properties Pad")](text-images/xs-resx-internal.png#lightbox)

-----

若要讓強型別字串屬性`public`，您必須以手動方式變更設定以**自訂工具： PublicResXFileCodeGenerator**，如以下螢幕擷取畫面所示：


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-public-sml.png "AppResources.Resx 的 [屬性] 視窗")](text-images/vs-resx-public.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "AppResources.Resx 的 properties Pad")](text-images/xs-resx-internal.png#lightbox)


[![](text-images/xs-resx-public-sml.png "AppResources.Resx 的 properties Pad")](text-images/xs-resx-public.png#lightbox)

-----

這項變更為選擇性，而且只需要如果您想要參考多個不同的組件的當地語系化的字串 （例如，如果您的程式碼的 RESX 檔案置於不同的組件中）。 本主題的範例會保留字串`internal`因為它們在相同 Xamarin.Forms.NET Standard 程式庫的組件中使用這些定義。

您只需要; 如上所示，在基底的 RESX 檔案上設定自訂的工具您不需要設定*任何*下列各節所述的特定語言的 RESX 檔案的建置工具。

##### <a name="editing-the-resx-file"></a>編輯 RESX 檔

不幸的是沒有內建的 RESX 編輯器中 Visual Studio for mac。 加入新翻譯的字串就必須加入新的 XML 程式`data`針對每個字串的項目。 每個`data`元素可以包含下列：

* `name` 這個可翻譯的字串的索引鍵屬性 （必要）。 它必須是有效的C#屬性的名稱-因此，不允許沒有空格或特殊字元。
* `value` 項目 （必要），這是會顯示在應用程式的實際字串。
* `comment` （選擇性） 的項目可以包含說明如何使用這個字串的轉譯器的指示。
* `xml:space` 屬性 （選擇性） 若要控制如何保留字串中的間距。

某些範例`data`項目如下所示：

```xml
<data name="NotesLabel" xml:space="preserve">
    <value>Notes:</value>
    <comment>label for input field</comment>
</data>
<data name="NotesPlaceholder" xml:space="preserve">
    <value>eg. buy milk</value>
    <comment>example input for notes field</comment>
</data>
<data name="AddButton" xml:space="preserve">
    <value>Add new item</value>
</data>
```

應用程式寫入時，每一項對使用者顯示的文字應該將基底的 RESX 資源檔中的新`data`項目。 建議您包含`comment`s 盡量以確保高品質的轉譯。

> [!NOTE]
> Visual Studio （包括免費的 Community edition） 包含基本的 RESX 編輯器。 如果您有 Windows 電腦的存取權，這可以是便利的方式，加入和編輯 RESX 檔中的字串。

#### <a name="language-specific-resources"></a>語言特有資源

一般而言，直到已寫入大量的應用程式區塊 （在此情況下預設 RESX 檔案將會包含大量字串），不會發生實際轉譯的預設文字字串。 它仍然是個不錯的主意，將語言特定資源新增及早在開發週期中，選擇性地填入提供的機器翻譯的文字，來協助測試當地語系化程式碼。

我們想要支援每種語言加入一個額外的 RESX 檔案。
特定語言資源檔案必須遵循特定的命名慣例： 基底的資源檔案 （例如使用相同的檔案名稱。 **AppResources**) 後面接著句號 （.），然後在語言程式碼。 簡單的範例包括：

* **AppResources.fr.resx** -法文語言的翻譯。
* **AppResources.es.resx** -西班牙文語言的翻譯。
* **AppResources.de.resx** -德文語言的翻譯。
* **AppResources.ja.resx** -日文語言的翻譯。
* **AppResources.zh Hans.resx** -中文 （簡體） 語言的翻譯。
* **AppResources.zh Hant.resx** -中文 （繁體） 語言的翻譯。
* **AppResources.pt.resx** -葡萄牙文語言的翻譯。
* **AppResources.pt BR.resx** -巴西葡萄牙文語言的翻譯。

一般的模式是使用兩個字母的語言代碼，但有一些範例 （例如繁體中文），使用不同的格式和其他範例 （例如巴西葡萄牙文） 需要四個字元的地區設定識別碼的情況。

這些特定語言資源檔案*則否*需要 **。 designer.cs**部分類別，因此成為規則 XML 檔案，與**建置動作： EmbeddedResource**設定。 此螢幕擷取畫面顯示包含特定語言資源檔的方案：

![](text-images/appresources-langs.png "語言特定資源檔")

應用程式開發及基底的 RESX 檔案已加入的文字時，您應該將它傳送給會轉譯每個轉譯器`data`項目並傳回特定語言資源檔 （使用顯示的命名慣例） 要包含在應用程式中。 某些 '機器翻譯' 範例如下所示：

**AppResources.es.resx （西班牙文）**

```xml
<data name="AddButton" xml:space="preserve">
    <value>Escribir un artículo</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.ja.resx （日文）**

```xml
<data name="AddButton" xml:space="preserve">
    <value>新しい項目を追加</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.pt BR.resx （巴西葡萄牙文）**

```xml
<data name="AddButton" xml:space="preserve">
    <value>adicionar novo item</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

只有`value`項目就必須更新的轉譯器-`comment`不是要轉譯。 請記住： 當編輯 XML 檔案來逸出保留的字元就像`<`， `>`，`&`具有`&lt;`， `&gt;`，和`&amp;`如果它們出現在`value`或`comment`。

<a name="incode" />

### <a name="using-resources-in-code"></a>程式碼中使用的資源

RESX 資源檔中的字串將可使用在使用者介面的程式碼使用`AppResources`類別。 `name`指派給每個字串中的 RESX 檔案會成為該類別可以在 Xamarin.Forms 程式碼中參考，如下所示的屬性：

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

對 iOS、 Android 和通用 Windows 平台 (UWP) 會轉譯成您的使用者介面所預期，但現在可將應用程式轉譯成多種語言，因為從資源載入文字，而非硬式編碼。 以下是顯示在轉譯之前的每個平台上的 UI 的螢幕擷取畫面：

![](text-images/simple-example-english.png "在轉譯之前的跨平台 Ui")

### <a name="troubleshooting"></a>疑難排解

#### <a name="testing-a-specific-language"></a>測試特定的語言

它可能很難切換模擬器或裝置到不同的語言，特別是在開發時您想要快速測試不同的文化特性。

您可以強制載入設定的特定語言`Culture`中此程式碼片段所示：

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

這種方法 – 上直接設定文化特性`AppResources`類別 – 也可以用來實作語言選取器內您的應用程式 （而非使用裝置的地區設定）。

#### <a name="loading-embedded-resources"></a>載入內嵌資源

嘗試偵錯 （例如 RESX 檔案） 的內嵌資源發生問題時，則下列程式碼片段會很有用。 將此程式碼新增至您的應用程式 （在初期的應用程式生命週期中），它會列出內嵌在組件，並顯示完整的資源識別項中的所有資源：

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly; // "EmbeddedImages" should be a class in your app
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

在  **AppResources.Designer.cs**檔案 (周圍*行 33*)，完整*資源管理員名稱*（例如指定。 `"UsingResxLocalization.Resx.AppResources"`) 類似於下列程式碼：

```csharp
System.Resources.ResourceManager temp =
        new System.Resources.ResourceManager(
                "UsingResxLocalization.Resx.AppResources",
                typeof(AppResources).GetTypeInfo().Assembly);
```

請檢查**應用程式輸出**為如上所示的偵錯程式碼的結果，以確認正確的資源會列出 （即`"UsingResxLocalization.Resx.AppResources"`).

如果沒有，則`AppResources`類別將無法載入它的資源。
檢查下列項目以解決問題，無法找到的資源：

* 專案的預設命名空間比對中的根命名空間**AppResources.Designer.cs**檔案。
* 如果**AppResources.resx**檔案會位於一個子目錄，子目錄名稱應該是部分的命名空間*和*的資源識別碼的一部分。
* **AppResources.resx**檔案具有**建置動作： EmbeddedResource**。
* **專案選項 > 來源程式碼 >.NET 命名原則 > 使用 Visual Studio 樣式資源名稱**已勾選。 如果您偏好 untick 這，然而參考您的 RESX 資源時所使用的命名空間會需要更新整個應用程式。

#### <a name="doesnt-work-in-debug-mode-android-only"></a>並不適用於偵錯模式 (僅限 Android)

如果在您的 Android 發行組建，但不是偵錯時，要使用的翻譯的字串，以滑鼠右鍵按一下**Android 專案**，然後選取**選項 > 建置 > Android 建置**，並確定**快速部署組件**未勾選。 此選項會造成載入資源的問題，並且不應在您要測試當地語系化的應用程式。

### <a name="displaying-the-correct-language"></a>顯示正確的語言

到目前為止我們已討論過如何撰寫程式碼，以便可以提供翻譯，但不是如何實際將它們顯示。 Xamarin.Forms 程式碼可以利用。NET 的資源，以載入正確的語言的翻譯，但我們需要查詢以判斷使用者選取哪一個語言的每個平台上的作業系統。

因為某些平台特定程式碼，才能取得使用者的語言喜好設定，使用[相依性服務](~/xamarin-forms/app-fundamentals/dependency-service/index.md)公開 Xamarin.Forms 應用程式中的資訊，並實作每個平台。

首先，定義一個介面以公開使用者的慣用文化特性，類似於下列程式碼：

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

其次，使用[DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) Xamarin.Forms 中`App`類別呼叫的介面，並將我們的 RESX 資源文化特性設定為正確的值。 請注意，我們不需要以手動方式將此值設定通用 Windows 平台，因為資源 framework 自動會將選取的語言辨識這些平台上。

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

資源`Culture`必須設定應用程式首次載入，因此會使用正確的語言字串時。 您可能會選擇性地更新根據平台特定事件可能會在 iOS 或 Android 上引發，如果使用者更新其語言喜好設定，應用程式執行時，這個值。

實作`ILocalize`介面所示[平台特定程式碼](#Platform-specific_Code)下一節。 這些實作充分運用此`PlatformCulture`協助程式類別：

```csharp
public class PlatformCulture
{
    public PlatformCulture (string platformCultureString)
    {
        if (String.IsNullOrEmpty(platformCultureString))
        {
            throw new ArgumentException("Expected culture identifier", "platformCultureString"); // in C# 6 use nameof(platformCultureString)
        }
        PlatformString = platformCultureString.Replace("_", "-"); // .NET expects dash, not underscore
        var dashIndex = PlatformString.IndexOf("-", StringComparison.Ordinal);
        if (dashIndex > 0)
        {
            var parts = PlatformString.Split('-');
            LanguageCode = parts[0];
            LocaleCode = parts[1];
        }
        else
        {
            LanguageCode = PlatformString;
            LocaleCode = "";
        }
    }
    public string PlatformString { get; private set; }
    public string LanguageCode { get; private set; }
    public string LocaleCode { get; private set; }
    public override string ToString()
    {
        return PlatformString;
    }
}
```

<a name="Platform-specific_Code" />

### <a name="platform-specific-code"></a>平台特定程式碼

偵測要顯示哪一個語言的程式碼必須是特定平台，因為 iOS、 Android 和 UWP 所有會稍有不同的方式公開此資訊。 程式碼`ILocalize`相依性服務會提供下列每個平台，以及其他平台特定需求，以確保當地語系化的文字才能正確轉譯。

平台特定程式碼也必須處理其中的作業系統可讓使用者設定不支援的地區設定識別碼的情況。NET 的`CultureInfo`類別。 在這些情況下，必須偵測到不受支援的地區設定，並以取代最佳撰寫自訂程式碼.NET 相容的地區設定。

#### <a name="ios-application-project"></a>iOS 應用程式專案

iOS 使用者從日期和時間格式的文化特性，請以個別選取其偏好的語言。 若要載入正確的資源要當地語系化的 Xamarin.Forms 應用程式，我們只需要查詢`NSLocale.PreferredLanguages`陣列中的第一個項目。

下列實作`ILocalize`相依性服務應該放在 iOS 應用程式專案。 因為 iOS 使用底線，而不是連字號 （這是.NET 的標準表示法） 程式碼會具現化之前取代底線`CultureInfo`類別：

```csharp
[assembly:Dependency(typeof(UsingResxLocalization.iOS.Localize))]

namespace UsingResxLocalization.iOS
{
public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale (CultureInfo ci)
        {
            Thread.CurrentThread.CurrentCulture = ci;
            Thread.CurrentThread.CurrentUICulture = ci;
        }
        public CultureInfo GetCurrentCultureInfo ()
        {
            var netLanguage = "en";
            if (NSLocale.PreferredLanguages.Length > 0)
            {
                var pref = NSLocale.PreferredLanguages [0];
                netLanguage = iOSToDotnetLanguage(pref);
            }
            // this gets called a lot - try/catch can be expensive so consider caching or something
            System.Globalization.CultureInfo ci = null;
            try
            {
                ci = new System.Globalization.CultureInfo(netLanguage);
            }
            catch (CultureNotFoundException e1)
            {
                // iOS locale not valid .NET culture (eg. "en-ES" : English in Spain)
                // fallback to first characters, in this case "en"
                try
                {
                    var fallback = ToDotnetFallbackLanguage(new PlatformCulture(netLanguage));
                    ci = new System.Globalization.CultureInfo(fallback);
                }
                catch (CultureNotFoundException e2)
                {
                    // iOS language not valid .NET culture, falling back to English
                    ci = new System.Globalization.CultureInfo("en");
                }
            }
            return ci;
        }
        string iOSToDotnetLanguage(string iOSLanguage)
        {
            var netLanguage = iOSLanguage;
            //certain languages need to be converted to CultureInfo equivalent
            switch (iOSLanguage)
            {
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":    // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "gsw-CH":  // "Schwiizertüütsch (Swiss German)" not supported .NET culture
                    netLanguage = "de-CH"; // closest supported
                    break;
                // add more application-specific cases here (if required)
                // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
        string ToDotnetFallbackLanguage (PlatformCulture platCulture)
        {
            var netLanguage = platCulture.LanguageCode; // use the first part of the identifier (two chars, usually);
            switch (platCulture.LanguageCode)
            {
                case "pt":
                    netLanguage = "pt-PT"; // fallback to Portuguese (Portugal)
                    break;
                case "gsw":
                    netLanguage = "de-CH"; // equivalent to German (Switzerland) for this app
                    break;
                // add more application-specific cases here (if required)
                // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
    }
}
```

> [!NOTE]
> `try/catch`區塊，以`GetCurrentCultureInfo`方法模擬常用的地區設定規範 – 如果在完全相符找不到，查看關閉的相符項目就根據語言 （地區設定中的字元的第一個區塊） 的後援行為。
>
> Xamarin.Forms，在某些地區設定 iOS 中的有效值，但不是會對應至有效`CultureInfo`.NET 和上述嘗試處理此程式碼中。
>
> 例如，iOS**設定 > 一般語言&amp;區域**畫面可讓您設定您的手機**語言**來**英文**但**區域**要**西班牙**，這會導致的地區設定字串`"en-ES"`。 當`CultureInfo`建立失敗，程式碼改回使用剛才的前兩個字母來選取顯示語言。
>
> 開發人員應該修改`iOSToDotnetLanguage`和`ToDotnetFallbackLanguage`方法來處理其支援的語言所需的特定案例。


自動 ios，例如轉譯一些系統定義的使用者介面元素**完成**按鈕`Picker`控制項。 若要強制這些元素，就必須指明我們支援哪些語言翻譯的 iOS **Info.plist**檔案。 您可以新增這些值，透過**Info.plist > 來源**如下所示：

![在 Info.plist 中的當地語系化金鑰](text-images/info-plist.png "當地語系化 Info.plist 中的索引鍵")

或者，開啟**Info.plist**檔案在 XML 編輯器中，並直接編輯的值：

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>de</string>
    <string>es</string>
    <string>fr</string>
    <string>ja</string>
    <string>pt</string> <!-- Brazil -->
    <string>pt-PT</string> <!-- Portugal -->
    <string>ru</string>
    <string>zh-Hans</string>
    <string>zh-Hant</string>
</array>
<key>CFBundleDevelopmentRegion</key>
<string>en</string>
```

一旦您已實作相依性服務，並更新**Info.plist**，iOS 應用程式將能夠顯示當地語系化的文字。

> [!NOTE]
> 請注意，預期稍有不同於您的 Apple 視為葡萄牙文。
> 從[其 docs](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2): _"作為 pt 的語言識別碼葡萄牙文所巴西和 PT-PT 中做為語言識別碼葡萄牙文像用於葡萄牙"_。
> 這表示當葡萄牙文語言會選擇在非標準的地區設定中，後援語言會是巴西葡萄牙文在 iOS 上，除非撰寫程式碼來變更此行為 (例如`ToDotnetFallbackLanguage`上方)。

如需 iOS 當地語系化的詳細資訊，請參閱[iOS 當地語系化](~/ios/app-fundamentals/localization/index.md)。

#### <a name="android-application-project"></a>Android 應用程式專案

Android 會公開目前所選的地區設定，透過`Java.Util.Locale.Default`，並也會使用底線分隔符號，而不是以連字號 （這會取代下列程式碼）。 此相依性服務將實作新增至 Android 應用程式專案：

```csharp
[assembly:Dependency(typeof(UsingResxLocalization.Android.Localize))]

namespace UsingResxLocalization.Android
{
    public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale(CultureInfo ci)
        {
            Thread.CurrentThread.CurrentCulture = ci;
            Thread.CurrentThread.CurrentUICulture = ci;
        }
        public CultureInfo GetCurrentCultureInfo()
        {
            var netLanguage = "en";
            var androidLocale = Java.Util.Locale.Default;
            netLanguage = AndroidToDotnetLanguage(androidLocale.ToString().Replace("_", "-"));
            // this gets called a lot - try/catch can be expensive so consider caching or something
            System.Globalization.CultureInfo ci = null;
            try
            {
                ci = new System.Globalization.CultureInfo(netLanguage);
            }
            catch (CultureNotFoundException e1)
            {
                // iOS locale not valid .NET culture (eg. "en-ES" : English in Spain)
                // fallback to first characters, in this case "en"
                try
                {
                    var fallback = ToDotnetFallbackLanguage(new PlatformCulture(netLanguage));
                    ci = new System.Globalization.CultureInfo(fallback);
                }
                catch (CultureNotFoundException e2)
                {
                    // iOS language not valid .NET culture, falling back to English
                    ci = new System.Globalization.CultureInfo("en");
                }
            }
            return ci;
        }
        string AndroidToDotnetLanguage(string androidLanguage)
        {
            var netLanguage = androidLanguage;
            //certain languages need to be converted to CultureInfo equivalent
            switch (androidLanguage)
            {
                case "ms-BN":   // "Malaysian (Brunei)" not supported .NET culture
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":   // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "in-ID":  // "Indonesian (Indonesia)" has different code in  .NET
                    netLanguage = "id-ID"; // correct code for .NET
                    break;
                case "gsw-CH":  // "Schwiizertüütsch (Swiss German)" not supported .NET culture
                    netLanguage = "de-CH"; // closest supported
                    break;
                    // add more application-specific cases here (if required)
                    // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
        string ToDotnetFallbackLanguage(PlatformCulture platCulture)
        {
            var netLanguage = platCulture.LanguageCode; // use the first part of the identifier (two chars, usually);
            switch (platCulture.LanguageCode)
            {
                case "gsw":
                    netLanguage = "de-CH"; // equivalent to German (Switzerland) for this app
                    break;
                    // add more application-specific cases here (if required)
                    // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
    }
}
```

> [!NOTE]
> `try/catch`區塊，以`GetCurrentCultureInfo`方法模擬常用的地區設定規範 – 如果在完全相符找不到，查看關閉的相符項目就根據語言 （地區設定中的字元的第一個區塊） 的後援行為。
>
> Xamarin.Forms，在某些地區設定不正確在 Android 中但不是會對應至有效`CultureInfo`.NET 和上述嘗試處理此程式碼中。
>
> 開發人員應該修改`iOSToDotnetLanguage`和`ToDotnetFallbackLanguage`方法來處理其支援的語言所需的特定案例。

此程式碼新增至 Android 應用程式專案之後, 會自動顯示翻譯的字串。

> [!NOTE]
>️**警告：** 如果在您的 Android 發行組建，但不是偵錯時，要使用的翻譯的字串，以滑鼠右鍵按一下**Android 專案**，然後選取**選項 > 建置 > Android建置**，並確定**快速部署組件**未勾選。 此選項會造成載入資源的問題，並且不應在您要測試當地語系化的應用程式。

如需 Android 當地語系化的詳細資訊，請參閱[Android 當地語系化](~/android/app-fundamentals/localization.md)。

#### <a name="universal-windows-platform"></a>通用 Windows 平台

通用 Windows 平台 (UWP) 專案不需要相依性服務。 相反地，此平台會自動設定資源的文化特性正確。

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

展開 .NET Standard 程式庫專案中的 屬性 節點，然後按兩下**AssemblyInfo.cs**檔案。 若要設定為 英文的中性資源組件語言檔案中加入下面這一行：

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

這會通知資源管理員的應用程式的預設文化特性，因此如此可確保語言中性 RESX 檔案中定義的字串 (**AppResources.resx**) 將會顯示應用程式在執行時，其中一個英文的地區設定。

### <a name="example"></a>範例

更新平台特定專案做為顯示上述，並使用翻譯的 RESX 檔案，重新編譯應用程式之後, 已更新的翻譯可在每個應用程式。 以下是範例程式碼轉譯成簡體中文的螢幕擷取畫面：

![](text-images/simple-example-hans.png "跨平台 Ui 轉譯成簡體中文")

如需 UWP 當地語系化的詳細資訊，請參閱[UWP 當地語系化](/windows/uwp/design/globalizing/globalizing-portal/)。

## <a name="localizing-xaml"></a>當地語系化 XAML

當建立 Xamarin.Forms 的使用者介面中 XAML 標記看起來會如下所示，使用字串內嵌在直接以 XML:

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

在理想情況下，我們可能會將轉譯直接在 XAML 中，我們可以這麼做建立的使用者介面控制項*標記延伸*。 公開 （expose） 至 XAML 的 RESX 資源的標記延伸模組的程式碼如下所示。 這個類別應該將 Xamarin.Forms 一般程式碼 （以及 XAML 頁面中）：

```csharp
using System;
using System.Globalization;
using System.Reflection;
using System.Resources;
using Xamarin.Forms;
using Xamarin.Forms.Xaml;

namespace UsingResxLocalization
{
    // You exclude the 'Extension' suffix when using in XAML
    [ContentProperty("Text")]
    public class TranslateExtension : IMarkupExtension
    {
        readonly CultureInfo ci = null;
        const string ResourceId = "UsingResxLocalization.Resx.AppResources";

        static readonly Lazy<ResourceManager> ResMgr = new Lazy<ResourceManager>(
            () => new ResourceManager(ResourceId, IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly));

        public string Text { get; set; }

        public TranslateExtension()
        {
            if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
            {
                ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
            }
        }

        public object ProvideValue(IServiceProvider serviceProvider)
        {
            if (Text == null)
                return string.Empty;

            var translation = ResMgr.Value.GetString(Text, ci);
            if (translation == null)
            {
#if DEBUG
                throw new ArgumentException(
                    string.Format("Key '{0}' was not found in resources '{1}' for culture '{2}'.", Text, ResourceId, ci.Name),
                    "Text");
#else
                translation = Text; // HACK: returns the key, which GETS DISPLAYED TO THE USER
#endif
            }
            return translation;
        }
    }
}
```

下面幾點說明上述程式碼中的重要項目：

* 類別名為`TranslateExtension`，但依照慣例，我們可以參考為**翻譯**在我們的標記。
* 此類別會實作`IMarkupExtension`，所需的 Xamarin.Forms 它運作。
* `"UsingResxLocalization.Resx.AppResources"` 是我們的 RESX 資源的資源識別碼。 它包含我們的預設命名空間、 資源檔所在的資料夾和預設的 RESX 檔案名稱。
* `ResourceManager`類別會使用建立`IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly)`若要判斷目前的組件載入資源，並快取中靜態`ResMgr`欄位。 它會建立為`Lazy`型別，如此會延後建立，直到在第一次使用`ProvideValue`方法。
* `ci` 若要從原生作業系統中取得使用者的所選的語言中使用相依性服務。
* `GetString` 是從資源檔擷取的實際的已翻譯的字串的方法。 通用 Windows 平台上`ci`將會是 null 因為`ILocalize`在這些平台上未實作介面。 這就相當於呼叫`GetString`只含第一個參數的方法。 相反地，資源 framework 會自動辨識的地區設定，並會從適當的 RESX 檔擷取的已翻譯的字串。
* 錯誤處理已包含在內，以協助偵錯遺漏的資源，藉由擲回例外狀況 (在`DEBUG`僅限模式)。

下列 XAML 程式碼片段示範如何使用標記延伸。 有兩個步驟，讓它運作：

1. 宣告自訂`xmlns:i18n`根節點中的命名空間。 `namespace`和`assembly`必須符合完全-在此範例中完全相同但可能會在您的專案不同的專案設定。
2. 使用`{Binding}`屬性，通常會包含要呼叫的文字語法`Translate`標記延伸。 資源索引鍵是唯一的必要的參數。

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        x:Class="UsingResxLocalization.FirstPageXaml"
        xmlns:i18n="clr-namespace:UsingResxLocalization;assembly=UsingResxLocalization">
    <StackLayout Padding="0, 20, 0, 0">
        <Label Text="{i18n:Translate NotesLabel}" />
        <Entry Placeholder="{i18n:Translate NotesPlaceholder}" />
        <Button Text="{i18n:Translate AddButton}" />
    </StackLayout>
</ContentPage>
```

下列更詳細的語法也是有效的標記延伸模組：

```xaml
<Button Text="{i18n:TranslateExtension Text=AddButton}" />
```

## <a name="localizing-platform-specific-elements"></a>當地語系化平台特定項目

雖然我們可以處理翻譯的 Xamarin.Forms 程式碼中的使用者介面，有一些必須完成每個平台專屬專案中的項目。 本節將討論如何當地語系化：

* Application Name
* 影像

範例專案包含當地語系化的映像，稱為**flag.png**中, 參考的C#，如下所示：

```csharp
var flag = new Image();
switch (Device.RuntimePlatform)
{
    case Device.iOS:
    case Device.Android:
        flag.Source = ImageSource.FromFile("flag.png");
        break;
    case Device.UWP:
        flag.Source = ImageSource.FromFile("Assets/Images/flag.png");
        break;
}
```

旗標的映像也會參考中的 XAML，就像這樣：

```xaml
<Image>
  <Image.Source>
    <OnPlatform x:TypeArguments="ImageSource">
        <On Platform="iOS, Android" Value="flag.png" />
        <On Platform="UWP" Value="Assets/Images/flag.png" />
    </OnPlatform>
  </Image.Source>
</Image>
```

所有平台，只要實作如下所述的專案結構，將會自動解除這些當地語系化版本的映像，映像參考。

### <a name="ios-application-project"></a>iOS 應用程式專案

iOS 使用命名標準稱為當地語系化專案或 **.lproj**目錄將包含影像和字串資源。 這些目錄可以包含在應用程式中使用的映像的當地語系化的版本，也**InfoPlist.strings**可以用來當地語系化應用程式名稱的檔案。 如需 iOS 當地語系化的詳細資訊，請參閱[iOS 當地語系化](~/ios/app-fundamentals/localization/index.md)。

#### <a name="images"></a>影像

此螢幕擷取畫面顯示 iOS 範例應用程式與語言特有 **.lproj**目錄。 西班牙文的目錄稱為**es.lproj**，包含當地語系化的版本的預設映像，以及**flag.png**:

![](text-images/ios-resources.png "iOS 當地語系化的專案目錄")

每個語言目錄包含一份**flag.png**、 該語言的當地語系化。 如果未不提供任何映像，則作業系統會預設為預設語言的目錄中的映像中。 如需完整的 Retina 支援，您應該提供**@2x**並**@3x**複本的每個映像。

#### <a name="app-name"></a>應用程式名稱

內容**InfoPlist.strings**只是單一索引鍵-值來設定應用程式名稱：

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

執行應用程式時，應用程式名稱和映像會同時當地語系化：

![](text-images/ios-imageicon.png "iOS 範例應用程式文字及影像當地語系化")

### <a name="android-application-project"></a>Android 應用程式專案

Android 會遵循不同的配置來儲存使用不同的當地語系化映像**drawable**並**字串**與語言的程式碼後置字元的目錄。 （例如 ZH-TW 或 PT-BR） 需要四個字母的地區設定程式碼時，請注意，Android 需要額外**r**下列 dash/上述地區設定程式碼 （例如。 zh rTW 或 pt rBR）。 如需 Android 當地語系化的詳細資訊，請參閱[Android 當地語系化](~/android/app-fundamentals/localization.md)。

#### <a name="images"></a>影像

此螢幕擷取畫面顯示 Android 範例，其中含有一些當地語系化的可繪製資源來和字串：

![](text-images/android-resources.png "Android 當地語系化的可繪製資源來和字串目錄")

請注意，Android 不會使用-Zh-hans 與簡體和繁體中文; Zh-hant 代碼相反地，它只支援特定國家/地區碼 ZH-CN 和 ZH-TW。

若要支援高密度的畫面不同解析度的影像，建立額外的語言資料夾`-*dpi`字尾，這類**可繪製資源來-es-mdpi**，**可繪製資源來-es-xdpi**， **可繪製資源來-es-xxdpi**等等。請參閱[提供替代 Android 資源](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources)如需詳細資訊。

#### <a name="app-name"></a>應用程式名稱

內容**strings.xml**只是單一索引鍵-值來設定應用程式名稱：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">ResxEspañol</string>
</resources>
```

更新**MainActivity.cs** Android 應用程式專案中以便`Label`參考 XML 的字串。

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

應用程式現在會當地語系化的應用程式名稱和影像。 以下是結果 （以西班牙文） 的螢幕擷取畫面：

![](text-images/android-imageicon.png "Android 範例應用程式文字及影像當地語系化")

### <a name="universal-windows-platform-application-projects"></a>通用 Windows 平台應用程式專案

通用 Windows 平台擁有資源的基礎結構，可簡化映像和應用程式名稱的當地語系化。 如需 UWP 當地語系化的詳細資訊，請參閱[UWP 當地語系化](/windows/uwp/design/globalizing/globalizing-portal/)。

#### <a name="images"></a>影像

您可以藉由將它們放在特定資源的資料夾中，當地語系化映像，如下列螢幕擷取畫面所示：

![](text-images/uwp-image-folder-structure.png "UWP 影像當地語系化資料夾結構")

在執行階段的 Windows 資源的基礎結構會選取適當的映像，根據使用者的地區設定。

## <a name="summary"></a>總結

Xamarin.Forms 應用程式可以使用 RESX 檔案與.NET 全球化類別來當地語系化。 除了少量的平台特定程式碼，來偵測使用者偏好的語言為何，大部分的當地語系化工作被集中在一般程式碼。

映像通常會以特定平台來善用提供 iOS 和 Android 中的多解析度支援處理。

## <a name="related-links"></a>相關連結

- [RESX 當地語系化範例](https://developer.xamarin.com/samples/xamarin-forms/UsingResxLocalization/)
- [TodoLocalized 範例應用程式](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalized/)
- [跨平台的當地語系化](~/cross-platform/app-fundamentals/localization.md)
- [iOS 當地語系化](~/ios/app-fundamentals/localization/index.md)
- [Android 的當地語系化](~/android/app-fundamentals/localization.md)
- [UWP 當地語系化](/windows/uwp/design/globalizing/globalizing-portal/)
- [使用 CultureInfo 類別 (MSDN)](http://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [尋找和使用資源的特定文化特性 (MSDN)](http://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
