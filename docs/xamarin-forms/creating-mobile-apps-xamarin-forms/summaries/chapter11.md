---
title: 第11章的摘要。 可系結基礎結構
description: 建立 Mobile Apps Xamarin.Forms ：第11章的摘要。 可系結基礎結構
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e2858d0606cf9c5c97a3457b5b29f620e7da2bad
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375131"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>第11章的摘要。 可系結基礎結構

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

每個 c # 程式設計人員都熟悉 c # *屬性* 。 屬性包含 *set* 存取子和（或） *get* 存取子。 它們通常稱為 Common Language Runtime 的 *CLR 屬性* 。

Xamarin.Forms 定義增強的屬性定義，稱為可系結的 *屬性* ，該類別是由類別所封裝 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 並且受 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 類別支援。 這些類別是相關的，但相當相異： `BindableProperty` 是用來定義屬性本身; `BindableObject` 就像是 `object` 因為它是定義可系結屬性之類別的基類（base class）。

## <a name="the-no-locxamarinforms-class-hierarchy"></a>Xamarin.Forms類別階層

[**ClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy)範例會使用反映來顯示的類別階層 Xamarin.Forms ，並示範此階層中所扮演的重要角色 `BindableObject` 。 `BindableObject` 衍生自 `Object` ，而且是從中衍生的父類別 [`Element`](xref:Xamarin.Forms.Element) [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 這是和的父類別 [`Page`](xref:Xamarin.Forms.Page) [`View`](xref:Xamarin.Forms.View) ，也就是要執行下列動作的父類別 [`Layout`](xref:Xamarin.Forms.Layout) ：

[![類別階層共用的三重螢幕擷取畫面](images/ch11fg01-small.png "類別階層共用")](images/ch11fg01-large.png#lightbox "類別階層共用")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>查看 BindableObject 和 BindableProperty

在許多 CLR 屬性衍生的類別中，稱為「受限於」可系結屬性的「 `BindableObject` 支援」。 例如，類別的 [`Text`](xref:Xamarin.Forms.Label.Text) 屬性 `Label` 是 CLR 屬性，但是 `Label` 類別也會定義一個名為的公用靜態唯讀欄位，其型別為 [`TextProperty`](xref:Xamarin.Forms.Label.TextProperty) `BindableProperty` 。

應用程式可以設定或取得 `Text` 正常的屬性 `Label` ，或應用程式可以藉 `Text` 由呼叫 [ `SetValue` ] (x：來設定 Xamarin.Forms 。BindableObject (Xamarin.Forms 。BindableProperty、System.object) # A3 方法，由 `BindableObject` 引數所定義 `Label.TextProperty` 。 同樣地，應用程式可以藉 `Text` 由呼叫 [ `GetValue` ] (x：來取得屬性的值 Xamarin.Forms 。BindableObject (Xamarin.Forms 。BindableProperty) # A3 方法，再加上 `Label.TextProperty` 引數。 [**PropertySettings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings)範例會示範這一點。

的確， `Text` CLR 屬性是使用 `SetValue` 和 `GetValue` `BindableObject` 靜態屬性一起定義的和方法來完全實作為 `Label.TextProperty` 。

`BindableObject` 並 `BindableProperty` 提供下列支援：

- 提供屬性預設值
- 儲存其目前的值
- 提供驗證屬性值的機制
- 維護單一類別中相關屬性之間的一致性
- 回應屬性變更
- 當屬性即將變更或變更時觸發通知
- 支援資料系結
- 支援樣式
- 支援動態資源

每當可系結屬性所支援的屬性變更時，就 `BindableObject` 會引發一個 [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) 事件來識別已變更的屬性。 當屬性設定為相同的值時，不會引發此事件。

某些屬性不會受到可系結屬性的支援，而某些 Xamarin.Forms 類別 &mdash; （例如） `Span` &mdash; 不會衍生自 `BindableObject` 。 只有衍生自的類別 `BindableObject` 可以支援可系結屬性 `BindableObject` ，因為會定義 `SetValue` 和 `GetValue` 方法。

由於 `Span` 並非衍生自 `BindableObject` ，因此其屬性 &mdash; （例如）都不 `Text` &mdash; 會受到可系結屬性的支援。 這就是為什麼 `DynamicResource` 屬性（property）上的設定在 `Text` `Span` 上一章的 [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) 範例中引發例外狀況的原因。 [**DynamicVsStaticCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode)範例示範如何在程式碼中使用 [ `SetDynamicResource` ] (x：來設定動態資源 Xamarin.Forms 。SetDynamicResource (Xamarin.Forms 。BindableProperty、System.string) # A3 方法定義 `Element` 。 第一個引數是類型的物件 `BindableProperty` 。

同樣地，[ `SetBinding` ] (x： Xamarin.Forms 。BindableObject. SetBinding (Xamarin.Forms 。BindableProperty， Xamarin.Forms 。由定義的 System.windows.data.bindingbase.delay) # A3 方法 `BindableObject` 具有類型的第一個引數 `BindableProperty` 。

## <a name="defining-bindable-properties"></a>定義可系結屬性

您可以使用 static [ `BindableProperty.Create` ] (x：來定義您自己的可系結屬性 Xamarin.Forms 。BindableProperty：建立 (System.string，System.string，system.object，. type，system.object Xamarin.Forms 。BindingMode， Xamarin.Forms 。BindableProperty. ValidateValueDelegate、 Xamarin.Forms 。BindableProperty. BindingPropertyChangedDelegate、 Xamarin.Forms 。BindableProperty. BindingPropertyChangingDelegate、 Xamarin.Forms 。BindableProperty. CoerceValueDelegate、 Xamarin.Forms 。BindableProperty. CreateDefaultValueDelegate) # A3 方法來建立類型的靜態唯讀欄位 `BindableProperty` 。

這會在 [`AltLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) [**Xamarin.Forms Book. 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫的類別中示範。 類別衍生自 `Label` ，並可讓您指定以點為單位的字型大小。 它會在 [**PointSizedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText) 範例中示範。

需要四個方法的引數 `BindableProperty.Create` ：

- `propertyName`：屬性的文字名稱 (與 CLR 屬性名稱相同) 
- `returnType`： CLR 屬性的型別
- `declaringType`：宣告屬性之類別的類型
- `defaultValue`：屬性的預設值

因為 `defaultValue` 的型別為 `object` ，所以編譯器必須能夠判斷預設值的型別。 例如，如果 `returnType` 是 `double` ，則應該將 `defaultValue` 設定為像是0.0，而不只是0，否則類型不相符將會在執行時間觸發例外狀況。

可系結屬性也很常見，可包含：

- `propertyChanged`：當屬性變更值時，所呼叫的靜態方法。 第一個引數是其屬性已變更之類別的實例。

的其他引數 `BindableProperty.Create` 並不常見：

- `defaultBindingMode`：用於與資料系結 (的連接，如第 [**16 章所述。資料**](chapter16.md) 系結) 
- `validateValue`：用來檢查有效值的回呼
- `propertyChanging`：用來指出屬性即將變更之時間的回呼
- `coerceValue`：將集合值強制轉型為另一個值的回呼
- `defaultValueCreate`：用來建立預設值的回呼，此值無法在類別的實例之間共用 (例如，集合) 

### <a name="the-read-only-bindable-property"></a>唯讀可系結屬性

可系結屬性可以是唯讀的。 建立唯讀可系結屬性需要呼叫靜態方法 [ `BindableProperty.CreateReadOnly` ] (x： Xamarin.Forms 。BindableProperty. CreateReadOnly (System.string、System.string、system.object、 Xamarin.Forms system.string、。BindingMode， Xamarin.Forms 。BindableProperty. ValidateValueDelegate、 Xamarin.Forms 。BindableProperty. BindingPropertyChangedDelegate、 Xamarin.Forms 。BindableProperty. BindingPropertyChangingDelegate、 Xamarin.Forms 。BindableProperty. CoerceValueDelegate、 Xamarin.Forms 。BindableProperty. CreateDefaultValueDelegate) # A3 來定義類型的私用靜態唯讀欄位 [`BindablePropertyKey`](xref:Xamarin.Forms.BindablePropertyKey) 。

然後，將 CLR 屬性 `set` accesor 定義為 `private` ，以呼叫 [ `SetValue` ] (x： Xamarin.Forms 。BindableObject (Xamarin.Forms 。BindablePropertyKey、System.object) # A3 多載與 `BindablePropertyKey` 物件。 這可防止屬性在類別之外進行設定。

這會在 [`CountedLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs)  [**BaskervillesCount**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount) 範例中使用的類別中示範。

## <a name="related-links"></a>相關連結

- [第11章完整文字 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [第11章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [可系結屬性](~/xamarin-forms/xaml/bindable-properties.md)
