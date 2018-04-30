---
title: 當地語系化
description: Xamarin.Forms 應用程式可以使用.NET 資源檔來當地語系化。
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: f179fcfc26dd73bf1655c786078dce1f6a02b3a9
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2018
---
# <a name="localization"></a>當地語系化

_Xamarin.Forms 應用程式可以使用.NET 資源檔來當地語系化。_

## <a name="overview"></a>總覽

當地語系化 .NET 應用程式使用的內建機制[RESX 檔案](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx)中的類別和`System.Resources`和`System.Globalization`命名空間。 RESX 檔案包含已翻譯的字串會內嵌 Xamarin.Forms 組件，以及編譯器所產生的類別，提供強型別存取翻譯。 接著，可以轉譯文字擷取程式碼中。

### <a name="sample-code"></a>程式碼範例

有兩個與這份文件相關聯的範例：

* [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization)非常簡單示範所說明的概念。 如下所示的程式碼片段是所有來自此範例。
* [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized)是基本的工作應用程式會使用這些當地語系化技巧。

#### <a name="shared-projects-are-not-recommended"></a>不建議使用共用的專案

TodoLocalized 範例包括[共用專案示範](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/)不過由於建置系統的限制資源檔不會收到 **.designer.cs**產生的檔案而中斷存取的能力翻譯字串[強型別程式碼中](~/xamarin-forms/app-fundamentals/localization.md)。

使用 Xamarin.Forms PCL 範本的專案與本文件其餘內容。

## <a name="globalizing-xamarinforms-code"></a>全球化 Xamarin.Forms 程式碼

**全球化**應用程式是程序讓 「 全球化 」。 這表示撰寫程式碼，可顯示不同的語言。

其中的主要部分的全球化應用程式正在建置使用者介面，使其沒有任何*硬式編碼*文字。 相反地，對使用者顯示的任何項目應該擷取從一組均已轉譯成其所選語言的字串。

在這份文件中，我們將檢驗如何使用 RESX 檔案來儲存這些字串，並擷取這些顯示根據使用者的喜好設定。

這些範例是以英文、 法文、 西班牙文、 德文、 中文、 日文、 俄文和巴西葡萄牙文語言為目標。 應用程式可以轉譯成所需的最少或最多語言。

### <a name="adding-resources"></a>將資源加入

全球化 Xamarin.Forms PCL 應用程式的第一個步驟加入將用於儲存應用程式中所使用的所有文字的 RESX 資源檔。 我們需要加入 RESX 檔案包含預設的文字，然後再加入其他 RESX 檔案，我們想要支援每種語言。

#### <a name="base-language-resource"></a>基底語言資源

基底資源 (RESX) 檔案將包含 （這些範例假設英文為預設語言） 的預設語言字串。 將檔案新增至 Xamarin.Forms 通用程式碼專案的專案上按一下滑鼠右鍵，然後選擇**新增 > 新的檔案...**.

選擇有意義的名稱，例如**AppResources**按**確定**。

