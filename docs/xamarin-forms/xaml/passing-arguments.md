---
title: 在 XAML 中傳遞引數
description: 這篇文章示範如何使用可用來將引數傳遞至非預設建構函式，呼叫 factory 方法，並指定的泛型引數類型的 XAML 屬性。
ms.prod: xamarin
ms.assetid: 8F3B267F-499E-4D79-9193-FCA99F199519
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2016
ms.openlocfilehash: 1baad2e2edfb661fff9f3ef0ccf52c9922e9f351
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051820"
---
# <a name="passing-arguments-in-xaml"></a>在 XAML 中傳遞引數

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/xaml/passingconstructorarguments/)

_這篇文章示範如何使用可用來將引數傳遞至非預設建構函式，呼叫 factory 方法，並指定的泛型引數類型的 XAML 屬性。_

## <a name="overview"></a>總覽

通常，它是為了具現化物件建構函式需要引數，或藉由呼叫靜態建立方法。 其做法在 XAML 中使用`x:Arguments`和`x:FactoryMethod`屬性：

- `x:Arguments`屬性用來指定非預設建構函式或 factory 方法物件宣告的建構函式引數。 如需詳細資訊，請參閱 <<c0> [ 傳遞建構函式引數](#constructor_arguments)。
- `x:FactoryMethod`屬性用來指定可以用來初始化物件的 factory 方法。 如需詳細資訊，請參閱 <<c0> [ 呼叫 Factory 方法](#factory_methods)。

颾魤 ㄛ`x:TypeArguments`屬性可以用來指定泛型型別的建構函式的泛型型別引數。 如需詳細資訊，請參閱 <<c0> [ 指定泛型型別引數](#generic_type_arguments)。

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>傳遞建構函式引數

引數可以傳遞至非預設建構函式，使用`x:Arguments`屬性。 每個建構函式引數必須加以分隔內 XML 項目，表示引數的型別。 Xamarin.Forms 會支援下列項目，適用於基本類型：

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

下列程式碼範例示範如何使用`x:Arguments`屬性具有三個[ `Color` ](xref:Xamarin.Forms.Color)建構函式：

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

中的項目數`x:Arguments`標籤，以及這些項目中，類型必須符合其中一個[ `Color` ](xref:Xamarin.Forms.Color)建構函式。 `Color` [建構函式](xref:Xamarin.Forms.Color.%23ctor(System.Double))具有單一參數需要 1 （白色） 灰階值從 0 （黑色）。 `Color` [建構函式](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))使用三個參數需要紅色、 綠色和藍色的值，範圍從 0 到 1。 `Color` [建構函式](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))具有四個參數將 alpha 色頻新增為第四個參數。

下列螢幕擷取畫面顯示每個呼叫的結果[ `Color` ](xref:Xamarin.Forms.Color)建構函式使用指定的引數的值：

![](passing-arguments-images/passing-arguments.png "使用 x： 引數所指定的 BoxView.Color")

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>呼叫 Factory 方法

可以在 XAML 中呼叫 factory 方法，藉由指定方法的名稱並使用`x:FactoryMethod`屬性，並使用其引數`x:Arguments`屬性。 Factory 方法`public static`方法會傳回物件或值型別的相同類別或結構，其定義的方法。

[ `Color` ](xref:Xamarin.Forms.Color)結構會定義幾個的 factory 方法，並在下列程式碼範例示範如何呼叫三個：

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

中的項目數`x:Arguments`標籤，以及這些項目中，類型必須符合要呼叫之 factory 方法的引數。 [ `FromRgba` ](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) Factory 方法需要四個[ `Int32` ](https://docs.microsoft.com/dotnet/api/system.int32)代表紅色、 綠色、 藍色和 alpha 值，從 0 到 255 範圍內分別的參數。 [ `FromHsla` ](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) Factory 方法需要四個[ `Double` ](https://docs.microsoft.com/dotnet/api/system.double)色調、 飽和度、 亮度和 alpha 值，範圍從 0 到 1 分別代表的參數。 [ `FromHex` ](xref:Xamarin.Forms.Color.FromHex(System.String)) Factory 方法需要[ `String` ](https://docs.microsoft.com/dotnet/api/system.string)表示十六進位 (A) 的 RGB 色彩。

下列螢幕擷取畫面顯示每個呼叫的結果[ `Color` ](xref:Xamarin.Forms.Color) factory 方法，使用指定的引數的值：

![](passing-arguments-images/factory-methods.png "BoxView.Color 指定 X:factorymethod，且 x： 引數")

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>指定泛型型別引數

您可以使用指定的泛型型別之建構函式的泛型類型引數`x:TypeArguments`屬性，如下列程式碼範例所示：

```xaml
<ContentPage ...>
  <StackLayout>
    <StackLayout.Margin>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,20,0,0" />
        <On Platform="Android" Value="5, 10" />
        <On Platform="UWP" Value="10" />
      </OnPlatform>
    </StackLayout.Margin>
  </StackLayout>
</ContentPage>
```

[ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1)類別是泛型類別，而且必須具現化與`x:TypeArguments`符合目標類型的屬性。 在  [ `On` ](xref:Xamarin.Forms.On)類別[ `Platform` ](xref:Xamarin.Forms.On.Platform)屬性可以接受單一`string`值或以逗號分隔的多個`string`值。 在此範例中， [ `StackLayout.Margin` ](xref:Xamarin.Forms.View.Margin)屬性設定為特定平台[ `Thickness` ](xref:Xamarin.Forms.Thickness)。

## <a name="summary"></a>總結

這篇文章示範如何使用 XAML 屬性，可用來將引數傳遞至非預設建構函式，呼叫 factory 方法，並指定之泛型引數的類型。


## <a name="related-links"></a>相關連結

- [XAML 命名空間](~/xamarin-forms/xaml/namespaces.md)
- [傳遞建構函式引數 （範例）](https://developer.xamarin.com/samples/xamarin-forms/xaml/passingconstructorarguments/)
- [呼叫 Factory 方法 （範例）](https://developer.xamarin.com/samples/xamarin-forms/xaml/callingfactorymethods/)
