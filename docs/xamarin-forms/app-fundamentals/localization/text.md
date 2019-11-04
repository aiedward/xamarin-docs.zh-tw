---
title: 字串及影像當地語系化
description: 您可以使用 .NET 資源檔將 Xamarin.Forms 應用程式當地語系化。
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: 96c775ba8e29597a8420b1c42d53e33357c7324c
ms.sourcegitcommit: 18b446a0032f3a7a064e5a6470e9d20b1ec2fbed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426454"
---
# <a name="localization"></a>當地語系化

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)

_您可以使用 .NET 資源檔將 Xamarin.Forms 應用程式當地語系化。_

## <a name="overview"></a>總覽

用於將 .NET 應用程式當地語系化的內建機制，使用 [RESX 檔案](https://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx)和 `System.Resources`、`System.Globalization` 命名空間中的類別。 包含已翻譯字串的 RESX 檔案內嵌在 Xamarin.Forms 組件中，外加編譯器產生的類別，此類別提供對翻譯的強型別存取。 然後即可在程式碼中擷取已翻譯文字。

### <a name="sample-code"></a>程式碼範例

本文件有兩個相關範例：

- [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization) 簡單示範所說明的概念。 以下顯示的程式碼片段全部來自於此範例。
- [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized) 是使用這些當地語系化技術的基本工作應用程式。

#### <a name="shared-projects-are-not-recommended"></a>不建議使用共用專案

TodoLocalized 範例包含[共用專案示範](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/)，但由於建置系統的限制，資源檔不會產生 **.designer.cs** 檔案，這會中斷在程式碼中存取強型別翻譯字串的能力。

本文件的其餘部分會使用 Xamarin.Forms .NET Standard 程式庫範本來與專案建立關聯。

## <a name="globalizing-xamarinforms-code"></a>全球化 Xamarin.Forms 程式碼

**全球化**應用程式是讓應用程式「可供全球使用」的程序。 這表示撰寫能夠顯示不同語言的程式碼。

全球化應用程式的其中一個主要部分，就是建立沒有「硬式編碼」文字的使用者介面。 相反地，向使用者顯示的任何內容，都應該擷取自一組已翻譯成其選擇語言的字串。

在本文件中，我們將探討如何使用 RESX 檔案來儲存這些字串，並根據使用者的喜好設定加以擷取顯示。

這些範例以英文、法文、西班牙文、德文、中文、日文、俄文和巴西葡萄牙文等語言為目標。 您可以視需要將應用程式翻譯成幾種或多種語言。

> [!NOTE]
> 在通用 Windows 平台上，應該使用 RESW 檔案進行推播通知當地語系化，而不是使用 RESX 檔案。 如需詳細資訊，請參閱 [UWP 當地語系化](/windows/uwp/design/globalizing/globalizing-portal/)。

### <a name="adding-resources"></a>新增資源

全球化 Xamarin.Forms .NET Standard 程式庫應用程式的第一個步驟是新增 RESX 資源檔，以用來儲存應用程式中所使用的所有文字。 我們需要新增一個包含預設文字的 RESX 檔案，然後針對我們想要支援的每種語言，新增其他的 RESX 檔案。

#### <a name="base-language-resource"></a>基礎語言資源

基底資源 (RESX) 檔會包含預設語言字串 (範例假設英文是預設語言)。 以滑鼠右鍵按一下專案，然後選擇 [新增] > [新增檔案...]，將檔案新增至 Xamarin.Forms 通用程式碼專案。

選擇有意義的名稱 (例如 **AppResources**)，然後按 [確定]。

[![新增資源檔](text-images/resx-new-file-sml.png "[新增檔案] 對話方塊")](text-images/resx-new-file.png#lightbox "[新增檔案] 對話方塊")

會有兩個檔案新增至專案：

- **AppResources.resx** 檔案，其中可翻譯的字串會儲存為 XML 格式。
- **AppResources.designer.cs** 檔案，這會宣告部分類別，以包含 RESX XML 檔中建立的所有項目參考。

方案樹狀目錄會將這些檔案顯示為相關。 您「應該」編輯 RESX 檔案來新增可翻譯的字串，但「不應該」編輯 **.designer.cs** 檔案。

![](text-images/appresources-tree.png "AppResources.resx File")

##### <a name="string-visibility"></a>字串可見度

根據預設，產生字串的強型別參考時，這些參考對組件是 `internal`。 這是因為 RESX 檔案的預設建置工具會產生具有 `internal` 屬性的 **.designer.cs** 檔案。

選取 **AppResources.resx** 檔案並顯示 **Properties** Pad，以查看此建置工具的設定位置。 下列螢幕擷取畫面顯示 [自訂工具：ResXFileCodeGenerator]。

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-internal-sml.png "Properties Window for AppResources.Resx")](text-images/vs-resx-internal.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Properties Pad for AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)

