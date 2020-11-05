---
title: 中的字串和影像當地語系化 Xamarin.Forms
description: Xamarin.Forms 您可以使用 .NET 資源檔來當地語系化應用程式。
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/01/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cb811972d622ffdcf6042e030528a88fb18abaf7
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369801"
---
# <a name="no-locxamarinforms-string-and-image-localization"></a>Xamarin.Forms 字串和影像當地語系化

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/usingresxlocalization)

當地語系化是調整應用程式以符合目標市場特定語言或文化需求的流程。 若要完成當地語系化，應用程式中的文字和影像可能需要轉譯成多種語言。 當地語系化的應用程式會根據行動裝置的文化特性設定，自動顯示翻譯的文字：

![IOS 和 Android 上當地語系化應用程式的螢幕擷取畫面](text-images/localizationdemo-screenshots.png)

.NET framework 包含內建機制，可使用 [Resx 資源檔](/dotnet/framework/resources/creating-resource-files-for-desktop-apps)來當地語系化應用程式。 資源檔會將文字和其他內容儲存為名稱/值組，以允許應用程式取得所提供金鑰的內容。 資源檔可讓當地語系化的內容與應用程式程式碼分開。

使用資源檔將 Xamarin.Forms 應用程式當地語系化，需要執行下列步驟：

