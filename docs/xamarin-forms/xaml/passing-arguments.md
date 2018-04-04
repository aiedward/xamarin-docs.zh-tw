---
title: 在 XAML 中傳遞引數
description: 本文將示範使用 XAML 屬性，可用來將引數傳遞至非預設建構函式，呼叫 factory 方法，並指定為泛型引數的類型。
ms.prod: xamarin
ms.assetid: 8F3B267F-499E-4D79-9193-FCA99F199519
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2016
ms.openlocfilehash: 8d3a7fd7534ccc5eca1432e7f4656c8d0652b126
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="passing-arguments-in-xaml"></a>在 XAML 中傳遞引數

_本文將示範使用 XAML 屬性，可用來將引數傳遞至非預設建構函式，呼叫 factory 方法，並指定為泛型引數的類型。_

## <a name="overview"></a>總覽

通常，它是為了具現化物件建構函式需要引數，或藉由呼叫靜態建立方法。 這可以在 XAML 中使用`x:Arguments`和`x:FactoryMethod`屬性：

- `x:Arguments`屬性用來指定非預設建構函式或 factory 方法物件宣告的建構函式引數。 如需詳細資訊，請參閱[傳遞建構函式引數](#constructor_arguments)。
- `x:FactoryMethod`屬性用來指定可以用來初始化物件的 factory 方法。 如需詳細資訊，請參閱[呼叫 Factory 方法](#factory_methods)。

此外，`x:TypeArguments`屬性可以用來指定泛型型別建構函式的泛型型別引數。 如需詳細資訊，請參閱[指定泛型型別引數](#generic_type_arguments)。

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>傳遞建構函式引數

引數可以傳遞至非預設建構函式使用`x:Arguments`屬性。 每個建構函式引數必須加以分隔中表示的引數類型的 XML 項目。 Xamarin.Forms 基本類型，支援下列項目：

- `x:Object`
- `x:Boolean`
- `x:Byte`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Single`
- `x:Double`
- `x:Decimal`
- `x:Char`
- `x:String`
- `x:TimeSpan`
- `x:Array`
- `x:DateTime`

下列程式碼範例示範如何使用`x:Arguments`具有三個屬性[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)建構函式：

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.9</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.25</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.75</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.8</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.2</x:Double>
        <x:Double>0.5</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

中的項目數`x:Arguments`標記，以及這些項目類型必須符合其中一個[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)建構函式。 `Color` [建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/)具有單一參數需要 1 （白色） 灰階值從 0 （黑色）。 `Color` [建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/)透過三個參數所需的範圍從 0 到 1 的紅色、 綠色和藍色值。 `Color` [建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/System.Double/)具有四個參數加入 alpha 色板做為第四個參數。

下列螢幕擷取畫面顯示每個呼叫的結果[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)建構函式具有指定的引數的值：

![](passing-arguments-images/passing-arguments.png "使用 x： 引數指定 BoxView.Color")

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>呼叫 Factory 方法

可以在 XAML 中呼叫 factory 方法，藉由指定方法的名稱並使用`x:FactoryMethod`屬性，並使用其引數`x:Arguments`屬性。 Factory 方法，是`public static`方法會傳回物件或類別或結構定義的方法，與相同類型的值。

[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)結構會定義的 factory 方法的數字及下列程式碼範例示範如何呼叫這三個它們：

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromRgba">
      <x:Arguments>
        <x:Int32>192</x:Int32>
        <x:Int32>75</x:Int32>
        <x:Int32>150</x:Int32>                      
        <x:Int32>128</x:Int32>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHsla">
      <x:Arguments>
        <x:Double>0.23</x:Double>
        <x:Double>0.42</x:Double>
        <x:Double>0.69</x:Double>
        <x:Double>0.7</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHex">
      <x:Arguments>
        <x:String>#FF048B9A</x:String>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

中的項目數`x:Arguments`標記，以及這些項目類型必須符合的 factory 方法，呼叫的引數。 [ `FromRgba` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgba/p/System.Int32/System.Int32/System.Int32/System.Int32/) Factory 方法需要四個[ `Int32` ](https://docs.microsoft.com/dotnet/api/system.int32)代表紅色、 綠色、 藍色以及 alpha 值，範圍從 0 到 255 分別的參數。 [ `FromHsla` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHsla/p/System.Double/System.Double/System.Double/System.Double/) Factory 方法需要四個[ `Double` ](https://docs.microsoft.com/dotnet/api/system.double)代表色調、 飽和度、 亮度以及 alpha 值，範圍從 0 到 1 分別的參數。 [ `FromHex` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHex/p/System.String/) Factory 方法需要[ `String` ](https://docs.microsoft.com/dotnet/api/system.string)表示十六進位 (A) RGB 色彩。

下列螢幕擷取畫面顯示每個呼叫的結果[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) factory 方法，以指定的引數的值：

![](passing-arguments-images/factory-methods.png "指定 X:factorymethod 且 X:arguments BoxView.Color")

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>指定泛型型別引數

可以使用指定的泛型型別建構函式的泛型型別引數`x:TypeArguments`屬性，如下列程式碼範例所示：

```xaml
<ContentPage ...>
  <StackLayout>
    <StackLayout.Margin>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,20,0,0" />
        <On Platform="Android" Value="5, 10" />
        <On Platform="WinPhone, Windows" Value="10" />
      </OnPlatform>
    </StackLayout.Margin>
  </StackLayout>
</ContentPage>
```

[ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/)類別是泛型類別，而且必須具現化與`x:TypeArguments`符合目標類型的屬性。 在[ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/)類別[ `Platform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Platform/)屬性可以接受單一`string`值或以逗號分隔的多個`string`值。 在此範例中， [ `StackLayout.Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)屬性設定為特定平台[ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)。

## <a name="summary"></a>總結

本文示範使用 XAML 屬性，可用來將引數傳遞至非預設建構函式，呼叫 factory 方法，並指定為泛型引數的類型。


## <a name="related-links"></a>相關連結

- [XAML 命名空間](~/xamarin-forms/xaml/namespaces.md)
- [傳遞建構函式引數 （範例）](https://developer.xamarin.com/samples/xamarin-forms/xaml/passingconstructorarguments/)
- [呼叫 Factory 方法 （範例）](https://developer.xamarin.com/samples/xamarin-forms/xaml/callingfactorymethods/)
