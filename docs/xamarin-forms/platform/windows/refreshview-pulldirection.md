---
標題：「Windows 上的 RefreshView 提取方向」描述：「平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Windows 平臺特定的，讓 RefreshView 的提取方向得以變更。」
assetid： 407A862B-281E-4384-9696-C0655830B84D ms-chap。技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. date： 09/20/2019 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="refreshview-pull-direction-on-windows"></a>Windows 上的 RefreshView 提取方向

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個通用 Windows 平臺平臺特定的可讓的提取方向 `RefreshView` 變更，以符合顯示資料之可滾動控制項的方向。 它會在 XAML 中使用，方法是將可系結 `RefreshView.RefreshPullDirection` 屬性設定為列舉的值 `RefreshPullDirection` ：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <RefreshView windows:RefreshView.RefreshPullDirection="LeftToRight"
                 IsRefreshing="{Binding IsRefreshing}"
                 Command="{Binding RefreshCommand}">
        <ScrollView>
            ...
        </ScrollView>
    </RefreshView>
 </ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
refreshView.On<Windows>().SetRefreshPullDirection(RefreshPullDirection.LeftToRight);
```

`RefreshView.On<Windows>`方法會指定此平臺特定只會在通用 Windows 平臺上執行。 `RefreshView.SetRefreshPullDirection`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 會用來設定的提取方向 `RefreshView` ， `RefreshPullDirection` 列舉會提供四個可能的值：

- `LeftToRight`表示從左至右提取會起始重新整理。
- `TopToBottom`表示從上到下的提取會起始重新整理，而且是的預設提取方向 `RefreshView` 。
- `RightToLeft`表示從右至左提取會起始重新整理。
- `BottomToTop`表示從底部到頂端的提取會起始重新整理。

此外， `GetRefreshPullDirection` 方法可以用來傳回目前 `RefreshPullDirection` 的 `RefreshView` 。

結果是指定的會套用 `RefreshPullDirection` 至 `RefreshView` ，以設定提取方向以符合顯示資料之可滾動控制項的方向。 下列螢幕擷取畫面顯示 `RefreshView` 具有 `LeftToRight` 提取方向的：

[![UWP 上由左到右提取方向的 RefreshView 螢幕擷取畫面](refreshview-pulldirection-images/refreshview-pulldirection.png "具有由左到右提取方向的 RefreshView")](refreshview-pulldirection-images/refreshview-pulldirection-large.png#lightbox "具有由左到右提取方向的 RefreshView")

> [!NOTE]
> 當您變更提取方向時，進度圓形的開始位置會自動旋轉，讓箭號在適當的提取方向位置上啟動。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
