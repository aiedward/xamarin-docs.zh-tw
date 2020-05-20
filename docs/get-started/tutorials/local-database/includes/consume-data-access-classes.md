---
ms.openlocfilehash: caee3eeda90a560f032c17657072ae5ba5023a69
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "77135116"
---
在此練習中，您將建立使用者介面來使用先前建立的資料存取類別。

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在 [方案總管]  的 **LocalDatabaseTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="LocalDatabaseTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="nameEntry"
                   Placeholder="Enter name" />
            <Entry x:Name="ageEntry"
                   Placeholder="Enter age" />
            <Button Text="Add to Database"
                    Clicked="OnButtonClicked" />
            <ListView x:Name="listView">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <TextCell Text="{Binding Name}"
                                  Detail="{Binding Age}" />
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </StackLayout>
    </ContentPage>
    ```

    此程式碼會以宣告的方式定義頁面的使用者介面，其包含兩個 [`Entry`](xref:Xamarin.Forms.Entry) 執行個體，[`Button`](xref:Xamarin.Forms.Button) 和 [`StackLayout`](xref:Xamarin.Forms.StackLayout)中的 [`ListView`](xref:Xamarin.Forms.ListView)。 每個 `Entry` 都有其 [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 屬性集，用來指定在使用者輸入之前顯示的預留位置文字。 `Button` 會將其 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件設定為名為 `OnButtonClicked` 的事件處理常式 (將在下一個步驟中建立)。 `ListView` 會將其 [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 屬性設定為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，以使用 [`TextCell`](xref:Xamarin.Forms.TextCell) 來定義 [`ListView`](xref:Xamarin.Forms.ListView) 中每列的外觀。 `TextCell` 資料會將其 [`Text`](xref:Xamarin.Forms.TextCell.Text) 和 [`Detail`](xref:Xamarin.Forms.TextCell.Detail) 屬性各別繫結到每個 `Person` 物件的 `Name` 和 `Age` 屬性。

    此外，[`Entry`](xref:Xamarin.Forms.Entry) 執行個體和 [`ListView`](xref:Xamarin.Forms.ListView) 會有以 `x:Name` 屬性指定的名稱。 這可讓程式碼後置檔案使用指派的名稱來存取這些物件。

1. 在 [方案總管]  的 **LocalDatabaseTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟。 然後，在 **MainPage.xaml.cs** 中，將 `OnAppearing` 覆寫和 `OnButtonClicked` 事件處理常式新增至類別：

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();
        listView.ItemsSource = await App.Database.GetPeopleAsync();
    }

    async void OnButtonClicked(object sender, EventArgs e)
    {
        if (!string.IsNullOrWhiteSpace(nameEntry.Text) && !string.IsNullOrWhiteSpace(ageEntry.Text))
        {
            await App.Database.SavePersonAsync(new Person
            {
                Name = nameEntry.Text,
                Age = int.Parse(ageEntry.Text)
            });

            nameEntry.Text = ageEntry.Text = string.Empty;
            listView.ItemsSource = await App.Database.GetPeopleAsync();
        }
    }
    ```

    `OnAppearing` 方法會將儲存在資料庫中的所有資料填入 [`ListView`](xref:Xamarin.Forms.ListView)。 `OnButtonClicked` 方法 (點選 [`Button`](xref:Xamarin.Forms.Button) 時執行) 會先將輸入的資料儲存到資料庫中，再清除兩個 [`Entry`](xref:Xamarin.Forms.Entry) 執行個體，然後重新整理 `ListView` 中的資料。

    > [!NOTE]
    > `OnAppearing` 方法覆寫會在 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 配置完成後 (但在變成可見狀態之前) 執行。 因此，這是設定 Xamarin.Forms 檢視內容的好地方。

