---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f314f3145b3573184cb8cdf7370394c975c66859
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84127071"
---
# <a name="passing-arguments-in-xaml"></a>在 XAML 中傳遞引數

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)

_本文示範如何使用 XAML 屬性，這些屬性可用來將引數傳遞至非預設的函式、呼叫 factory 方法，以及指定泛型引數的類型。_

## <a name="overview"></a>概觀

通常必須使用需要引數的函式，或藉由呼叫靜態建立方法來具現化物件。 您可以使用和屬性，在 XAML 中達成這個目的 `x:Arguments` `x:FactoryMethod` ：

- `x:Arguments`屬性是用來指定非預設的函式或 factory 方法物件宣告的函數引數。 如需詳細資訊，請參閱傳遞函式[引數](#constructor_arguments)。
- `x:FactoryMethod`屬性是用來指定可用來初始化物件的 factory 方法。 如需詳細資訊，請參閱[呼叫 Factory 方法](#factory_methods)。

此外， `x:TypeArguments` 屬性可以用來指定泛型型別之函式的泛型型別引數。 如需詳細資訊，請參閱[指定泛型型別引數](#generic_type_arguments)。

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>傳遞函數引數

您可以使用屬性，將引數傳遞至非預設的函式 `x:Arguments` 。 每個函式引數都必須在代表引數類型的 XML 元素中分隔。 Xamarin.Forms支援基本類型的下列元素：

- `x:Array`
- `x:Boolean`
- `x:Byte`
- `x:Char`
- `x:DateTime`
- `x:Decimal`
- `x:Double`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Object`
- `x:Single`
- `x:String`
- `x:TimeSpan`

下列程式碼範例將示範如何使用 `x:Arguments` 具有三個構造函式的屬性 [`Color`](xref:Xamarin.Forms.Color) ：

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

標記內的元素數目 `x:Arguments` 和這些元素的類型，必須符合其中一個函式 [`Color`](xref:Xamarin.Forms.Color) 。 `Color`具有單一參數[的函](xref:Xamarin.Forms.Color.%23ctor(System.Double))式需要從0（黑色）到1（白色）的灰階值。 `Color` [constructor](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))具有三個參數的處理函式需要介於0到1的紅色、綠色和藍色值。 `Color`具有四個參數[的函](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))式會將 Alpha 通道加入為第四個參數。

下列螢幕擷取畫面顯示 [`Color`](xref:Xamarin.Forms.Color) 使用指定的引數值呼叫每個函數的結果：

![BoxView 使用 x:Arguments 指定的色彩](passing-arguments-images/passing-arguments.png)

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>呼叫 Factory 方法

可以在 XAML 中呼叫 Factory 方法，方法是使用屬性來指定方法的名稱 `x:FactoryMethod` ，以及使用屬性（attribute）的引數 `x:Arguments` 。 Factory 方法是一種 `public static` 方法，它會傳回與定義方法的類別或結構相同類型的物件或值。

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

標記內的元素數目 `x:Arguments` 和這些元素的類型，必須符合所呼叫 factory 方法的引數。 [`FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32))Factory 方法需要四個 [`Int32`](https://docs.microsoft.com/dotnet/api/system.int32) 參數，分別代表紅色、綠色、藍色和 Alpha 值，範圍介於0到255之間。 [`FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))Factory 方法需要四個 [`Double`](https://docs.microsoft.com/dotnet/api/system.double) 參數，分別表示色調、飽和度、亮度和 Alpha 值，範圍介於0到1之間。 [`FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String))Factory 方法需要 [`String`](https://docs.microsoft.com/dotnet/api/system.string) 代表十六進位（a） RGB 色彩的。

下列螢幕擷取畫面顯示 [`Color`](xref:Xamarin.Forms.Color) 使用指定的引數值呼叫每個 factory 方法的結果：

![BoxView 使用 x:FactoryMethod 和 x:Arguments 指定的色彩](passing-arguments-images/factory-methods.png)

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>指定泛型型別引數

您可以使用屬性來指定泛型型別之函式的泛型型別引數 `x:TypeArguments` ，如下列程式碼範例所示：

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

[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)類別是泛型類別，而且必須使用 `x:TypeArguments` 符合目標型別的屬性具現化。 在 [`On`](xref:Xamarin.Forms.On) 類別中， [`Platform`](xref:Xamarin.Forms.On.Platform) 屬性可以接受單一 `string` 值或多個逗號分隔 `string` 值。 在此範例中， [`StackLayout.Margin`](xref:Xamarin.Forms.View.Margin) 屬性會設定為平臺特定 [`Thickness`](xref:Xamarin.Forms.Thickness) 。

如需泛型型別引數的詳細資訊，請參閱[ Xamarin.Forms XAML 中的泛型](generics.md)。

## <a name="related-links"></a>相關連結

- [傳遞函數引數（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)
- [呼叫 Factory 方法（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-callingfactorymethods)
- [XAML 命名空間](~/xamarin-forms/xaml/namespaces.md)
- [XAML 中的泛型 Xamarin.Forms](generics.md)
