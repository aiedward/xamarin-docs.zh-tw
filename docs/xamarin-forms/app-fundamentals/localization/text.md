---
title: 中的字串和影像當地語系化 Xamarin.Forms
description: Xamarin.Forms 應用程式可以使用 .NET 資源檔來當地語系化。
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
ms.openlocfilehash: d68227fbf4a4c9db8f4ba8858fdeda6637ce4732
ms.sourcegitcommit: 14d67a2db82e67471584b1749e0d5b9ec0c0c09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88228598"
---
# <a name="no-locxamarinforms-string-and-image-localization"></a>Xamarin.Forms 字串和影像當地語系化

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)

當地語系化是指將應用程式調整為符合目標市場的特定語言或文化需求的流程。 若要完成當地語系化，應用程式中的文字和影像可能需要轉譯成多種語言。 當地語系化的應用程式會根據行動裝置的文化特性設定，自動顯示翻譯的文字：

![IOS 和 Android 上當地語系化應用程式的螢幕擷取畫面](text-images/localizationdemo-screenshots.png)

.NET framework 包含內建機制，可使用 [Resx 資源檔](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps)來當地語系化應用程式。 資源檔會將文字和其他內容儲存為名稱/值組，以允許應用程式抓取所提供金鑰的內容。 資源檔可讓當地語系化的內容與應用程式代碼分開。

使用資源檔來當地語系化 Xamarin.Forms 應用程式時，您需要執行下列步驟：