[![將資源檔加入](localization-images/resx-new-file-sml.png "新檔案對話方塊")](localization-images/resx-new-file.png#lightbox "新檔案 對話方塊")

兩個檔案會加入至專案：

* **AppResources.resx**可翻譯的字串以 XML 格式儲存的檔案。
* **AppResources.designer.cs**包含參考的 RESX XML 檔案中建立的所有項目將部分類別宣告的檔案。

方案樹狀目錄會顯示為相關的檔案。 RESX 檔案*應該*編輯以新增新的可翻譯字串; **.designer.cs**檔案應該*不*進行編輯。

![](localization-images/appresources-tree.png "AppResources.resx 檔案")

##### <a name="string-visibility"></a>字串的可見性

依預設會產生強類型字串的參考，將予以`internal`組件。 這是因為 RESX 檔案的預設建置工具會產生 **.designer.cs**檔案搭配`internal`屬性。

選取**AppResources.resx**檔案，並顯示**屬性**若要查看此建置工具所在的填補設定。 螢幕擷取畫面所示**自訂工具： ResXFileCodeGenerator**。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](localization-images/vs-resx-internal-sml.png "AppResources.Resx 屬性 視窗")](localization-images/vs-resx-internal.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](localization-images/xs-resx-internal-sml.png "AppResources.Resx 屬性板")](localization-images/xs-resx-internal.png#lightbox)

-----

若要讓強類型字串屬性`public`，您必須手動設定變更為**自訂工具： PublicResXFileCodeGenerator**，如以下螢幕擷取畫面所示：


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](localization-images/vs-resx-public-sml.png "AppResources.Resx 屬性 視窗")](localization-images/vs-resx-public.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](localization-images/xs-resx-internal-sml.png "AppResources.Resx 屬性板")](localization-images/xs-resx-internal.png#lightbox)


[![](localization-images/xs-resx-public-sml.png "AppResources.Resx 屬性板")](localization-images/xs-resx-public.png#lightbox)

-----

這項變更是選擇性的而且僅在需要如果您想要參考多個不同的組件的當地語系化的字串 （例如，如果您的程式碼 RESX 檔案置於不同的組件中）。 本主題的範例會保留字串`internal`因為其定義中使用這些相同的 Xamarin.Forms PCL 組件。

您只需要; 如上所示，在基底的 RESX 檔案上設定自訂的工具您不需要設定*任何*下列各節中討論的特定語言 RESX 檔案的建置工具。

##### <a name="editing-the-resx-file"></a>編輯 RESX 檔案

不幸的是沒有內建的 RESX 編輯器中 Visual Studio for mac。 加入新的可翻譯字串需要加入新的 XML`data`每個字串的項目。 每個`data`項目可以包含下列：

* `name` 這個轉譯字串的索引鍵屬性 （必要）。 它必須是有效 C# 屬性名稱-因此允許沒有空格或特殊字元。
* `value` 項目 （必要），也就是實際顯示在應用程式中的字串。
* `comment` （選擇性） 的項目可以包含說明如何使用這個字串的轉譯器的指示。
* `xml:space` （選擇性） 若要控制字串中的間距的保留方式的屬性。

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

當寫入應用程式，每個向使用者顯示的文字片段中，加入基底 RESX 資源檔新`data`項目。 建議您包含`comment`s 盡可能以確保高品質轉譯。

> [!NOTE]
> Visual Studio （包括免費的 Community 版本） 包含基本的 RESX 編輯器。 如果您有存取權的 Windows 電腦，可以是便利的方式加入和編輯 RESX 檔案中的字串。

#### <a name="language-specific-resources"></a>語言專屬的資源

一般而言之前已寫入大量的應用程式區塊 （在此情況下預設 RESX 檔案將會包含大量字串）,，不會進行預設文字字串的實際的轉換。 它仍然是建議您將語言特定資源新增及早在開發週期中，選擇性地填入機器翻譯的文字，來協助測試當地語系化的程式碼。

我們想要支援每種語言加入一個額外的 RESX 檔案。
將語言特定資源檔案必須遵循特定的命名慣例： 基底資源檔案 （例如使用相同的檔案名稱 **AppResources**) 後面接著句號 （.），然後的語言代碼。 簡單的範例包括：

* **AppResources.fr.resx** -法文語言翻譯。
* **AppResources.es.resx** -西班牙文語言翻譯。
* **AppResources.de.resx** -德文語言翻譯。
* **AppResources.ja.resx** -日文語言翻譯。
* **AppResources.zh Hans.resx** -中文 （簡體） 語言翻譯。
* **AppResources.zh Hant.resx** -中文 （繁體） 語言翻譯。
* **AppResources.pt.resx** -葡萄牙文語言翻譯。
* **AppResources.pt BR.resx** -巴西葡萄牙文語言翻譯。

一般模式將會是使用兩個字母的語言代碼，但有一些範例 （例如中文） 使用不同格式的位置和其他範例 （例如巴西葡萄牙文） 需要四個字元的地區設定識別碼的地方。

這些語言專屬的資源檔案*不*需要 **.designer.cs**部分類別，因此可以將它們加入為規則的 XML 檔案，與**建置動作： EmbeddedResource**設定。 這個螢幕擷取畫面會顯示包含特定語言的資源檔的方案：

![](localization-images/appresources-langs.png "將語言特定資源檔")

應用程式的開發與基底 RESX 檔案已加入文字時，您應該將它傳送給會轉譯每個轉譯器`data`項目並傳回特定語言的資源檔 （使用顯示的命名慣例） 要包含在應用程式中。 某些 '機器轉譯' 範例如下所示：

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

只有`value`項目需要更新的轉譯器-`comment`不是要轉譯。 請記住： 當編輯 XML 檔案來逸出保留的字元，像是`<`， `>`，`&`與`&lt;`， `&gt;`，和`&amp;`如果它們出現在`value`或`comment`。

<a name="incode" />

### <a name="using-resources-in-code"></a>程式碼中使用的資源

RESX 資源檔中的字串將可使用在使用者介面程式碼中使用`AppResources`類別。 `name`指派給每個字串中 RESX 檔案會成為可以 Xamarin.Forms 程式碼中參考，如下所示的類別的屬性：

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

IOS、 Android 和的通用 Windows 平台 (UWP) 會轉譯成您的使用者介面所預期的除了現在可以將應用程式轉譯成多種語言，因為正在從資源載入文字而非硬式編碼。 以下是顯示在轉譯之前的每個平台上的 UI 螢幕擷取畫面：

![](localization-images/simple-example-english.png "跨平台 Ui 之前轉譯")

### <a name="troubleshooting"></a>疑難排解

#### <a name="testing-a-specific-language"></a>測試特定的語言

它可能很困難，不同的語言，當您想要快速測試不同的文化特性在開發期間 particulary 切換模擬器或裝置。

您可以強制載入藉由設定特定語言`Culture`此程式碼片段所示：

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

這種方法 – 上直接設定文化特性`AppResources`類別 – 也可以用來實作您的應用程式 （而非使用裝置的地區設定） 內語言選取器。

#### <a name="loading-embedded-resources"></a>載入內嵌資源

下列程式碼片段時，嘗試偵錯 （例如 RESX 檔案） 的內嵌資源的問題。 將這段程式碼加入至您的應用程式 （在早期的應用程式生命週期中），它會列出內嵌在組件，並顯示完整資源識別碼中的所有資源：

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

在**AppResources.Designer.cs**檔案 (周圍*行 33*)，完整*資源管理員名稱*指定 （例如。 `"UsingResxLocalization.Resx.AppResources"`) 類似於下列程式碼：

