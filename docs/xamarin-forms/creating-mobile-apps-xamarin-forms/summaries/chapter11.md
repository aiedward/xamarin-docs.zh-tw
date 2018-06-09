---
title: 第 11 章的摘要。 可繫結基礎結構
description: 使用 Xamarin.Forms 建立行動裝置應用程式： 摘要第 11 章。 可繫結基礎結構
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 7a878be4cf43e2168c37fdfd8e6fcdb79feb1602
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241535"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>第 11 章的摘要。 可繫結基礎結構

每個 C# 程式設計人員會熟悉 C#*屬性*。 屬性包含*設定*存取子和 （或)*取得*存取子。 它們通常稱為*CLR 屬性*通用語言執行平台。

Xamarin.Forms 會定義稱為增強的屬性定義*可繫結屬性*由封裝[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)類別，並支援[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)類別。 這些類別是相關但相當不同：`BindableProperty`用來定義屬性本身。`BindableObject`就像是`object`，它是類別，定義可繫結屬性的基底類別。

## <a name="the-xamarinforms-class-hierarchy"></a>Xamarin.Forms 類別階層架構

[ **ClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy)範例使用反映來顯示類別階層架構的 Xamarin.Forms，並示範所扮演重要角色`BindableObject`此階層中。 `BindableObject` 衍生自`Object`是父類別和[ `Element` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/)從中[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)衍生。 這是父類別[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)和[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)，這是父類別[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/):

[![共用的類別階層的三個螢幕擷取畫面](images/ch11fg01-small.png "類別階層架構共用")](images/ch11fg01-large.png#lightbox "共用的類別階層")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>瀏覽到 BindableObject 和 BindableProperty

在類別中衍生自`BindableObject`許多 CLR 屬性稱為 「 受支援 「 可繫結屬性。 例如， [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)屬性`Label`類別是 CLR 屬性，但`Label`類別也會定義的公用靜態唯讀欄位名為[ `TextProperty` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextProperty/)的型別`BindableProperty`。

應用程式可以設定或取得`Text`屬性`Label`一般來說，應用程式可以設定或`Text`藉由呼叫[ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/)方法所定義`BindableObject`與`Label.TextProperty`引數。 同樣地，應用程式可以取得的值`Text`屬性，藉由呼叫[ `GetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.GetValue/p/Xamarin.Forms.BindableProperty/)方法，然後再次`Label.TextProperty`引數。 這示範[ **PropertySettings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings)範例。

事實上，`Text`使用完全實作的 CLR 屬性`SetValue`和`GetValue`所定義的方法`BindableObject`搭配`Label.TextProperty`靜態屬性。

`BindableObject` 和`BindableProperty`提供支援：

- 提供屬性的預設值
- 儲存其目前值
- 提供機制來驗證屬性值
- 維護單一類別中的相關屬性之間的一致性
- 屬性變更回應
- 屬性即將變更或已變更時觸發通知
- 支援資料繫結
- 支援的樣式
- 支援的動態資源

當屬性，可繫結的屬性變更，並受到`BindableObject`引發[ `PropertyChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.BindableObject.PropertyChanged/)識別已變更之屬性的事件。 當屬性設定為相同的值，不會引發這個事件。

有些屬性不支援可繫結的屬性，以及一些 Xamarin.Forms 類別&mdash;例如`Span`&mdash;不是衍生自`BindableObject`。 只有類別衍生自`BindableObject`支援可繫結的屬性，因為`BindableObject`定義`SetValue`和`GetValue`方法。

因為`Span`不是衍生自`BindableObject`，其屬性&mdash;例如`Text`&mdash;都由可繫結的屬性。 這就是為什麼`DynamicResource`上設定`Text`屬性`Span`引發例外狀況以[ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic)前面章節中的範例。 [ **DynamicVsStaticCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode)範例會示範如何在程式碼中使用設定動態資源[ `SetDynamicResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Element.SetDynamicResource/p/Xamarin.Forms.BindableProperty/System.String/)方法所定義`Element`。 第一個引數是類型的物件`BindableProperty`。

同樣地， [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/)方法所定義`BindableObject`有第一個引數的型別`BindableProperty`。

## <a name="defining-bindable-properties"></a>定義可繫結屬性

您可以定義您自己的繫結屬性使用靜態[ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/)方法或稍微較短者[ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/)多載來建立類型的靜態唯讀欄位`BindableProperty`。

這示範於[ `AltLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫。 類別衍生自`Label`並讓您以點為單位指定字型的大小。 它會示範[ **PointSizedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText)範例。

四個引數的`BindableProperty.Create`方法所需：

- `propertyName`: （相同的 CLR 屬性名稱） 之屬性的文字名稱
- `returnType`: CLR 屬性的型別
- `declaringType`： 宣告屬性類別的型別
- `defaultValue`： 將屬性的預設值

因為`defaultValue`的型別`object`，編譯器必須能夠判斷預設值的類型。 例如，如果`returnType`是`double`、`defaultValue`應該設定為 0.0，而不是只 0 類似或類型不符的情況會觸發程序在執行階段發生例外狀況。

它也是非常普遍的可繫結的屬性，以包括：

- `propertyChanged`： 變更屬性值時，呼叫靜態方法。 第一個引數是其屬性已變更之類別的執行個體。

其他引數至`BindableProperty.Create`並不常用：

- `defaultBindingMode`： 使用與資料繫結 (如所述[**第 16 章。資料繫結**](chapter16.md))
- `validateValue`： 回呼檢查是否有有效的值
- `propertyChanging`： 表示屬性即將變更的回呼
- `coerceValue`： 要強制轉型為另一個值的設定值的回呼
- `defaultValueCreate`： 若要建立預設值，不能在類別 （例如集合） 的執行個體之間共用的回呼

### <a name="the-read-only-bindable-property"></a>唯讀的可繫結屬性

可繫結屬性可以是唯讀狀態。 建立唯讀的可繫結屬性需要呼叫靜態方法[ `BindableProperty.CreateReadOnly` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateReadOnly/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/)或較矮[ `BindableProperty.CreateReadOnly` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateReadOnly/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/)來定義私用靜態唯讀欄位的型別[ `BindablePropertyKey`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindablePropertyKey/).

然後，定義的 CLR 屬性`set`做為存取`private`呼叫[ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindablePropertyKey/System.Object/)多載`BindablePropertyKey`物件。 這可防止在類別外部設定屬性。

這示範於[ `CountedLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs)類別用於[ **BaskervillesCount** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount)範例。



## <a name="related-links"></a>相關連結

- [第 11 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [第 11 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
