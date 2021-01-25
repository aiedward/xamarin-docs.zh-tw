---
ms.openlocfilehash: 456bc5fc0c30563c5950f4cacf3e3e7bdb177134
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98689800"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在 **MainPage.xaml** 中修改 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 宣告，以便其將 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 屬性設定為 `Single`，並設定 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) 事件的處理常式：

    ```xaml
    <CollectionView ItemsSource="{Binding Monkeys}"
                    SelectionMode="Single"
                    SelectionChanged="OnSelectionChanged" />
    ```

    此程式碼設定在 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 中啟用單一項目選取，並將 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) 事件設定為名為 `OnSelectionChanged` 的事件處理常式。 下一個步驟將會建立此事件處理常式。

1. 在 [方案總管] 的 **CollectionViewTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟。 然後，在 **MainPage.xaml.cs** 中，將 `OnSelectionChanged` 事件處理常式新增至類別：

    ```csharp
    void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
    {
        Monkey selectedItem = e.CurrentSelection[0] as Monkey;
    }
    ```

    在 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 中選取一個項目時，就會引發 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) 事件，而這會執行 `OnSelectionChanged` 方法。 此方法的 `sender` 引數是負責引發事件的 `CollectionView` 物件，可用來存取 `CollectionView` 物件。 `OnSelectionChanged` 方法的 [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) 引數會提供選取的項目。

1. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式：

    [![iOS 和 Android 上回應項目選取的 CollectionView 螢幕擷取畫面](../images/item-selection.png "CollectionView 項目選取")](../images/item-selection-large.png#lightbox "CollectionView 項目選取")

    在 `OnSelectionChanged` 事件處理常式中設定中斷點，然後在 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 中選取一個項目。 檢查 `selectedItem` 變數的值，以確保其包含您所選取項目的資料。

    在 Visual Studio 中，停止應用程式。

    如需項目選取的詳細資訊，請參閱 [Xamarin.Forms CollectionView 選取](~/xamarin-forms/user-interface/collectionview/selection.md)。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 **MainPage.xaml** 中修改 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 宣告，以便其將 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 屬性設定為 `Single`，並設定 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) 事件的處理常式：

    ```xaml
    <CollectionView ItemsSource="{Binding Monkeys}"
                    SelectionMode="Single"
                    SelectionChanged="OnSelectionChanged" />
    ```

    此程式碼設定在 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 中啟用單一項目選取，並將 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) 事件設定為名為 `OnSelectionChanged` 的事件處理常式。 下一個步驟將會建立此事件處理常式。

1. 在 [Solution Pad] 的 **CollectionViewTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟。 然後，在 **MainPage.xaml.cs** 中，將 `OnSelectionChanged` 事件處理常式新增至類別：

    ```csharp
    void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
    {
        Monkey selectedItem = e.CurrentSelection[0] as Monkey;
    }
    ```

    在 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 中選取一個項目時，就會引發 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) 事件，而這會執行 `OnSelectionChanged` 方法。 此方法的 `sender` 引數是負責引發事件的 `CollectionView` 物件，可用來存取 `CollectionView` 物件。 `OnSelectionChanged` 方法的 [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) 引數會提供選取的項目。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![iOS 和 Android 上回應項目選取的 CollectionView 螢幕擷取畫面](../images/item-selection.png "CollectionView 項目選取")](../images/item-selection-large.png#lightbox "CollectionView 項目選取")

    在 `OnSelectionChanged` 事件處理常式中設定中斷點，然後在 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 中選取一個項目。 檢查 `selectedItem` 變數的值，以確保其包含您所選取項目的資料。

    在 Visual Studio for Mac 中，停止應用程式。

    如需項目選取的詳細資訊，請參閱 [Xamarin.Forms CollectionView 選取](~/xamarin-forms/user-interface/collectionview/selection.md)。
