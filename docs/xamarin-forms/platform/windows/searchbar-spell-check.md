---
標題：「Windows 上的搜尋列拼寫檢查」描述：「平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Windows 平臺特定的，讓搜尋列與拼寫檢查引擎互動。」
assetid： 7974C91F-7502-4DB3-B0E9-C45E943DDA26 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：10/24/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="searchbar-spell-check-on-windows"></a>Windows 上的搜尋列拼寫檢查

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個通用 Windows 平臺平臺特定的可讓與 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 拼寫檢查引擎互動。 它會在 XAML 中使用，方法是將 [`SearchBar.IsSpellCheckEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) 附加屬性設定為 `boolean` 值：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

`SearchBar.On<Windows>`方法會指定此平臺特定只會在通用 Windows 平臺上執行。 [ `SearchBar.SetIsSpellCheckEnabled` ] （X： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. 搜尋列. SetIsSpellCheckEnabled （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。搜尋列}，system.string）方法，在 [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 命名空間中，開啟和關閉拼寫檢查。 此外， `SearchBar.SetIsSpellCheckEnabled` 方法可以藉由呼叫 [ `SearchBar.GetIsSpellCheckEnabled` ] （x：，用來切換拼寫檢查 Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. 搜尋列. GetIsSpellCheckEnabled （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。搜尋列}））方法，以傳回是否已啟用拼寫檢查：

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

結果是在中輸入的文字 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 可以進行拼寫檢查，並對使用者指出不正確的拼寫：

![搜尋列拼寫檢查平臺特定](searchbar-spell-check-images/searchbar-spellcheck.png "搜尋列拼寫檢查平臺特定")

> [!NOTE]
> `SearchBar`命名空間中的類別 [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 也具有 [`EnableSpellCheck`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*) 和 [`DisableSpellCheck`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*) 方法，可用來分別在上啟用和停用拼寫檢查 `SearchBar` 。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
