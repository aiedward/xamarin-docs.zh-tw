---
title: Xamarin 中的字串和圖像當地語系化。
description: Xamarin.Forms 應用可以使用 .NET 資源檔進行當地語系化。
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/01/2019
ms.openlocfilehash: bf99873d88a69a715cdf7969ad94afd66372b5e3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "74135390"
---
# <a name="xamarinforms-string-and-image-localization"></a>Xamarin.表單字串和影像當地語系化

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)

當地語系化是調整應用程式以滿足目標市場的特定語言或文化要求的過程。 要完成當地語系化,可能需要將應用程式中的文本和圖像翻譯成多種語言。 本地化應用程式根據行動裝置的區域性設定自動顯示翻譯的文字:

![iOS 和 Android 上的當地語系化應用程式的螢幕截圖](text-images/localizationdemo-screenshots.png)

.NET 框架包括一個內建機制,用於使用[Resx 資源檔](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps)對應用程式進行當地語系化。 資源檔將文本和其他內容存儲為名稱/值對,允許應用程式檢索提供的密鑰的內容。 資源文件允許將當地語系化的內容與應用程式代碼分開。

使用資源檔案當地語系化 Xamarin.Forms 應用程式需要執行以下步驟:

1. 建立包含翻譯文字[的 Resx 檔案](#create-resx-files)。
1. [在分享項目中指定預設區域性](#specify-the-default-culture)。
1. [本地化 Xamarin.forms 中的文字](#localize-text-in-xamarinforms)。
1. 以每個平台的區域性設定[對影像進行當地語系化](#localize-images)。
1. [本地化每個平臺上的應用程式名稱](#localize-the-application-name)。
1. [測試](#test-localization)每個平臺上的當地語系化。

## <a name="create-resx-files"></a>建立 Resx 檔案

資源檔是 XML 檔,**其副檔名**在生成過程中編譯為二進位資源 (.resource) 檔案。 Visual Studio 2019 生成一個類,該類提供用於檢索資源的 API。 當地語系化應用程式通常包含包含應用程式中使用的所有字串的預設資源檔,以及每種受支援語言的資源檔。 範例應用程式在包含資源檔案的分享專案中有一個**Resx**資料夾,其預設資源檔案稱為**AppResources.resx**。

資源檔包含每個項目的以下資訊:

- **名稱**指定用於存取代碼中文字的鍵。
- **值**指定翻譯的文字。
- **註釋**是包含其他資訊的可選欄位。

::: zone pivot="windows"

從 Visual Studio 2019 中新增新增**新項目「** 對話框的資源檔案:

![在視覺工作室 2019 中添加新資源](text-images/pc-add-resource-file.png)

新增檔案後,可以為每個文字資源加入行:

![在 .resx 檔案中指定預設文字資源](text-images/pc-default-strings.png)

**"存取修改器**"下拉設置確定 Visual Studio 如何生成用於存取資源的類。 將「訪問修改器」設置為**公共**或**內部**,在具有指定可存取層級的生成類中生成。 將「存取修改器」設定為 **「無代碼產生**」不會生成類檔。 預設資源檔應配置為生成類檔,這將導致將**具有 .designer.cs**副檔名的檔案添加到專案中。

建立預設資源檔後,可以為應用程式支援的每個區域性創建其他檔。 每個附加資源檔都應在檔名中包括翻譯區域性,並且應將**Access 修改器**設置為 **「無代碼產生**」。 

在運行時,應用程序嘗試按特定性的順序解析資源請求。 例如,如果裝置區域性為**en-US,** 則應用程式按以下順序搜尋資源檔:

1. 應用程式資源.en-US.resx
1. 應用程式資源.en.resx
1. AppResources.resx(預設)

以下螢幕截圖顯示了**一個 AppResources.es.cs**的西班牙語翻譯檔:

![在 .resx 檔案中指定預設文字資源](text-images/pc-spanish-strings.png)

翻譯檔案使用預設檔案中指定的相同**Name**值,但在 **「值」** 欄中包含西班牙文字串。 此外,**訪問修改器**設置為 **「無代碼產生**」。

::: zone-end
::: zone pivot="macos"

從 Visual Studio 2019 中為 Mac**添加「新增新檔案**」 對話框,將新增資源檔:

![在 Visual Studio 2019 中為 Mac 添加新資源](text-images/mac-add-resource-file.png)

建立預設資源檔案後,可以透過資源檔案中`data``root`的元素中建立元素來新增文字:

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

可以透過在資源檔案選項中設定**自訂工具**屬性來建立 **.designer.cs**類別檔案:

![在資源檔案的屬性中指定的自訂工具](text-images/mac-resx-properties.png)

將**自訂工具**設置為**公共ResXFileCodeGenerator**將導致生成`public`具有存取權限的類。 將**自訂工具**設置為**內部ResXFileCodeGenerator**`internal`將導致生成具有訪問許可權的類。 空**自定義工具**值不會生成類。 生成的類名稱將與資源檔名匹配。 例如 **,AppResources.resx**檔將導致在名為**AppResources.designer.cs**的檔中`AppResources`創建類。

可以為每個受支援的區域性創建其他資源檔。 每個語言檔都應在檔案名中包含翻譯區域性,以便針對**es-MX**的檔案應命名為**AppResources.es-MX.resx**。

在運行時,應用程序嘗試按特定性的順序解析資源請求。 例如,如果裝置區域性為**en-US,** 則應用程式按以下順序搜尋資源檔:

1. 應用程式資源.en-US.resx
1. 應用程式資源.en.resx
1. AppResources.resx(預設)

語言翻譯檔案應具有與預設檔案相同的**Name**值。 以下 XML 顯示名為**AppResources.es.resx**的西班牙語翻譯檔:

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

## <a name="specify-the-default-culture"></a>指定預設區域性

要使資源檔正常工作,應用程式必須指定。 `NeutralResourcesLanguage` 在共用專案中,應自定義**AssemblyInfo.cs**檔以指定預設區域性。 以下代碼展示如何在**AssemblyInfo.cs**檔`NeutralResourcesLanguage`中設定到**en-US:**

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en-US")]
```

> [!WARNING]
> 如果不指定屬性,`NeutralResourcesLanguage``ResourceManager`類別將`null`傳回 沒有特定資源檔的任何區域性的值。 指定預設區域性時,`ResourceManager`傳回來自不支援區域性的預設 Resx 檔。 因此,建議您始終指定,`NeutralResourcesLanguage`以便為不支援的區域性顯示文本。

建立預設資源檔並在**AssemblyInfo.cs**檔案中指定的預設區域性後,應用程式可以在運行時檢索本地化字串。

關於資源檔的詳細資訊,請參閱為[.NET 應用建立資源檔](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps)。

## <a name="localize-text-in-xamarinforms"></a>當地語系化 Xamarin 中的文字。

文字在 Xamarin 中當地`AppResources`語系化。 此類是根據預設資源檔名命名的。 由於範例專案資源檔名為**AppResources.cs,Visual**Studio 將`AppResources`生成名為的匹配類。 靜態屬性在資源檔中每`AppResources`行的類中生成。 以下靜態屬性在範例應用程式的`AppResources`類別產生:

- AddButton
- 註解標籤
- 註解位置持有人

將這些值存取為[x:靜態](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md#the-xstatic-markup-extension)屬性允許在 XAML 中顯示本地化文字:

```xaml
<ContentPage ...
             xmlns:resources="clr-namespace:LocalizationDemo.Resx">
    <Label Text="{x:Static resources:AppResources.NotesLabel}" />
    <Entry Placeholder="{x:Static resources:AppResources.NotesPlaceholder}" />
    <Button Text="{x:Static resources:AppResources.AddButton}" />
</ContentPage>
```

也可以在代碼中檢索本地化文字:

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

類中`AppResources`的屬性使用`System.Globalization.CultureInfo.CurrentUICulture`的 當前值來確定要從中檢索值的區域性資源檔。

## <a name="localize-images"></a>本地化影像

除了存儲文本之外,Resx 檔案還能夠存儲不僅僅是文本,還可以存儲圖像和二進位資料。 但是,行動裝置具有一系列螢幕大小和密度,每個行動平臺都有用於顯示與密度相關的圖像的功能。 因此,應使用平臺映像當地語系化功能,而不是將映像存儲在資源檔中。

### <a name="localize-images-on-android"></a>在 Android 上本地化影像

在 Android 上,使用**參考資料**目錄中的資料夾的命名約定儲存當地化的可繪製(圖像)。 資料夾被命名為**可繪製**,後綴為目標語言。 例如,西班牙文資料夾名為 **「可繪製」。**

當需要四個字母的區域設置代碼時,Android 需要額外的**r。** 例如,墨西哥區域設定 (es-MX) 資料夾應命名為**可繪製的 es-rMX**。 每個區域設定資料夾中的影像檔名應相同:

![Android 專案中的本地化影像](text-images/pc-android-images.png)

有關詳細資訊,請參閱[Android 當地語系化](~/android/app-fundamentals/localization.md)。

### <a name="localize-images-on-ios"></a>在 iOS 上本地化影像

在 iOS 上,使用**參考資料**目錄中的資料夾的命名約定存儲當地語系化圖像。 預設資料夾名為**Base.lproj**。 特定於語言的資料夾使用語言或區域設定名稱命名,後跟 **.lproj**。 例如,西班牙文資料夾名為**es.lproj**。

四個字母的本地碼的工作方式與兩個字母的語言代碼類似。 例如,墨西哥區域設定 (es-MX) 資料夾應命名為**es-MX.lproj**。 每個區域設定資料夾中的影像檔名應相同:

![iOS 專案中的本地化影像](text-images/pc-ios-images.png)

> [!NOTE]
> iOS 支援創建當地語系化的資產目錄,而不是使用 .lproj 資料夾結構。 但是,必須在 Xcode 中創建和管理這些。

有關詳細資訊,請參閱[iOS 當地語系化](~/ios/app-fundamentals/localization/index.md)。

### <a name="localize-images-on-uwp"></a>在 UWP 上本地化影像

在 UWP 上,使用 **「資產/圖像**」目錄中的資料夾的命名約定存儲當地語系化圖像。 資料夾使用語言或區域設定命名。 例如,西班牙文資料夾名為**es,** 墨西哥區域設定資料夾應命名為**es-MX**。 每個區域設定資料夾中的影像檔名應相同:

![UWP 專案中的本地化影像](text-images/pc-uwp-images.png)

如需詳細資訊，請參閱 [UWP 當地語系化](/windows/uwp/design/globalizing/globalizing-portal/)。

### <a name="consume-localized-images"></a>使用本地端影像

由於每個平台都儲存具有唯一檔結構的影像,因此 XAML`OnPlatform``ImageSource`使用 類別 根據當前平台設定屬性:

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
> 標記`OnPlatform`擴展提供了一種更簡潔的方法來指定特定於平臺的值。 有關詳細資訊,請參閱[平台上標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

可以基於代碼中`Device.RuntimePlatform`的屬性設定影像來源:

```csharp
string imgSrc = Device.RuntimePlatform == Device.UWP ? "Assets/Images/flag.png" : "flag.png";
Image flag = new Image
{
    Source = ImageSource.FromFile(imgSrc),
    WidthRequest = 100
};
```

## <a name="localize-the-application-name"></a>本地化應用程式名稱

應用程式名稱是按平臺指定的,不使用 Resx 資源檔。 在 Android 上本地化應用程式名稱,請參閱[在 Android 上本地化應用程式名稱](~/android/app-fundamentals/localization.md#stringsxml-file-format)。 在 iOS 上本地化應用程式名稱,請參閱[在 iOS 上本地化應用程式名稱](~/ios/app-fundamentals/localization/index.md#app-name)。 要本地化 UWP 上的應用程式名稱,請參閱[UWP 套件清單中的本地化字串](https://docs.microsoft.com/windows/uwp/app-resources/localize-strings-ui-manifest)。

## <a name="test-localization"></a>測試本地化

測試當地語系化最好透過更改設備語言來實現。 可以在代碼中設置`System.Globalization.CultureInfo.CurrentUICulture`值 ,但跨平台的行為不一致,因此不建議測試。

在 iOS 上,在設置應用中,您可以專門設置每個應用的語言,而無需更改設備語言。

在 Android 上,應用程式啟動時將檢測並緩存語言設置。 如果更改語言,則可能需要退出並重新啟動應用程式以查看應用的更改。

## <a name="related-links"></a>相關連結

- [本地化範例專案](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)
- [建立 .NET 應用程式的資源檔](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps)
- [跨平台當地語系化](~/cross-platform/app-fundamentals/localization.md)
- [使用 CultureInfo 類別 (MSDN)](https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo)
- [Android 當地語系化](~/android/app-fundamentals/localization.md)
- [iOS 當地語系化](~/ios/app-fundamentals/localization/index.md)
- [UWP 當地語系化](/windows/uwp/design/globalizing/globalizing-portal/)
- [找出和使用特定文化特性資源 (MSDN)](https://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
