---
標題：「Android 上的 ViewCell 內容動作」描述：「平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用可啟用 ViewCell 內容動作舊版模式的 Android 平臺特定。
ms-chap： xamarin assetid： 8BD71B10-5037-443F-9975-B941CE393E5A ms. 技術： xamarin-表單作者： davidbritch ms-chap： dabritch ms. date： 09/24/2019 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="viewcell-context-actions-on-android"></a>Android 上的 ViewCell 內容動作

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

根據4.3 預設 Xamarin.Forms ，當 [`ViewCell`](xref:Xamarin.Forms.ViewCell) Android 應用程式中的為中的每個專案定義內容動作時 [`ListView`](xref:Xamarin.Forms.ListView) ，當中選取的專案變更時，就會更新內容動作功能表 `ListView` 。 不過，在舊版的 Xamarin.Forms 內容動作功能表並未更新，而且這種行為稱為 `ViewCell` 舊版模式。 如果 `ListView` 使用 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) `ItemTemplate` 從 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 定義不同內容動作的物件設定其，則此舊版模式可能會導致不正確的行為。

此 Android 平臺特定會啟用 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 內容動作功能表舊版模式，以提供回溯相容性，以便在變更中選取的專案時，不會更新內容動作功能表 [`ListView`](xref:Xamarin.Forms.ListView) 。 將可系結屬性設定為，即可在 XAML 中使用它 `ViewCell.IsContextActionsLegacyModeEnabled` `true` ：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ItemsSource="{Binding Items}">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell android:ViewCell.IsContextActionsLegacyModeEnabled="true">
                        <ViewCell.ContextActions>
                            <MenuItem Text="{Binding Item1Text}" />
                            <MenuItem Text="{Binding Item2Text}" />
                        </ViewCell.ContextActions>
                        <Label Text="{Binding Text}" />
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

viewCell.On<Android>().SetIsContextActionsLegacyModeEnabled(true);
```

`ViewCell.On<Android>`方法會指定此平臺特定僅在 Android 上執行。 `ViewCell.SetIsContextActionsLegacyModeEnabled`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 可用來啟用 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 內容動作功能表舊版模式，因此當變更中的選取專案時，不會更新內容動作功能表 [`ListView`](xref:Xamarin.Forms.ListView) 。 此外， `ViewCell.GetIsContextActionsLegacyModeEnabled` 方法可以用來傳回是否已啟用內容動作的舊版模式。

下列螢幕擷取畫面顯示 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 已啟用的內容動作舊版模式：

![在 Android 上啟用 ViewCell 舊版模式的螢幕擷取畫面](viewcell-context-actions-images/legacy-mode-enabled.png "ViewCell 舊版模式已啟用")

在此模式中，儘管針對資料格2定義了不同的內容功能表項目，顯示的內容動作功能表項目與儲存格1和資料格2相同。

下列螢幕擷取畫面顯示 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 已停用的內容動作舊版模式，這是預設 Xamarin.Forms 行為：

![已停用 Android 上 ViewCell 舊版模式的螢幕擷取畫面](viewcell-context-actions-images/legacy-mode-disabled.png "已停用 ViewCell 舊版模式")

在此模式中，會針對儲存格1和資料格2顯示正確的內容動作功能表項目。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