1. 在 Visual Studio 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式。

    輸入數個資料項目，點選每個資料項目的 [`Button`](xref:Xamarin.Forms.Button)。 這會將資料儲存至資料庫，並以所有資料庫資料重新填入 [`ListView`](xref:Xamarin.Forms.ListView)：

    [![螢幕擷取畫面：iOS 和 Android 上的本機 SQLite.NET 資料庫資料保留](../images/consume-data-access-classes.png "本機資料庫資料保留")](../images/consume-data-access-classes-large.png#lightbox "本機資料庫資料保留")

    如需 Xamarin.Forms 中有關本機資料庫的詳細資訊，請參閱 [Xamarin.Forms 本機資料庫 (指南)](~/xamarin-forms/data-cloud/data/databases.md)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 [Solution Pad]  的 **LocalDatabaseTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="LocalDatabaseTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="nameEntry"
                   Placeholder="Enter name" />
            <Entry x:Name="ageEntry"
                   Placeholder="Enter age" />
            <Button Text="Add to Database"
                    Clicked="OnButtonClicked" />
            <ListView x:Name="listView">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <TextCell Text="{Binding Name}"
                                  Detail="{Binding Age}" />
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </StackLayout>
    </ContentPage>
    ```

    此程式碼會以宣告的方式定義頁面的使用者介面，其包含兩個 [`Entry`](xref:Xamarin.Forms.Entry) 執行個體，[`Button`](xref:Xamarin.Forms.Button) 和 [`StackLayout`](xref:Xamarin.Forms.StackLayout)中的 [`ListView`](xref:Xamarin.Forms.ListView)。 每個 `Entry` 都有其 [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 屬性集，用來指定在使用者輸入之前顯示的預留位置文字。 `Button` 會將其 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件設定為名為 `OnButtonClicked` 的事件處理常式 (將在下一個步驟中建立)。 `ListView` 會將其 [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 屬性設定為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，以使用 [`TextCell`](xref:Xamarin.Forms.TextCell) 來定義 [`ListView`](xref:Xamarin.Forms.ListView) 中每列的外觀。 `TextCell` 資料會將其 [`Text`](xref:Xamarin.Forms.TextCell.Text) 和 [`Detail`](xref:Xamarin.Forms.TextCell.Detail) 屬性各別繫結到每個 `Person` 物件的 `Name` 和 `Age` 屬性。

    此外，[`Entry`](xref:Xamarin.Forms.Entry) 執行個體和 [`ListView`](xref:Xamarin.Forms.ListView) 會有以 `x:Name` 屬性指定的名稱。 這可讓程式碼後置檔案使用指派的名稱來存取這些物件。

1. 在 [Solution Pad]  的 **LocalDatabaseTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟。 然後，在 **MainPage.xaml.cs** 中，將 `OnAppearing` 覆寫和 `OnButtonClicked` 事件處理常式新增至類別：

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();
        listView.ItemsSource = await App.Database.GetPeopleAsync();
    }

    async void OnButtonClicked(object sender, EventArgs e)
    {
        if (!string.IsNullOrWhiteSpace(nameEntry.Text) && !string.IsNullOrWhiteSpace(ageEntry.Text))
        {
            await App.Database.SavePersonAsync(new Person
            {
                Name = nameEntry.Text,
                Age = int.Parse(ageEntry.Text)
            });

            nameEntry.Text = ageEntry.Text = string.Empty;
            listView.ItemsSource = await App.Database.GetPeopleAsync();
        }
    }
    ```

    `OnAppearing` 方法會將儲存在資料庫中的所有資料填入 [`ListView`](xref:Xamarin.Forms.ListView)。 `OnButtonClicked` 方法 (點選 [`Button`](xref:Xamarin.Forms.Button) 時執行) 會先將輸入的資料儲存到資料庫中，再清除兩個 [`Entry`](xref:Xamarin.Forms.Entry) 執行個體，然後重新整理 `ListView` 中的資料。

    > [!NOTE]
    > `OnAppearing` 方法覆寫會在 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 配置完成後 (但在變成可見狀態之前) 執行。 因此，這是設定 Xamarin.Forms 檢視內容的好地方。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式。

    輸入數個資料項目，點選每個資料項目的 [`Button`](xref:Xamarin.Forms.Button)。 這會將資料儲存至資料庫，並以所有資料庫資料重新填入 [`ListView`](xref:Xamarin.Forms.ListView)：

    [![螢幕擷取畫面：iOS 和 Android 上的本機 SQLite.NET 資料庫資料保留](../images/consume-data-access-classes.png "本機資料庫資料保留")](../images/consume-data-access-classes-large.png#lightbox "本機資料庫資料保留")

    如需 Xamarin.Forms 中有關本機資料庫的詳細資訊，請參閱 [Xamarin.Forms 本機資料庫 (指南)](~/xamarin-forms/data-cloud/data/databases.md)
