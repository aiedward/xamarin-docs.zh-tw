---
title: 在 XAML 中傳遞引數
description: 本文示範如何使用 XAML 屬性，這些屬性可用來將引數傳遞至非預設的函式、呼叫 factory 方法，以及指定泛型引數的類型。
ms.prod: xamarin
ms.assetid: 8F3B267F-499E-4D79-9193-FCA99F199519
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2016
ms.openlocfilehash: 80f332e45d6c46ad49543923e85cbb2eceadb378
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "78911336"
---
# <a name="passing-arguments-in-xaml"></a>在 XAML 中傳遞引數

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)

_本文示範如何使用 XAML 屬性，這些屬性可用來將引數傳遞至非預設的函式、呼叫 factory 方法，以及指定泛型引數的類型。_

## <a name="overview"></a>概觀

通常必須使用需要引數的函式，或藉由呼叫靜態建立方法來具現化物件。 這可以在 XAML 中使用 `x:Arguments` 和 `x:FactoryMethod` 屬性來達成：

- `x:Arguments` 屬性是用來指定非預設的函式或 factory 方法物件宣告的函數引數。 如需詳細資訊，請參閱傳遞函式[引數](#constructor_arguments)。
- `x:FactoryMethod` 屬性是用來指定可以用來初始化物件的 factory 方法。 如需詳細資訊，請參閱[呼叫 Factory 方法](#factory_methods)。

此外，`x:TypeArguments` 屬性可以用來指定泛型型別之函式的泛型型別引數。 如需詳細資訊，請參閱[指定泛型型別引數](#generic_type_arguments)。

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>傳遞函數引數

您可以使用 `x:Arguments` 屬性，將引數傳遞至非預設的函式。 每個函式引數都必須在代表引數類型的 XML 元素中分隔。 Xamarin 支援下列基本類型的元素：

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

下列程式碼範例將示範如何使用具有三個[`Color`](xref:Xamarin.Forms.Color)的 `x:Arguments` 屬性（attribute）：

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

`x:Arguments` 標記內的元素數目，以及這些元素的類型，必須符合其中一個[`Color`](xref:Xamarin.Forms.Color)的函式。 [具有單一參數的 `Color`](xref:Xamarin.Forms.Color.%23ctor(System.Double))的處理函式需要從0（黑色）到1（白色）的灰階值。 具有三[個參數的 `Color` 處理](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))函式需要介於0到1之間的紅色、綠色和藍色值。 具有四[個參數的 `Color` 處理](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))程式會將 Alpha 色板加入為第四個參數。

下列螢幕擷取畫面顯示使用指定的引數值呼叫每個[`Color`](xref:Xamarin.Forms.Color)的函式的結果：

![BoxView 使用 x:Arguments 指定的色彩](passing-arguments-images/passing-arguments.png)

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>呼叫 Factory 方法

可以在 XAML 中呼叫 Factory 方法，方法是使用 `x:FactoryMethod` 屬性來指定方法的名稱，以及使用 `x:Arguments` 屬性的引數。 Factory 方法是一種 `public static` 方法，會傳回與定義方法的類別或結構相同類型的物件或值。

[`Color`](xref:Xamarin.Forms.Color)結構會定義一些 factory 方法，下列程式碼範例將示範如何呼叫其中三個：

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

`x:Arguments` 標記內的元素數目，以及這些元素的類型，必須符合所呼叫 factory 方法的引數。 [`FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) factory 方法需要四個[`Int32`](https://docs.microsoft.com/dotnet/api/system.int32)參數，分別代表紅色、綠色、藍色和 Alpha 值，範圍介於0到255之間。 [`FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) factory 方法需要四個[`Double`](https://docs.microsoft.com/dotnet/api/system.double)參數，分別表示色調、飽和度、亮度和 Alpha 值，範圍介於0到1之間。 [`FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) factory 方法需要代表十六進位（a） RGB 色彩的[`String`](https://docs.microsoft.com/dotnet/api/system.string) 。

下列螢幕擷取畫面顯示使用指定的引數值呼叫每個[`Color`](xref:Xamarin.Forms.Color) factory 方法的結果：

![BoxView 使用 x:FactoryMethod 和 x:Arguments 指定的色彩](passing-arguments-images/factory-methods.png)

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>指定泛型型別引數

您可以使用 `x:TypeArguments` 屬性來指定泛型型別之函式的泛型型別引數，如下列程式碼範例所示：

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

[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)類別是泛型類別，而且必須使用符合目標型別的 `x:TypeArguments` 屬性具現化。 在[`On`](xref:Xamarin.Forms.On)類別中， [`Platform`](xref:Xamarin.Forms.On.Platform)屬性可以接受單一 `string` 值，或以逗號分隔的多個 `string` 值。 在此範例中， [`StackLayout.Margin`](xref:Xamarin.Forms.View.Margin)屬性會設定為平臺特定的[`Thickness`](xref:Xamarin.Forms.Thickness)。

## <a name="summary"></a>摘要

本文示範了如何使用 XAML 屬性，將引數傳遞至非預設的函式、呼叫 factory 方法，以及指定泛型引數的類型。

## <a name="related-links"></a>相關連結

- [XAML 命名空間](~/xamarin-forms/xaml/namespaces.md)
- [傳遞函數引數（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)
- [呼叫 Factory 方法（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-callingfactorymethods)
