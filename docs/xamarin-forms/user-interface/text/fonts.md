---
title: Xamarin 中的字型。
description: 本文介紹如何指定在 Xamarin.Forms 應用程式中顯示文本的控制項上的字體資訊。
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2020
ms.openlocfilehash: 4f14d66e5321a0fb19078be4d97ae4df69f688c9
ms.sourcegitcommit: 6f3281a32017cfcebadde8a2d6e10651a277828f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80587439"
---
# <a name="fonts-in-xamarinforms"></a>Xamarin 中的字型。

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)

本文介紹了 Xamarin.Forms 如何允許您在顯示文本的控制項上指定字型屬性(包括粗細和大小)。 字型資訊可以在[代碼中指定](#Setting_Font_in_Code)或[XAML 中指定](#Setting_Font_in_Xaml)。 使用[自訂字型](#Using_a_Custom_Font)與[顯示字型圖示](#display-font-icons)。

<a name="Setting_Font_in_Code" />

## <a name="set-the-font-in-code"></a>在代碼中設定字型

使用顯示文字的任何控制項的三個與字型相關的屬性:

- **字型系列**&ndash;字`string`型 名稱。
- **字型將**&ndash;字型大小調整`double`為 。
- **Fontattributes 一**&ndash;個字串,指定樣式資訊(如*斜體*和**粗體**(使用`FontAttributes`C# 中的枚 舉)。

此程式碼展示如何建立分頁並指定要顯示的字型大小和粗細:

```csharp
var about = new Label
{
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

<a name="FontSize" />

### <a name="font-size"></a>字型大小

屬性`FontSize`可以設定為雙精度值,例如:

```csharp
label.FontSize = 24;
```

大小值以與設備無關的單位度量。 有關詳細資訊,請參閱[度量單位](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement)。

Xamarin.Forms 還定義枚舉[`NamedSize`](xref:Xamarin.Forms.NamedSize)中 表示特定字體大小的欄位。 關於命名字體大小的詳細資訊,請參考[的命名稱大小](#named-font-sizes)。

<a name="FontAttributes" />

### <a name="font-attributes"></a>字型屬性

可以在`FontAttributes`屬性上設置粗**體**和*斜體*等字體樣式。 目前支援以下值:

- **None**
- **大膽**
- **斜**

Entle`FontAttribute``OR`

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>設定每個平台的字型資訊

或者,`Device.RuntimePlatform`該屬性可用於在每個平台上設定不同的字型名稱,如以下代碼所示:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "MarkerFelt-Thin" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

iOS 的字體資訊來源[iosfonts.com。](http://iosfonts.com)

<a name="Setting_Font_in_Xaml" />

## <a name="set-the-font-in-xaml"></a>在 XAML 中設定字型

顯示文字的 Xamarin.Forms 控制`FontSize`項都 具有可在 XAML 中設定的屬性。 在 XAML 中設定字型的最簡單方法是使用命名的大小枚舉值,如以下範例所示:

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

屬性有一個內置轉換器`FontSize`,允許在 XAML 中將所有字體設置表示為字串值。 此外,`FontAttributes`該屬性可用於指定字型屬性:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

該[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-specific-values)屬性還可用於 XAML 中,以在每個平臺上呈現不同的字體。 下面的範例在每個平臺上使用不同的字型:

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="MarkerFelt-Thin" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

## <a name="named-font-sizes"></a>具名字型大小

Xamarin.Forms 在枚舉[`NamedSize`](xref:Xamarin.Forms.NamedSize)中 定義表示特定字體大小的欄位。 下表顯示`NamedSize`iOS、Android 和通用 Windows 平臺 (UWP) 上的成員及其預設大小:

| member | iOS | Android | UWP |
| --- | --- | --- | --- |
| `Default` | 16 | 14 | 14 |
| `Micro` | 11 | 10 | 15.667 |
| `Small` | 13 | 14 | 18.667 |
| `Medium` | 16 | 17 | 22.667 |
| `Large` | 20 | 22 | 32 |
| `Body` | 17 | 16 | 14 |
| `Header` | 17 | 96 | 46 |
| `Title` | 28 | 24 | 24 |
| `Subtitle` | 22 | 16 | 20 |
| `Caption` | 12 | 12 | 12 |

大小值以與設備無關的單位度量。 有關詳細資訊,請參閱[度量單位](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement)。

命名字體大小可以通過 XAML 和代碼進行設置。 此外,`Device.GetNamedSize`還可以呼叫 方法以傳回表示`double`命名字體 大小的 。

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> 在 iOS 和 Android 上,命名字體大小將根據作業系統輔助功能選項自動縮放。 此行為可以在 iOS 上使用特定於平臺的 iOS 禁用。 有關詳細資訊,請參閱[iOS 上命名字體大小的輔助功能縮放](~/xamarin-forms/platform/ios/named-font-size-scaling.md)。

<a name="Using_a_Custom_Font" />

## <a name="use-a-custom-font"></a>使用自訂字型

使用內建字體以外的字體需要一些特定於平臺的編碼。 此螢幕截圖顯示了使用 Xamarin.Forms 呈現[的 Google 開源字型中的自訂字型](https://www.google.com/fonts)**龍蝦**。

 [![iOS 和 Android 上的自訂字型](fonts-images/custom-sml.png "自訂字型範例")](fonts-images/custom.png#lightbox "自訂字型範例")

下面概述了每個平臺所需的步驟。 將自訂字型檔與應用程式一起包含時,請確保驗證該字體的許可證是否允許分發。

### <a name="ios"></a>iOS

可以通過首先確保載入自訂字型,然後使用 Xamarin.Forms`Font`方法按名稱引用自訂字體來顯示它。
依[此部落格文章](https://devblogs.microsoft.com/xamarin/custom-fonts-in-ios/)的說明:

1. 使用產生操作新增字型檔案 **:捆綁資源**,以及
2. 更新**Info.plist**檔案(**應用程式的字型**或`UIAppFonts`, 鍵), 然後
3. 在 Xamarin 中定義字型時,請按名稱引用它。

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" : null // set only for iOS
}
```

### <a name="android"></a>Android

適用於 Android 的 Xamarin.Forms 可以引用已添加到專案的自訂字體,該自訂字體遵循特定的命名標準。 首先將字型檔案加入應用程式項目**中的資產**資料夾,並設定*產生操作:AndroidAsset*。 然後使用完整路徑與*字型名稱*分隔以哈希 (#) 作為 Xamarin.Forms 中的字型名稱,如下代碼段所示:

```csharp
new Label
{
  Text = "Hello, Forms!",
  FontFamily = Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : null // set only for Android
}
```

### <a name="windows"></a>Windows

適用於 Windows 平臺的 Xamarin.窗體可以引用已添加到專案的自訂字型,該自訂字體遵循特定的命名標準。 首先將字型檔案加入到應用程式項目中的 **/Assets/Fonts/** 資料夾,然後設定**產生操作:內容**。 然後使用完整路徑和字型檔名,後跟哈希 (#) 和**字型名稱**,如下代碼段所示:

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.UWP ? "Assets/Fonts/Lobster-Regular.ttf#Lobster" : null // set only for UWP apps
}
```

> [!NOTE]
> 請注意,字體檔名和字體名稱可能不同。 要在 Windows 上發現字型名稱,請右鍵單擊 .ttf 檔並選擇 **「預覽**」。。 然後可以從預覽視窗中確定字體名稱。

### <a name="xaml"></a>XAML

您還可以使用[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads)XAML 來呈現自訂字型:

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="Lobster-Regular" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

## <a name="use-a-custom-font-preview"></a>使用自訂字型 (預覽)

自定義字體可以添加到 Xamarin.Forms 共用專案中,並由平臺專案使用,而無需任何其他工作。 完成此動作的程序如下所示：

1. 將字型添加到 Xamarin.Forms 共用專案中作為嵌入式資源(**生成操作:嵌入式資源**)。
1. 使用`ExportFont`屬性在**AssemblyInfo.cs**等檔中向程式集註冊字體檔。 也可以指定可選別名。

> [!NOTE]
> 嵌入字體需要使用 Xamarin.窗體 4.5.0.530 或更高版本。

下面的範例顯示了在程式集中註冊的龍蝦常規字體以及別名:

```csharp
using Xamarin.Forms;

[assembly: ExportFont("Lobster-Regular.ttf", Alias = "Lobster")]
```

> [!NOTE]
> 字體可以駐留在共用專案中的任何資料夾中,而無需在向程式集註冊字體時指定資料夾名稱。

然後,透過引用其名稱,無需檔副檔名,可以在每個平臺上使用字型:

```xaml
<!-- Use font name -->
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster-Regular" />
```

或者,可以通過引用其別名在每個平臺上使用它:

```xaml
<!-- Use font alias -->
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster" />
```

對等的 C# 程式碼為：

```csharp
// Use font name
Label label1 = new Label
{
    Text = "Hello Xamarin.Forms!",
    FontFamily = "Lobster-Regular"
};

// Use font alias
Label label2 = new Label
{
    Text = "Hello Xamarin.Forms!",
    FontFamily = "Lobster"
};
```

以下螢幕截圖顯示自訂字型:

[![iOS 和 Android 上的自訂字型](fonts-images/custom-sml.png "自訂字型範例")](fonts-images/custom.png#lightbox "自訂字型範例")

## <a name="display-font-icons"></a>顯示字型圖示

Xamarin.Forms 應用程式可以`FontImageSource`通過指定 物件中的字體圖示數據來顯示字體圖示。 此類派生自類[`ImageSource`](xref:Xamarin.Forms.ImageSource),具有以下屬性:

- `Glyph`• 字型圖示的單碼字元值,指定`string`為 。
- `Size`•`double`指示呈現字體圖示的大小(以設備無關單位)的值。 預設值是 30。 此外,此屬性可以設置為命名字體大小。
- `FontFamily`•`string`表示字體圖示所屬的字體系列。
- `Color`•[`Color`](xref:Xamarin.Forms.Color)顯示 字體圖示時要使用的可選值。

此資料用於建立 PNG,這個 PNG 可以顯示的任何檢視顯示`ImageSource`。 此方法允許字體圖示(如表情符號)由多個檢視顯示,而不是將字體圖示顯示限制為單個文本呈現視圖(如[`Label`](xref:Xamarin.Forms.Label))。

> [!IMPORTANT]
> 字體圖示目前只能由其單碼字元表示形式指定。

以下 XAML 範例有檢視顯示的[`Image`](xref:Xamarin.Forms.Image)字型圖示:

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

此代碼在[`Image`](xref:Xamarin.Forms.Image)檢視中顯示來自 Ionicon 字體系列的 XBox 圖示。 請注意,雖然此圖示的單碼字元是`\uf30c`,但它必須在 XAML 中轉義,`&#xf30c;`因此變為 。 對等的 C# 程式碼為：

```csharp
Image image = new Image { BackgroundColor = Color.FromHex("#D1D1D1") };
image.Source = new FontImageSource
{
    Glyph = "\uf30c",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Ionicons" : "ionicons.ttf#",
    Size = 44
};
```

以下螢幕擷取(來自[可綁定佈局範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts))顯示可綁定佈局顯示的幾個字型圖示:

![在 iOS 和 Android 上顯示的字型圖示的螢幕截圖](fonts-images/font-image-source.png "影像檢視中顯示的字型圖示")

## <a name="related-links"></a>相關連結

- [字型範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)
- [文字(範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [可結合佈局 (例如範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [可繫結的版面配置](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
