---
title: Xamarin. Forms 可系結屬性
description: 本文提供可系結屬性的簡介，並示範如何建立和使用它們。
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
ms.openlocfilehash: 3f06e4d94103e895bdceb2836c67709eb0f48c9a
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490099"
---
# <a name="xamarinforms-bindable-properties"></a>Xamarin. Forms 可系結屬性

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

可系結屬性會藉由支援具有[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)類型的屬性來擴充 CLR 屬性功能，而不是以欄位來支援屬性。 可系結屬性的目的是要提供支援資料系結、樣式、範本，以及透過父子式關聯性所設定之值的屬性系統。 此外，可系結屬性可以提供預設值、屬性值的驗證，以及監視屬性變更的回呼。

屬性應該實作為可系結屬性，以支援下列一或多項功能：

- 作為資料系結的有效*目標*屬性。
- 透過[樣式](~/xamarin-forms/user-interface/styles/index.md)設定屬性。
- 提供預設屬性值，其與屬性類型的預設值不同。
- 驗證屬性的值。
- 監視屬性變更。

Xamarin. Forms 可系結屬性的範例包括[`Label.Text`](xref:Xamarin.Forms.Label.Text)、 [`Button.BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius)和[`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation)。 每個可系結的屬性都有一個類型[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)的對應 `public static readonly` 屬性，該類別會在相同的類別上公開，而這是可系結屬性的識別碼。 例如，`Label.Text` 屬性的對應可系結屬性識別碼為[`Label.TextProperty`](xref:Xamarin.Forms.Label.TextProperty)。

## <a name="create-a-bindable-property"></a>建立可系結屬性

建立可系結屬性的流程如下所示：

1. 使用其中一個[`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create*)方法多載來建立[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)實例。
1. 定義[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)實例的屬性存取子。

請注意，您必須在 UI 執行緒上建立所有[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)實例。 這表示只有在 UI 執行緒上執行的程式碼可以取得或設定可系結屬性的值。 不過，您可以使用[`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action))方法封送處理至 UI 執行緒，以從其他執行緒存取 `BindableProperty` 實例。

### <a name="create-a-property"></a>建立屬性

若要建立 `BindableProperty` 實例，包含的類別必須衍生自[`BindableObject`](xref:Xamarin.Forms.BindableObject)類別。 不過，在類別階層中，`BindableObject` 類別很高，所以用於使用者介面功能的大部分類別都支援可系結的屬性。

您可以宣告[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)類型的 `public static readonly` 屬性來建立可系結的屬性。 可系結屬性應設定為其中一個[`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法多載的傳回值。 宣告應位於[`BindableObject`](xref:Xamarin.Forms.BindableObject)衍生類別的主體內，但不應位於任何成員定義的外部。

建立[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)時，至少必須指定識別碼，以及下列參數：

- [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)的名稱。
- 屬性的類型。
- 擁有物件的型別。
- 屬性的預設值。 這可確保屬性一律會在未設定時傳回特定的預設值，而且它可以與屬性類型的預設值不同。 在可系結屬性上呼叫[`ClearValue`](xref:Xamarin.Forms.BindableObject.ClearValue(Xamarin.Forms.BindableProperty))方法時，將會還原預設值。

下列程式碼顯示可系結屬性的範例，其中包含四個必要參數的識別碼和值：

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

這會建立一個名為 `EventName`的[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)實例，其類型為 `string`。 屬性是由 `EventToCommandBehavior` 類別所擁有，且預設值為 `null`。 可系結屬性的命名慣例是可系結屬性識別碼必須符合在 `Create` 方法中指定的屬性名稱，並附加 "Property"。 因此，在上述範例中，可系結的屬性識別碼為 `EventNameProperty`。

（選擇性）建立[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)實例時，可以指定下列參數：

- 繫結模式。 這是用來指定將傳播屬性值變更的方向。 在預設系結模式中，變更會從*來源*傳播至*目標*。
- 當設定屬性值時，將會叫用的驗證委派。 如需詳細資訊，請參閱[驗證回呼](#validation-callbacks)。
- 當屬性值變更時，將會叫用的屬性變更委派。 如需詳細資訊，請參閱偵測[屬性變更](#detect-property-changes)。
- 當屬性值變更時，將會叫用的屬性變更委派。 這個委派與屬性已變更的委派具有相同的簽章。
- 當屬性值已變更時，將會叫用的強制型轉值委派。 如需詳細資訊，請參閱強制型轉[值回呼](#coerce-value-callbacks)。
- 用來初始化預設屬性值的 `Func`。 如需詳細資訊，請參閱[使用 Func 建立預設值](#create-a-default-value-with-a-func)。

### <a name="create-accessors"></a>建立存取子

屬性存取子必須使用屬性語法來存取可系結的屬性。 `Get` 存取子應該會傳回包含在對應之可系結屬性中的值。 呼叫[`GetValue`](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty))方法，傳入要取得值的可系結屬性識別碼，然後將結果轉換成所需的型別，即可達成此目的。 `Set` 存取子應設定對應之可系結屬性的值。 呼叫[`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))方法，傳入要設定值的可系結屬性識別碼，以及要設定的值，即可達成此目的。