```csharp
System.Resources.ResourceManager temp =
        new System.Resources.ResourceManager(
                "UsingResxLocalization.Resx.AppResources",
                typeof(AppResources).GetTypeInfo().Assembly);
```

請檢查**應用程式輸出**如上所示的偵錯程式碼的結果，確認正確的資源分別列 （即`"UsingResxLocalization.Resx.AppResources"`).

如果沒有，則`AppResources`類別將無法載入其資源。
選取下列選項以解決問題，無法在其中找到的資源：

* 專案的預設命名空間符合的根命名空間**AppResources.Designer.cs**檔案。
* 如果**AppResources.resx**檔案位於子目錄、 子目錄名稱應該是部分的命名空間*和*屬於的資源識別碼。
* **AppResources.resx**檔案具有**建置動作： EmbeddedResource**。
* **專案選項 > 來源程式碼 >.NET Naming Policies > 使用 Visual Studio 樣式資源名稱**核。 如果您偏好 untick 這，不過將需要參考 RESX 資源時所使用的命名空間更新整個應用程式。

#### <a name="doesnt-work-in-debug-mode-android-only"></a>無法在偵錯模式 (僅 Android)

如果 Android 發行組建中，但不是偵錯時使用的已翻譯的字串，以滑鼠右鍵按一下**Android 專案**選取**選項 > 建置 > Android 建置**，並確定**快速組件部署**不的話。 這個選項會導致問題的資源，並不應該使用如果您要測試當地語系化的應用程式。

### <a name="displaying-the-correct-language"></a>顯示正確的語言

到目前為止我們已經檢查過如何撰寫程式碼，以便可以提供翻譯，但不是如何實際上讓它們顯示。 Xamarin.Forms 程式碼可以利用。網路的資源載入正確的語言的翻譯，但我們需要查詢來判斷使用者選取的語言的每個平台上的作業系統。

因為某些平台專屬的程式碼才能取得使用者的語言喜好設定，使用[相依性服務](~/xamarin-forms/app-fundamentals/dependency-service/index.md)實作每個平台和公開 Xamarin.Forms 應用程式中的資訊。

首先，定義介面，以公開使用者的慣用文化特性，類似於下列程式碼：

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

接著，請使用[DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) Xamarin.Forms 中`App`類別來呼叫的介面，並將我們 RESX 資源的文化特性設定為正確的值。 請注意，我們不需要手動將此值設定通用 Windows 平台，因為資源 framework 自動在這些平台上識別選取的語言。

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

資源`Culture`必須設定應用程式第一次載入，因此會使用正確的語言字串時。 您可能會選擇性地更新可能會在 iOS 或 Android 引發，如果使用者更新其語言喜好設定，在應用程式執行的特定平台事件根據此值。

