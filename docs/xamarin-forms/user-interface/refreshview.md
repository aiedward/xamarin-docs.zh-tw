---
title: Xamarin.FormsRefreshView
description: Xamarin.FormsRefreshView 是一個容器控制項，可提供可滾動內容的提取至重新整理功能。
ms.prod: xamarin
ms.assetId: 58DBD23B-ADB9-40DA-B331-4DDB6E698990
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/19/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d84e6bb6ed41f2fbc213cd15051d071521f588cd
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84127591"
---
# <a name="xamarinforms-refreshview"></a>Xamarin.FormsRefreshView

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-refreshviewdemo/)

`RefreshView`是一個容器控制項，可提供可滾動內容的提取至重新整理功能。 因此，的子系 `RefreshView` 必須是可滾動的控制項，例如 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 、 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 或 [`ListView`](xref:Xamarin.Forms.ListView) 。

`RefreshView` 會定義下列屬性：

- `Command`，屬於類型 `ICommand` ，會在觸發重新整理時執行。
- `CommandParameter`，屬於 `object` 類型，這是傳遞至 `Command` 的參數。
- `IsRefreshing`，屬於類型 `bool` ，表示的目前狀態 `RefreshView` 。
- `RefreshColor`，屬於類型 `Color` ，這是重新整理期間所顯示的進度圓形色彩。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

> [!NOTE]
> 在通用 Windows 平臺上， `RefreshView` 可以使用平臺特定的來設定的提取方向。 如需詳細資訊，請參閱[RefreshView 提取方向](~/xamarin-forms/platform/windows/refreshview-pulldirection.md)。

## <a name="create-a-refreshview"></a>建立 RefreshView

下列範例顯示如何 `RefreshView` 在 XAML 中具現化：

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <ScrollView>
        <FlexLayout Direction="Row"
                    Wrap="Wrap"
                    AlignItems="Center"
                    AlignContent="Center"
                    BindableLayout.ItemsSource="{Binding Items}"
                    BindableLayout.ItemTemplate="{StaticResource ColorItemTemplate}" />
    </ScrollView>
</RefreshView>
```

`RefreshView`也可以在程式碼中建立：

```csharp
RefreshView refreshView = new RefreshView();
ICommand refreshCommand = new Command(() =>
{
    // IsRefreshing is true
    // Refresh data here
    refreshView.IsRefreshing = false;
});
refreshView.Command = refreshCommand;

ScrollView scrollView = new ScrollView();
FlexLayout flexLayout = new FlexLayout { ... };
scrollView.Content = flexLayout;
refreshView.Content = scrollView;
```

在此範例中， `RefreshView` 會提供將重新整理功能給的 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 子系為的 [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) 。 會使用可系結的配置，藉由系結 `FlexLayout` 至專案集合來產生其內容，並使用來設定每個專案的外觀 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 如需可系結版面配置的詳細資訊，請參閱[中 Xamarin.Forms ](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)的可系結配置。

屬性的值 `RefreshView.IsRefreshing` 表示的目前狀態 `RefreshView` 。 當使用者觸發重新整理時，這個屬性會自動轉換為 `true` 。 重新整理完成後，您應該將屬性重設為 `false` 。

當使用者起始重新整理時， `ICommand` `Command` 會執行由屬性定義的，這應該會重新整理所顯示的專案。 重新整理視覺效果會在進行重新整理時顯示，這是由動畫的進度圓形所組成：

[![在 iOS 和 Android 上 RefreshView 重新整理資料的螢幕擷取畫面](refreshview-images/default-progress-circle.png "RefreshView 重新整理資料")](refreshview-images/default-progress-circle-large.png#lightbox "RefreshView 重新整理資料")

> [!NOTE]
> `IsRefreshing`將屬性手動設定為 `true` 將會觸發重新整理視覺效果，並執行 `ICommand` 屬性所定義的 `Command` 。

## <a name="refreshview-appearance"></a>RefreshView 外觀

除了 `RefreshView` 繼承自類別的屬性之外 [`VisualElement`](xref:Xamarin.Forms.VisualElement) ，也會 `RefreshView` 定義 `RefreshColor` 屬性。 您可以設定這個屬性來定義重新整理期間所顯示的進度圓形色彩：

```xaml
<RefreshView RefreshColor="Teal"
             ... />
```

下列螢幕擷取畫面顯示已 `RefreshView` `RefreshColor` 設定屬性的：

[![在 iOS 和 Android 上具有青色進度圓形的 RefreshView 螢幕擷取畫面](refreshview-images/teal-progress-circle.png "具有青色進度圓形的 RefreshView")](refreshview-images/teal-progress-circle-large.png#lightbox "具有青色進度圓形的 RefreshView")

此外，您 `BackgroundColor` 可以將屬性設定為 [`Color`](xref:Xamarin.Forms.Color) ，代表進度圓形的背景色彩。

> [!NOTE]
> 在 iOS 上， `BackgroundColor` 屬性 `UIView` 會設定包含進度圓形之的背景色彩。

## <a name="disable-a-refreshview"></a>停用 RefreshView

應用程式可能會進入 [提取至重新整理] 不是有效作業的狀態。 在這種情況下， `RefreshView` 可以藉由將其 `IsEnabled` 屬性設為來停用 `false` 。 這會讓使用者無法觸發提取以重新整理。

或者，在定義 `Command` 屬性時， `CanExecute` 可以指定的委派 `ICommand` 來啟用或停用命令。

## <a name="related-links"></a>相關連結

- [RefreshView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-refreshviewdemo/)
- [中可系結的版面配置Xamarin.Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
- [RefreshView 提取方向平臺特定](~/xamarin-forms/platform/windows/refreshview-pulldirection.md)