1. [建立](#create-resx-files) 包含翻譯文字的 Resx 檔案。
1. 指定共用專案中[的預設文化](#specify-the-default-culture)特性。
1. 將[中的 Xamarin.Forms 文字當地語系化](#localize-text-in-xamarinforms)。
1. 根據每個平臺的文化特性設定來[當地語系化影像](#localize-images)。
1. 將每個平臺上[的應用程式名稱當地語系化](#localize-the-application-name)。
1. 在每個平臺上[測試當地語系化](#test-localization)。

## <a name="create-resx-files"></a>建立 Resx 檔案

資源檔是副檔名為 **.resx** 的 XML 檔案，會在建立程式期間) 檔案編譯成二進位資源 (。 Visual Studio 2019 產生的類別會提供用來取出資源的 API。 當地語系化的應用程式通常會包含預設資源檔，其中包含應用程式中使用的所有字串，以及每種支援語言的資源檔。 範例應用程式在共用專案中有一個 **Resx** 資料夾，其中包含資源檔，以及其預設資源檔，稱為 **>appresources.resx .resx** 。

資源檔包含每個專案的下列資訊：

- **名稱** ：指定用來存取程式碼中文字的索引鍵。
- **值** 指定翻譯的文字。
- **批註** 是包含其他資訊的選擇性欄位。

::: zone pivot="windows"

資源檔會使用 Visual Studio 2019 中的 [ **加入新專案** ] 對話方塊來新增：

![在 Visual Studio 2019 中新增資源](text-images/pc-add-resource-file.png)

一旦加入檔案，就可以針對每個文字資源加入資料列：

![指定 .resx 檔中的預設文字資源](text-images/pc-default-strings.png)

[ **存取修飾** 詞] 下拉式設定會決定 Visual Studio 如何產生用來存取資源的類別。 使用指定的存取範圍層級，將存取修飾詞設定為 **公用** 或 **內部** 結果產生的類別。 將存取修飾詞設定為不產生程式 **代碼** 不會產生類別檔案。 預設資源檔應設定為產生類別檔案，以將副檔名為 **designer.cs** 的檔案新增至專案。

一旦建立預設資源檔，就可以為應用程式支援的每個文化特性建立額外的檔案。 每個額外的資源檔都應該在檔案名中包含翻譯文化特性，且 **存取修飾** 詞必須設定為 **不產生程式碼** 。

在執行時間，應用程式會嘗試以明確的順序來解析資源要求。 例如，如果裝置文化特性是 **en-us** ，則應用程式會依下列順序尋找資源檔：

1. >appresources.resx-en-us .resx
1. >appresources.resx
1. >appresources.resx (預設) 的 .resx

下列螢幕擷取畫面顯示名為 **AppResources.es.cs** 的西班牙文轉譯檔案：

![指定 .resx 檔中的預設西班牙文文字資源](text-images/pc-spanish-strings.png)

轉譯檔案使用預設檔案中指定的相同 **名稱** 值，但在 [ **值** ] 資料行中包含西班牙文語言字串。 此外， **存取修飾** 詞設定為 **不產生程式碼** 。

::: zone-end
::: zone pivot="macos"

資源檔會使用 Visual Studio 2019 for Mac 中 **的 [新增** 檔案] 對話方塊來新增：

![在適用于 Mac 的 Visual Studio 2019 中新增資源](text-images/mac-add-resource-file.png)

建立預設資源檔之後，您可以在 `data` 資源檔中的專案內建立元素，藉以新增文字 `root` ：

```xml
<?xml version="1.0" encoding="utf-8"?>
<root>
    ...
    <data name="AddButton" xml:space="preserve">
        <value>Add Note</value>
    </data>
    <data name="NotesLabel" xml:space="preserve">
        <value>Notes:</value>
    </data>
    <data name="NotesPlaceholder" xml:space="preserve">
        <value>e.g. Get Milk</value>
    </data>
</root>
```

您可以在資源檔選項中設定 **自訂工具** 屬性，藉以建立 **designer.cs** 類別檔案：

![資源檔案屬性中指定的自訂工具](text-images/mac-resx-properties.png)

將 **自訂工具** 設定為 **PublicResXFileCodeGenerator** 會導致產生的類別具有 `public` 存取權。 將 **自訂工具** 設定為 **InternalResXFileCodeGenerator** 會導致產生的類別具有 `internal` 存取權。 空白的 **自訂工具** 值將不會產生類別。 產生的類別名稱會與資源檔名稱相符。 例如， **>appresources.resx .resx** 檔案將導致在名為 AppResources.designer.cs 的檔案中建立 `AppResources` 類別。 **AppResources.designer.cs**

您可以為每個支援的文化特性建立額外的資源檔。 每個語言檔案都應該在檔案名中包含翻譯文化特性，以便將目標為 **es-mx** 的檔案命名為 **AppResources.es-mx .resx** 。

在執行時間，應用程式會嘗試以明確的順序來解析資源要求。 例如，如果裝置文化特性是 **en-us** ，則應用程式會依下列順序尋找資源檔：

1. >appresources.resx-en-us .resx
1. >appresources.resx
1. >appresources.resx (預設) 的 .resx

語言轉譯檔案的 **名稱** 值必須指定為預設檔案。 下列 XML 會顯示名為 **>appresources.resx** 的西班牙文轉譯檔案：

```xml
<?xml version="1.0" encoding="utf-8"?>
<root>
    ...
    <data name="NotesLabel" xml:space="preserve">
        <value>Notas:</value>
    </data>
    <data name="NotesPlaceholder" xml:space="preserve">
        <value>por ejemplo . comprar leche</value>
    </data>
    <data name="AddButton" xml:space="preserve">
        <value>Agregar nuevo elemento</value>
    </data>
</root>
```

::: zone-end

## <a name="specify-the-default-culture"></a>指定預設文化特性

若要讓資源檔正常運作，應用程式必須具有 `NeutralResourcesLanguage` 指定的。 在共用專案中，應自訂 **AssemblyInfo.cs** 檔以指定預設文化特性。 下列程式碼顯示如何 `NeutralResourcesLanguage` 在 **AssemblyInfo.cs** 檔中將設定為 **en-us** ：

```csharp
using System.Resources;

// The resources from the neutral language .resx file are stored directly
// within the library assembly. For that reason, changing en-US to a different
// language in this line will not by itself change the language shown in the
// app. See the discussion of UltimateResourceFallbackLocation in the
// documentation for additional information:
// https://docs.microsoft.com/dotnet/api/system.resources.neutralresourceslanguageattribute
[assembly: NeutralResourcesLanguage("en-US")]
```

> [!WARNING]
> 如果您未指定 `NeutralResourcesLanguage` 屬性， `ResourceManager` 類別 `null` 會傳回任何不含特定資源檔的文化特性值。 當指定預設文化特性時，會傳回 `ResourceManager` 預設 Resx 檔的結果，以取得不支援的文化特性。 因此，建議您一律指定， `NeutralResourcesLanguage` 讓文字顯示為不支援的文化特性。

一旦建立預設資源檔並在 **AssemblyInfo.cs** 檔中指定預設文化特性之後，應用程式就可以在執行時間取出當地語系化的字串。

如需資源檔的詳細資訊，請參閱 [建立 .net 應用程式的資源檔](/dotnet/framework/resources/creating-resource-files-for-desktop-apps)。

## <a name="specify-supported-languages-on-ios"></a>在 iOS 上指定支援的語言

在 iOS 上，您必須在專案的 **plist** 檔案中宣告所有支援的語言。 在 **plist** 檔案中，使用 **來源** 視圖來設定索引鍵的陣列 `CFBundleLocalizations` ，並提供對應至 Resx 檔案的值。 此外，請確定您已透過機碼設定預期的語言 `CFBundleDevelopmentRegion` ：

![Plist 編輯器的螢幕擷取畫面，其中顯示當地語系化區段](text-images/info-plist.png)

或者，在 XML 編輯器中開啟 **plist** 檔案，並新增下列內容：

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

> [!NOTE]
> Apple 會將葡萄牙文視為與您預期的方式稍有不同。 如需詳細資訊，請參閱在 developer.apple.com 上 [新增語言](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2) 。

如需詳細資訊，請參閱 [在 Info. plist 中指定預設和支援的語言](~/ios/app-fundamentals/localization/index.md#specifying-default-and-supported-languages-in-infoplist)。

## <a name="localize-text-in-no-locxamarinforms"></a>當地語系化中的文字 Xamarin.Forms

Xamarin.Forms使用產生的類別來當地語系化文字 `AppResources` 。 這個類別是根據預設的資源檔名稱來命名。 由於範例專案資源檔的名稱為 **AppResources.cs** ，Visual Studio 會產生一個名為的對應類別 `AppResources` 。 資源檔中的每個資料列都會在類別中產生靜態屬性 `AppResources` 。 下列靜態屬性會在範例應用程式的類別中產生 `AppResources` ：

- AddButton
- NotesLabel
- NotesPlaceholder

將這些值作為 [x:Static](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md#the-xstatic-markup-extension) 屬性存取，可讓您在 XAML 中顯示當地語系化的文字：

```xaml
<ContentPage ...
             xmlns:resources="clr-namespace:LocalizationDemo.Resx">
    <Label Text="{x:Static resources:AppResources.NotesLabel}" />
    <Entry Placeholder="{x:Static resources:AppResources.NotesPlaceholder}" />
    <Button Text="{x:Static resources:AppResources.AddButton}" />
</ContentPage>
```

您也可以在程式碼中取出當地語系化的文字：

```csharp
public LocalizedCodePage()
{
    Label notesLabel = new Label
    {
        Text = AppResources.NotesLabel,
        // ...
    };

    Entry notesEntry = new Entry
    {
        Placeholder = AppResources.NotesPlaceholder,
        //...
    };

    Button addButton = new Button
    {
        Text = AppResources.AddButton,
        // ...
    };

    Content = new StackLayout
    {
        Children = {
            notesLabel,
            notesEntry,
            addButton
        }
    };
}
```

類別中的屬性會 `AppResources` 使用的目前值 `System.Globalization.CultureInfo.CurrentUICulture` 來判斷要從中取出值的文化特性資源檔。

## <a name="localize-images"></a>將影像當地語系化

除了儲存文字之外，Resx 檔還能儲存不只是文字，也可以儲存影像和二進位資料。 不過，行動裝置具有各種螢幕大小和密度，而且每個行動平臺都有可顯示密度相關影像的功能。 因此，您應該使用平臺映射當地語系化功能，而不是將影像儲存在資源檔中。

### <a name="localize-images-on-android"></a>將 Android 上的影像當地語系化

在 Android 上，會使用 **Resources** 目錄中的資料夾命名慣例儲存) 的當地語系化可繪製資源 (映射。 資料夾會以目的語言的尾碼命名為可 **繪製** 。 例如，西班牙文語言資料夾的名稱為「可 **繪製的-es** 」。

當需要四個字母的地區設定程式碼時，Android 會在虛線之後需要額外的 **r** 。 例如，墨西哥地區設定 (es-MX) 資料夾應命名為 **rMX** 。 每個地區設定資料夾中的影像檔案名稱應該相同：

![Android 專案中的當地語系化影像](text-images/pc-android-images.png)

如需詳細資訊，請參閱 [Android 當地語系化](~/android/app-fundamentals/localization.md)。

### <a name="localize-images-on-ios"></a>在 iOS 上將影像當地語系化

在 iOS 上，會使用 **Resources** 目錄中的資料夾命名慣例來儲存當地語系化的影像。 預設資料夾的名稱是 **>.lproj** 。 特定語言的資料夾會使用語言或地區設定名稱來命名，後面接著 **. >.lproj** 。 例如，西班牙文語言資料夾的名稱是 **es. >.lproj** 。

四個字母的本機代碼的運作方式就像兩個字母的語言代碼。 例如，墨西哥地區設定 (es-MX) 資料夾應命名為 **es-mx. >.lproj** 。 每個地區設定資料夾中的影像檔案名稱應該相同：

![IOS 專案中的當地語系化影像](text-images/pc-ios-images.png)

> [!NOTE]
> iOS 支援建立當地語系化的資產目錄，而不是使用 >.lproj 資料夾結構。 但是，這些必須在 Xcode 中建立和管理。

如需詳細資訊，請參閱 [IOS 當地語系化](~/ios/app-fundamentals/localization/index.md)。

### <a name="localize-images-on-uwp"></a>在 UWP 上當地語系化影像

在 UWP 上，會使用 **資產/影像** 目錄中的資料夾命名慣例來儲存當地語系化的影像。 資料夾的命名方式為語言或地區設定。 例如，西班牙文語言資料夾的名稱是 **es** ，且墨西哥 locale 資料夾應命名為 **es-MX** 。 每個地區設定資料夾中的影像檔案名稱應該相同：

![UWP 專案中的當地語系化影像](text-images/pc-uwp-images.png)

如需詳細資訊，請參閱 [UWP 當地語系化](/windows/uwp/design/globalizing/globalizing-portal/)。

### <a name="consume-localized-images"></a>使用當地語系化的影像

因為每個平臺都會儲存具有唯一檔案結構的影像，所以 XAML 會使用 `OnPlatform` 類別， `ImageSource` 根據目前的平臺來設定屬性：

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

> [!NOTE]
> 標記延伸可讓您 `OnPlatform` 更精確地指定平臺特定的值。 如需詳細資訊，請參閱 [OnPlatform 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

您可以根據程式碼中的屬性來設定影像來源 `Device.RuntimePlatform` ：

```csharp
string imgSrc = Device.RuntimePlatform == Device.UWP ? "Assets/Images/flag.png" : "flag.png";
Image flag = new Image
{
    Source = ImageSource.FromFile(imgSrc),
    WidthRequest = 100
};
```

## <a name="localize-the-application-name"></a>將應用程式名稱當地語系化

應用程式名稱是在每個平臺上指定，且不會使用 Resx 資源檔。 若要將 Android 上的應用程式名稱當地語系化，請參閱 [在 android 上](~/android/app-fundamentals/localization.md#stringsxml-file-format)將應用程式名稱當地語系化。 若要將 iOS 上的應用程式名稱當地語系化，請參閱將 [ios 上的應用程式名稱當地語系化](~/ios/app-fundamentals/localization/index.md#app-name)。 若要在 UWP 上將應用程式名稱當地語系化，請參閱 [uwp 套件資訊清單中的當地語系化字串](/windows/uwp/app-resources/localize-strings-ui-manifest)。

## <a name="test-localization"></a>測試當地語系化

您可以藉由變更您的裝置語言來完成當地語系化的測試。 您可以在程式碼中設定的值， `System.Globalization.CultureInfo.CurrentUICulture` 但在不同平臺上的行為不一致，因此不建議用於測試。

在 iOS 上，您可以在 [設定] 應用程式中，明確地設定每個應用程式的語言，而不需要變更您的裝置語言。

在 Android 上，當應用程式啟動時，會偵測並快取語言設定。 如果您變更語言，可能需要結束並重新啟動應用程式，才能看到已套用的變更。

## <a name="related-links"></a>相關連結

- [當地語系化範例專案](/samples/xamarin/xamarin-forms-samples/usingresxlocalization)
- [建立 .NET 應用程式的資源檔](/dotnet/framework/resources/creating-resource-files-for-desktop-apps)
- [跨平台當地語系化](~/cross-platform/app-fundamentals/localization.md)
- [使用 CultureInfo 類別 (MSDN)](/dotnet/api/system.globalization.cultureinfo)
- [Android 當地語系化](~/android/app-fundamentals/localization.md)
- [iOS 當地語系化](~/ios/app-fundamentals/localization/index.md)
- [UWP 當地語系化](/windows/uwp/design/globalizing/globalizing-portal/)
- [找出和使用特定文化特性資源 (MSDN)](/previous-versions/visualstudio/visual-studio-2008/s9ckwb4b(v=vs.90))