實作`ILocalize`介面如下所示[平台專屬的程式碼](#Platform-specific_Code)下一節。 這些實作利用這個`PlatformCulture`協助程式類別：

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

### <a name="platform-specific-code"></a>平台專屬的程式碼

偵測要顯示哪一個語言的程式碼必須是特定平台，因為 iOS、 Android 和 UWP 所有會稍有不同的方式公開此資訊。 程式碼`ILocalize`相依性服務會提供下列每個平台，以及其他特定平台需求，以確保當地語系化的文字正確呈現。

平台專屬的程式碼也必須處理其中作業系統可讓使用者設定不支援的地區設定識別碼的情況。網路的`CultureInfo`類別。 在這些情況下您必須撰寫自訂程式碼，以偵測到不支援的地區設定，並以最佳的取代。NET 相容的地區設定。

#### <a name="ios-application-project"></a>iOS 應用程式專案

iOS 使用者從日期和時間格式的文化特性，請以個別選取其慣用的語言。 若要載入正確的資源来當地語系化的 Xamarin.Forms 應用程式，我們只需要查詢`NSLocale.PreferredLanguages`陣列中的第一個項目。

下列實作`ILocalize`相依性服務應該放在 iOS 應用程式專案。 因為 iOS 使用底線，而不是連字號 （這是.NET 標準表示法） 程式碼會具現化之前取代底線`CultureInfo`類別：

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
                case "ms-SG":   // "Malaysian (Singapore)" not supported .NET culture
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
> `try/catch`區塊以`GetCurrentCultureInfo`方法會模仿後援行為通常會搭配地區設定的規範 – 如果完全相符找不到，查看關閉的相符項目只會根據語言 （地區設定中的字元的第一個區塊）。
>
> 透過 Xamarin.Forms，在某些地區設定是有效的 iOS 但不是會對應到有效`CultureInfo`.NET 和上方嘗試處理這個事件的程式碼中。
>
> 例如，在 iOS**設定 > 一般語言&amp;區域**螢幕可讓您設定您的電話**語言**至**英文**但**區域**至**西班牙**，而導致的地區設定字串`"en-ES"`。 當`CultureInfo`建立動作失敗，程式碼退而使用剛才前兩個字母來選取顯示語言。
>
> 開發人員應該修改`iOSToDotnetLanguage`和`ToDotnetFallbackLanguage`方法以處理特定的情況下所需的支援的語言。


有一些系統定義的使用者介面項目會自動轉譯的 Io，例如**完成**按鈕`Picker`控制項。 若要強制轉譯這些項目，我們需要用來表示我們在支援何種語言的 iOS **Info.plist**檔案。 您可以新增這些值透過**Info.plist > 來源**如下所示：

![當地語系化 Info.plist 中的索引鍵](localization-images/info-plist.png "當地語系化 Info.plist 中的索引鍵")

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

一旦您實作相依性服務，並更新**Info.plist**，iOS 應用程式將無法顯示當地語系化的文字。

> [!NOTE]
> 請注意，所預期的方式稍有不同於您的 Apple 視為葡萄牙文。
> 從[其文件](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2): _」 使用 pt 語言識別碼為葡萄牙文它用在巴西和 PT-PT 語言識別碼為葡萄牙文因為它正使用葡萄牙"_。
> 這表示當葡萄牙文語言會選擇非標準的地區設定，後援語言巴西葡萄牙文在 iOS 上，除非會撰寫程式碼來變更此行為 (例如`ToDotnetFallbackLanguage`上方)。

#### <a name="android-application-project"></a>Android 應用程式專案

Android 公開 （expose) 目前選取的地區設定，透過`Java.Util.Locale.Default`，同時也會使用底線分隔符號，而不是以破折號 （這會取代下列程式碼）。 Android 應用程式專案中加入此相依性服務實作：

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
> `try/catch`區塊以`GetCurrentCultureInfo`方法會模仿後援行為通常會搭配地區設定的規範 – 如果完全相符找不到，查看關閉的相符項目只會根據語言 （地區設定中的字元的第一個區塊）。
>
> 透過 Xamarin.Forms，在某些地區設定是有效的 Android 但不是會對應到有效`CultureInfo`.NET 和上方嘗試處理這個事件的程式碼中。
>
> 開發人員應該修改`iOSToDotnetLanguage`和`ToDotnetFallbackLanguage`方法以處理特定的情況下所需的支援的語言。


