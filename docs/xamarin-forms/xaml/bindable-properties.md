---
title: 可繫結屬性
description: 本文介紹可繫結的屬性，並示範如何建立及使用它們。
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/02/2016
ms.openlocfilehash: 5e39e8eb3d7ffb3ed33ea2a585d8d367302e9baa
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245972"
---
# <a name="bindable-properties"></a>可繫結屬性

_透過 Xamarin.Forms，在 common language runtime (CLR) 屬性的功能被擴充可繫結屬性。可繫結屬性是屬性的特殊類型，其中會追蹤 Xamarin.Forms 屬性系統屬性的值。本文介紹可繫結的屬性，並示範如何建立及使用它們。_

## <a name="overview"></a>總覽

可繫結屬性所支援的屬性延伸 CLR 屬性功能[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)型別，而不是備份含有欄位的屬性。 可繫結屬性的目的是要提供支援資料繫結、 樣式、 範本的屬性系統，而且透過父子式關聯性的值。 此外，可繫結屬性可以提供預設值，驗證的屬性值和監視屬性變更的回呼。

屬性應該會實作為可繫結的屬性，以支援一或多個下列功能：

- 做為有效*目標*資料繫結的屬性。
- 透過將屬性設定[樣式](~/xamarin-forms/user-interface/styles/index.md)。
- 提供與屬性類型的預設值不同的預設屬性值。
- 正在驗證屬性的值。
- 監視屬性變更。

Xamarin.Forms 可繫結屬性的範例包括[ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)， [ `Button.BorderRadius` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderRadius/)，和[ `StackLayout.Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Orientation/)。 每個可繫結的屬性都有對應`public static readonly`型別的屬性[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)相同類別上公開，而這就是可繫結屬性的識別項。 例如，對應可繫結屬性的識別項的`Label.Text`屬性是[ `Label.TextProperty` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Label.TextProperty/)。

<a name="consuming-bindable-property" />

## <a name="creating-and-consuming-a-bindable-property"></a>建立和使用可繫結屬性

建立可繫結屬性的程序如下所示：

1. 建立[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)與其中一個執行個體[ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/)方法多載。
1. 定義屬性的存取子[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)執行個體。

請注意，所有[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)必須在 UI 執行緒上建立執行個體。 這表示 UI 執行緒上執行的程式碼來取得或設定可繫結屬性的值。 不過，`BindableProperty`執行個體可以從其他執行緒存取 UI 執行緒與封送處理[ `Device.BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/)方法。

### <a name="creating-a-property"></a>建立屬性

若要建立`BindableProperty`執行個體，包含的類別必須衍生自[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)類別。 不過，`BindableObject`類別很高，類別階層架構中，因此大部分的類別用於使用者介面功能支援可繫結屬性。

您可以建立可繫結的屬性宣告`public static readonly`型別的屬性[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)。 可繫結的屬性應該設定為傳回的值，其中的[ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/)方法多載。 宣告應該是主體內[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)衍生的類別，但之外的任何成員定義。

至少一個識別項時，必須指定建立[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)，以及下列參數：

- 名稱[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)。
- 屬性的類型。
- 主控的物件類型。
- 屬性的預設值。 這可確保未設定，且可以是不同的屬性類型的預設值時，屬性永遠傳回特定的預設值。 預設值將會是還原時[ `ClearValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.ClearValue/p/Xamarin.Forms.BindableProperty/)可繫結的屬性上呼叫方法。

下列程式碼會顯示可繫結的屬性，以識別項和四個必要參數的值範例：

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

這會建立[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)名為執行個體`EventName`，型別`string`。 屬性由所擁有`EventToCommandBehavior`類別，以及具有預設值是`null`。 可繫結屬性的命名慣例是可繫結屬性的識別項必須符合在指定的屬性名稱`Create`方法，並包含附加至其 「 屬性 」。 因此，在上述範例中，可繫結屬性的識別項是`EventNameProperty`。

（選擇性） 建立時[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)執行個體，這個參數可以指定：

- 繫結模式。 這用來指定屬性值變更會將傳播的方向。 在預設的繫結模式中，變更會傳播從*來源*至*目標*。
- 設定屬性值時將會叫用驗證委派。 如需詳細資訊，請參閱[驗證回呼](#validation)。
- 屬性變更屬性值變更時將會叫用的委派。 如需詳細資訊，請參閱[偵測屬性變更](#propertychanges)。
- 屬性，變更屬性值將會變更時將會叫用的委派。 這個委派會有相同的簽章和已變更屬性的委派。
- 強制轉型值的委派，會叫用的屬性值變更時。 如需詳細資訊，請參閱[Coerce 值回呼](#coerce)。
- A `Func` ，用來初始化預設屬性值。 如需詳細資訊，請參閱[函式以建立預設值](#defaultfunc)。

### <a name="creating-accessors"></a>建立存取子

屬性存取子，才能使用屬性語法來存取可繫結的屬性。 `Get`存取子應會傳回包含值，對應的可繫結屬性中。 這可藉由呼叫[ `GetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.GetValue/p/Xamarin.Forms.BindableProperty/)方法中，傳入可繫結屬性的識別項，以取得值，然後再將結果為必要類型轉換。 `Set`存取子都應該設定相對應的可繫結屬性的值。 這可藉由呼叫[ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/)方法，傳入要設定此值，而要設定的值可繫結屬性的識別項。

