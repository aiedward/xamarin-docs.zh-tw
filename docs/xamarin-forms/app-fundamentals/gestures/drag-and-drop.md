---
title: 新增拖放手勢辨識器
description: 本文說明如何使用來辨識拖放手勢 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 4CB2F270-908A-4A89-B852-70BC04066E8C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/04/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2d929043a6b4cd5dd8b06318df0d7a347708fe6c
ms.sourcegitcommit: c3329ab25d377907d8804cdd5e26dc84a274f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130912"
---
# <a name="add-drag-and-drop-gesture-recognizers"></a>新增拖放手勢辨識器

![發行前版本 API](~/media/shared/preview.png)

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-draganddropgesture/)

拖放手勢可讓您使用連續手勢，將專案及其相關聯的資料封裝從一個螢幕位置拖曳至另一個位置。 拖放功能可以在單一應用程式中執行，也可以在一個應用程式中啟動，並以另一端結束。

> [!IMPORTANT]
> Xamarin.Forms拖放手勢辨識器目前為實驗性，只能透過設定旗標來使用 `DragAndDrop_Experimental` 。 如需詳細資訊，請參閱[實驗性旗標](~/xamarin-forms/internals/experimental-flags.md)。
>
> IOS、Android 及通用 Windows 平臺 (UWP) 都支援「拖放手勢」的識別。 不過，在 iOS 上，需要 iOS 11 的最低平臺。

*拖曳來源*是拖曳手勢起始的專案，可以藉由擴展資料封裝物件來提供要傳送的資料。 當拖曳來源放開時，會進行卸載。 *放置目標*，這是拖曳來源底下的元素，然後處理資料封裝。

在應用程式中啟用拖放功能的進程如下所示：

