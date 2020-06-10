---
標題：「Android 上的 ListView 快速滾動」描述：「平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的，讓您能夠快速地透過 ListView 中的資料進行滾動。」
assetid： 37D95A2D-74AC-488A-B903-2BDD799EAA5C ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：07/10/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="listview-fast-scrolling-on-android"></a>ListView 在 Android 上快速滾動

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定是用來啟用透過中資料的快速滾動 [`ListView`](xref:Xamarin.Forms.ListView) 。 它會在 XAML 中使用，方法是將 `ListView.IsFastScrollEnabled` 附加屬性設定為 `boolean` 值：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        ...
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  GroupDisplayBinding="{Binding Key}"
                  IsGroupingEnabled="true"
                  android:ListView.IsFastScrollEnabled="true">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

`ListView.On<Android>`方法會指定此平臺特定僅在 Android 上執行。 `ListView.SetIsFastScrollEnabled`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 是用來啟用透過中資料的快速滾動 [`ListView`](xref:Xamarin.Forms.ListView) 。 此外， `SetIsFastScrollEnabled` 方法可以藉由呼叫方法來切換快速滾動，以傳回 `IsFastScrollEnabled` 是否啟用快速滾動：

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

結果是可以啟用透過中的資料快速滾動 [`ListView`](xref:Xamarin.Forms.ListView) ，這會變更捲動方塊的大小：

[![](listview-fast-scrolling-images/fastscroll.png "ListView FastScroll Platform-Specific")](listview-fast-scrolling-images/fastscroll-large.png#lightbox "ListView FastScroll Platform-Specific")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
