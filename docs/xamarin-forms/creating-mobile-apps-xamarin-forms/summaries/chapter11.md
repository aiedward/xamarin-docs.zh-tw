---
title: 第11章的摘要。 可系結的基礎結構
description: 建立 Mobile Apps Xamarin.Forms ：第11章的摘要。 可系結的基礎結構
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: edc3dfd97457fe93a04edd82574f6ed419f5fdc1
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136795"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>第11章的摘要。 可系結的基礎結構

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)

每個 c # 程式設計人員都熟悉 c #*屬性*。 屬性包含*set*存取子和（或） *get*存取子。 它們通常稱為 Common Language Runtime 的*CLR 屬性*。

Xamarin.Forms定義名為可系結屬性的增強屬性定義，該*屬性*是由類別所封裝 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，並受到 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 類別支援。 這些類別是相關的，但截然不同： `BindableProperty` 用來定義屬性本身; 類似于 `BindableObject` `object` ，這是定義可系結屬性之類別的基類。

## <a name="the-xamarinforms-class-hierarchy"></a>類別階層架構 Xamarin.Forms

[**ClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy)範例會使用反映來顯示的類別階層 Xamarin.Forms ，並示範此階層中所扮演的重要角色 `BindableObject` 。 `BindableObject`衍生自 `Object` ，而且是 [`Element`](xref:Xamarin.Forms.Element) 衍生來源的父類別 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 這是和的父類別 [`Page`](xref:Xamarin.Forms.Page) [`View`](xref:Xamarin.Forms.View) ，也就是的父類別 [`Layout`](xref:Xamarin.Forms.Layout) ：