-----

若要將強型別字串屬性設為 `public`，您必須以手動方式將組態變更為 [自訂工具：PublicResXFileCodeGenerator]，如下列螢幕擷取畫面所示：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-public-sml.png "Properties Window for AppResources.Resx")](text-images/vs-resx-public.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Properties Pad for AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)

[![](text-images/xs-resx-public-sml.png "Properties Pad for AppResources.Resx")](text-images/xs-resx-public.png#lightbox)

-----

這是選擇性變更，只有在您想要參考跨不同組件的當地語系化字串時才需要 (例如，如果您將 RESX 檔案放在與程式碼不同的組件中)。 本主題中的範例會將字串保留為 `internal`，因為定義這些字串所在的 Xamarin.Forms .NET Standard 程式庫組件與使用這些字串所在的組件相同。

您只需要在基底 RESX 檔案上設定自訂工具 (如上所示)，而不需要在特定語言的 RESX 檔案上設定「任何」組建工具 (如下列各節所述)。

##### <a name="editing-the-resx-file"></a>編輯 RESX 檔案

不幸的是，Visual Studio for Mac 中沒有內建 RESX 編輯器。 新增可翻譯的字串時，需要針對每個字串新增 XML `data` 項目。 每個 `data` 項目可以包含下列項目：

- `name` 屬性 (必要)，這是可翻譯字串的索引鍵。 它必須是有效的 C# 屬性名稱，不得使用空格或特殊字元。
- `value` 項目 (必要)，這是應用程式中顯示的實際字串。
- `comment` 項目 (選擇性)，可包含翻譯人員的指示，說明字串的使用方式。
- `xml:space` 屬性 (選擇性)，可控制字串中保留間距的方式。

以下顯示一些範例 `data` 項目：

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

撰寫應用程式時，向使用者顯示的每個文字都應該以新 `data` 項目新增至基底 RESX 資源檔。 建議您盡可能包含許多 `comment`，以確保有高品質的翻譯。

> [!NOTE]
> Visual Studio (包括免費的 Community Edition) 隨附基本的 RESX 編輯器。 如果您具有 Windows 電腦的存取權，這可方便您在 RESX 檔案中新增和編輯字串。

#### <a name="language-specific-resources"></a>特定語言的資源

一般而言，在撰寫大部分的應用程式之前，不會實際翻譯預設文字字串 (在此情況下，預設 RESX 檔案會包含大量字串)。 在開發週期初期新增特定語言的資源，並選擇性地填入機器翻譯的文字來協助測試當地語系化程式碼，仍然是不錯的做法。

針對我們想要支援的每種語言，會新增一個額外的 RESX 檔案。
特定語言的資源檔必須遵循特定命名慣例：使用與基底資源檔相同的檔名 (例如 **AppResources**)，後面接著句號 (.)，再接著語言代碼。 簡單的範例包括：

- **AppResources.fr.resx** - 法文語言翻譯。
- **AppResources.es.resx** - 西班牙文語言翻譯。
- **AppResources.de.resx** - 德文語言翻譯。
- **AppResources.ja.resx** - 日文語言翻譯。
- **AppResources.zh-Hans.resx** - 中文 (簡體) 語言翻譯。
- **AppResources.zh-Hant.resx** - 中文 (繁體) 語言翻譯。
- **AppResources.pt.resx** - 葡萄牙文語言翻譯。
- **AppResources.pt-BR.resx** - 巴西葡萄牙文語言翻譯。

一般模式是使用兩個字母的語言代碼，但有些範例 (例如中文) 使用不同的格式，而其他範例 (例如巴西葡萄牙文) 則需要四個字元的地區設定識別碼。

這些特定語言的資源檔「不」需要 **.designer.cs** 部分類別，因此可當作一般 XML 檔新增，並設定 [建置動作：EmbeddedResource]。 下列螢幕擷取畫面顯示包含特定語言資源檔的方案：

![](text-images/appresources-langs.png "Language-Specific Resource Files")

開發應用程式並將文字新增至基底 RESX 檔案之後，您應該將它傳送給會翻譯每個 `data` 項目的翻譯人員，並傳回要包含在應用程式中的特定語言資源檔 (使用顯示的命名慣例)。 以下顯示一些「機器翻譯」的範例：

**AppResources.es.resx (西班牙文)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>Escribir un artículo</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.ja.resx (日文)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>新しい項目を追加</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.pt-BR.resx (巴西葡萄牙文)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>adicionar novo item</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

翻譯人員只需更新 `value` 項目 (`comment` 並不適合翻譯)。 請記住：如果 `value` 或 `comment` 中出現 `<`、`>` 和 `&` 等保留字元，請編輯 XML 檔案以 `&lt;`、`&gt;` 和 `&amp;` 將這些字元逸出。

<a name="incode" />

### <a name="using-resources-in-code"></a>在程式碼中使用資源

您可以透過 `AppResources` 類別，在使用者介面程式碼中使用 RESX 資源檔的字串。 指派給 RESX 檔案中每個字串的 `name` 會變成該類別的屬性，以便在 Xamarin.Forms 程式碼中參考，如下所示：

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

iOS、Android 和通用 Windows 平台 (UWP) 上的使用者介面會如您預期般呈現，但由於文字是從資源載入而非硬式編碼，因此現在可以將應用程式翻譯成多種語言。 下列螢幕擷取畫面顯示每個平台上翻譯前的 UI：

![](text-images/simple-example-english.png "Cross-Platform UIs Prior to Translation")

### <a name="troubleshooting"></a>疑難排解

#### <a name="testing-a-specific-language"></a>測試特定語言

您可能很難將模擬器或裝置切換到不同的語言，特別是在開發期間想要快速測試不同文化特性 (Culture) 時。

您可以透過設定 `Culture` 來強制載入特定語言，如下列程式碼片段所示：

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

直接在 `AppResources` 類別上設定文化特性 (Culture) 的這個方法，也可以用來實作應用程式內部的語言選取器 (而不是使用裝置的地區設定)。

#### <a name="loading-embedded-resources"></a>載入內嵌資源

若要嘗試偵錯內嵌資源 (例如 RESX 檔案) 的問題，下列程式碼片段會很有用。 請將此程式碼新增至您的應用程式 (在應用程式週期初期)，它會列出內嵌於組件中的所有資源，並顯示完整的資源識別項：

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

在 **AppResources.Designer.cs** 檔案中 (約「第 33 行」處)，已指定完整的「資源管理員名稱」(例如 `"UsingResxLocalization.Resx.AppResources"`)，如下列程式碼所示：

```csharp
System.Resources.ResourceManager temp =
        new System.Resources.ResourceManager(
                "UsingResxLocalization.Resx.AppResources",
                typeof(AppResources).GetTypeInfo().Assembly);
```

檢查偵錯程式碼結果的**應用程式輸出** (如上所示)，確認列出的資源正確 (即 `"UsingResxLocalization.Resx.AppResources"`)。

如果不正確，`AppResources` 類別將無法載入其資源。
請檢查下列項目，以解決找不到資源的問題：

- 專案的預設命名空間符合 **AppResources.Designer.cs** 檔案中的根命名空間。
- 如果 **AppResources.resx** 檔案位於子目錄中，則子目錄名稱應該是由部分命名空間「及」部分資源識別項所組成。
- **AppResources.resx** 檔案具有 [建置動作：EmbeddedResource]。
- 已核取 [專案選項] > [原始程式碼] > [.NET 命名原則] > [使用 Visual Studio 樣式資源名稱]。 您可以視需要取消核取此選項，但必須在整個應用程式內更新用來參考 RESX 資源的命名空間。

#### <a name="doesnt-work-in-debug-mode-android-only"></a>不適用於 DEBUG 模式 (僅限 Android)

如果翻譯的字串可用於您的 Android 發行組建，但無法用於偵錯，請以滑鼠右鍵按一下 [Android 專案]，然後選取 [選項] > [組建] > [Android 組建]，並確定「未」核取 [快速部署組件]。 此選項會導致載入資源時發生問題；如果您要測試當地語系化的應用程式，則請勿使用。

### <a name="displaying-the-correct-language"></a>顯示正確的語言

到目前為止，我們探討了如何撰寫程式碼以便提供翻譯，而不是如何實際顯示翻譯。 Xamarin.Forms 程式碼可以利用 .NET 資源載入正確的語言翻譯，但需要在每個平台上查詢作業系統，以判斷使用者選取哪種語言。

由於需要某些平台專用程式碼才能取得使用者的語言喜好設定，因此請使用[相依性服務](~/xamarin-forms/app-fundamentals/dependency-service/index.md)在 Xamarin.Forms 應用程式中公開該項資訊，並針對每個平台加以實作。

首先，定義介面以公開使用者的慣用文化特性 (Culture)，如下列程式碼所示：

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

其次，在 Xamarin.Forms `App` 類別中使用 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) 來呼叫介面，並將 RESX 資源文化特性 (Culture) 設定為正確的值。 請注意，我們不需要以手動方式為通用 Windows 平台設定此值，因為資源架構會自動辨識這些平台上所選取的語言。

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

