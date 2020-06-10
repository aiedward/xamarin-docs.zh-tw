---
標題：「Android 上的填補和遮蔽」描述：「平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的，其使用 Android 按鈕的預設填補和陰影值。」
assetid： BD2B60D1-DE6E-4691-A777-8EC5F560A4E9 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. date： 07/10/2018 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="button-padding-and-shadows-on-android"></a>Android 上的按鈕填補和陰影

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定控制按鈕是否 Xamarin.Forms 使用 Android 按鈕的預設填補和陰影值。 它會在 XAML 中使用，方法是將 [`Button.UseDefaultPadding`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPaddingProperty) 和 [`Button.UseDefaultShadow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadowProperty) 附加屬性設定為 `boolean` 值：

```xaml
<ContentPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button ...
                android:Button.UseDefaultPadding="true"
                android:Button.UseDefaultShadow="true" />         
    </StackLayout>
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

button.On<Android>().SetUseDefaultPadding(true).SetUseDefaultShadow(true);
```

`Button.On<Android>`方法會指定此平臺特定僅在 Android 上執行。 [ `Button.SetUseDefaultPadding` ] （X： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. SetUseDefaultPadding （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。Button}、system.string）和 [ `Button.SetUseDefaultShadow` ] （x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. SetUseDefaultShadow （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。[Button}，system.string）] 方法（在 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 命名空間中）用來控制按鈕是否 Xamarin.Forms 使用 Android 按鈕的預設填補和陰影值。 此外，[ `Button.UseDefaultPadding` ] （x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. UseDefaultPadding （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。按鈕}））和 [ `Button.UseDefaultShadow` ] （x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. UseDefaultShadow （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。按鈕}））方法可以用來傳回按鈕是否分別使用預設的填補值和預設的陰影值。

結果是 Xamarin.Forms 按鈕可以使用 Android 按鈕的預設填補和陰影值：

![](button-padding-shadow-images/button-padding-and-shadow.png "Default Padding and Shadow Values on Android Buttons")

請注意，在上面的螢幕擷取畫面中，每個 [`Button`](xref:Xamarin.Forms.Button) 都有相同的定義，不同之處在于右側會 `Button` 使用 Android 按鈕的預設填補和陰影值。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
