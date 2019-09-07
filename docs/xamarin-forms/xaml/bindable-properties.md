---
title: 可繫結屬性
description: 這篇文章介紹可繫結的屬性，並示範如何建立和使用它們。
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
ms.openlocfilehash: 50efd6b37d70fa835436c28c73b3d4f9fc6c7c83
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758101"
---
# <a name="bindable-properties"></a>可繫結屬性

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

_在 Xamarin.Forms 中，通用語言執行平台 (CLR) 屬性的功能會擴充可繫結的屬性。可繫結的屬性是屬性的特殊類型，其中 Xamarin.Forms 屬性系統會追蹤屬性的值。這篇文章介紹可繫結的屬性，並示範如何建立和使用它們。_

## <a name="overview"></a>總覽

可繫結的屬性延伸 CLR 屬性功能所支援的屬性[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)型別，而非備份含有欄位的屬性。 可繫結屬性的目的是要提供屬性系統，可支援資料繫結、 樣式、 範本，並透過父子式關聯性設定的值。 此外，可繫結的屬性可以提供預設值，驗證的屬性值和回呼，以監視屬性變更。

屬性應該實作為可繫結的屬性，以支援一或多個下列功能：

- 做為有效*目標*進行資料繫結的屬性。
- 透過將屬性設定[樣式](~/xamarin-forms/user-interface/styles/index.md)。
- 提供的預設值為屬性的型別不同的預設屬性值。
- 正在驗證屬性的值。
- 監視屬性變更。