[![類別階層共用的三向螢幕擷取畫面](images/ch11fg01-small.png "類別階層共用")](images/ch11fg01-large.png#lightbox "類別階層共用")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>查看 BindableObject 和 BindableProperty

在衍生自許多 CLR 屬性的類別中，稱為「受支援」可系結 `BindableObject` 屬性。 例如，類別的 [`Text`](xref:Xamarin.Forms.Label.Text) 屬性 `Label` 是 CLR 屬性，但 `Label` 類別也定義了一個型別為的公用靜態唯讀欄位 [`TextProperty`](xref:Xamarin.Forms.Label.TextProperty) `BindableProperty` 。

應用程式可以設定或取得 `Text` 一般的屬性 `Label` ，或者應用程式可以藉 `Text` 由呼叫 [ `SetValue` ] （x：）來設定。 Xamarin.FormsBindableObject. SetValue （ Xamarin.Forms 。BindableProperty，System.object））方法，由 `BindableObject` 引數所定義 `Label.TextProperty` 。 同樣地，應用程式可以藉 `Text` 由呼叫 [ `GetValue` ] （x：，取得屬性的值 Xamarin.Forms 。BindableObject. GetValue （ Xamarin.Forms 。BindableProperty））方法，再次使用 `Label.TextProperty` 引數。 這是由[**PropertySettings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings)範例所示範。

事實上， `Text` CLR 屬性完全是使用與 `SetValue` `GetValue` `BindableObject` 靜態屬性一起定義的和方法來執行 `Label.TextProperty` 。

`BindableObject`和 `BindableProperty` 提供下列支援：

- 提供屬性預設值
- 儲存其目前的值
- 提供驗證屬性值的機制
- 維護單一類別中相關屬性間的一致性
- 回應屬性變更
- 當屬性即將變更或變更時觸發通知
- 支援資料系結
- 支援的樣式
- 支援動態資源

每當可系結屬性所支援的屬性變更時，就會 `BindableObject` 引發事件，以 [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) 識別已變更的屬性。 當屬性設定為相同的值時，不會引發此事件。

某些屬性不是由可系結屬性所支援，而某些 Xamarin.Forms 類別 &mdash; （例如）則 `Span` &mdash; 不是衍生自 `BindableObject` 。 只有衍生自的類別 `BindableObject` 可以支援可系結屬性 `BindableObject` ，因為會定義 `SetValue` 和 `GetValue` 方法。

因為不是 `Span` 衍生自 `BindableObject` ，所以其所有屬性 &mdash; （例如） `Text` &mdash; 都不會受到可系結屬性的支援。 這就是為什麼 `DynamicResource` `Text` 屬性的設定在 `Span` 上一章的[**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic)範例中引發例外狀況的原因。 [**DynamicVsStaticCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode)範例會示範如何使用 [ `SetDynamicResource` ] （x：，在程式碼中設定動態資源 Xamarin.Forms 。SetDynamicResource （ Xamarin.Forms 。BindableProperty，System.string））方法所定義 `Element` 。 第一個引數是類型的物件 `BindableProperty` 。

同樣地，[ `SetBinding` ] （x： Xamarin.Forms 。BindableObject. SetBinding （ Xamarin.Forms 。BindableProperty， Xamarin.Forms 。System.windows.data.bindingbase.delay））所定義的方法 `BindableObject` 具有類型的第一個引數 `BindableProperty` 。

## <a name="defining-bindable-properties"></a>定義可系結屬性

您可以使用 static [ `BindableProperty.Create` ] （x：）來定義您自己的可系結屬性 Xamarin.Forms 。BindableProperty。 Create （System.string，System.object，type，system.object Xamarin.Forms ，，BindingMode， Xamarin.Forms 。BindableProperty. ValidateValueDelegate、 Xamarin.Forms 。BindableProperty. BindingPropertyChangedDelegate、 Xamarin.Forms 。BindableProperty. BindingPropertyChangingDelegate、 Xamarin.Forms 。BindableProperty. CoerceValueDelegate、 Xamarin.Forms 。BindableProperty. CreateDefaultValueDelegate））方法，以建立類型的靜態唯讀欄位 `BindableProperty` 。

這會在 [`AltLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫的類別中示範。 類別衍生自 `Label` ，並可讓您指定以點為單位的字型大小。 它會在[**PointSizedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText)範例中示範。

需要方法的四個引數 `BindableProperty.Create` ：

- `propertyName`：屬性的文字名稱（與 CLR 屬性名稱相同）
- `returnType`： CLR 屬性的類型
- `declaringType`：宣告屬性之類別的型別
- `defaultValue`：屬性的預設值

因為 `defaultValue` 的類型是 `object` ，所以編譯器必須能夠判斷預設值的類型。 例如，如果 `returnType` 是 `double` ，則應該將 `defaultValue` 設定為類似0.0 而不是只有0的專案，否則類型不相符將會在執行時間觸發例外狀況。

可系結的屬性也很常見，包括：

- `propertyChanged`：當屬性變更值時，所呼叫的靜態方法。 第一個引數是類別的實例，其屬性已變更。

的其他引數 `BindableProperty.Create` 不是常見的：

- `defaultBindingMode`：用於與資料系結（如第16章所述）的連接中[**。資料**](chapter16.md)系結）
- `validateValue`：用來檢查有效值的回呼
- `propertyChanging`：回呼，指出屬性即將變更的時間
- `coerceValue`：將集合值強制轉型為另一個值的回呼
- `defaultValueCreate`：用來建立預設值的回呼，無法在類別的實例之間共用（例如，集合）

### <a name="the-read-only-bindable-property"></a>唯讀可系結屬性

可系結屬性可以是唯讀的。 建立唯讀可系結屬性需要呼叫靜態方法 [ `BindableProperty.CreateReadOnly` ] （x： Xamarin.Forms 。BindableProperty. CreateReadOnly （System.string，System.object，type，system.object，， Xamarin.Forms 。BindingMode， Xamarin.Forms 。BindableProperty. ValidateValueDelegate、 Xamarin.Forms 。BindableProperty. BindingPropertyChangedDelegate、 Xamarin.Forms 。BindableProperty. BindingPropertyChangingDelegate、 Xamarin.Forms 。BindableProperty. CoerceValueDelegate、 Xamarin.Forms 。BindableProperty. CreateDefaultValueDelegate）），以定義類型的私用靜態唯讀欄位 [`BindablePropertyKey`](xref:Xamarin.Forms.BindablePropertyKey) 。

然後，將 CLR 屬性 `set` accesor 定義為， `private` 以呼叫 [ `SetValue` ] （x： Xamarin.Forms 。BindableObject. SetValue （ Xamarin.Forms 。BindablePropertyKey，System.object）多載 `BindablePropertyKey` 物件。 這可避免將屬性設定在類別之外。

這會在 [`CountedLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs) [**BaskervillesCount**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount)範例中使用的類別中示範。

## <a name="related-links"></a>相關連結

- [第11章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [第11章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [可繫結的屬性](~/xamarin-forms/xaml/bindable-properties.md)
