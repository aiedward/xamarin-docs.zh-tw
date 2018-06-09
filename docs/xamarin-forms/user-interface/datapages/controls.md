---
title: DataPages 控制項參考
description: 本文介紹 Xamarin.Forms DataPages NuGet 套件中可用的控制項。
ms.prod: xamarin
ms.assetid: 891615D0-E8BD-4ACC-A7F0-4C3725FBCC31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: c907d55f09d334e167c831a19f9d0edc4c97732f
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243125"
---
# <a name="datapages-controls-reference"></a>DataPages 控制項參考

![](~/media/shared/preview.png "這個 API 目前處於預覽狀態")

> [!IMPORTANT]
> 需要 DataPages [Xamarin.Forms 佈景主題](~/xamarin-forms/user-interface/themes/index.md)來呈現的參考。


Xamarin.Forms DataPages Nuget 包含數個可以利用資料來源繫結的控制項。

若要在 XAML 中使用這些控制項，確保已加入命名空間，例如請參閱 <<c0> `xmlns:pages` 下列宣告：

```xaml
<ContentPage
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:pages="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
    x:Class="DataPagesDemo.Detail">
```

下列範例包含`DynamicResource`會想要用於專案的資源字典中存在的參考。 另外還有如何建置範例[自訂控制項](#custom)

## <a name="built-in-controls"></a>內建控制項

* [HeroImage](#heroimage)
* [清單項目](#listitem)

<a name="heroimage" />

### <a name="heroimage"></a>HeroImage

`HeroImage`控制項有四個屬性：

* Text
* 詳細資料
* ImageSource
* 外觀

```xaml
<pages:HeroImage
    ImageSource="{ DynamicResource HeroImageImage }"
    Text="Keith Ballinger"
    Detail="Xamarin"
/>
```

**Android**

![](controls-images/heroimage-light-android.png "在 Android 上 HeroImage 控制項") ![ ] (controls-images/heroimage-dark-android.png "HeroImage 在 Android 上的控制項")

**iOS**

![](controls-images/heroimage-light-ios.png "在 iOS 上 HeroImage 控制項") ![ ] (controls-images/heroimage-dark-ios.png "HeroImage 在 iOS 上的控制項")


<a name="listitem" />

### <a name="listitem"></a>ListItem

`ListItem`控制項的版面配置是類似於原生 iOS 和 Android 的清單或資料表的資料列，不過它也可用來當作一般的檢視。 在範例中其下的程式碼所示內託管`StackLayout`，但是它可以用在資料繫結 scolling 清單控制項中。

有五個屬性：

* 標題
* 詳細資料
* ImageSource
* PlaceholdImageSource
* 外觀

```xaml
<StackLayout Spacing="0">
    <pages:ListItemControl
        Detail="Xamarin"
        ImageSource="{ DynamicResource UserImage }"
        Title="Miguel de Icaza"
        PlaceholdImageSource="{ DynamicResource IconImage }"
    />
```

這些螢幕擷取畫面顯示`ListItem`在 iOS 和 Android 平台使用淺色調和暗色調佈景主題：

**Android**

![](controls-images/listitem-light-android.png "在 Android 上的 ListItem 控制項") ![ ](controls-images/listitem-dark-android.png "在 Android 上的 ListItem 控制項")

**iOS**

![](controls-images/listitem-light-ios.png "在 iOS 上的 ListItem 控制項") ![ ](controls-images/listitem-dark-ios.png "在 iOS 上的 ListItem 控制項")


## <a name="custom-control-example"></a>自訂控制項範例

此自訂的目標`CardView`控制項是類似於原生 Android CardView。

它會包含三個屬性：

* Text
* 詳細資料
* ImageSource

目標是看起來像下列程式碼的自訂控制項 (請注意，自訂`xmlns:local`需要參考目前的組件):

```xaml
<local:CardView
  ImageSource="{ DynamicResource CardViewImage }"
  Text="CardView Text"
  Detail="CardView Detail"
/>
```

它看起來應該像下面使用對應至內建的淺色調和暗色調佈景主題色彩螢幕擷取畫面：

**Android**

![](controls-images/cardview-light-android.png "在 Android 上 CardView 自訂控制項") ![ ] (controls-images/cardview-dark-android.png "CardView 自訂控制項，在 Android 上")

**iOS**

![](controls-images/cardview-light-ios.png "在 iOS 上 CardView 自訂控制項") ![ ] (controls-images/cardview-dark-ios.png "CardView 在 iOS 上的自訂控制項")

<a name="custom" />

### <a name="building-the-custom-cardview"></a>建置自訂 CardView

1. [DataView 子類別](#1)
2. [定義字型、 配置和邊界](#2)
3. [建立控制項的子系的樣式](#3)
4. [建立控制項的版面配置範本](#4)
5. [將佈景主題特定資源新增](#5)
6. [設定的 ControlTemplate CardView 類別](#6)
7. [將控制項加入頁面](#7)

<a name="1" />

#### <a name="1-dataview-subclass"></a>1.DataView 子類別

C# 子類別的`DataView`定義控制項的可繫結屬性。

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

#### <a name="2-define-font-layout-and-margins"></a>2.定義字型、 配置和邊界

控制項的設計工具會找出這些值，做為自訂控制項的使用者介面設計的一部分。 平台專屬的規格是必要項目，其中`OnPlatform`使用項目。

請注意，有些值是指`StaticResource`s – 這些將會定義在[步驟 5](#5)。

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

#### <a name="3-create-styles-for-the-controls-children"></a>3.建立控制項的子系的樣式

參考要將使用的子系建立自訂控制項中所定義的所有項目：

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

#### <a name="4-create-the-control-layout-template"></a>4.建立控制項的版面配置範本

自訂控制項的視覺化設計已明確宣告在 [控制項] 範本，使用以上定義的資源：

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

#### <a name="5-add-the-theme-specific-resources"></a>5.將佈景主題特定資源新增

因為這是自訂控制項，請新增符合您使用的資源字典的佈景主題的資源：

##### <a name="light-theme-colors"></a>淺色佈景主題色彩

```xaml
<Color x:Key="iOSCardViewBackgroundColor">#FFFFFF</Color>
<Color x:Key="AndroidCardViewBackgroundColor">#FFFFFF</Color>

<Color x:Key="AndroidCardViewTextTextColor">#030303</Color>
<Color x:Key="iOSCardViewTextTextColor">#030303</Color>

<Color x:Key="AndroidCardViewDetailTextColor">#8F8E94</Color>
<Color x:Key="iOSCardViewDetailTextColor">#8F8E94</Color>
```

##### <a name="dark-theme-colors"></a>深色佈景主題色彩

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

#### <a name="6-set-the-controltemplate-for-the-cardview-class"></a>6.設定的 ControlTemplate CardView 類別

最後，請確定在中建立的 C# 類別[步驟 1](#1)使用控制項範本中定義[步驟 4](#4)使用`Style``Setter`項目

```xml
<Style TargetType="local:CardView">
    <Setter Property="ControlTemplate" Value="{ StaticResource CardViewControlControlTemplate }" />
  ... some custom styling omitted
  <Setter Property="BackgroundColor" Value="{ StaticResource CardViewBackgroundColor }" />
</Style>
```

<a name="7" />

#### <a name="7-add-the-control-to-a-page"></a>7.將控制項加入頁面

`CardView`控制項現在加入至頁面。 下列範例會顯示它裝載於`StackLayout`:

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