此程式碼加入至 Android 應用程式專案之後, 會自動顯示翻譯的字串。

> [!NOTE]
>️**警告：**如果 Android 發行組建中，但不是偵錯時使用的已翻譯的字串，以滑鼠右鍵按一下**Android 專案**選取**選項 > 建置 > Android建置**，並確定**快速組件部署**不的話。 這個選項會導致問題的資源，並不應該使用如果您要測試當地語系化的應用程式。

#### <a name="universal-windows-platform"></a>通用 Windows 平台

通用 Windows 平台 (UWP) 專案不需要相依性服務。 相反地，此平台會自動設定資源的文化特性正確。

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

展開 可攜式類別程式庫 (PCL) 專案中的 屬性 節點，然後按兩下**AssemblyInfo.cs**檔案。 若要設定為英文的中性資源組件語言檔案中加入下行：

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

這會通知資源管理員的應用程式的預設文化特性，因此如此可確保語言中性 RESX 檔案中定義的字串 (**AppResources.resx**) 將會顯示應用程式在執行時，一個英文地區設定。

### <a name="example"></a>範例

之後更新平台專屬專案所示上述使用翻譯的 RESX 檔案，重新編譯應用程式，都可在每個應用程式更新的翻譯。 以下是範例程式碼轉譯為簡體中文螢幕擷取畫面：

![](localization-images/simple-example-hans.png "跨平台 Ui 轉譯為中文 （簡體）")

## <a name="localizing-xaml"></a>當地語系化 XAML

當建立 Xamarin.Forms 使用者介面在 XAML 標記看起來會如下所示，使用字串直接內嵌於 XML:

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

在理想情況下，我們無法將轉譯直接在 XAML 中，我們可以透過建立執行中的使用者介面控制項*標記延伸*。 可公開 RESX 資源為 XAML 的標記延伸的程式碼如下所示。 這個類別應加入至 Xamarin.Forms 通用程式碼 （以及的 XAML 頁面）：

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

下列項目符號將說明上述程式碼中的重要元素：

* 類別的名稱為`TranslateExtension`，但是我們可以參考的慣例則為**翻譯**我們標記中。
* 此類別會實作`IMarkupExtension`，所需的 Xamarin.Forms，其工作。
* `"UsingResxLocalization.Resx.AppResources"` 是我們 RESX 資源的資源識別碼。 它包含我們的預設命名空間、 資源檔所在的資料夾和預設 RESX 檔名。
* `ResourceManager`類別建立使用`IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly)`來判斷目前的組件載入資源，並快取中靜態`ResMgr`欄位。 它會建立為`Lazy`類型，讓其建立延後，直到在第一次使用`ProvideValue`方法。
* `ci` 若要從原生作業系統取得的使用者所選擇的語言使用相依性服務。
* `GetString` 是從資源檔擷取實際的已翻譯的字串的方法。 通用 Windows 平台上`ci`將傳回 null 因為`ILocalize`不在這些平台上實作介面。 這就相當於呼叫`GetString`只含第一個參數的方法。 相反地，資源架構會自動辨識的地區設定，並從適當的 RESX 檔案擷取的已翻譯的字串。
* 錯誤處理已經以協助偵錯遺失的資源，藉由擲回例外狀況 (在`DEBUG`僅限模式)。

下列 XAML 程式碼片段示範如何使用標記延伸。 有兩個步驟，讓它運作：

1. 宣告自訂`xmlns:i18n`在根節點的命名空間。 `namespace`和`assembly`必須符合完全-在此範例中是一樣的但可能會在您的專案不同的專案設定。
2. 使用`{Binding}`屬性通常會包含呼叫的文字語法`Translate`標記延伸。 資源索引鍵是唯一的必要的參數。

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

下列更詳細的語法也是有效的標記延伸：

```xaml
<Button Text="{i18n:TranslateExtension Text=AddButton}" />
```

## <a name="localizing-platform-specific-elements"></a>當地語系化平台特定項目

雖然我們可以處理 Xamarin.Forms 程式碼中的使用者介面中的轉譯，有一些必須完成每個平台專屬專案中的項目。 本節將討論如何當地語系化：

* Application Name
* 影像

範例專案包含當地語系化的映像，稱為**flag.png**，參考的 C# 中，如下所示：

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

旗標影像也會參考在 XAML 中，像這樣：

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

所有平台，只要實作如下所述的專案結構，將會自動解除像當地語系化版本的映像，這類的影像參考。