當應用程式第一次載入時，必須設定資源的 `Culture`，以便使用正確的語言字串。 如果使用者在應用程式執行期間更新其語言喜好設定，您可以根據 iOS 或 Android 上可能引發的特定平台事件，選擇性地更新此值。

以下[平台專用的程式碼](#Platform-specific_Code)一節中將說明 `ILocalize` 介面的實作。 這些實作利用此 `PlatformCulture` 協助程式類別：

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

### <a name="platform-specific-code"></a>平台專用的程式碼

用來偵測要顯示哪種語言的程式碼必須是平台專用的程式碼，因為 iOS、Android 和 UWP 公開這項資訊的方式稍有不同。 以下提供每個平台的 `ILocalize` 相依性服務程式碼，以及其他平台特定需求，以確保正確呈現當地語系化的文字。

平台專用的程式碼也必須處理下列情況：作業系統允許使用者設定 .NET `CultureInfo` 類別不支援的地區設定識別碼。 在此情況下，必須撰寫自訂程式碼來偵測不受支援的地區設定，並替代為與 .NET 相容的最佳地區設定。

#### <a name="ios-application-project"></a>iOS 應用程式專案

iOS 使用者個別選取慣用語言，以及日期與時間格式文化特性 (Culture)。 若要載入正確的資源將 Xamarin.Forms 應用程式當地語系化，只需要查詢 `NSLocale.PreferredLanguages` 陣列中的第一個項目即可。

下列 `ILocalize` 相依性服務實作應放在 iOS 應用程式專案中。 由於 iOS 使用底線而非連字號 (這是 .NET 標準表示法)，因此程式碼會取代底線，再具現化 `CultureInfo` 類別：

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
            // .NET cultures don't support underscores
            string netLanguage = iOSLanguage.Replace("_", "-");

            //certain languages need to be converted to CultureInfo equivalent
            switch (iOSLanguage)
            {
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":    // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "gsw-CH":  // "Schwiizerdüütsch (Swiss German)" not supported .NET culture
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
> `GetCurrentCultureInfo` 方法中的 `try/catch` 區塊會模擬常用於地區設定規範的遞補行為：如果找不到完全相符的項目，則只會根據該語言尋找接近的符合項目 (地區設定中的第一組字元)。
>
> 在 Xamarin.Forms 案例中，某些地區設定在 iOS 中有效，但未對應至 .NET 中的有效 `CultureInfo`，因此上述程式碼會嘗試處理此案例。
>
> 例如，iOS 的 [Settings] \(設定\) > [General Language &amp; Region] \(一般語言與區域\) 畫面可讓您將手機的 [Language] \(語言\) 設定為 [English] \(英文)，但將 [Region] \(區域\) 設定為 [Spain] \(西班牙文)，這會導致地區設定字串為 `"en-ES"`。 當 `CultureInfo` 建立失敗時，程式碼會改為只使用前兩個字母來選取顯示語言。
>
> 開發人員應該修改 `iOSToDotnetLanguage` 和 `ToDotnetFallbackLanguage` 方法，以處理其支援語言所需的特定案例。

iOS 會自動翻譯一些系統定義的使用者介面項目，例如 `Picker` 控制項上的 [完成] 按鈕。 若要強制 iOS 翻譯這些項目，則必須在 **Info.plist** 檔案中指定支援哪些語言。 您可以透過 Info.plist > [來源] 新增這些值，如下所示：

![資訊中的當地語系化金鑰 plist](text-images/info-plist.png "資訊中的當地語系化金鑰 plist")

或者，在 XML 編輯器中開啟 **Info.plist** 檔案，並直接編輯下列值：

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

一旦您實作相依性服務並更新 **Info.plist** 之後，iOS 應用程式將能夠顯示當地語系化的文字。

> [!NOTE]
> 請注意，Apple 處理葡萄牙文的方式與您預期稍有不同。
> [其文件](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2)指出：「使用 pt 作為巴西所使用的葡萄牙文語言識別碼，並使用 pt-PT 作為葡萄牙所使用的葡萄牙文語言識別碼」。
> 這表示在 iOS 上，若在非標準地區設定中選擇葡萄牙文語言，則遞補語言會是巴西葡萄牙文，除非撰寫程式碼來變更此行為 (例如上述的 `ToDotnetFallbackLanguage`)。

如需 iOS 當地語系化的詳細資訊，請參閱 [iOS 當地語系化](~/ios/app-fundamentals/localization/index.md)。

#### <a name="android-application-project"></a>Android 應用程式專案

Android 會透過 `Java.Util.Locale.Default` 公開目前選取的地區設定，也會使用底線分隔符號而非連字號 (下列程式碼中會予以取代)。 將此相依性服務實作新增至 Android 應用程式專案：

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
                    // Android language not valid .NET culture, falling back to English
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
                case "gsw-CH":  // "Schwiizerdüütsch (Swiss German)" not supported .NET culture
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
> `GetCurrentCultureInfo` 方法中的 `try/catch` 區塊會模擬常用於地區設定規範的遞補行為：如果找不到完全相符的項目，則只會根據該語言尋找接近的符合項目 (地區設定中的第一組字元)。
>
> 在 Xamarin.Forms 案例中，某些地區設定在 Android 中有效，但未對應至 .NET 中的有效 `CultureInfo`，因此上述程式碼會嘗試處理此案例。
>
> 開發人員應該修改 `iOSToDotnetLanguage` 和 `ToDotnetFallbackLanguage` 方法，以處理其支援語言所需的特定案例。

一但將此程式碼新增至 Android 應用程式專案之後，就能夠自動顯示翻譯的字串。

> [!WARNING]
> 如果翻譯的字串可用於您的 Android 發行組建，但無法用於偵錯，請以滑鼠右鍵按一下 [Android 專案]，然後選取 [選項] > [組建] > [Android 組建]，並確定「未」核取 [快速部署組件]。 此選項會導致載入資源時發生問題；如果您要測試當地語系化的應用程式，則請勿使用。

如需 Android 當地語系化的詳細資訊，請參閱 [Android 當地語系化](~/android/app-fundamentals/localization.md)。

#### <a name="universal-windows-platform"></a>通用 Windows 平台

通用 Windows 平台 (UWP) 專案不需要相依性服務。 相反地，此平台會自動正確設定資源的文化特性 (Culture)。

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

在 .NET Standard 程式庫專案中展開 [屬性] 節點，然後按兩下 **AssemblyInfo.cs** 檔案。 在檔案中新增下列程式碼行，以將中性資源組件語言設定為英文：

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

這會通知資源管理員應用程式的預設文化特性 (Culture)，因此可確保當應用程式在英文地區設定中執行時，會顯示與語言無關 RESX 檔案 (**AppResources.resx**) 中定義的字串。

### <a name="example"></a>範例

更新平台特定專案 (如上所示) 並以翻譯的 RESX 檔案重新編譯應用程式之後，就可以在每個應用程式中取得更新的翻譯。 以下是翻譯成簡體中文的範例程式碼螢幕擷取畫面：

![](text-images/simple-example-hans.png "Cross-Platform UIs Translated to Simplified Chinese")

如需 UWP 當地語系化的詳細資訊，請參閱 [UWP 當地語系化](/windows/uwp/design/globalizing/globalizing-portal/)。

## <a name="localizing-xaml"></a>當地語系化 XAML

在 XAML 中建立 Xamarin.Forms 使用者介面時，標記看起來類似如下，其字串會直接內嵌於 XML：

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

在理想情況下，我們可以直接在 XAML 中翻譯使用者介面控制項，做法是透過建立「標記延伸」。 以下顯示對 XAML 公開 RESX 資源的標記延伸程式碼。 此類別應該新增至 Xamarin.Forms 通用程式碼 (以及 XAML 頁面)：

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

下列項目符號說明上述程式碼中的重要項目：

- 類別的名稱為 `TranslateExtension`，但依照慣例，我們可以在標記中以 **Translate** 來參考它。
- 類別會實作 `IMarkupExtension`，Xamarin.Forms 需要此實作才能運作。
- `"UsingResxLocalization.Resx.AppResources"` 是 RESX 資源的資源識別項。 它包含我們的預設命名空間、資源檔所在的資料夾，以及預設 RESX 檔名。
- `ResourceManager` 類別是使用 `IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly)` 所建立 (以決定要從中載入資源的目前組件)，並可在靜態 `ResMgr` 欄位中快取。 它是以 `Lazy` 類型建立，因此會延遲到第一次在 `ProvideValue` 方法中使用才建立。
- `ci` 使用相依性服務，從原生作業系統取得使用者所選擇的語言。
- `GetString` 是從資源檔擷取實際翻譯字串的方法。 在通用 Windows 平台上，由於這些平台上不會實作 `ILocalize` 介面，因此 `ci` 為 Null。 這相當於呼叫 `GetString` 方法，但只提供第一個參數。 相反地，資源架構會自動辨識地區設定，並從適當的 RESX 檔案擷取所翻譯字串。
- 其中已包含錯誤處理功能，藉由擲回例外狀況來協助偵錯遺漏的資源 (僅限 `DEBUG` 模式)。

下列 XAML 程式碼片段示範如何使用標記延伸。 有兩個執行步驟：

1. 在根節點中宣告自訂 `xmlns:i18n` 命名空間。 `namespace` 和 `assembly` 必須完全符合專案設定 (在此範例中，兩者完全相同，但在您的專案中則可能不同)。
2. 在通常包含要呼叫 `Translate` 標記延伸之文字的屬性上，使用 `{Binding}` 語法。 資源索引鍵是唯一需要的參數。

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

下列是也對標記延伸有效的更詳細語法：

```xaml
<Button Text="{i18n:TranslateExtension Text=AddButton}" />
```

## <a name="localizing-platform-specific-elements"></a>當地語系化平台特定項目

雖然我們可以在 Xamarin.Forms 程式碼中處理使用者介面的翻譯，但有些項目必須在每個平台特定的專案中完成。 本節將討論如何當地語系化：

- Application Name
- 影像

範例專案包含稱為 **flag.png** 的當地語系化影像，在 C# 中參考如下：

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

此旗標影像也會在 XAML 中參考，如下所示：

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

所有平台會將這類影像參考自動解析成影像的當地語系化版本，只要實作以下所述的專案結構。

### <a name="ios-application-project"></a>iOS 應用程式專案

iOS 使用稱為「當地語系化專案」的命名標準或 **.lproj** 目錄來包含影像和字串資源。 這些目錄可包含應用程式中所使用影像的當地語系化版本，以及可用來當地語系化應用程式名稱的 **InfoPlist.strings** 檔案。 如需 iOS 當地語系化的詳細資訊，請參閱 [iOS 當地語系化](~/ios/app-fundamentals/localization/index.md)。

#### <a name="images"></a>影像

下列螢幕擷取畫面顯示 iOS 範例應用程式及特定語言的 **.lproj** 目錄。 西班牙文目錄稱為 **es.lproj**，其中包含預設影像的當地語系化版本，以及 **flag.png**：

![](text-images/ios-resources.png "iOS Localization Project Directories")

每個語言目錄包含一份針對該語言當地語系化的 **flag.png** 複本。 如果未提供任何影像，則作業系統會預設為預設語言目錄中的影像。 如需完整的 Retina 支援，您應該針對每個影像提供 **@2x** 和 **@3x** 個複本。

#### <a name="app-name"></a>應用程式名稱

**InfoPlist.strings** 的內容就是要設定應用程式名稱的單一索引鍵/值：

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

執行應用程式時，會同時當地語系化應用程式名稱和影像：

![](text-images/ios-imageicon.png "iOS Sample App Text and Image Localization")

### <a name="android-application-project"></a>Android 應用程式專案

Android 會遵循不同的配置，使用不同的**可繪製資源**和**字串**目錄搭配語言代碼後置字元來儲存當地語系化影像。 需要四個字母的地區設定代碼時 (例如 zh-TW 或 pt-BR)，請注意 Android 要求在連字號之後/地區設定代碼之前加上 **r** (例如 zh-rTW 或 pt-rBR)。 如需 Android 當地語系化的詳細資訊，請參閱 [Android 當地語系化](~/android/app-fundamentals/localization.md)。

#### <a name="images"></a>影像

下列螢幕擷取畫面顯示 Android 範例，其中包含一些當地語系化的可繪製資源和字串：

![](text-images/android-resources.png "Android Localized Drawables and String Directories")

請注意，Android 不會使用 zh-Hans 和 zh-Hant 代碼來表示簡體中文和繁體中文；相反地，它只支援特定國碼 (地區碼) zh-CN 和 zh-TW。

若要支援高密度螢幕的不同解析度影像，請使用 `-*dpi` 尾碼建立其他語言資料夾，例如**可繪製資源-mDPI**、**可繪製資源-es-xDPI**、**可繪製資源-es-xxDPI**等。如需詳細資訊，請參閱[提供替代的 Android 資源](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources)。

#### <a name="app-name"></a>應用程式名稱

**strings.xml** 的內容就是要設定應用程式名稱的單一索引鍵/值：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">ResxEspañol</string>
</resources>
```

在 Android 應用程式專案中更新 **MainActivity.cs**，讓 `Label` 參考字串 XML。

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

應用程式現在會當地語系化應用程式名稱和影像。 結果 (西班牙文) 的螢幕擷取畫面如下：

![](text-images/android-imageicon.png "Android Sample App Text and Image Localization")

### <a name="universal-windows-platform-application-projects"></a>通用 Windows 平台應用程式專案

通用 Windows 平台擁有可簡化影像和應用程式名稱當地語系化的資源基礎結構。 如需 UWP 當地語系化的詳細資訊，請參閱 [UWP 當地語系化](/windows/uwp/design/globalizing/globalizing-portal/)。

#### <a name="images"></a>影像

將影像放在資源特定資料夾中即可當地語系化，如下列螢幕擷取畫面所示：

![](text-images/uwp-image-folder-structure.png "UWP Image Localization Folder Structure")

在執行階段，Windows 資源基礎結構會根據使用者的地區設定來選取適當的影像。

## <a name="summary"></a>總結

您可以使用 RESX 檔案和 .NET 全球化類別將 Xamarin.Forms 應用程式當地語系化。 除了用來偵測使用者慣用語言的少量平台專用程式碼之外，大部分的當地語系化工作會集中在通用程式碼。

影像的處理方式通常取決於平台，以利用 iOS 和 Android 所提供的多解析度支援。

## <a name="related-links"></a>相關連結

- [RESX 當地語系化範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)
- [TodoLocalized 範例應用程式](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalized)
- [跨平台當地語系化](~/cross-platform/app-fundamentals/localization.md)
- [iOS 當地語系化](~/ios/app-fundamentals/localization/index.md)
- [Android 當地語系化](~/android/app-fundamentals/localization.md)
- [UWP 當地語系化](/windows/uwp/design/globalizing/globalizing-portal/)
- [使用 CultureInfo 類別 (MSDN)](https://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [找出和使用特定文化特性資源 (MSDN)](https://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
