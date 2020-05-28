---
title: ''
description: 本文介紹 DataPages NuGet 套件中提供的控制項 Xamarin.Forms 。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 093ef4b9b3ae7bde25da276330894bcf4e399145
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84134442"
---
# <a name="datapages-controls-reference"></a>DataPages 控制項參考

![](~/media/shared/preview.png "This API is currently in preview")

> [!IMPORTANT]
> DataPages 需要 Xamarin.Forms 呈現的主題參考。 這牽涉到安裝[ Xamarin.Forms 。主題。基底](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/)NuGet 封裝到您的專案中，後面接著[ Xamarin.Forms 。主題 Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/)或[ Xamarin.Forms 。主題. 深色](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/)NuGet 套件。

Xamarin.FormsDataPages NuGet 包含一些可以利用資料來源系結的控制項。

若要在 XAML 中使用這些控制項，請確定已包含命名空間，例如，請參閱下面的宣告 `xmlns:pages` ：

```xaml
<ContentPage
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:pages="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
    x:Class="DataPagesDemo.Detail">
```

下列範例包含 `DynamicResource` 必須存在於專案資源字典中才能正常執行的參考。 另外還有如何建立[自訂控制項](#custom)的範例

## <a name="built-in-controls"></a>內建控制項

* [HeroImage](#heroimage)
* [ListItem](#listitem)

<a name="heroimage" />

### <a name="heroimage"></a>HeroImage

`HeroImage`控制項有四個屬性：

* 文字
* 詳細資料
* ImageSource
* 層面

```xaml
<pages:HeroImage
    ImageSource="{ DynamicResource HeroImageImage }"
    Text="Keith Ballinger"
    Detail="Xamarin"
/>
```

**Android**

![](controls-images/heroimage-light-android.png "Android 上的 HeroImage 控制項") ![](controls-images/heroimage-dark-android.png "Android 上的 HeroImage 控制項")

**iOS**

![](controls-images/heroimage-light-ios.png "IOS 上的 HeroImage 控制項") ![](controls-images/heroimage-dark-ios.png "IOS 上的 HeroImage 控制項")

<a name="listitem" />

### <a name="listitem"></a>ListItem

`ListItem`控制項的版面配置類似于原生 iOS 和 Android 清單或資料表資料列，不過它也可以當做一般的視圖使用。 在下面的範例程式碼中，它會顯示裝載于內 `StackLayout` ，但也可用於資料系結的 scolling 清單控制項。

有五個屬性：

* 標題
* 詳細資料
* ImageSource
* PlaceholdImageSource
* 層面

```xaml
<StackLayout Spacing="0">
    <pages:ListItemControl
        Detail="Xamarin"
        ImageSource="{ DynamicResource UserImage }"
        Title="Miguel de Icaza"
        PlaceholdImageSource="{ DynamicResource IconImage }"
    />
```

這些螢幕擷取畫面會 `ListItem` 在 iOS 和 Android 平臺上顯示，同時使用淺色和深色主題：

**Android**

![](controls-images/listitem-light-android.png "Android 上的上個控制項") ![](controls-images/listitem-dark-android.png "Android 上的上個控制項")

**iOS**

![](controls-images/listitem-light-ios.png "IOS 上的上個控制項") ![](controls-images/listitem-dark-ios.png "IOS 上的上個控制項")

## <a name="custom-control-example"></a>自訂控制項範例

此自訂控制項的目標 `CardView` 是要與原生 Android CardView 類似。

它會包含三個屬性：

* 文字
* 詳細資料
* ImageSource

目標是自訂控制項，看起來會類似下列程式碼（請注意，需要自訂 `xmlns:local` 才能參考目前的元件）：

```xaml
<local:CardView
  ImageSource="{ DynamicResource CardViewImage }"
  Text="CardView Text"
  Detail="CardView Detail"
/>
```

它看起來應該類似以下的螢幕擷取畫面，使用與內建淺色和深色主題對應的色彩：

**Android**

![](controls-images/cardview-light-android.png "在 Android 上 CardView 自訂控制項") ![](controls-images/cardview-dark-android.png "在 Android 上 CardView 自訂控制項")

**iOS**

![](controls-images/cardview-light-ios.png "IOS 上的 CardView 自訂控制項") ![](controls-images/cardview-dark-ios.png "IOS 上的 CardView 自訂控制項")

<a name="custom" />

### <a name="building-the-custom-cardview"></a>建立自訂 CardView

1. [DataView 子類別](#1)
2. [定義字型、版面配置和邊界](#2)
3. [建立控制項子系的樣式](#3)
4. [建立控制項版面配置範本](#4)
5. [新增主題特定資源](#5)
6. [設定 CardView 類別的 ControlTemplate](#6)
7. [將控制項新增至頁面](#7)

<a name="1" />

#### <a name="1-dataview-subclass"></a>1. DataView 子類別

的 c # 子類別會 `DataView` 定義控制項的可系結屬性。

```csharp
public class CardView : DataView
{
    public static readonly BindableProperty TextProperty =
        BindableProperty.Create ("Text", typeof (string), typeof (CardView), null, BindingMode.TwoWay);

    public string Text
    {
        get { return (string)GetValue (TextProperty); }
        set { SetValue (TextProperty, value); }
    }

    public static readonly BindableProperty DetailProperty =
        BindableProperty.Create ("Detail", typeof (string), typeof (CardView), null, BindingMode.TwoWay);

    public string Detail
    {
        get { return (string)GetValue (DetailProperty); }
        set { SetValue (DetailProperty, value); }
    }

    public static readonly BindableProperty ImageSourceProperty =
        BindableProperty.Create ("ImageSource", typeof (ImageSource), typeof (CardView), null, BindingMode.TwoWay);

    public ImageSource ImageSource
    {
        get { return (ImageSource)GetValue (ImageSourceProperty); }
        set { SetValue (ImageSourceProperty, value); }
    }

    public CardView()
    {
    }
}
```

<a name="2" />

#### <a name="2-define-font-layout-and-margins"></a>2. 定義字型、版面配置和邊界

控制項設計工具會在自訂控制項的使用者介面設計中，找出這些值。 需要平臺特定規格時， `OnPlatform` 會使用元素。

請注意，有些值參考 `StaticResource` s –這些會在[步驟 5](#5)中定義。

```xml
<!-- CARDVIEW FONT SIZES -->
<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewTextFontSize">
        <On Platform="iOS, Android" Value="15" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewDetailFontSize">
        <On Platform="iOS, Android" Value="13" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewTextTextColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewTextTextColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewTextTextColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="Thickness"    x:Key="CardViewTextlMargin">
        <On Platform="iOS" Value="12,10,12,4" />
        <On Platform="Android" Value="20,0,20,5" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewDetailTextColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewDetailTextColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewDetailTextColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="Thickness"    x:Key="CardViewDetailMargin">
        <On Platform="iOS" Value="12,0,10,12" />
        <On Platform="Android" Value="20,0,20,20" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewBackgroundColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewBackgroundColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewBackgroundColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewShadowSize">
        <On Platform="iOS" Value="2" />
        <On Platform="Android" Value="5" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewCornerRadius">
        <On Platform="iOS" Value="0" />
        <On Platform="Android" Value="4" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewShadowColor">
        <On Platform="iOS, Android" Value="#CDCDD1" />
</OnPlatform>
```

<a name="3" />

#### <a name="3-create-styles-for-the-controls-children"></a>3. 建立控制項子系的樣式

參考所有定義的元素，以建立將在自訂控制項中使用的子系：

```xml
<!-- EXPLICIT STYLES (will be Classes) -->
<Style TargetType="Label" x:Key="CardViewTextStyle">
    <Setter Property="FontSize" Value="{ StaticResource CardViewTextFontSize }" />
    <Setter Property="TextColor" Value="{ StaticResource CardViewTextTextColor }" />
    <Setter Property="HorizontalOptions" Value="Start" />
    <Setter Property="Margin" Value="{ StaticResource CardViewTextlMargin }" />
    <Setter Property="HorizontalTextAlignment" Value="Start" />
</Style>

<Style TargetType="Label" x:Key="CardViewDetailStyle">
    <Setter Property="HorizontalTextAlignment" Value="Start" />
    <Setter Property="TextColor" Value="{ StaticResource CardViewDetailTextColor }" />
    <Setter Property="FontSize" Value="{ StaticResource CardViewDetailFontSize }" />
    <Setter Property="HorizontalOptions" Value="Start" />
    <Setter Property="Margin" Value="{ StaticResource CardViewDetailMargin }" />
</Style>

<Style TargetType="Image" x:Key="CardViewImageImageStyle">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="Center" />
    <Setter Property="WidthRequest" Value="220"/>
    <Setter Property="HeightRequest" Value="165"/>
</Style>
```

<a name="4" />

#### <a name="4-create-the-control-layout-template"></a>4. 建立控制項版面配置範本

自訂控制項的視覺化設計會使用上述定義的資源，在控制項範本中明確宣告：

```xml
<!--- CARDVIEW -->
<ControlTemplate x:Key="CardViewControlControlTemplate">
  <StackLayout
    Spacing="0"
    BackgroundColor="{ TemplateBinding BackgroundColor }"
  >

    <!-- CARDVIEW IMAGE -->
    <Image
      Source="{ TemplateBinding ImageSource }"
      HorizontalOptions="FillAndExpand"
      VerticalOptions="StartAndExpand"
      Aspect="AspectFill"
      Style="{ StaticResource CardViewImageImageStyle }"
    />

    <!-- CARDVIEW TEXT -->
    <Label
      Text="{ TemplateBinding Text }"
      LineBreakMode="WordWrap"
      VerticalOptions="End"
      Style="{ StaticResource CardViewTextStyle }"
    />

    <!-- CARDVIEW DETAIL -->
    <Label
      Text="{ TemplateBinding Detail }"
      LineBreakMode="WordWrap"
      VerticalOptions="End"
      Style="{ StaticResource CardViewDetailStyle }" />

  </StackLayout>

</ControlTemplate>
```

<a name="5" />

#### <a name="5-add-the-theme-specific-resources"></a>5. 新增主題特定資源

因為這是自訂控制項，所以請新增符合您使用資源字典之主題的資源：

##### <a name="light-theme-colors"></a>淺色主題色彩

```xaml
<Color x:Key="iOSCardViewBackgroundColor">#FFFFFF</Color>
<Color x:Key="AndroidCardViewBackgroundColor">#FFFFFF</Color>

<Color x:Key="AndroidCardViewTextTextColor">#030303</Color>
<Color x:Key="iOSCardViewTextTextColor">#030303</Color>

<Color x:Key="AndroidCardViewDetailTextColor">#8F8E94</Color>
<Color x:Key="iOSCardViewDetailTextColor">#8F8E94</Color>
```

##### <a name="dark-theme-colors"></a>深色主題色彩

```xaml
<!-- CARD VIEW COLORS -->
            <Color x:Key="iOSCardViewBackgroundColor">#404040</Color>
            <Color x:Key="AndroidCardViewBackgroundColor">#404040</Color>

            <Color x:Key="AndroidCardViewTextTextColor">#FFFFFF</Color>
            <Color x:Key="iOSCardViewTextTextColor">#FFFFFF</Color>

            <Color x:Key="AndroidCardViewDetailTextColor">#B5B4B9</Color>
            <Color x:Key="iOSCardViewDetailTextColor">#B5B4B9</Color>
```

<a name="6" />

#### <a name="6-set-the-controltemplate-for-the-cardview-class"></a>6. 設定 CardView 類別的 ControlTemplate

最後，請確定在[步驟 1](#1)中建立的 c # 類別使用在[步驟 4](#4)中使用 `Style` `Setter` 元素定義的控制項範本

```xml
<Style TargetType="local:CardView">
    <Setter Property="ControlTemplate" Value="{ StaticResource CardViewControlControlTemplate }" />
  ... some custom styling omitted
  <Setter Property="BackgroundColor" Value="{ StaticResource CardViewBackgroundColor }" />
</Style>
```

<a name="7" />

#### <a name="7-add-the-control-to-a-page"></a>7. 將控制項加入至頁面

`CardView`控制項現在可以加入至頁面。 下列範例顯示它裝載于中 `StackLayout` ：

```xaml
<StackLayout Spacing="0">
  <local:CardView
    Margin="12,6"
    ImageSource="{ DynamicResource CardViewImage }"
    Text="CardView Text"
    Detail="CardView Detail"
  />
</StackLayout>

```
