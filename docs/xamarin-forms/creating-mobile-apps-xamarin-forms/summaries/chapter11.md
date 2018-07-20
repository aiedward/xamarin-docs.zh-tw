---
title: 第 11 章摘要。 可繫結的基礎結構
description: 使用 Xamarin.Forms 建立行動應用程式： 第 11 章摘要。 可繫結的基礎結構
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: charlespetzold
ms.author: chape
ms.date: 07/19/2018
ms.openlocfilehash: 9f3c077d7bae3557178236b81073afaf4892a272
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2018
ms.locfileid: "39156557"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>第 11 章摘要。 可繫結的基礎結構

每個 C# 程式設計人員會熟悉 C#*屬性*。 屬性包含*設定*存取子和 （或)*取得*存取子。 它們通常稱為*CLR 屬性*通用語言執行平台。

Xamarin.Forms 可定義增強的屬性定義中稱為*可繫結的屬性*由封裝[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)類別，並支援[ `BindableObject` ](xref:Xamarin.Forms.BindableObject)類別。 這些類別是相關但非常不同：`BindableProperty`用來定義屬性本身;`BindableObject`就像是`object`在於它是類別，定義可繫結屬性的基底類別。

## <a name="the-xamarinforms-class-hierarchy"></a>Xamarin.Forms 類別階層

[ **ClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy)範例會使用反映來顯示類別階層架構的 Xamarin.Forms，並示範所扮演的角色相當重要`BindableObject`此階層中。 `BindableObject` 衍生自`Object`是父類別，以[ `Element` ](xref:Xamarin.Forms.Element)從中[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)衍生。 這是父類別[ `Page` ](xref:Xamarin.Forms.Page)並[ `View` ](xref:Xamarin.Forms.View)，這是父類別[ `Layout` ](xref:Xamarin.Forms.Layout):

[![共用的類別階層架構的三個螢幕擷取畫面](images/ch11fg01-small.png "類別階層共用")](images/ch11fg01-large.png#lightbox "類別階層共用")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>一窺 BindableObject 和 BindableProperty

從衍生類別中`BindableObject`許多 CLR 屬性就指 「 受到 」 可繫結屬性。 例如， [ `Text` ](xref:Xamarin.Forms.Label.Text)屬性`Label`類別是 CLR 屬性，但`Label`類別也會定義公用靜態唯讀欄位命名為[ `TextProperty` ](xref:Xamarin.Forms.Label.TextProperty)的型別`BindableProperty`。

應用程式可以設定或取得`Text`的屬性`Label`一般來說，應用程式可以設定`Text`藉由呼叫[ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))所定義的方法`BindableObject`使用`Label.TextProperty`引數。 同樣地，應用程式可以取得的值`Text`屬性，藉由呼叫[ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty))方法，以再次`Label.TextProperty`引數。 最好的證明[ **PropertySettings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings)範例。

事實上，`Text`使用完全實作的 CLR 屬性`SetValue`並`GetValue`所定義的方法`BindableObject`搭配`Label.TextProperty`靜態屬性。

`BindableObject` 和`BindableProperty`提供支援：

- 讓屬性預設值
- 儲存目前的值
- 提供的機制來驗證屬性值
- 維護在單一類別中的相關屬性之間的一致性
- 回應屬性變更
- 屬性即將變更或已變更時觸發通知
- 支援資料繫結
- 支援的樣式
- 支援的動態資源

當可繫結的屬性變更，做為後盾的屬性`BindableObject`引發[ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged)識別已變更之屬性的事件。 當屬性設定為相同的值，不會引發此事件。

有些屬性不支援可繫結的屬性，以及一些 Xamarin.Forms 類別&mdash;這類`Span`&mdash;不是衍生自`BindableObject`。 只有衍生自類別`BindableObject`可支援可繫結屬性，因為`BindableObject`定義`SetValue`和`GetValue`方法。

因為`Span`不是衍生自`BindableObject`，其屬性&mdash;這類`Text`&mdash;受到可繫結的屬性。 這就是為什麼`DynamicResource`上設定`Text`屬性`Span`引發的例外狀況[ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic)前面章節中的範例。 [ **DynamicVsStaticCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode)範例會示範如何在程式碼中使用設定動態資源[ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource(Xamarin.Forms.BindableProperty,System.String))所定義的方法`Element`。 第一個引數是類型的物件`BindableProperty`。

同樣地， [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))所定義的方法`BindableObject`具有類型的第一個引數`BindableProperty`。

## <a name="defining-bindable-properties"></a>定義可繫結屬性

您可以定義您自己的可繫結屬性，使用靜態[ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法來建立類型的靜態唯讀欄位`BindableProperty`。

這示範於[ `AltLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫。 類別衍生自`Label`並讓您指定的字型大小以點為單位。 中會示範[ **PointSizedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText)範例。

四個引數的`BindableProperty.Create`方法所需：

- `propertyName`： 文字屬性的名稱 （與相同的 CLR 屬性名稱）
- `returnType`: CLR 屬性的型別
- `declaringType`： 宣告屬性類別的型別
- `defaultValue`： 屬性的預設值

因為`defaultValue`別的`object`，編譯器必須能夠判斷預設值的類型。 例如，如果`returnType`已`double`，則`defaultValue`應該設定為 0.0，而不是只是 0，類似或類型不相符的情況會觸發在執行階段發生例外狀況。

它也是非常普遍的可繫結的屬性，以包括：

- `propertyChanged`： 在屬性變更值時，會呼叫靜態方法。 第一個引數是其屬性已變更之類別的執行個體。

其他引數`BindableProperty.Create`並不常用：

- `defaultBindingMode`： 用於與資料繫結 (如所述[**第 16 章。資料繫結**](chapter16.md))
- `validateValue`: 回呼，以檢查是否有有效的值
- `propertyChanging`: 回呼，指出屬性即將變更
- `coerceValue`： 要強制轉型為另一個值的設定值的回撥
- `defaultValueCreate`： 要建立的預設值，不能在類別 （例如集合） 的執行個體之間共用的回撥

### <a name="the-read-only-bindable-property"></a>唯讀的可繫結屬性

可繫結的屬性可以是唯讀狀態。 建立唯讀的可繫結屬性需要呼叫靜態方法[ `BindableProperty.CreateReadOnly` ](xref:Xamarin.Forms.BindableProperty.CreateReadOnly(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))來定義私用靜態唯讀欄位的型別[ `BindablePropertyKey` ](xref:Xamarin.Forms.BindablePropertyKey)。

然後，定義的 CLR 屬性`set`做為存取`private`來呼叫[ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindablePropertyKey,System.Object))多載`BindablePropertyKey`物件。 這可防止類別之外所設定的屬性。

這示範於[ `CountedLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs)中所使用的類別[ **BaskervillesCount** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount)範例。

## <a name="related-links"></a>相關連結

- [第 11 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [第 11 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [可繫結的屬性](~/xamarin-forms/xaml/bindable-properties.md)
