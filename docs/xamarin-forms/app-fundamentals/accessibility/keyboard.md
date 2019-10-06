---
title: 鍵盤協助工具
description: 與其使用預設的定位順序，有時候您必須使用 TabIndex 和 IsTapStop 屬性的組合來指定定位順序以微調 UI 的協助工具。
ms.prod: xamarin
ms.assetid: 8be8f498-558a-4894-a01f-91a0d3ef927e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 4bca8de32ec0dfa6a273b784333e99c5bbfe968d
ms.sourcegitcommit: 621649fb4a119981290fed7a1061cbae30b982a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2019
ms.locfileid: "71975859"
---
# <a name="keyboard-accessibility-in-xamarinforms"></a>Xamarin.Forms 中的鍵盤協助工具

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)

如果應用程式未提供適當的鍵盤存取，可能會讓需使用螢幕閱讀程式或行動不便的使用者遇到使用上的困難。 Xamarin.Forms 應用程式可以指定預期的定位順序，以改善其可用性和協助工具。 指定控制項的定位順序時，可啟用鍵盤導覽、讓應用程式頁面做好以特定順序接收輸入的準備，以及允許螢幕閱讀程式向使用者朗讀可設定焦點的元素。

根據預設，控制項的定位順序與其列在 XAML 中的順序或以程式設計方式新增至子集合的順序相同。 此順序為將透過鍵盤巡覽並由螢幕閱讀程式朗讀之控制項的順序；這個預設順序通常也是最佳的順序。 不過，預設順序不一定等於預期的順序，如下列 XAML 程式碼範例所示：

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

下列螢幕擷取畫面顯示此程式碼範例的預設定位順序：

![](keyboard-images/default-tab-order.png "預設的資料列式定位順序")

此處的定位順序是以資料列為主，即為 XAML 中列出的控制項順序。 因此，按下 Tab 鍵時會從名字 [`Entry`](xref:Xamarin.Forms.Entry) 執行個體，接著巡覽至姓氏 `Entry` 執行個體。 不過，更直覺式的體驗就是使用資料行優先的定位導覽，以便在按下 Tab 鍵時巡覽名字與姓氏組合。 這可藉由指定輸入控制項的定位順序來完成。

> [!NOTE]
> 您可以定義通用 Windows 平台上的鍵盤快速鍵，以提供一種直覺的方式，讓使用者透過鍵盤而非觸控或滑鼠，來快速巡覽應用程式的可見 UI 並與其互動。 如需詳細資訊，請參閱[設定 VisualElement 便捷鍵](~/xamarin-forms/platform/windows/visualelement-access-keys.md)。

## <a name="setting-the-tab-order"></a>設定定位順序

`VisualElement.TabIndex` 屬性可用來指定當使用者按下 Tab 鍵巡覽控制項時，[`VisualElement`](xref:Xamarin.Forms.VisualElement) 執行個體接收焦點的順序。 屬性的預設值為 0，而且可以設定為任何 `int` 值。

在使用預設定位順序或設定 `TabIndex` 屬性時，會套用下列規則：

- `TabIndex` 等於 0 的 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 執行個體會根據其在 XAML 或子集合中的宣告順序新增至定位順序。
- `TabIndex` 大於 0 的 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 執行個體則會根據其 `TabIndex` 值新增至定位順序。
- `TabIndex` 小於 0 的 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 執行個體會新增至定位順序並顯示在任何零值之前。
- `TabIndex` 的衝突由宣告順序來解決。

在您定義定位順序之後，按下 Tab 鍵時即會以遞增 `TabIndex` 順序循環控制項的焦點，從開頭一直循環到最後一個控制項。

下列 XAML 範例顯示輸入控制項上設定的 `TabIndex` 屬性，其可啟用資料行優先的定位導覽：

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

下列螢幕擷取畫面顯示此程式碼範例的定位順序：

![](keyboard-images/correct-tab-order.png "資料行式定位順序")

此處的定位順序是以資料行為基礎。 因此，按下 Tab 鍵時會巡覽名字與姓氏 [`Entry`](xref:Xamarin.Forms.Entry) 組合。

> [!IMPORTANT]
> iOS 和 Android 上的螢幕閱讀程式在朗讀畫面上的無障礙元素時，將會接受 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 的 `TabIndex`。

## <a name="excluding-controls-from-the-tab-order"></a>從定位順序中排除控制項

除了設定控制項的定位順序，您可能也需要從定位順序中排除控制項。 其中一種完成上述作業的方式，是將控制項的 [`IsEnabled`](xref:Xamarin.Forms.VisualElement) 屬性設為 `false`，因為已停用的控制項會從定位順序中排除。

不過，有時候即使未停用控制項，可能也需要將其從定位順序中排除。 上述作業可透過 `VisualElement.IsTabStop` 屬性來完成，其可指出定位導覽中是否包含 [`VisualElement`](xref:Xamarin.Forms.VisualElement)。 其預設值為 `true`；當其值為 `false` 時，不論是否設定 `TabIndex`，定位導覽基礎結構均會略過控制項。

## <a name="supported-controls"></a>支援的控制項

下列控制項支援 `TabIndex` 和 `IsTapStop` 屬性，其可接受一或多個平台上的鍵盤輸入：

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
> 這些控制項不見得是每個平台上的可設定焦點定位。

## <a name="related-links"></a>相關連結

- [Accessibility (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility) (協助工具 (範例))
