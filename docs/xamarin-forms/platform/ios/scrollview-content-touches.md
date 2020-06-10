---
title： "ScrollView Content 觸控 on iOS" 描述：「平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的來控制 ScrollView 是否處理觸控手勢，或將它傳遞至其內容。」
assetid： 99F823DB-B379-40F0-A343-A9783C341120 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：10/24/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="scrollview-content-touches-on-ios"></a>IOS 上的 ScrollView 內容潤色

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

當觸控筆勢在 iOS 上開始時，會觸發隱含計時器， [`ScrollView`](xref:Xamarin.Forms.ScrollView) 並 `ScrollView` 根據計時器範圍內的使用者動作決定是否應處理手勢，或將其傳遞至其內容。 根據預設，iOS 會 `ScrollView` 延遲內容的觸控，但在某些情況下，這可能會造成問題，而 `ScrollView` 內容不會在發生時獲勝。 因此，此平臺特定會控制是否 `ScrollView` 處理觸控手勢，或將其傳遞至其內容。 它會在 XAML 中使用，方法是將 `ScrollView.ShouldDelayContentTouches` 附加屬性設定為 `boolean` 值：

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <MasterDetailPage.Master>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

`ScrollView.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 `ScrollView.SetShouldDelayContentTouches`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 可用來控制是否 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 處理觸控手勢，或將它傳遞至其內容。 此外， `SetShouldDelayContentTouches` 方法可以藉由呼叫 `ShouldDelayContentTouches` 方法，以傳回是否延遲內容的觸控，來切換延遲內容的觸控：

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

結果是， [`ScrollView`](xref:Xamarin.Forms.ScrollView) 可以停用延遲接收內容的觸控，因此在此案例中，會 [`Slider`](xref:Xamarin.Forms.Slider) 接收手勢，而不是的 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 頁面 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) ：

[![](scrollview-content-touches-images/scrollview-delay-content-touches.png "ScrollView Delay Content Touches Platform-Specific")](scrollview-content-touches-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView Delay Content Touches Platform-Specific")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
