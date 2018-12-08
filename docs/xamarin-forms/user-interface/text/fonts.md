---
title: 在 Xamarin.Forms 中的字型
description: 這篇文章說明如何在 Xamarin.Forms 應用程式中顯示文字的控制項上，指定字型資訊。
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 1b90a3184b89ba9147525a87b52e048bbb59f5af
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061143"
---
# <a name="fonts-in-xamarinforms"></a>在 Xamarin.Forms 中的字型

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFonts/)

這篇文章描述 Xamarin.Forms 如何讓您指定的字型屬性 （包括權數和大小） 上顯示文字的控制項。 字型資訊可能[程式碼中指定](#Setting_Font_in_Code)或是[XAML 中指定](#Setting_Font_in_Xaml)。
您也可使用[自訂字型](#Using_a_Custom_Font)。

<a name="Setting_Font_in_Code" />

## <a name="setting-font-in-code"></a>程式碼中設定的字型

使用顯示文字的任何控制項的三個與字型相關屬性：

- **FontFamily** &ndash; `string`字型名稱。
- **FontSize** &ndash;字型大小為`double`。
- **FontAttributes** &ndash;字串，指定樣式的資訊，例如*斜體*並**粗體**(使用`FontAttributes`列舉型別在 C# 中)。

此程式碼示範如何建立標籤和指定的字型大小和加權來顯示：

```csharp
var about = new Label {
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

<a name="FontSize" />

### <a name="font-size"></a>字型大小

`FontSize`屬性可以設定為雙精度浮點數值，例如：

```csharp
label.FontSize = 24;
```

您也可以使用`NamedSize`列舉，有四個內建的選項;Xamarin.Forms 會選擇每個平台的最佳大小。

-  **Micro**
-  **小型**
-  **媒體**
-  **大型**

`NamedSize`列舉型別可以是使用的地方`FontSize`您可以使用指定`Device.GetNamedSize`方法，將值轉換成`double`:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

<a name="FontAttributes" />

### <a name="font-attributes"></a>字型屬性

這類設定字型的樣式**粗體**並*斜體*上，可以設定`FontAttributes`屬性。 目前支援下列值：

-  **無**
-  **粗體**
-  **斜體**

`FontAttribute`列舉型別可用，如下所示 (您可以指定單一屬性或`OR`接合):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="setting-font-info-per-platform"></a>設定每個平台的字型資訊

或者，`Device.RuntimePlatform`屬性可以用來設定每個平台上的不同的字型名稱，此程式碼所示：

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/Lobster-Regular.ttf#Lobster",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

適用於 iOS 的字型資訊的不錯的來源是[iosfonts.com](http://iosfonts.com)。

<a name="Setting_Font_in_Xaml" />

## <a name="setting-the-font-in-xaml"></a>在 XAML 中設定的字型

Xamarin.Forms 會控制所有具有該顯示文字`Font`可以在 XAML 中設定的屬性。 將字型設定在 XAML 中的最簡單方式是使用具名的大小的列舉值，在此範例中所示：

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

沒有內建的轉換器`Font`屬性，可讓所有的字型設定，以表示在 XAML 中的字串值。 下列範例會示範如何指定的字型屬性和大小在 XAML 中：

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

若要指定多個`Font`設定，結合成單一的必要的設定`Font`屬性字串。 字型屬性字串的格式應為`"[font-face],[attributes],[size]"`。 參數的順序很重要，所有參數都是選擇性的與多重`attributes`可以指定，例如：

```xaml
<Label Text="Small bold text" Font="Bold, Micro" />
<Label Text="Medium custom font" Font="MarkerFelt-Thin, 42" />
<Label Text="Really big bold and italic text" Font="Bold, Italic, 72"  />
```

[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-values) 也可在 XAML 中呈現每個平台上不同的字型。 下列範例會在 iOS 上使用自訂的字型 (<span style="font-family:MarkerFelt-Thin">MarkerFelt 精簡</span>) 和其他平台上，指定只有大小/屬性：

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="MarkerFelt-Thin" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

指定自訂的字型字樣，它一律是個不錯的主意，若要使用`OnPlatform`，很難找到可用於所有平台的字型。

<a name="Using_a_Custom_Font" />

## <a name="using-a-custom-font"></a>使用自訂的字型

使用非內建的字樣的字型不需要某些平台特定程式碼。 此螢幕擷取畫面顯示自訂字型**Lobster**從[Google 的開放原始碼字型](https://www.google.com/fonts)使用 Xamarin.Forms 來呈現。

 [![IOS 和 Android 上的自訂字型](fonts-images/custom-sml.png "自訂字型範例")](fonts-images/custom.png#lightbox "自訂字型範例")

每個平台所需的步驟說明如下。 包含自訂字型檔案與應用程式時，請務必確認字型的授權可允許發佈。

### <a name="ios"></a>iOS

您可先確保已將它載入，然後使用 Xamarin.Forms 來依據名稱參考它，藉此顯示自訂字型`Font`方法。
請依照下列中的指示[此部落格文章](http://blog.xamarin.com/custom-fonts-in-ios/):

1. 加入字型檔案**建置動作： BundleResource**，及
2. 更新**Info.plist**檔案 (**應用程式所提供的字型**，或`UIAppFonts`、 索引鍵)，然後
3. 它依名稱參考任何您定義在 Xamarin.Forms 中的字型位置 ！

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" : null // set only for iOS
}
```

### <a name="android"></a>Android

適用於 Android 的 Xamarin.Forms 可以參考自訂字型已加入至專案遵循特定的命名標準。 首先將字型檔案，以新增**資產**資料夾中的應用程式專案和組*建置動作： AndroidAsset*。 然後使用完整路徑和*字型名稱*隔開雜湊 （#） 做為在 Xamarin.Forms 中，字型名稱，如下列程式碼片段所示：

```csharp
new Label
{
  Text = "Hello, Forms!",
  FontFamily = Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : null // set only for Android
}
```

### <a name="windows"></a>Windows

適用於 Windows 平台 Xamarin.Forms 可以參考自訂字型已加入至專案遵循特定的命名標準。 首先將字型檔案，以新增 **/Assets 字型美金**資料夾中的應用程式專案和組<span class="UIItem">建置動作： 內容</span>。 然後使用完整路徑和字型的檔案名稱，後面接著雜湊 （#） 和<span class="UIItem">字型名稱</span>，如下列程式碼片段所示：

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.UWP ? "Assets/Fonts/Lobster-Regular.ttf#Lobster" : null // set only for UWP apps
}
```

> [!NOTE]
> 請注意，字型檔名和字型名稱可能不同。 若要探索在 Windows 上的字型名稱，以滑鼠右鍵按一下.ttf 檔案，然後選取**預覽**。 然後您可以從 [預覽] 視窗判斷的字型名稱。

現已完成應用程式的通用程式碼。 平台專屬的電話撥號員程式碼現在會當作 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) 實作。

### <a name="xaml"></a>XAML

您也可以使用[ `Device.RuntimePlatform` ](~/xamarin-forms/platform/device.md#providing-platform-values)來呈現自訂字型的 XAML 中：

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

<a name="Summary" />

## <a name="summary"></a>總結

Xamarin.Forms 提供簡單的預設設定，以讓您輕鬆地為所有支援的平台的文字的大小。 它也可讓您指定的字體和大小&ndash;甚至是以不同的方式為每個平台&ndash;何時需要更細微的控制。

字型資訊也可以指定在 XAML 使用格式正確的字型屬性。

## <a name="related-links"></a>相關連結

- [FontsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFonts/)
- [文字 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/)