Xamarin.Forms 可繫結屬性的範例包括[ `Label.Text` ](xref:Xamarin.Forms.Label.Text)， [ `Button.BorderRadius` ](xref:Xamarin.Forms.Button.BorderRadius)，以及[ `StackLayout.Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation)。 每個可繫結的屬性都有對應`public static readonly`型別的屬性[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)相同類別上公開，且可繫結屬性的識別項。 例如，對應可繫結的屬性識別項`Label.Text`屬性是[ `Label.TextProperty` ](xref:Xamarin.Forms.Label.TextProperty)。

<a name="consuming-bindable-property" />

## <a name="creating-and-consuming-a-bindable-property"></a>建立及使用可繫結的屬性

建立可繫結屬性的程序如下所示：

1. 建立[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)執行個體，其中[ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create*)方法多載。
1. 定義屬性的存取子[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)執行個體。

請注意，所有[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)必須在 UI 執行緒上建立執行個體。 這表示只有在 UI 執行緒執行的程式碼可以取得或設定可繫結屬性的值。 不過，`BindableProperty`執行個體可以由封送處理至 UI 執行緒存取來自其他執行緒[ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action))方法。

### <a name="creating-a-property"></a>建立屬性

若要建立`BindableProperty`執行個體，包含的類別必須衍生自[ `BindableObject` ](xref:Xamarin.Forms.BindableObject)類別。 不過，`BindableObject`類別是在類別階層中，高，因此大部分的類別用於使用者介面功能支援可繫結屬性。

您可以建立可繫結的屬性宣告`public static readonly`型別的屬性[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)。 可繫結的屬性應該設定為傳回的值，其中[ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法多載。 宣告應該是主體內[ `BindableObject` ](xref:Xamarin.Forms.BindableObject)衍生類別，但是不在任何成員定義。

至少必須指定識別碼時建立[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)，以及下列參數：

- 名稱[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)。
- 屬性的類型。
- 主控物件的型別。
- 屬性的預設值。 這可確保未設定，且它可以是不同的屬性類型的預設值時，屬性永遠傳回特定的預設值。 預設值將會是還原時[ `ClearValue` ](xref:Xamarin.Forms.BindableObject.ClearValue(Xamarin.Forms.BindableProperty))可繫結的屬性上呼叫方法。

下列程式碼示範可繫結的屬性，識別項和四個必要參數的值：

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

這會建立[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)具名執行個體`EventName`，型別的`string`。 屬性由所擁有`EventToCommandBehavior`類別，並具有預設值是`null`。 可繫結屬性的命名慣例是可繫結屬性的識別項必須符合在指定的屬性名稱`Create`方法，並有"Property"附加到它。 因此，在上述範例中，可繫結屬性的識別項是`EventNameProperty`。

（選擇性） 建立時[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)執行個體，這個參數可以指定：

- 繫結模式。 這用來指定屬性值變更會傳播所在的方向。 在預設的繫結模式，變更會傳播從*來源*要*目標*。
- 可設定屬性值時將會叫用的驗證委派。 如需詳細資訊，請參閱 <<c0> [ 驗證回撥](#validation)。
- 屬性已變更屬性值變更時將會叫用的委派。 如需詳細資訊，請參閱 <<c0> [ 偵測屬性變更](#propertychanges)。
- 屬性，變更屬性值將會變更時將會叫用的委派。 這個委派會具有相同的簽章已變更屬性的委派。
- 強制轉型值的委派，會叫用屬性值變更時。 如需詳細資訊，請參閱 <<c0> [ 強制轉型值回撥](#coerce)。
- A `Func` ，用來初始化預設屬性值。 如需詳細資訊，請參閱 <<c0> [ 建立預設值使用 Func](#defaultfunc)。

### <a name="creating-accessors"></a>建立存取子

屬性存取子，才能使用屬性語法來存取可繫結的屬性。 `Get`存取子應會傳回包含值，對應的可繫結屬性中。 這可藉由呼叫[ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty))方法中，傳入可繫結的屬性識別項，以取得值，然後再轉換為要求的類型結果。 `Set`存取子都應該設定相對應的可繫結屬性的值。 這可藉由呼叫[ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))方法並傳入要設定值，並要設定的值可繫結的屬性識別項。

下列程式碼範例顯示存取子`EventName`可繫結屬性：

```csharp
public string EventName {
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

### <a name="consuming-a-bindable-property"></a>使用可繫結的屬性

一旦建立可繫結的屬性，便可以從 XAML 或程式碼。 XAML，在這之後，即可與指出 CLR 命名空間名稱，並選擇性地組件名稱的命名空間宣告中宣告具有前置詞的命名空間。 如需詳細資訊，請參閱 < [XAML 命名空間](~/xamarin-forms/xaml/namespaces.md)。

下列程式碼範例示範包含可繫結的屬性，這在相同的組件所參考的自訂類型的應用程式程式碼內定義的自訂類型的 XAML 命名空間：

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

設定時，會使用命名空間宣告`EventName`可繫結的屬性，如下列 XAML 程式碼範例所示：

```xaml
<ListView ...>
  <ListView.Behaviors>
    <local:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

對等的 C# 程式碼會顯示在以下程式碼範例中：

```csharp
var listView = new ListView ();
listView.Behaviors.Add (new EventToCommandBehavior {
  EventName = "ItemSelected",
  ...
});
```

<a name="advanced" />

## <a name="advanced-scenarios"></a>進階的案例

建立時[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)執行個體，有幾個可以實現進階的可繫結屬性中設定的選擇性參數。 本節會探討這些案例。

<a name="propertychanges" />

### <a name="detecting-property-changes"></a>偵測屬性變更

A`static`屬性變更的回呼方法可以註冊具有可繫結的屬性，藉由指定`propertyChanged`參數[ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法。 可繫結屬性的值變更時，便會叫用指定的回呼方法。

下列程式碼範例示範如何`EventName`可繫結屬性暫存器`OnEventNameChanged`做為屬性變更的回呼方法的方法：

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create (
    "EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
...

static void OnEventNameChanged (BindableObject bindable, object oldValue, object newValue)
{
  // Property changed implementation goes here
}
```

在屬性變更的回呼方法中， [ `BindableObject` ](xref:Xamarin.Forms.BindableObject)參數會用來表示哪個主控類別的執行個體發生了變更，以及兩個值`object`參數代表的舊和新值可繫結的屬性。

<a name="validation" />

### <a name="validation-callbacks"></a>驗證回撥

A`static`驗證回撥方法可以註冊具有可繫結的屬性，藉由指定`validateValue`參數[ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法。 設定可繫結屬性的值時，會叫用指定的回呼方法。

下列程式碼範例示範如何`Angle`可繫結屬性暫存器`IsValidValue`做為驗證回呼方法的方法：

```csharp
public static readonly BindableProperty AngleProperty =
  BindableProperty.Create ("Angle", typeof(double), typeof(HomePage), 0.0, validateValue: IsValidValue);
...

static bool IsValidValue (BindableObject view, object value)
{
  double result;
  bool isDouble = double.TryParse (value.ToString (), out result);
  return (result >= 0 && result <= 360);
}
```

驗證回撥所提供的值，而且應該傳回`true`的值是否有效的屬性，否則`false`。 如果傳回的驗證回呼，就會引發例外狀況`false`，這應該由開發人員。 設定可繫結的屬性時，驗證回撥方法的典型用法限制整數或雙精度浮點數的值。 例如，`IsValidValue`方法會檢查屬性值為`double`0 到 360 的範圍內。

<a name="coerce" />

### <a name="coerce-value-callbacks"></a>強制轉型值回撥

A`static`強制轉型值回呼方法可以註冊可繫結的屬性，藉由指定`coerceValue`參數[ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法。 可繫結屬性的值變更時，便會叫用指定的回呼方法。

強制轉型的值回撥來強制重新評估它的可繫結的屬性，屬性的值變更時。 比方說，強制轉型值回呼可以用於確保一個可繫結屬性的值不大於另一個可繫結屬性的值。

下列程式碼範例示範如何`Angle`可繫結屬性暫存器`CoerceAngle`為強制轉型值回呼方法的方法：

```csharp
public static readonly BindableProperty AngleProperty = BindableProperty.Create (
  "Angle", typeof(double), typeof(HomePage), 0.0, coerceValue: CoerceAngle);
public static readonly BindableProperty MaximumAngleProperty = BindableProperty.Create (
  "MaximumAngle", typeof(double), typeof(HomePage), 360.0);
...

static object CoerceAngle (BindableObject bindable, object value)
{
  var homePage = bindable as HomePage;
  double input = (double)value;

  if (input > homePage.MaximumAngle) {
    input = homePage.MaximumAngle;
  }

  return input;
}
```

`CoerceAngle`方法會檢查的值`MaximumAngle`屬性，而如果`Angle`屬性值大於它，它強制轉型值`MaximumAngle`屬性值。

<a name="defaultfunc" />

### <a name="creating-a-default-value-with-a-func"></a>使用函式建立的預設值

A`Func`可以用來初始化繫結的屬性，預設值中，如下列程式碼範例所示：

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

`defaultValueCreator`參數設定為`Func`叫用[ `Device.GetNamedSize` ](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type))方法來傳回`double`表示具名的大小上使用的字型[ `Label` ](xref:Xamarin.Forms.Label)原生的平台上。

## <a name="summary"></a>總結

本文介紹可繫結的屬性，並示範如何建立和使用它們。 可繫結的屬性是屬性的特殊類型，其中 Xamarin.Forms 屬性系統會追蹤屬性的值。

## <a name="related-links"></a>相關連結

- [XAML 命名空間](~/xamarin-forms/xaml/namespaces.md)
- [事件要命令行為 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [驗證回撥 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-validationcallback)
- [強制轉型值回撥 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-coercevaluecallback)
- [BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [BindableObject](xref:Xamarin.Forms.BindableObject)