下列程式碼範例顯示存取子`EventName`可繫結屬性：

```csharp
public string EventName {
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

### <a name="consuming-a-bindable-property"></a>使用可繫結屬性

一旦建立可繫結的屬性，它可以取用從 XAML 或程式碼。 在 XAML 中，這是由宣告具有前置詞的命名空間與 CLR 命名空間名稱，並選擇性地組件名稱，表示命名空間宣告完成。 如需詳細資訊，請參閱[XAML 命名空間](~/xamarin-forms/xaml/namespaces.md)。

下列程式碼範例示範包含可繫結的屬性，這在相同的組件參考自訂類型的應用程式程式碼中定義的自訂類型的 XAML 命名空間：

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

設定時，使用命名空間宣告`EventName`可繫結的屬性，以下列 XAML 程式碼範例示範：

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

建立時[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)執行個體，有幾個可以設定以啟用進階可繫結屬性的案例的選擇性參數。 本節會探討這些案例。

<a name="propertychanges" />

### <a name="detecting-property-changes"></a>偵測屬性變更

A`static`屬性變更的回呼方法可以註冊可繫結的屬性，藉由指定`propertyChanged`參數[ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/)方法。 可繫結屬性的值變更時，便會叫用指定的回呼方法。

下列程式碼範例示範如何`EventName`可繫結屬性暫存器`OnEventNameChanged`方法做為屬性變更的回呼方法：

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

在變更屬性的回呼方法中， [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)參數用來表示哪個主控類別的執行個體已回報了變更，以及兩個值`object`參數代表舊的和新的值可繫結的屬性。

<a name="validation" />

### <a name="validation-callbacks"></a>驗證回撥

A`static`驗證回呼方法可以註冊可繫結的屬性，藉由指定`validateValue`參數[ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/)方法。 設定可繫結屬性的值時，會叫用指定的回呼方法。

下列程式碼範例示範如何`Angle`可繫結屬性暫存器`IsValidValue`和驗證回呼方法的方法：

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

驗證回呼會提供一個值，並應該傳回`true`如果值為有效的屬性，否則`false`。 如果傳回的驗證回呼，會引發例外狀況`false`，但應該處理由開發人員。 設定可繫結的屬性時，驗證回呼方法的一般用法條件約束的整數或雙精度浮點數的值。 例如，`IsValidValue`方法會檢查屬性值是`double`0 到 360 的範圍內。

<a name="coerce" />

### <a name="coerce-value-callbacks"></a>強制轉型值回呼

A`static`強制轉型的值可以藉由指定的可繫結內容來註冊回呼方法`coerceValue`參數[ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/)方法。 可繫結屬性的值變更時，便會叫用指定的回呼方法。

Coerce 回呼用於屬性的值變更時，強制重新評估可繫結屬性的值。 例如，強制值回呼可以用於確保一個可繫結屬性的值不大於另一個可繫結屬性的值。

下列程式碼範例示範如何`Angle`可繫結屬性暫存器`CoerceAngle`方法做為強制轉型值回呼方法：

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

`CoerceAngle`方法會檢查值`MaximumAngle`屬性，而且如果`Angle`屬性值大於它，它會強制值`MaximumAngle`屬性值。

<a name="defaultfunc" />

### <a name="creating-a-default-value-with-a-func"></a>使用函式建立預設值

A`Func`可以用來初始化繫結屬性的預設值中，如下列程式碼範例所示：

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

`defaultValueCreator`參數設定為`Func`會叫用[ `Device.GetNamedSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.GetNamedSize/p/Xamarin.Forms.NamedSize/System.Type/)方法以傳回`double`代表具名的大小上使用的字型[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)原生的平台上。

## <a name="summary"></a>總結

本文章提供簡介可繫結的屬性，並示範如何建立及使用它們。 可繫結屬性是屬性的特殊類型，其中會追蹤 Xamarin.Forms 屬性系統屬性的值。


## <a name="related-links"></a>相關連結

- [XAML 命名空間](~/xamarin-forms/xaml/namespaces.md)
- [事件至命令行為 （範例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)
- [驗證回呼 （範例）](https://developer.xamarin.com/samples/xamarin-forms/xaml/validationcallback/)
- [Coerce 值回呼 （範例）](https://developer.xamarin.com/samples/xamarin-forms/xaml/coercevaluecallback/)
- [BindableProperty](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)
- [BindableObject](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)
