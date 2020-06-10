---
標題：「iOS 上的 VisualElement 第一個回應程式」描述：「平臺詳細資訊可讓您使用僅適用于特定平臺的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，讓 VisualElement 物件成為觸控事件的第一個回應者。」
assetid： 3A77BA02-B87A-44EC-AC51-9D3130EF314C ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：01/15/2020 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="visualelement-first-responder-on-ios"></a>在 iOS 上 VisualElement 第一個回應程式

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定可讓 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 物件成為觸控事件的第一個回應程式，而不是包含專案的頁面。 將可系結屬性設定為，即可在 XAML 中使用它 `VisualElement.CanBecomeFirstResponder` `true` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry Placeholder="Enter text" />
        <Button ios:VisualElement.CanBecomeFirstResponder="True"
                Text="OK" />
    </StackLayout>
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Entry entry = new Entry { Placeholder = "Enter text" };
Button button = new Button { Text = "OK" };
button.On<iOS>().SetCanBecomeFirstResponder(true);
```

`VisualElement.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 `VisualElement.SetCanBecomeFirstResponder`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 會用來設定， `VisualElement` 成為觸控事件的第一個回應程式。 此外， `VisualElement.CanBecomeFirstResponder` 方法可以用來傳回是否 `VisualElement` 為觸控事件的第一個回應程式。

結果是， [`VisualElement`](xref:Xamarin.Forms.VisualElement) 可以成為觸控事件的第一個回應程式，而不是包含專案的頁面。 這可讓聊天應用程式之類的案例不會在按下時關閉鍵盤 [`Button`](xref:Xamarin.Forms.Button) 。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