下列程式碼範例顯示 `EventName` 可系結屬性的存取子：

```csharp
public string EventName
{
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

## <a name="consume-a-bindable-property"></a>使用可系結屬性

一旦建立可系結的屬性之後，就可以從 XAML 或程式碼取用它。 在 XAML 中，這是藉由宣告具有前置詞的命名空間來達成，其中命名空間宣告會指示 CLR 命名空間名稱，並選擇性地宣告元件名稱。 如需詳細資訊，請參閱[XAML 命名空間](~/xamarin-forms/xaml/namespaces.md)。

下列程式碼範例示範自訂類型的 XAML 命名空間，其中包含可系結的屬性，其定義在與參考自訂類型的應用程式程式碼相同的元件中：

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

設定 `EventName` 可系結屬性時，會使用命名空間宣告，如下列 XAML 程式碼範例所示：

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
listView.Behaviors.Add (new EventToCommandBehavior
{
  EventName = "ItemSelected",
  ...
});
```

## <a name="advanced-scenarios"></a>進階案例

建立[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)實例時，有一些選擇性參數可設定為啟用 advanced 可系結屬性案例。 本節將探討這些案例。

### <a name="detect-property-changes"></a>偵測屬性變更

`static` 屬性變更的回呼方法可以透過指定[`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法的 `propertyChanged` 參數，向可系結的屬性註冊。 當可系結屬性的值變更時，將會叫用指定的回呼方法。

下列程式碼範例顯示 `EventName` 可系結屬性如何將 `OnEventNameChanged` 方法註冊為屬性變更的回呼方法：

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

在屬性變更的回呼方法中， [`BindableObject`](xref:Xamarin.Forms.BindableObject)參數是用來表示擁有類別的哪個實例已報告變更，而兩個 `object` 參數的值代表可系結屬性的舊值和新值。

### <a name="validation-callbacks"></a>驗證回呼

`static` 驗證回呼方法可以透過指定[`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法的 `validateValue` 參數，向可系結的屬性註冊。 當已設定可系結屬性的值時，將會叫用指定的回呼方法。

下列程式碼範例顯示 `Angle` 可系結屬性如何將 `IsValidValue` 方法註冊為驗證回呼方法：

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

驗證回呼是以值提供，如果此值對屬性有效，應傳回 `true`，否則 `false`。 如果驗證回呼傳回 `false`（應由開發人員處理），就會引發例外狀況。 驗證回呼方法的一般用法是在設定可系結屬性時，限制整數或雙精度浮點數的值。 例如，`IsValidValue` 方法會檢查屬性值是否為0到360範圍內的 `double`。

### <a name="coerce-value-callbacks"></a>強制型轉值回呼

`static` 強制型轉值回呼方法可以透過指定[`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法的 `coerceValue` 參數，向可系結的屬性註冊。 當可系結屬性的值變更時，將會叫用指定的回呼方法。

當屬性的值變更時，強制重新評估可系結屬性的強制值回呼。 例如，強制型轉值回呼可以用來確保一個可系結屬性的值不大於另一個可系結屬性的值。

下列程式碼範例顯示 `Angle` 可系結屬性如何將 `CoerceAngle` 方法註冊為強制值回呼方法：

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

  if (input > homePage.MaximumAngle)
  {
    input = homePage.MaximumAngle;
  }
  return input;
}
```

`CoerceAngle` 方法會檢查 `MaximumAngle` 屬性的值，如果 `Angle` 屬性值大於該值，則會將值強制轉型為 `MaximumAngle` 屬性值。

### <a name="create-a-default-value-with-a-func"></a>使用 Func 建立預設值

`Func` 可以用來初始化可系結屬性的預設值，如下列程式碼範例所示：

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

`defaultValueCreator` 參數會設定為 `Func`，它會叫用[`Device.GetNamedSize`](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type))方法，以傳回代表在原生平臺上用於[`Label`](xref:Xamarin.Forms.Label)之字型的已命名大小的 `double`。

## <a name="related-links"></a>相關連結

- [XAML 命名空間](~/xamarin-forms/xaml/namespaces.md)
- [事件至命令列為（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [驗證回呼（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-validationcallback)
- [強制轉型值回呼（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-coercevaluecallback)
- [BindableProperty API](xref:Xamarin.Forms.BindableProperty)
- [BindableObject API](xref:Xamarin.Forms.BindableObject)
