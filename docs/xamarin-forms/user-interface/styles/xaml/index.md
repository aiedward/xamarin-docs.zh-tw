---
標題：「 Xamarin.Forms 使用 Xaml 樣式設定應用程式的樣式」描述：「本指南說明如何 Xamarin.Forms 使用 xaml 樣式自訂應用程式的外觀」。
assetid： 344A34AA-B19A-4765-BC8A-875D9A6B5EA8 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. date： 01/30/2019 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="styling-xamarinforms-apps-using-xaml-styles"></a>Xamarin.Forms使用 XAML 樣式設定應用程式的樣式

## <a name="introduction"></a>[簡介](introduction.md)

Xamarin.Forms應用程式通常會包含多個具有相同外觀的控制項。 設定每個個別控制項的外觀可能是重複且容易出錯的工作。 相反地，您可以建立樣式，藉由群組和設定控制項類型上可用的屬性來自訂控制面板。

## <a name="explicit-styles"></a>[明確樣式](explicit.md)

*明確*樣式是藉由設定其屬性，選擇性地套用至控制項 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 。

## <a name="implicit-styles"></a>[隱含樣式](implicit.md)

*隱含*樣式是由相同的所有控制項所使用 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) ，而不需要每個控制項參考樣式。

## <a name="global-styles"></a>[全域樣式](application.md)

藉由將樣式新增至應用程式的，可以將其設為全域可供使用 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。 這有助於避免跨頁面或控制項的樣式重複。

## <a name="style-inheritance"></a>[樣式繼承](inheritance.md)

樣式可以繼承自其他樣式，以減少重複程度並讓重複使用。

## <a name="dynamic-styles"></a>[動態樣式](dynamic.md)

樣式不會回應屬性變更，而且在應用程式的持續時間內會保持不變。 不過，應用程式可以使用動態資源，在執行時間動態回應樣式變更。

## <a name="device-styles"></a>[裝置樣式](device.md)

Xamarin.Forms在類別中包含六個*動態*樣式（稱為*裝置*樣式） [`Devices.Styles`](xref:Xamarin.Forms.Device.Styles) 。 這六種樣式都只能套用至 [`Label`](xref:Xamarin.Forms.Label) 實例。

## <a name="style-classes"></a>[樣式類別](style-class.md)

Xamarin.Forms樣式類別可以讓多個樣式套用至控制項，而不需要採用樣式繼承。