1. [建立](#create-resx-files) 包含翻譯文字的 Resx 檔案。
1. 指定共用專案中[的預設文化](#specify-the-default-culture)特性。
1. [當地語系化中的 Xamarin.Forms 文字](#localize-text-in-xamarinforms)。
1. 根據每個平臺的文化特性設定來[當地語系化影像](#localize-images)。
1. 將每個平臺上[的應用程式名稱當地語系化](#localize-the-application-name)。
1. [測試](#test-localization) 每個平臺上的當地語系化。

## <a name="create-resx-files"></a>建立 Resx 檔案

資源檔是具有 **.resx** 副檔名的 XML 檔案，編譯成二進位資源 ( .resources 會在建立程式期間) 檔案。 Visual Studio 2019 產生的類別會提供用來捕獲資源的 API。 當地語系化的應用程式通常會包含預設資源檔，其中含有應用程式中使用的所有字串，以及每個支援語言的資源檔。 範例應用程式在共用專案中有一個 **Resx** 資料夾，其中包含資源檔和其預設資源檔，稱為 **appresources.resx properties .resx**。

資源檔包含每個專案的下列資訊：

- **名稱** 指定用來存取程式碼中文字的索引鍵。
- **值** 指定翻譯的文字。
- [**批註**] 是包含其他資訊的選擇性欄位。

::: zone pivot="windows"

在 Visual Studio 2019 中，會使用 [新增 **專案** ] 對話方塊來新增資源檔：

![在 Visual Studio 2019 中新增資源](text-images/pc-add-resource-file.png)

新增檔案之後，就可以為每個文字資源新增資料列：

![指定 .resx 檔案中的預設文字資源](text-images/pc-default-strings.png)

[ **存取修飾** 詞] 下拉式設定會決定 Visual Studio 如何產生用來存取資源的類別。 將存取修飾詞設定為 [ **公用** ] 或 [ **內部** ]，會產生具有指定之存取範圍層級的產生類別。 將存取修飾詞設定為 [ **無程式碼產生** ] 並不會產生類別檔案。 預設資源檔應設定為產生類別檔案，這會導致檔案的副檔名為 **designer.cs** 的檔案新增至專案。

建立預設資源檔之後，即可為應用程式支援的每個文化特性建立額外的檔案。 每個額外的資源檔都應該在檔案名中包含翻譯文化特性，且 **存取修飾** 詞應設定為 **不產生程式碼**。

在執行時間，應用程式會嘗試依照明確的順序來解析資源要求。 例如，如果裝置文化特性為 **en-us** ，應用程式會依此順序尋找資源檔：

1. Appresources.resx properties. en-us .resx
1. Appresources.resx properties. en .resx
1.  (預設) 的 Appresources.resx properties .resx

下列螢幕擷取畫面顯示名為 **AppResources.es.cs**的西班牙文翻譯檔案：

![指定 .resx 檔案中的預設文字資源](text-images/pc-spanish-strings.png)

翻譯檔案會使用預設檔案中指定的相同 **名稱** 值，但在 [ **值** ] 資料行中包含西班牙文語言字串。 此外， **存取修飾** 詞設定為 **不產生程式碼**。

::: zone-end
::: zone pivot="macos"

資源檔會使用 Visual Studio 2019 for Mac 中 **的 [新增** 檔案] 對話方塊來新增：

![在 Visual Studio 2019 for Mac 中新增資源](text-images/mac-add-resource-file.png)

建立預設資源檔之後，您可以在 `data` 資源檔的專案內建立元素，藉以新增文字 `root` ：

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

您可以在資源檔選項中設定**自訂工具**屬性來建立**designer.cs**類別檔案：

![在資源檔的屬性中指定的自訂工具](text-images/mac-resx-properties.png)

將 **自訂工具** 設定為 **publicresxfilecodegenerator]** 會產生具有 `public` 存取權的類別。 將 **自訂工具** 設定為 **InternalResXFileCodeGenerator** 會產生具有 `internal` 存取權的類別。 空白的 **自訂工具** 值不會產生類別。 產生的類別名稱會符合資源檔名稱。 例如， **appresources.resx properties .resx** 檔案會導致在 `AppResources` 名為 **AppResources.designer.cs**的檔案中建立類別。

可以為每個支援的文化特性建立額外的資源檔。 每個語言檔案都應該在檔案名中包含翻譯文化特性，以便將目標為 **ES mx** 的檔案命名為 **AppResources.es**。

在執行時間，應用程式會嘗試依照明確的順序來解析資源要求。 例如，如果裝置文化特性為 **en-us** ，應用程式會依此順序尋找資源檔：

1. Appresources.resx properties. en-us .resx
1. Appresources.resx properties. en .resx
1.  (預設) 的 Appresources.resx properties .resx

語言轉譯檔案的 **名稱** 值應指定為預設檔案。 下列 XML 顯示名為 **appresources.resx properties**的西班牙文翻譯檔案：

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

若要讓資源檔正常運作，應用程式必須具有 `NeutralResourcesLanguage` 指定的。 在共用的專案中，應自訂 **AssemblyInfo.cs** 檔案以指定預設的文化特性。 下列程式碼顯示如何 `NeutralResourcesLanguage` 在**AssemblyInfo.cs**檔案中將設定為**en-us** ：

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en-US")]
```

> [!WARNING]
> 如果您未指定 `NeutralResourcesLanguage` 屬性， `ResourceManager` 類別 `null` 會傳回任何不含特定資源檔之文化特性的值。 指定預設文化特性時，會 `ResourceManager` 從預設的 Resx 檔案傳回不支援文化特性的結果。 因此，建議您一律指定， `NeutralResourcesLanguage` 以顯示不支援文化特性的文字。

建立預設資源檔並在 **AssemblyInfo.cs** 檔中指定預設文化特性之後，應用程式就可以在執行時間捕獲當地語系化的字串。

如需資源檔的詳細資訊，請參閱 [建立 .net 應用程式的資源檔](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps)。

## <a name="specify-supported-languages-on-ios"></a>在 iOS 上指定支援的語言

在 iOS 上，您必須在專案的 **plist** 檔案中宣告所有支援的語言。 在 **plist** 檔案中，使用 **來源** 視圖來設定索引鍵的陣列 `CFBundleLocalizations` ，並提供對應至 Resx 檔案的值。 此外，請確定您已透過金鑰設定預期的語言 `CFBundleDevelopmentRegion` ：

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
> Apple 會以與您預期不同的方式來對待葡萄牙文。 如需詳細資訊，請參閱在 developer.apple.com 上 [新增語言](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2) 。

如需詳細資訊，請參閱 [在 Info. plist 中指定預設和支援的語言](~/ios/app-fundamentals/localization/index.md#specifying-default-and-supported-languages-in-infoplist)。

## <a name="localize-text-in-no-locxamarinforms"></a>當地語系化中的文字 Xamarin.Forms

中的文字會 Xamarin.Forms 使用產生的 `AppResources` 類別來當地語系化。 這個類別是根據預設資源檔名稱來命名。 由於範例專案資源檔的名稱為 **AppResources.cs**，Visual Studio 會產生一個名為的相符類別 `AppResources` 。 系統會 `AppResources` 針對資源檔中的每個資料列，在類別中產生靜態屬性。 下列靜態屬性會在範例應用程式的類別中產生 `AppResources` ：

- AddButton
- NotesLabel
- NotesPlaceholder

將這些值當做 [x:Static](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md#the-xstatic-markup-extension) 屬性存取，可讓當地語系化的文字在 XAML 中顯示：

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

類別中的屬性會 `AppResources` 使用的目前值， `System.Globalization.CultureInfo.CurrentUICulture` 來決定要從哪一個文化特性資源檔中取得值。

## <a name="localize-images"></a>當地語系化影像

除了儲存文字之外，Resx 檔案還能夠儲存不只是文字，也可以儲存影像和二進位資料。 不過，行動裝置具有各種螢幕大小和密度，而每個行動平臺都有功能可顯示密度相依影像。 因此，應使用平臺映射當地語系化功能，而不是將影像儲存在資源檔中。

### <a name="localize-images-on-android"></a>在 Android 上將影像當地語系化

在 Android 上，當地語系化的可繪製資源 (影像) 會使用 **Resources** 目錄中資料夾的命名慣例來儲存。 資料夾會以目的語言的尾碼命名為可 **繪製** 。 例如，西班牙文語言資料夾名為「可 **繪製-es**」。

當需要四個字母的地區設定代碼時，Android 會在破折號之後需要額外的 **r** 。 例如， (es) 資料夾的墨西哥地區設定應該命名為可 **繪製-es-rMX**。 每個地區設定資料夾中的影像檔案名稱應該相同：

![Android 專案中的當地語系化影像](text-images/pc-android-images.png)

如需詳細資訊，請參閱 [Android 當地語系化](~/android/app-fundamentals/localization.md)。

### <a name="localize-images-on-ios"></a>在 iOS 上將影像當地語系化

在 iOS 上，當地語系化的影像會使用 **Resources** 目錄中資料夾的命名慣例來儲存。 預設資料夾的名稱是 **.lproj**。 語言特定資料夾是以語言或地區設定名稱命名，後面接著 **. .lproj**。 例如，西班牙文語言資料夾的名稱是 **.lproj**。

四個字母的本機代碼的作用就像兩個字母的語言代碼。 例如， (es) 資料夾的墨西哥地區設定應該命名為 **es-mx. .lproj**。 每個地區設定資料夾中的影像檔案名稱應該相同：

![IOS 專案中的當地語系化映射](text-images/pc-ios-images.png)

> [!NOTE]
> iOS 支援建立當地語系化的資產目錄，而不是使用 .lproj 資料夾結構。 不過，這些必須在 Xcode 中建立和管理。

如需詳細資訊，請參閱 [IOS 當地語系化](~/ios/app-fundamentals/localization/index.md)。

### <a name="localize-images-on-uwp"></a>在 UWP 上將影像當地語系化

在 UWP 上，當地語系化的影像會使用 [ **資產/影像** ] 目錄中資料夾的命名慣例來儲存。 資料夾是以語言或地區設定來命名。 例如，西班牙文語言資料夾的名稱為 **es** ，而墨西哥地區設定資料夾應命名為 **es-MX**。 每個地區設定資料夾中的影像檔案名稱應該相同：

![UWP 專案中的當地語系化影像](text-images/pc-uwp-images.png)

如需詳細資訊，請參閱 [UWP 當地語系化](/windows/uwp/design/globalizing/globalizing-portal/)。

### <a name="consume-localized-images"></a>使用當地語系化的影像

由於每個平臺都使用唯一的檔案結構來儲存影像，因此 XAML 會使用類別，根據 `OnPlatform` `ImageSource` 目前的平臺來設定屬性：

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
> `OnPlatform`標記延伸提供更精確的方法來指定平臺特定的值。 如需詳細資訊，請參閱 [OnPlatform 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

可以根據程式碼中的屬性來設定映射來源 `Device.RuntimePlatform` ：

```csharp
string imgSrc = Device.RuntimePlatform == Device.UWP ? "Assets/Images/flag.png" : "flag.png";
Image flag = new Image
{
    Source = ImageSource.FromFile(imgSrc),
    WidthRequest = 100
};
```

## <a name="localize-the-application-name"></a>將應用程式名稱當地語系化

應用程式名稱是在每個平臺上指定，並不會使用 Resx 資源檔。 若要在 Android 上將應用程式名稱當地語系化，請參閱 [在 android 上](~/android/app-fundamentals/localization.md#stringsxml-file-format)將應用程式名稱當地語系化。 若要將 iOS 上的應用程式名稱當地語系化，請參閱 [在 ios 上當地語系化應用程式名稱](~/ios/app-fundamentals/localization/index.md#app-name)。 若要將 UWP 上的應用程式名稱當地語系化，請參閱 [在 uwp 套件資訊清單中將字串當地語系化](https://docs.microsoft.com/windows/uwp/app-resources/localize-strings-ui-manifest)。

## <a name="test-localization"></a>測試當地語系化

您可以藉由變更您的裝置語言，來測試當地語系化的效果。 您可以在程式碼中設定的值， `System.Globalization.CultureInfo.CurrentUICulture` 但行為在不同平臺之間不一致，因此不建議用於測試。

在 iOS 的 [設定] 應用程式中，您可以明確設定每個應用程式的語言，而不需要變更您的裝置語言。

在 Android 上，系統會在應用程式啟動時偵測到語言設定並加以快取。 如果您變更語言，可能需要結束並重新啟動應用程式，才能看到所套用的變更。

## <a name="related-links"></a>相關連結

- [當地語系化範例專案](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)
- [建立 .NET 應用程式的資源檔](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps)
- [跨平台當地語系化](~/cross-platform/app-fundamentals/localization.md)
- [使用 CultureInfo 類別 (MSDN)](https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo)
- [Android 當地語系化](~/android/app-fundamentals/localization.md)
- [iOS 當地語系化](~/ios/app-fundamentals/localization/index.md)
- [UWP 當地語系化](/windows/uwp/design/globalizing/globalizing-portal/)
- [找出和使用特定文化特性資源 (MSDN)](https://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
