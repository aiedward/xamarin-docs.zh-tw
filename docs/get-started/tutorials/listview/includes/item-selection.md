# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 **MainPage.xaml** 中修改 [`ListView`](xref:Xamarin.Forms.ListView) 宣告，以便其設定 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 和 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件的處理常式：

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped" />
    ```

    此程式碼會將 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件設定成名為 `OnListViewItemSelected` 的事件處理常式，並將 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件設定成名為 `OnListViewItemTapped` 的事件處理常式。 下一個步驟將會建立這兩個事件處理常式。

1. 在 [方案總管] 的 **ListViewTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟。 然後，在 **MainPage.xaml.cs** 中，將 `OnListViewItemSelected` 和 `OnListViewItemTapped` 事件處理常式新增至類別：

    ```csharp
    void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
    {
        Monkey selectedItem = e.SelectedItem as Monkey;
    }

    void OnListViewItemTapped(object sender, ItemTappedEventArgs e)
    {
        Monkey tappedItem = e.Item as Monkey;
    }
    ```

    點選 [`ListView`](xref:Xamarin.Forms.ListView) 中的項目時，分別執行 `OnListViewItemSelected` 和 `OnListItemTapped` 方法的 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 和 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 會隨即引發。 這兩種方法的 `sender` 引數是負責引發事件的 `ListView` 物件，並且可用來存取 `ListView` 物件。 `OnListViewItemSelected` 方法中的 [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) 引數會提供已選取的項目，而 `OnListViewItemTapped` 方法中的 [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) 引數會提供已點選的項目。

    > [!IMPORTANT]
    > 只有在 [`ListView`](xref:Xamarin.Forms.ListView) 中選取新項目時，才會引發 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件。 因此，點選兩次相同的項目時會引發兩個 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件，但只會引發一個 `ItemSelected` 事件。

1. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式：

    [![iOS 和 Android 上回應項目選取及點選的 ListView 螢幕擷取畫面](../images/item-selection.png "ListView 項目選取")](../images/item-selection-large.png#lightbox "ListView 項目選取")

    在兩個事件處理常式中設定中斷點，並點選 [`ListView`](xref:Xamarin.Forms.ListView) 中的項目。 請注意，只有在 [`ListView`](xref:Xamarin.Forms.ListView) 中選取新項目時，才會引發 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件，而 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件會在每次點選項目時引發。

    如需有關項目選取及點選的詳細資訊，請參閱 [ListView 互動功能](~/xamarin-forms/user-interface/listview/interactivity.md)指南中的[選取與點選](~/xamarin-forms/user-interface/listview/interactivity.md#selection--taps)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 **MainPage.xaml** 中修改 [`ListView`](xref:Xamarin.Forms.ListView) 宣告，以便其設定 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 和 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件的處理常式：

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped" />
    ```

    此程式碼會將 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件設定成名為 `OnListViewItemSelected` 的事件處理常式，並將 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件設定成名為 `OnListViewItemTapped` 的事件處理常式。 下一個步驟將會建立這兩個事件處理常式。

1. 在 [Solution Pad] 的 **ListViewTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟。 然後，在 **MainPage.xaml.cs** 中，將 `OnListViewItemSelected` 和 `OnListViewItemTapped` 事件處理常式新增至類別：

    ```csharp
    void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
    {
        Monkey selectedItem = e.SelectedItem as Monkey;
    }

    void OnListViewItemTapped(object sender, ItemTappedEventArgs e)
    {
        Monkey tappedItem = e.Item as Monkey;
    }
    ```

    點選 [`ListView`](xref:Xamarin.Forms.ListView) 中的項目時，分別執行 `OnListViewItemSelected` 和 `OnListItemTapped` 方法的 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 和 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 會隨即引發。 這兩種方法的 `sender` 引數是負責引發事件的 `ListView` 物件，並且可用來存取 `ListView` 物件。 `OnListViewItemSelected` 方法中的 [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) 引數會提供已選取的項目，而 `OnListViewItemTapped` 方法中的 [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) 引數會提供已點選的項目。

    > [!IMPORTANT]
    > 只有在 [`ListView`](xref:Xamarin.Forms.ListView) 中選取新項目時，才會引發 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件。 因此，點選兩次相同的項目時會引發兩個 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件，但只會引發一個 `ItemSelected` 事件。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![iOS 和 Android 上回應項目選取及點選的 ListView 螢幕擷取畫面](../images/item-selection.png "ListView 項目選取")](../images/item-selection-large.png#lightbox "ListView 項目選取")

    在兩個事件處理常式中設定中斷點，並點選 [`ListView`](xref:Xamarin.Forms.ListView) 中的項目。 請注意，只有在 [`ListView`](xref:Xamarin.Forms.ListView) 中選取新項目時，才會引發 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件，而 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件會在每次點選項目時引發。

    如需有關項目選取及點選的詳細資訊，請參閱 [ListView 互動功能](~/xamarin-forms/user-interface/listview/interactivity.md)指南中的[選取與點選](~/xamarin-forms/user-interface/listview/interactivity.md#selection--taps)。
