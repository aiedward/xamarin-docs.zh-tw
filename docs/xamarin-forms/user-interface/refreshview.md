---
title: Xamarin.Forms RefreshView
description: Xamarin.Forms RefreshView 是一個容器控制項，可針對可滾動的內容提供提取以重新整理功能。
ms.prod: xamarin
ms.assetId: 58DBD23B-ADB9-40DA-B331-4DDB6E698990
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/19/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
- RefreshView
- Universal Windows Platform
ms.openlocfilehash: 470093465191897a56cd54a6edaf828afbf40e11
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372869"
---
# <a name="no-locxamarinforms-no-locrefreshview"></a>Xamarin.Forms RefreshView

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-refreshviewdemo/)

`RefreshView`是一個容器控制項，可針對可滾動的內容提供提取以重新整理功能。 因此，的子系 `RefreshView` 必須是可滾動的控制項，例如 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 、 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 或 [`ListView`](xref:Xamarin.Forms.ListView) 。

`RefreshView` 會定義下列屬性：

- `Command`，類型為 `ICommand` ，在觸發重新整理時執行。
- `CommandParameter`，屬於 `object` 類型，這是傳遞至 `Command` 的參數。
- `IsRefreshing`，類型為 `bool` ，表示的目前狀態 `RefreshView` 。
- `RefreshColor`，屬於類型，在重新整理 `Color` 期間出現的進度圓圈色彩。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

> [!NOTE]
> 在上 Universal Windows Platform ，的提取方向 `RefreshView` 可以使用平臺特定來設定。 如需詳細資訊，請參閱[ RefreshView 提取方向](~/xamarin-forms/platform/windows/refreshview-pulldirection.md)。

## <a name="create-a-no-locrefreshview"></a>建立 RefreshView

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

您 `RefreshView` 也可以在程式碼中建立：

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

在此範例中，會將的提取重新整理 `RefreshView` 功能提供給 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 其子系為的 [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) 。 會使用可系結配置，藉由系結 `FlexLayout` 至專案的集合來產生其內容，並使用來設定每個專案的外觀 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 如需可系結配置的詳細資訊，請參閱[中 Xamarin.Forms ](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)的可系結版面配置。

屬性的值 `RefreshView.IsRefreshing` 表示的目前狀態 `RefreshView` 。 當使用者觸發重新整理時，這個屬性會自動轉換為 `true` 。 重新整理完成之後，您應該將屬性重設為 `false` 。

當使用者起始重新整理時， `ICommand` `Command` 會執行由屬性定義的，這應該會重新整理所顯示的專案。 重新整理時，會顯示重新整理視覺效果，這是由動畫的進度圓形所組成：

[![螢幕擷取畫面：在 iOS 和 Android 上，：：： no loc (Refreshview 拖動) ：：：重新整理資料](refreshview-images/default-progress-circle.png "：：：非 loc (Refreshview 拖動) ：：：重新整理資料")](refreshview-images/default-progress-circle-large.png#lightbox "：：：非 loc (Refreshview 拖動) ：：：重新整理資料")

> [!NOTE]
> 將屬性手動設定 `IsRefreshing` 為 `true` 將會觸發重新整理視覺效果，並且會執行 `ICommand` 由屬性定義的 `Command` 。

## <a name="no-locrefreshview-appearance"></a>RefreshView 外觀

除了 `RefreshView` 繼承自類別的屬性之外 [`VisualElement`](xref:Xamarin.Forms.VisualElement) ，也會 `RefreshView` 定義 `RefreshColor` 屬性。 您可以設定這個屬性來定義在重新整理期間出現的進度圓圈色彩：

```xaml
<RefreshView RefreshColor="Teal"
             ... />
```

下列螢幕擷取畫面顯示 `RefreshView` 包含 `RefreshColor` 屬性集的：

[![IOS 和 Android 上的：：：非 loc (Refreshview 拖動) ：：：具有青綠色進度圓圈的螢幕擷取畫面](refreshview-images/teal-progress-circle.png "：：：非 loc (Refreshview 拖動) ：：：具有青綠色的進度圓圈")](refreshview-images/teal-progress-circle-large.png#lightbox "：：：非 loc (Refreshview 拖動) ：：：具有青綠色的進度圓圈")

此外， `BackgroundColor` 也可以將屬性設定為 [`Color`](xref:Xamarin.Forms.Color) ，代表進度圓形的背景色彩。

> [!NOTE]
> 在 iOS 上， `BackgroundColor` 屬性 `UIView` 會設定包含進度圓形之的背景色彩。

## <a name="disable-a-no-locrefreshview"></a>停用 RefreshView

應用程式可能會進入狀態，其中的提取重新整理不是有效的作業。 在這種情況下，您 `RefreshView` 可以藉由將其 `IsEnabled` 屬性設定為來停用 `false` 。 這會讓使用者無法觸發提取以重新整理。

或者，在定義 `Command` 屬性時， `CanExecute` 可以指定的委派 `ICommand` 來啟用或停用此命令。

## <a name="related-links"></a>相關連結

- [RefreshView (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-refreshviewdemo/)
- [中的可系結版面配置 Xamarin.Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
- [RefreshView 特定平臺的提取方向](~/xamarin-forms/platform/windows/refreshview-pulldirection.md)