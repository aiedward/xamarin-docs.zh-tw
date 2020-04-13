---
title: 第11章摘要。 可結合基礎架構
description: 使用 Xamarin.表單創建行動應用程式:第 11 章摘要。 可結合基礎架構
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: f9e3326c0f55469cfa84a019a674679d82dfc007
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334296"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>第11章摘要。 可結合基礎架構

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)

每個C#程式師都熟悉C#*屬性*。 屬性包含*集*訪問器和/或*獲取*訪問器。 它們通常是通用語言執行時的*CLR 屬性*。

Xamarin.Forms 定義了一個增強的屬性定義,稱為[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)由 類封裝[`BindableObject`](xref:Xamarin.Forms.BindableObject)並由 類支援的*可綁定屬性*。 這些類是相關的,但非常不同:`BindableProperty`用於定義屬性本身;`BindableObject`就像在它`object`定義可綁定屬性的類的基類中一樣。

## <a name="the-xamarinforms-class-hierarchy"></a>Xamarin.Forms 類別結構

[**ClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy)示例使用反射來顯示 Xamarin.Forms 的類層次結構,並演示此`BindableObject`層次結構中 所扮演的關鍵作用。 `BindableObject`派生自`Object`和[`Element`](xref:Xamarin.Forms.Element)[`VisualElement`](xref:Xamarin.Forms.VisualElement)是 派生到的父類。 這是[`Page`](xref:Xamarin.Forms.Page)到[`View`](xref:Xamarin.Forms.View)和 的父類[`Layout`](xref:Xamarin.Forms.Layout),它是 要 的父類:

[![類別結構共用的三重螢幕截圖](images/ch11fg01-small.png "類別結構共用")](images/ch11fg01-large.png#lightbox "類別結構共用")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>窺視可結合物件及可結合屬性

在派生自`BindableObject`許多 CLR 屬性的類中,表示由可綁定屬性「備份」。。 [`Text`](xref:Xamarin.Forms.Label.Text)例如,`Label`類的屬性是 CLR`Label`屬性, 但類[`TextProperty`](xref:Xamarin.Forms.Label.TextProperty)還定義了名為的公共靜態唯`BindableProperty`讀欄位,

應用程式可以設定或取得`Text`正常的屬性`Label`,或者應用程式可以透過呼`Text`[`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))`BindableObject``Label.TextProperty`叫參數定義的方法來設定 。 同樣,應用程式可以通過調`Text`[`GetValue`](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty))用 方法(`Label.TextProperty`再次使用 參數)來獲取屬性的值。 屬性[**設置**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings)範例演示了這一點。

實際上`Text`,CLR`SetValue`屬性完全`GetValue``BindableObject``Label.TextProperty`使用與靜態屬性結合定義和 的方法實現。

`BindableObject`並為`BindableProperty`:

- 提供屬性預設值
- 儲存目前的目前值
- 提供驗證屬性值的機制
- 保持單個類中相關屬性之間的一致性
- 回應屬性變更
- 當屬性即將變更或已變更時觸發通知
- 支援資料繫結
- 支援樣式
- 支援動態資源

每當由可綁定屬性支援的屬性發生更改時,`BindableObject`將觸發標識[`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged)已更改的屬性的事件。 當屬性設置為相同的值時,不會觸發此事件。

某些屬性&mdash;不由可綁定屬性支援,某些 Xamarin.窗體類`Span`&mdash;(如不派`BindableObject`生自 ) 只有派生`BindableObject`自的類才能支援可綁定屬性`BindableObject`, 因為定義`GetValue``SetValue`和。

因為`Span``BindableObject`不派生自 ,&mdash;因此其 屬性`Text`&mdash;(如) 都不是由可綁定屬性支援的。 `DynamicResource`這就是為什麼上一章中的`Text` [**DynamicVStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic)示例中`Span`對 屬性 設置引發異常的原因。 [**DynamicVStaticCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode)範例示範如何使用[`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource(Xamarin.Forms.BindableProperty,System.String))`Element`定義的方法在代碼中設置動態資源。 第一個參數是類型的`BindableProperty`物件。

同樣,[`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))定義`BindableObject`的方法具有`BindableProperty`type 的第一個參數。

## <a name="defining-bindable-properties"></a>定義可結合屬性

可以使用靜態[`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法定義自己的可綁定屬性,以創建類型`BindableProperty`的 靜態唯讀欄位。

這在[`AltLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs)[**Xamarin.FormsBook.工具套件**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類中演示。 該類派生自`Label`,允許您以磅指定字體大小。 它在[**PointSized 文字**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText)範例中演示。

`BindableProperty.Create`該方法需要四個參數:

- `propertyName`:屬性的文字名稱(與 CLR 屬性名稱相同)
- `returnType`:CLR 屬性的類型
- `declaringType`:宣告屬性的類別類型
- `defaultValue`:屬性的預設值

因為`defaultValue``object`是類型,因此編譯器必須能夠確定預設值的類型。 例如,如果`returnType`為`double``defaultValue`, 應設置為 0.0 而不是僅 0 之類的內容,否則類型不匹配將在運行時觸發異常。

可結合屬性還包括:

- `propertyChanged`:屬性更改值時調用的靜態方法。 第一個參數是屬性已更改的類的實例。

要的其他參數`BindableProperty.Create`並不常見:

- `defaultBindingMode`: 用於與資料綁定相關的(如第[**16 章所述)。資料繫結**](chapter16.md))
- `validateValue`:用於檢查有效值的回調
- `propertyChanging`: 回檔,指示屬性何時即將更改
- `coerceValue`:將集值強制為另一個值的回調
- `defaultValueCreate`:呼叫,以建立無法在類的實體(例如集合)之間共用的預設值

### <a name="the-read-only-bindable-property"></a>唯讀可結合屬性

可綁定屬性可以是唯讀的。 創建唯讀可綁定屬性需要呼叫靜態方法[`BindableProperty.CreateReadOnly`](xref:Xamarin.Forms.BindableProperty.CreateReadOnly(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))來定義類型[`BindablePropertyKey`](xref:Xamarin.Forms.BindablePropertyKey)的專用靜態唯讀欄位。

然後,將`set`CLR 屬性`private`accor[`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindablePropertyKey,System.Object))`BindablePropertyKey`定義為調用 物件重載。 這可以防止在類之外設置該屬性。

這在[**巴斯克維爾統計**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount)示[`CountedLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs)例 中使用的類中演示。

## <a name="related-links"></a>相關連結

- [第11章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [第11章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [可繫結的屬性](~/xamarin-forms/xaml/bindable-properties.md)