1. 將 `DragGestureRecognizer` 物件新增至其 `GestureRecognizers` 集合，並將 `DragGestureRecognizer.CanDrag` 屬性設定為，以啟用元素的拖曳 `true` 。 如需詳細資訊，請參閱[Enable 拖曳](#enable-drag)。
1. 選擇性建立資料封裝。 Xamarin.Forms會自動填入影像和文字控制項的資料封裝，但對於其他內容，您將需要建立自己的資料封裝。 如需詳細資訊，請參閱[建立資料封裝](#build-a-data-package)。
1. 藉由加入 `DropGestureRecognizer` 物件的 `GestureRecognizers` 集合，並將屬性設定為，來啟用元素的 drop `DropGestureRecognizer.AllowDrop` `true` 。 如需詳細資訊，請參閱[Enable drop](#enable-drop)。
1. 選擇性處理 `DropGestureRecognizer.DragOver` 事件，以指出放置目標允許的作業類型。 如需詳細資訊，請參閱[處理 system.windows.dragdrop.dragover> 事件](#handle-the-dragover-event)。
1. 選擇性處理資料封裝以接收捨棄的內容。 Xamarin.Forms會自動從資料封裝抓取影像和文字資料，但對於其他內容，您將需要處理資料封裝。 如需詳細資訊，請參閱[處理資料封裝](#process-the-data-package)。

> [!NOTE]
> 目前不支援將專案拖曳到和 from [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。

## <a name="enable-drag"></a>啟用拖曳

在中 Xamarin.Forms ，拖曳手勢識別是由類別所提供 `DragGestureRecognizer` 。 這個類別會定義下列屬性：

- `CanDrag`，屬於類型 `bool` ，表示筆勢辨識器附加到的專案是否可以是拖曳來源。 此屬性的預設值為 `false`。
- `DragStartingCommand`，屬於類型 `ICommand` ，可在第一次辨識拖曳手勢時執行。
- `DragStartingCommandParameter`，屬於 `object` 類型，這是傳遞至 `DragStartingCommand` 的參數。
- `DropCompletedCommmand`，屬於類型 `ICommand` ，會在拖放來源時執行。
- `DropCompletedCommandParameter`，屬於 `object` 類型，這是傳遞至 `DropCompletedCommand` 的參數。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

`DragGestureRecognizer`類別也會定義 `DragStarting` 和 `DropCompleted` 事件。 當 `DragGestureRecognizer` 物件偵測到拖曳手勢時，它會執行 `DragStartingCommand` 並叫用 `DragStarting` 事件。 然後，當 `DragGestureRecognizer` 物件偵測到卸載手勢完成時，它會執行並叫用 `DropCompletedCommand` `DropCompleted` 事件。

`DragStartingEventArgs`伴隨事件的物件會 `DragStarting` 定義下列屬性：

- `Handled`，屬於類型 `bool` ，表示事件處理常式是否已處理事件，或是否 Xamarin.Forms 應該繼續其本身的處理。
- `Cancel`，屬於類型 `bool` ，表示是否應取消事件。
- `Data`，屬於類型 `DataPackage` ，表示拖曳來源隨附的資料封裝。 這是一個唯讀屬性。

`DropCompletedEventArgs`事件隨附的物件 `DropCompleted` 具有唯讀 `DropResult` 屬性，其類型為 `DataPackageOperation` 。 如需列舉的詳細資訊 `DataPackageOperation` ，請參閱[處理 system.windows.dragdrop.dragover> 事件](#handle-the-dragover-event)。

下列 XAML 範例顯示 `DragGestureRecognizer` 附加至的 [`Image`](xref:Xamarin.Forms.Image) ：

```xaml
<Image Source="monkeyface.png">
    <Image.GestureRecognizers>
        <DragGestureRecognizer CanDrag="True" />
    </Image.GestureRecognizers>
</Image>
```

在此範例中，可以在上起始拖曳手勢 [`Image`](xref:Xamarin.Forms.Image) 。

> [!TIP]
> 在 iOS、Android 和 UWP 上，會以長按一下的拖曳動作來起始拖曳手勢。

## <a name="build-a-data-package"></a>建立資料封裝

Xamarin.Forms會自動為您建立資料套件，並在起始拖曳時，針對下列控制項：

- 文字控制項。 文字值可以從 [`CheckBox`](xref:Xamarin.Forms.CheckBox) 、 [`DatePicker`](xref:Xamarin.Forms.DatePicker) 、 [`Editor`](xref:Xamarin.Forms.Editor) 、、、 [`Entry`](xref:Xamarin.Forms.Entry) 、 [`Label`](xref:Xamarin.Forms.Label) [`RadioButton`](xref:Xamarin.Forms.RadioButton) [`Switch`](xref:Xamarin.Forms.Switch) 和 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 物件拖曳出來。
- 影像控制項。 您可以從 [`Button`](xref:Xamarin.Forms.Button) 、 [`Image`](xref:Xamarin.Forms.Image) 和控制項拖曳影像 [`ImageButton`](xref:Xamarin.Forms.ImageButton) 。

下表顯示在文字控制項上起始拖曳時，所讀取的屬性，以及任何嘗試的轉換：

| 控制 | 屬性 | 轉換 |
| --- | --- | --- |
| `CheckBox` | `IsChecked` | 已轉換成 `string` 的 `bool`。 |
| `DatePicker` | `Date` | 已轉換成 `string` 的 `DateTime`。 |
| `Editor` | `Text` ||
| `Entry` | `Text` ||
| `Label` | `Text` ||
| `RadioButton` | `IsChecked` | 已轉換成 `string` 的 `bool`。 |
| `Switch` | `IsToggled` | 已轉換成 `string` 的 `bool`。 |
| `TimePicker` | `Time` | 已轉換成 `string` 的 `TimeSpan`。 |

對於文字和影像以外的內容，您必須自行建立資料封裝。

資料封裝是由類別表示 `DataPackage` ，其定義下列屬性：

- `Properties`，屬於類型 `DataPackagePropertySet` ，這是包含在中之資料的屬性集合 `DataPackage` 。 這個屬性是唯讀屬性。
- `Image`，屬於類型 [`ImageSource`](xref:Xamarin.Forms.ImageSource) ，這是包含在中的影像 `DataPackage` 。
- `Text`，屬於類型 `string` ，這是包含在中的文字 `DataPackage` 。
- `View`，屬於類型 `DataPackageView` ，這是的唯讀版本 `DataPackage` 。

`DataPackagePropertySet`類別代表儲存為的屬性包 `Dictionary<string,object>` 。 如需類別的詳細資訊 `DataPackageView` ，請參閱[處理資料封裝](#process-the-data-package)。

### <a name="store-image-or-text-data"></a>儲存影像或文字資料

藉由將資料儲存在或屬性中，可以將影像或文字資料與拖曳來源產生關聯 `DataPackage.Image` `DataPackage.Text` 。 這可以在事件的處理常式中完成 `DragStarting` 。

下列 XAML 範例顯示 `DragGestureRecognizer` ，它會註冊事件的處理常式 `DragStarting` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="4">
    <Path.GestureRecognizers>
        <DragGestureRecognizer CanDrag="True"
                               DragStarting="OnDragStarting" />
    </Path.GestureRecognizers>
    <Path.Data>
        <!-- PathGeometry goes here -->
    </Path.Data>
</Path>
```

在此範例中， `DragGestureRecognizer` 會附加至 `Path` 物件。 在 `DragStarting` 上偵測到拖曳手勢 `Path` （執行 `OnDragStarting` 事件處理常式）時，就會引發事件：

```csharp
void OnDragStarting(object sender, DragStartingEventArgs e)
{
    e.Data.Text = "My text data goes here";
}
```

`DragStartingEventArgs`事件隨附的物件 `DragStarting` 具有 `Data` 類型的屬性 `DataPackage` 。 在此範例中， `Text` 物件的屬性 `DataPackage` 會設定為 `string` 。 `DataPackage`然後，可以在 drop 時存取，以抓取 `string` 。

### <a name="store-data-in-the-property-bag"></a>將資料儲存在屬性包中

任何資料（包括影像和文字）都可以藉由將資料儲存在集合中，與拖曳來源產生關聯 `DataPackage.Properties` 。 這可以在事件的處理常式中完成 `DragStarting` 。

下列 XAML 範例顯示 `DragGestureRecognizer` ，它會註冊事件的處理常式 `DragStarting` ：

```xaml
<Rectangle Stroke="Red"
           Fill="DarkBlue"
           StrokeThickness="4"
           HeightRequest="200"
           WidthRequest="200">
    <Rectangle.GestureRecognizers>
        <DragGestureRecognizer CanDrag="True"
                               DragStarting="OnDragStarting" />
    </Rectangle.GestureRecognizers>
</Rectangle>
```

在此範例中， `DragGestureRecognizer` 會附加至 `Rectangle` 物件。 在 `DragStarting` 上偵測到拖曳手勢 `Rectangle` （執行 `OnDragStarting` 事件處理常式）時，就會引發事件：

```csharp
void OnDragStarting(object sender, DragStartingEventArgs e)
{
    Shape shape = (sender as Element).Parent as Shape;
    e.Data.Properties.Add("Square", new Square(shape.Width, shape.Height));
}
```

`DragStartingEventArgs`事件隨附的物件 `DragStarting` 具有 `Data` 類型的屬性 `DataPackage` 。 `Properties` `DataPackage` 可以修改物件的集合，也就是 `Dictionary<string, object>` 集合，以儲存任何必要的資料。 在此範例中， `Properties` 會修改字典以儲存 `Square` 物件，其代表的大小 `Rectangle` （針對「正方形」索引鍵）。

## <a name="enable-drop"></a>啟用 drop

在中 Xamarin.Forms ，drop 手勢辨識是由類別所提供 `DropGestureRecognizer` 。 這個類別會定義下列屬性：

- `AllowDrop`，屬於類型 `bool` ，表示筆勢辨識器附加到的專案是否可以是放置目標。 此屬性的預設值為 `false`。
- `DragOverCommand`，屬於類型 `ICommand` ，這是在拖曳來源拖放到放置目標上時執行的。
- `DragOverCommandParameter`，屬於 `object` 類型，這是傳遞至 `DragOverCommand` 的參數。
- `DropCommand`，屬於類型 `ICommand` ，當拖曳來源放在放置目標上時，就會執行此型別。
- `DropCommandParameter`，屬於 `object` 類型，這是傳遞至 `DropCommand` 的參數。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

`DropGestureRecognizer`類別也會定義 `DragOver` 和 `Drop` 事件。 當透過 `DropGestureRecognizer` 放置目標來辨識拖曳來源時，它會執行並叫用 `DragOverCommand` `DragOver` 事件。 然後，當在 `DropGestureRecognizer` 放置目標上辨識卸載手勢時，它會執行並叫用 `DropCommand` `Drop` 事件。

`DragEventArgs`伴隨事件的類別會 `DragOver` 定義下列屬性：

- `Data`，屬於類型 `DataPackage` ，其中包含與拖曳來源相關聯的資料。 這個屬性是唯讀的。
- `AcceptedOperation`，屬於類型 `DataPackageOperation` ，指定放置目標允許的作業。

如需列舉的詳細資訊 `DataPackageOperation` ，請參閱[處理 system.windows.dragdrop.dragover> 事件](#handle-the-dragover-event)。

`DropEventArgs`伴隨事件的類別會 `Drop` 定義下列屬性：

- `Data`，屬於類型 `DataPackageView` ，這是資料封裝的唯讀版本。
- `Handled`，屬於類型 `bool` ，表示事件處理常式是否已處理事件，或是否 Xamarin.Forms 應該繼續其本身的處理。

下列 XAML 範例顯示 `DropGestureRecognizer` 附加至的 [`Image`](xref:Xamarin.Forms.Image) ：

```xaml
<Image BackgroundColor="Silver"
       HeightRequest="300"
       WidthRequest="250">
    <Image.GestureRecognizers>
        <DropGestureRecognizer AllowDrop="True" />
    </Image.GestureRecognizers>
</Image>
```

在此範例中，將拖曳來源放在放置目標上時， [`Image`](xref:Xamarin.Forms.Image) 只要拖曳來源為，就會將拖曳來源複製到放置目標 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 。 這是因為 Xamarin.Forms 會自動將拖曳的影像和文字複製到相容的放置目標。

## <a name="handle-the-dragover-event"></a>處理 DragOver 事件

您 `DropGestureRecognizer.DragOver` 可以選擇性地處理事件，以指出放置目標允許的作業類型。 這可以藉由設定 `AcceptedOperation` `DataPackageOperation` 事件隨附之物件的屬性（屬於類型）來完成 `DragEventArgs` `DragOver` 。

`DataPackageOperation` 列舉會定義下列成員：

- `None`，表示不會執行任何動作。
- `Copy`，表示會將拖曳來源內容複寫到放置目標。

> [!IMPORTANT]
> `DragEventArgs`建立物件時， `AcceptedOperation` 屬性會預設為 `DataPackageOperation.Copy` 。

下列 XAML 範例顯示 `DropGestureRecognizer` ，它會註冊事件的處理常式 `DragOver` ：

```xaml
<Image BackgroundColor="Silver"
       HeightRequest="300"
       WidthRequest="250">
    <Image.GestureRecognizers>
        <DropGestureRecognizer AllowDrop="True"
                               DragOver="OnDragOver" />
    </Image.GestureRecognizers>
</Image>
```

在此範例中， `DropGestureRecognizer` 會附加至 [`Image`](xref:Xamarin.Forms.Image) 物件。 `DragOver`當拖曳來源拖放到放置目標上，但尚未卸載（執行事件處理常式）時，就會引發事件 `OnDragOver` ：

```csharp
void OnDragOver(object sender, DragEventArgs e)
{
    e.AcceptedOperation = DataPackageOperation.None;
}
```

在此範例中， `AcceptedOperation` 物件的屬性 `DragEventArgs` 會設定為 `DataPackageOperation.None` 。 這可確保在拖放目標上放置拖曳來源時，不會採取任何動作。

## <a name="process-the-data-package"></a>處理資料封裝

`Drop`當拖曳來源在放置目標上放開時，就會引發事件。 發生這種情況時， Xamarin.Forms 當拖曳來源放到下列控制項時，將會自動嘗試從資料封裝取出資料：

- 文字控制項。 文字值可以放在 [`CheckBox`](xref:Xamarin.Forms.CheckBox) 、 [`DatePicker`](xref:Xamarin.Forms.DatePicker) 、、 [`Editor`](xref:Xamarin.Forms.Editor) [`Entry`](xref:Xamarin.Forms.Entry) 、、、 [`Label`](xref:Xamarin.Forms.Label) [`RadioButton`](xref:Xamarin.Forms.RadioButton) [`Switch`](xref:Xamarin.Forms.Switch) 和物件上 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 。
- 影像控制項。 影像可以放到 [`Button`](xref:Xamarin.Forms.Button) 、 [`Image`](xref:Xamarin.Forms.Image) 和控制項上 [`ImageButton`](xref:Xamarin.Forms.ImageButton) 。

下表顯示在文字控制項上放置文字型拖曳來源時，所設定的屬性，以及任何嘗試的轉換：

| 控制 | 屬性 | 轉換 |
| --- | --- | --- |
| `CheckBox` | `IsChecked` | `string`會轉換成 `bool` 。 |
| `DatePicker` | `Date` | `string`會轉換成 `DateTime` 。 |
| `Editor` | `Text` ||
| `Entry` | `Text` ||
| `Label` | `Text` ||
| `RadioButton` | `IsChecked` | `string`會轉換成 `bool` 。 |
| `Switch` | `IsToggled` | `string`會轉換成 `bool` 。 |
| `TimePicker` | `Time` | `string`會轉換成 `TimeSpan` 。 |

對於文字和影像以外的內容，您必須自行處理資料封裝。

`DropEventArgs`伴隨事件的類別會 `Drop` 定義 `Data` 類型為的屬性 `DataPackageView` 。 此屬性代表資料封裝的唯讀版本。

### <a name="retrieve-image-or-text-data"></a>取出影像或文字資料

您可以使用類別中定義的方法，從事件的處理常式中的資料封裝抓取影像或文字資料 `Drop` `DataPackageView` 。

`DataPackageView`類別包含 `GetImageAsync` 和 `GetTextAsync` 方法。 `GetImageAsync`方法會從資料封裝抓取影像，並將其儲存在屬性中 `DataPackage.Image` ，然後傳回 `Task<ImageSource>` 。 同樣地， `GetTextAsync` 方法會從資料封裝中抓取儲存在屬性中的文字 `DataPackage.Text` ，然後傳回 `Task<string>` 。

下列範例顯示的 `Drop` 事件處理常式會從的資料封裝中抓取文字 `Path` ：

```csharp
async void OnDrop(object sender, DropEventArgs e)
{
    string text = await e.Data.GetTextAsync();

    // Perform logic to take action based on the text value.
}
```

在此範例中，會使用方法，從資料封裝中取出文字資料 `GetTextAsync` 。 接著，可以取得以文字值為基礎的動作。

### <a name="retrieve-data-from-the-property-bag"></a>從屬性包中取出資料

您可以藉 `Drop` 由存取資料封裝的集合，從事件的處理常式中的資料封裝抓取任何資料 `Properties` 。

`DataPackageView`類別會定義 `Properties` 類型為的屬性 `DataPackagePropertySetView` 。 `DataPackagePropertySetView`類別代表儲存為的唯讀屬性包 `Dictionary<string, object>` 。

下列範例顯示的 `Drop` 事件處理常式會從的資料封裝的屬性包中抓取資料 `Rectangle` ：

```csharp
void OnDrop(object sender, DropEventArgs e)
{
    Square square = (Square)e.Data.Properties["Square"];

    // Perform logic to take action based on retrieved value.
}
```

在此範例中， `Square` 會藉由指定 "方形" 字典索引鍵，從資料封裝的屬性包中抓取物件。 接著可以採用以抓取值為基礎的動作。

## <a name="related-links"></a>相關連結

- [拖放手勢 (範例) ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-draganddropgesture/)
