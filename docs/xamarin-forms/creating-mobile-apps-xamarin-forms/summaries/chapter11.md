---
title: 第11章的摘要。 可系結的基礎結構
description: 使用 Xamarin 建立 Mobile Apps：第11章的摘要。 可系結的基礎結構
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: f9e3326c0f55469cfa84a019a674679d82dfc007
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334296"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>第11章的摘要。 可系結的基礎結構

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)

每C#一位程式設計C#人員都熟悉*屬性*。 屬性包含*set*存取子和（或） *get*存取子。 它們通常稱為 Common Language Runtime 的*CLR 屬性*。

Xamarin 會定義一個增強的屬性定義，稱為可系結的*屬性*，由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)類別封裝，並由[`BindableObject`](xref:Xamarin.Forms.BindableObject)類別支援。 這些類別是相關的，但截然不同： `BindableProperty` 是用來定義屬性本身;`BindableObject` 就像 `object`，因為它是定義可系結屬性之類別的基類。

## <a name="the-xamarinforms-class-hierarchy"></a>Xamarin. Forms 類別階層架構

[**ClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy)範例會使用反映來顯示 Xamarin 的類別階層架構，並示範此階層中 `BindableObject` 所扮演的重要角色。 `BindableObject` 衍生自 `Object`，而是[`VisualElement`](xref:Xamarin.Forms.VisualElement)從中衍生[`Element`](xref:Xamarin.Forms.Element)的父類別。 這是要[`Page`](xref:Xamarin.Forms.Page)和[`View`](xref:Xamarin.Forms.View)的父類別，也就是要[`Layout`](xref:Xamarin.Forms.Layout)的父類別：

[![類別階層共用的三向螢幕擷取畫面](images/ch11fg01-small.png "類別階層共用")](images/ch11fg01-large.png#lightbox "類別階層共用")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>查看 BindableObject 和 BindableProperty

在衍生自的類別中 `BindableObject` 許多 CLR 屬性稱為「受支援」可系結屬性。 例如，`Label` 類別的[`Text`](xref:Xamarin.Forms.Label.Text)屬性是 CLR 屬性，但 `Label` 類別也會定義名為[`TextProperty`](xref:Xamarin.Forms.Label.TextProperty)類型為 `BindableProperty`的公用靜態唯讀欄位。

應用程式可以正常設定或取得 `Label` 的 `Text` 屬性，或者應用程式可以藉由呼叫 `BindableObject` 引數所定義的[`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))方法，來設定 `Text`。`Label.TextProperty` 同樣地，應用程式可以藉由呼叫[`GetValue`](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty))方法，再次使用 `Label.TextProperty` 引數來取得 `Text` 屬性的值。 這是由[**PropertySettings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings)範例所示範。

事實上，`Text` CLR 屬性會使用 `BindableObject` 與 `Label.TextProperty` 靜態屬性一起定義的 `SetValue` 和 `GetValue` 方法，完全實作為實。

`BindableObject` 和 `BindableProperty` 提供下列支援：

- 提供屬性預設值
- 儲存其目前的值
- 提供驗證屬性值的機制
- 維護單一類別中相關屬性間的一致性
- 回應屬性變更
- 當屬性即將變更或變更時觸發通知
- 支援資料系結
- 支援的樣式
- 支援動態資源

每當可系結屬性所支援的屬性變更時，`BindableObject` 會引發[`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged)事件來識別已變更的屬性。 當屬性設定為相同的值時，不會引發此事件。

某些屬性不是由可系結屬性所支援，而某些 Xamarin. Forms 類別 &mdash; 例如 `Span` &mdash; 不是衍生自 `BindableObject`。 只有衍生自 `BindableObject` 的類別可以支援可系結屬性，因為 `BindableObject` 會定義 `SetValue` 和 `GetValue` 方法。

因為 `Span` 不是衍生自 `BindableObject`，所以其屬性 &mdash; （例如 `Text` &mdash;）不會受到可系結屬性的支援。 這就是為什麼 `Span` 的 `Text` 屬性 `DynamicResource` 設定在上一章的[**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic)範例中引發例外狀況的原因。 [**DynamicVsStaticCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode)範例會示範如何使用 `Element`所定義的[`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource(Xamarin.Forms.BindableProperty,System.String))方法，在程式碼中設定動態資源。 第一個引數是 `BindableProperty`類型的物件。

同樣地，`BindableObject` 所定義的[`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))方法具有 `BindableProperty`類型的第一個引數。

## <a name="defining-bindable-properties"></a>定義可系結屬性

您可以使用靜態[`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法來定義您自己的可系結屬性，以建立 `BindableProperty`類型的靜態唯讀欄位。

這會在[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫的[`AltLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs)類別中示範。 類別衍生自 `Label`，可讓您指定以點為單位的字型大小。 它會在[**PointSizedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText)範例中示範。

需要 `BindableProperty.Create` 方法的四個引數：

- `propertyName`：屬性的文字名稱（與 CLR 屬性名稱相同）
- `returnType`： CLR 屬性的類型
- `declaringType`：宣告屬性之類別的型別
- `defaultValue`：屬性的預設值

因為 `defaultValue` 的類型 `object`，所以編譯器必須能夠判斷預設值的類型。 例如，如果 `double``returnType`，則 `defaultValue` 應設定為類似0.0 而不是只有0的專案，否則類型不相符將會在執行時間觸發例外狀況。

可系結的屬性也很常見，包括：

- `propertyChanged`：當屬性變更值時，所呼叫的靜態方法。 第一個引數是類別的實例，其屬性已變更。

`BindableProperty.Create` 的其他引數並不常見：

- `defaultBindingMode`：用於與資料系結的連接（如第[**16 章所討論）。資料**](chapter16.md)系結）
- `validateValue`：用來檢查有效值的回呼
- `propertyChanging`：回呼，指出屬性即將變更的時間
- `coerceValue`：將設定值強制轉型為另一個值的回呼
- `defaultValueCreate`：用來建立預設值的回呼，無法在類別的實例之間共用（例如，集合）

### <a name="the-read-only-bindable-property"></a>唯讀可系結屬性

可系結屬性可以是唯讀的。 建立唯讀可系結屬性需要呼叫靜態方法[`BindableProperty.CreateReadOnly`](xref:Xamarin.Forms.BindableProperty.CreateReadOnly(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) ，以定義[`BindablePropertyKey`](xref:Xamarin.Forms.BindablePropertyKey)類型的私用靜態唯讀欄位。

然後，將 CLR 屬性 `set` accesor 定義為 `private`，以 `BindablePropertyKey` 物件呼叫[`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindablePropertyKey,System.Object))多載。 這可避免將屬性設定在類別之外。

這會在[**BaskervillesCount**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount)範例中使用的[`CountedLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs)類別中示範。

## <a name="related-links"></a>相關連結

- [第11章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [第11章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [可繫結的屬性](~/xamarin-forms/xaml/bindable-properties.md)
