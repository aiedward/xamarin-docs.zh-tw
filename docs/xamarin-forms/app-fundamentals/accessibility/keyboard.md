---
title: 鍵盤導覽
description: 而不是使用預設的定位順序，它有時候是必要的 TabIndex 和 IsTapStop 屬性組合使用指定的索引標籤順序來微調您的 UI。
ms.prod: xamarin
ms.assetid: 8be8f498-558a-4894-a01f-91a0d3ef927e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/15/2018
ms.openlocfilehash: f703dff56d2947c35a9bc76e0eb909bfe9023bac
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131164"
---
# <a name="keyboard-navigation-in-xamarinforms"></a>在 Xamarin.Forms 中的鍵盤導覽

某些使用者可能難以使用應用程式不提供適當的鍵盤存取。 指定控制項的定位順序時，可讓鍵盤瀏覽，並準備以特定順序接收輸入的應用程式頁面。

根據預設，控制項的定位順序是它們會列在 XAML，或以程式設計方式加入子集合的相同順序。 這是在其中的控制項將瀏覽透過使用鍵盤，順序與此預設順序通常是最佳的順序。 不過，預設的順序不一定與預期的順序相同下列 XAML 程式碼範例所示：

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="0.5*" />
        <ColumnDefinition Width="0.5*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Label Text="You"
           HorizontalOptions="Center" />
    <Label Grid.Column="1"
           Text="Manager"
           HorizontalOptions="Center" />
    <Entry Grid.Row="1"
           Placeholder="Enter forename" />
    <Entry Grid.Column="1"
           Grid.Row="1"
           Placeholder="Enter forename" />
    <Entry Grid.Row="2"
           Placeholder="Enter surname" />
    <Entry Grid.Column="1"
           Grid.Row="2"
           Placeholder="Enter surname" />
</Grid>
```

下列螢幕擷取畫面顯示預設的定位順序，此程式碼範例：

![](keyboard-images/default-tab-order.png "預設的資料列為主的定位順序")

定位順序是資料列為主，控制項在 XAML 中列出的順序。 因此，按下 Tab 鍵巡覽名字[ `Entry` ](xref:Xamarin.Forms.Entry)執行個體，後面接著姓氏`Entry`執行個體。 不過，更直覺式的體驗就是使用資料行第一個索引標籤上的導覽中，以便在按下 Tab 鍵巡覽名字姓氏組。 這可藉由指定的輸入控制項的定位順序。

> [!NOTE]
> 通用 Windows 平台上的鍵盤快速鍵可定義，提供應用程式的顯示 UI，透過鍵盤而不是透過觸控或滑鼠使用者快速瀏覽並與其互動以直覺的方式。 如需詳細資訊，請參閱 <<c0> [ 設定 VisualElement 便捷鍵](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#visualelement-accesskeys)。

## <a name="setting-the-tab-order"></a>設定定位順序

`VisualElement.TabIndex`屬性用來指定的順序[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)執行個體收到焦點時使用者按下 Tab 鍵巡覽控制項。 屬性的預設值為 0，而且可以設定為任何`int`值。

下列規則適用於當使用預設的定位順序，或將`TabIndex`屬性：

 - [`VisualElement`](xref:Xamarin.Forms.VisualElement) 執行個體與`TabIndex`等於 0 會新增至定位順序根據其 XAML 或子集合中的宣告順序。
 - [`VisualElement`](xref:Xamarin.Forms.VisualElement) 執行個體`TabIndex`大於 0 會新增至定位順序的根據其`TabIndex`值。
 - [`VisualElement`](xref:Xamarin.Forms.VisualElement) 執行個體與`TabIndex`小於 0 已加入至定位順序，而且任何之前，會顯示零值。
 - 在衝突`TabIndex`解決的宣告順序。

定義索引標籤的順序之後, 在按下 Tab 鍵將循環中遞增的控制項焦點`TabIndex`達到最後一個控制項時，周圍包裝開頭的順序。

下列 XAML 範例所示`TabIndex`輸入控制項上設定，讓資料行第一個索引標籤瀏覽屬性：

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="0.5*" />
        <ColumnDefinition Width="0.5*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Label Text="You"
           HorizontalOptions="Center" />
    <Label Grid.Column="1"
           Text="Manager"
           HorizontalOptions="Center" />
    <Entry Grid.Row="1"
           Placeholder="Enter forename"
           TabIndex="1" />
    <Entry Grid.Column="1"
           Grid.Row="1"
           Placeholder="Enter forename"
           TabIndex="3" />
    <Entry Grid.Row="2"
           Placeholder="Enter surname"
           TabIndex="2" />
    <Entry Grid.Column="1"
           Grid.Row="2"
           Placeholder="Enter surname"
           TabIndex="4" />
</Grid>
```

下列螢幕擷取畫面顯示此程式碼範例的索引標籤順序：

![](keyboard-images/correct-tab-order.png "資料行的定位順序")

定位順序是資料行為基礎。 因此，按下 Tab 鍵巡覽名字姓氏[ `Entry` ](xref:Xamarin.Forms.Entry)組。

## <a name="excluding-controls-from-the-tab-order"></a>排除的定位順序中的控制項

除了設定控制項的定位順序，可能需要排除的定位順序中的控制項。 這是設定其中一種方式達成[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement)屬性的控制項`false`，因為從定位順序中排除已停用的控制項。

不過，它可能需要排除從定位順序的控制項，即使它們未停用。 這可透過`VisualElement.IsTapStop`屬性，指出是否[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)包括在 tab 導覽。 其預設值是`true`，且其值為時`false`控制項如果會略過 索引標籤瀏覽基礎結構，而不管`TabIndex`設定。

## <a name="supported-controls"></a>支援的控制項

`TabIndex`和`IsTapStop`支援下列在控制項上，接受一或多個平台上的鍵盤輸入的屬性：

- [`Button`](xref:Xamarin.Forms.Button)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`SearchBar`](xref:Xamarin.Forms.SearchBar)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)
- [`Switch`](xref:Xamarin.Forms.Switch)
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)

> [!NOTE]
> 這些控制項不是每個平台上的 可設定焦點的索引標籤。

## <a name="related-links"></a>相關連結

- [協助工具 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Accessibility/)