### <a name="ios-application-project"></a>iOS 應用程式專案

iOS 會使用稱為當地語系化的專案命名標準或 **.lproj**包含影像和字串資源的目錄。 這些目錄可以包含當地語系化的版本的應用程式中使用的映像以及**InfoPlist.strings**可用來當地語系化應用程式名稱的檔案。

#### <a name="images"></a>影像

這個螢幕擷取畫面顯示 iOS 範例應用程式與語言特有 **.lproj**目錄。 西班牙文的目錄稱為**es.lproj**，包含當地語系化的版本的預設影像，以及**flag.png**:

![](localization-images/ios-resources.png "iOS 當地語系化的專案目錄")

每個語言目錄包含一份**flag.png**且該語言的當地語系化。 如果未不提供任何映像，則作業系統會預設為預設語言目錄中的映像。 如需完整的視網膜支援，您應該提供**@2x**和**@3x**每個影像的複本。

#### <a name="app-name"></a>應用程式名稱

內容**InfoPlist.strings**是只是單一索引鍵-值來設定應用程式名稱：

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

當應用程式執行時，應用程式名稱和映像會同時當地語系化：

![](localization-images/ios-imageicon.png "範例應用程式文字和影像當地語系化的 iOS")

### <a name="android-application-project"></a>Android 應用程式專案

Android 遵循不同的配置來儲存使用不同的映像當地語系化**drawable**和**字串**語言程式碼後置詞使用的目錄。 （例如 ZH-TW 或 PT-BR） 需要四個字母的地區設定程式碼時，請注意，Android 需要額外**r**遵循上述 dash/地區設定程式碼 （例如。 zh-chs 和 rTW 或 pt rBR）。

#### <a name="images"></a>影像

這個螢幕擷取畫面顯示 Android 當地語系化 drawables 和字串的一些範例：

![](localization-images/android-resources.png "Android 當地語系化 Drawables 和字串的目錄")

請注意 Android 不使用 zh-chs 和 Hans zh-chs 和 Hant 字碼簡體及繁體中文。相反地，它只支援國家/地區特定代碼 ZH-CN 和 ZH-TW。

若要支援適用之高密度的畫面不同解析度的影像，建立與其他語言資料夾`-*dpi`尾碼，例如**drawables-es-mdpi**， **drawables-es-xdpi**， **drawables-es-xxdpi**等等。請參閱[提供的替代方式 Android 資源](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources)如需詳細資訊。

#### <a name="app-name"></a>應用程式名稱

內容**strings.xml**是只是單一索引鍵-值來設定應用程式名稱：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">ResxEspañol</string>
</resources>
```

更新**Weatherapp** Android 應用程式專案中以便`Label`參考 XML 的字串。

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

應用程式現在當地語系化的應用程式名稱和映像。 以下是結果 （以西班牙文） 的螢幕擷取畫面：

![](localization-images/android-imageicon.png "Android 的範例應用程式文字和影像當地語系化")

### <a name="universal-windows-platform-application-projects"></a>通用 Windows 平台應用程式專案

通用 Windows 平台擁有資源基礎結構，可簡化將映像和應用程式名稱的當地語系化。

#### <a name="images"></a>影像

可以將它們放在特定資源的資料夾，當地語系化映像，如下列螢幕擷取畫面所示：

![](localization-images/uwp-image-folder-structure.png "UWP 映像當地語系化資料夾結構")

在執行階段的 Windows 資源基礎結構會選取適當的映像會根據使用者的地區設定。

## <a name="summary"></a>總結

Xamarin.Forms 應用程式可以使用 RESX 檔案和.NET 全球化類別來當地語系化。 除了少量的平台專屬程式碼，來偵測使用者慣用何種語言，大部分的當地語系化工作被集中在一般程式碼。

映像通常會在利用提供在 iOS 和 Android 的多重解析度支援的平台特定方式處理。 

## <a name="related-links"></a>相關連結

- [RESX 當地語系化範例](https://developer.xamarin.com/samples/xamarin-forms/UsingResxLocalization/)
- [TodoLocalized 範例應用程式](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalized/)
- [跨平台當地語系化](~/cross-platform/app-fundamentals/localization.md)
- [iOS 當地語系化](~/ios/app-fundamentals/localization/index.md)
- [Android 的當地語系化](~/android/app-fundamentals/localization.md)
- [使用 CultureInfo 類別 (MSDN)](http://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [尋找和使用資源的特定文化特性 (MSDN)](http://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
