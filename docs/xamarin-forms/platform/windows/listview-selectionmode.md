---
標題：「Windows 上的 ListView SelectionMode」描述：「平臺特定的可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Windows 平臺特定的來控制 ListView 中的專案是否可以回應點按手勢。」
assetid： 57EF3A7F-1407-4B31-AE21-D149293D4228 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：10/24/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="listview-selectionmode-on-windows"></a>Windows 上的 ListView SelectionMode

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

根據預設，在通用 Windows 平臺上，會 Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 使用原生 `ItemClick` 事件來回應互動，而不是原生 `Tapped` 事件。 這會提供協助工具功能，讓 Windows 朗讀程式和鍵盤可以與互動 `ListView` 。 不過，它也會在無法運作的內呈現任何攻點手勢 `ListView` 。

這個通用 Windows 平臺平臺特定控制項中的專案是否 [`ListView`](xref:Xamarin.Forms.ListView) 可以回應點按手勢，以及原生是否 `ListView` 引發 `ItemClick` 或 `Tapped` 事件。 它會在 XAML 中使用，方法是將 [`ListView.SelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty) 附加屬性設為列舉的值 [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) ：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <ListView ... windows:ListView.SelectionMode="Inaccessible">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

`ListView.On<Windows>`方法會指定此平臺特定只會在通用 Windows 平臺上執行。 [ `ListView.SetSelectionMode` ] （X： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. SetSelectionMode （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。ListView}、 Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. ListViewSelectionMode））方法，在 [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 命名空間中，可用來控制中的專案是否 [`ListView`](xref:Xamarin.Forms.ListView) 可以回應點按手勢，列舉會 [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) 提供兩個可能的值：

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible)–表示 `ListView` 將會引發原生 `ItemClick` 事件來處理互動，因而提供協助工具功能。 因此，Windows 朗讀程式和鍵盤可以與互動 `ListView` 。 不過，中的專案 `ListView` 無法回應點碰手勢。 這是 `ListView` 通用 Windows 平臺上實例的預設行為。
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible)–表示 `ListView` 將會引發原生 `Tapped` 事件以處理互動。 因此，中的專案 `ListView` 可以回應點碰手勢。 不過，沒有任何協助工具功能，因此 Windows 朗讀程式和鍵盤無法與互動 `ListView` 。

> [!NOTE]
> `Accessible`和 `Inaccessible` 選取模式是互斥的，而且您必須在 [`ListView`](xref:Xamarin.Forms.ListView) 可回應點按手勢的可存取或之間選擇 `ListView` 。

此外，[ `GetSelectionMode` ] （x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. GetSelectionMode （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。ListView}））方法可以用來傳回目前的 [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) 。

結果是，會將指定的套用 [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) 至 [`ListView`](xref:Xamarin.Forms.ListView) ，以控制中的專案是否 `ListView` 可以回應點按手勢，以及是否要 `ListView` 引發 `ItemClick` 或 `Tapped` 事件。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
