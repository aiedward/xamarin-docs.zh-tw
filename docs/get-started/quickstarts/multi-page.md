---
title: 在多頁 Xamarin.Forms 應用程式中執行巡覽
description: 本文說明如何將單一頁面應用程式（能夠儲存單一便箋）變成多頁應用程式，以儲存多個便箋。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 9DC3B3D6-6CBC-4705-BE80-3D86A9E65F92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: 9ce02b4c6412eab1f4b1003b262573c59940286c
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "68653788"
---
# <a name="perform-navigation-in-a-multi-page-xamarinforms-application"></a>在多頁的 Xamarin. Forms 應用程式中執行流覽

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)

在本快速入門中，您將瞭解如何：

- 將其他頁面新增至 [Xamarin] 表單方案。
- 執行頁面之間的流覽。
- 使用資料系結來同步處理使用者介面專案與其資料來源之間的資料。

本快速入門會逐步解說如何將單一頁面的跨平臺 Xamarin 應用程式（可儲存單一便箋）轉換成多頁應用程式，能夠儲存多個便箋。 最終的應用程式如下所示：

[![](multi-page-images/screenshots1-sml.png "Notes Page")](multi-page-images/screenshots1.png#lightbox "Notes Page")
[![](multi-page-images/screenshots2-sml.png "Note Entry Page")](multi-page-images/screenshots2.png#lightbox "Note Entry Page")

### <a name="prerequisites"></a>Prerequisites

您應該先成功完成[先前的快速入門](single-page.md)，再嘗試進行本快速入門。 或者，下載[先前的快速入門範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/)，並使用它做為本快速入門的起點。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新應用程式

1. 啟動 Visual Studio。 在 [開始] 視窗中，按一下 [最近使用的專案/方案] 清單中的 [**附注**] 方案，或按一下 [**開啟專案或方案**]，然後在 [**開啟專案/方案**] 對話方塊中，選取 Notes 專案的方案檔：

    ![](multi-page-images/vs/open-solution.png "Open Project")

2. 在**方案總管**中，以滑鼠右鍵按一下 [ **Notes** ] 專案，然後選取 [**加入 > 新增資料夾**]：

    ![](multi-page-images/vs/add-new-item.png "Add New Item")

3. 在**方案總管**中，將新的資料夾命名為**模型**：

    ![](multi-page-images/vs/name-folder.png "Models Folder")

4. 在**方案總管**中，選取 [**模型**] 資料夾，以滑鼠右鍵按一下，然後選取 [**新增 > 新專案**]：

    ![](multi-page-images/vs/add-new-models-file.png "Add New File")

5. 在 [**加入新專案**] 對話方塊中，選取 [ **Visual C# Items > 類別**]，將新檔案命名為**Note**，然後按一下 [**新增**] 按鈕：

    ![](multi-page-images/vs/add-note-class.png "Add Note Class")

    這會將名為**Note**的類別新增至**Notes**專案的 [**模型**] 資料夾。

6. 在**Note.cs**中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;

    namespace Notes.Models
    {
        public class Note
        {
            public string Filename { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    這個類別會定義一個 `Note` 模型，以儲存應用程式中每個便箋的相關資料。    

    按下**CTRL + S**，將變更儲存到**Note.cs** ，然後關閉檔案。

7. 在**方案總管**中，以滑鼠右鍵按一下 [ **Notes** ] 專案，然後選取 [**加入 > 新增專案**...]在 [**加入新專案**] 對話方塊中，選取 [**視覺C#專案] > [Xamarin] [> 內容] 頁面**，將新檔案命名為**NoteEntryPage**，然後按一下 [**新增**] 按鈕：

    ![](multi-page-images/vs/add-note-entry-page.png "Add Xamarin.Forms ContentPage")

    這會將名為**NoteEntryPage**的新頁面加入至專案的根資料夾。 此頁面將會是應用程式中的第二個頁面。

8. 在**NoteEntryPage**中，移除所有範本程式碼，並取代為下列程式碼：

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.NoteEntryPage"
                   Title="Note Entry">
          <StackLayout Margin="20">
              <Editor Placeholder="Enter your note"
                      Text="{Binding Text}"
                      HeightRequest="100" />
              <Grid>
                  <Grid.ColumnDefinitions>
                      <ColumnDefinition Width="*" />
                      <ColumnDefinition Width="*" />
                  </Grid.ColumnDefinitions>
                  <Button Text="Save"
                          Clicked="OnSaveButtonClicked" />
                  <Button Grid.Column="1"
                          Text="Delete"
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      這段程式碼會以宣告方式定義頁面的使用者介面，其中包含文字輸入的[`Editor`](xref:Xamarin.Forms.Editor) ，以及指示應用程式儲存或刪除檔案的兩個[`Button`](xref:Xamarin.Forms.Button)實例。 這兩個 `Button` 實例是以水準方式配置在[`Grid`](xref:Xamarin.Forms.Grid)中，`Editor` 和 `Grid` 會在[`StackLayout`](xref:Xamarin.Forms.StackLayout)中垂直配置。 此外，`Editor` 會使用資料系結來系結至 `Note` 模型的 `Text` 屬性。 如需資料系結的詳細資訊，請參閱 Xamarin 中的[資料](deepdive.md#data-binding)系結[快速入門深入探討](deepdive.md)。

      按下**CTRL + S**，將變更儲存到**NoteEntryPage** ，然後關閉檔案。

9. 在**NoteEntryPage.xaml.cs**中，移除所有範本程式碼，並取代為下列程式碼：

      ```csharp
      using System;
      using System.IO;
      using Xamarin.Forms;
      using Notes.Models;

      namespace Notes
      {
          public partial class NoteEntryPage : ContentPage
          {
              public NoteEntryPage()
              {
                  InitializeComponent();
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  await Navigation.PopAsync();
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  await Navigation.PopAsync();
              }
          }
      }
      ```

      此程式碼會在頁面的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)中儲存代表單一便箋的 `Note` 實例。 當按下 [**儲存** [`Button`](xref:Xamarin.Forms.Button) ] 時，就會執行 `OnSaveButtonClicked` 事件處理常式，這會將 `Editor` 的內容儲存至具有隨機產生之檔案名的新檔案，或如果正在更新便箋，則會將其儲存至現有的檔案。 在這兩種情況下，檔案都會儲存在應用程式的本機應用程式資料檔案夾中。 然後，方法會流覽回到前一頁。 當按下 **刪除**`Button` 時，就會執行 `OnDeleteButtonClicked` 事件處理常式，這會刪除檔案（如果有的話），然後再流覽回到上一頁。 如需有關導覽的詳細資訊，請參閱 Xamarin 中的[導覽](deepdive.md#navigation)[深入探討](deepdive.md)。

      按下**CTRL + S**，將變更儲存到**NoteEntryPage.xaml.cs** ，然後關閉檔案。

      > [!WARNING]
      > 此時嘗試建立應用程式將會導致在後續步驟中修正的錯誤。

10. 在**方案總管**中，以滑鼠右鍵按一下 [ **Notes** ] 專案，然後選取 [**加入 > 新增專案**...]在 [**加入新專案**] 對話方塊中，選取 [**視覺C#專案] > [Xamarin] [> 內容] 頁面**，將新檔案命名為**NotesPage**，然後按一下 [**新增**] 按鈕。

      這會將名為**NotesPage**的頁面加入至專案的根資料夾。 此頁面將會是應用程式的根頁面。

11. 在**NotesPage**中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>
        <ListView x:Name="listView"
                  Margin="20"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage>
    ```

    此程式碼會以宣告方式定義頁面的使用者介面，其中包含[`ListView`](xref:Xamarin.Forms.ListView)和[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)。 @No__t_0 會使用資料系結來顯示應用程式所取出的任何附注，而選取附注將會流覽至可修改便箋的 `NoteEntryPage`。 或者，您也可以按下 [`ToolbarItem`] 來建立新的便箋。 如需資料系結的詳細資訊，請參閱 Xamarin 中的[資料](deepdive.md#data-binding)系結[快速入門深入探討](deepdive.md)。

    按下**CTRL + S**，將變更儲存到**NotesPage** ，然後關閉檔案。

12. 在**NotesPage.xaml.cs**中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Xamarin.Forms;
    using Notes.Models;

    namespace Notes
    {
        public partial class NotesPage : ContentPage
        {
            public NotesPage()
            {
                InitializeComponent();
            }

            protected override void OnAppearing()
            {
                base.OnAppearing();

                var notes = new List<Note>();

                var files = Directory.EnumerateFiles(App.FolderPath, "*.notes.txt");
                foreach (var filename in files)
                {
                    notes.Add(new Note
                    {
                        Filename = filename,
                        Text = File.ReadAllText(filename),
                        Date = File.GetCreationTime(filename)
                    });
                }

                listView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnNoteAddedClicked(object sender, EventArgs e)
            {
                await Navigation.PushAsync(new NoteEntryPage
                {
                    BindingContext = new Note()
                });
            }

            async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
            {
                if (e.SelectedItem != null)
                {
                    await Navigation.PushAsync(new NoteEntryPage
                    {
                        BindingContext = e.SelectedItem as Note
                    });
                }
            }
        }
    }
    ```    

    此程式碼會定義 `NotesPage` 的功能。 當頁面出現時，會執行 `OnAppearing` 方法，這會將已從本機應用程式資料檔案夾中取出的任何附注填入[`ListView`](xref:Xamarin.Forms.ListView)中。 當按下[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)時，就會執行 `OnNoteAddedClicked` 事件處理常式。 這個方法會導覽至 `NoteEntryPage`，將 `NoteEntryPage` 的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)設定為新的 `Note` 實例。 選取 `ListView` 中的專案時，就會執行 `OnListViewItemSelected` 事件處理常式。 這個方法會導覽至 `NoteEntryPage`，並將 `NoteEntryPage` 的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)設定為選取的 `Note` 實例。 如需有關導覽的詳細資訊，請參閱 Xamarin 中的[導覽](deepdive.md#navigation)[深入探討](deepdive.md)。

    按下**CTRL + S**，將變更儲存到**NotesPage.xaml.cs** ，然後關閉檔案。

    > [!WARNING]
    > 此時嘗試建立應用程式將會導致在後續步驟中修正的錯誤。

13. 在**方案總管**中，按兩下**App.xaml.cs**以將其開啟。 然後將現有的程式碼取代為下列程式碼：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {
            public static string FolderPath { get; private set; }

            public App()
            {
                InitializeComponent();
                FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
                MainPage = new NavigationPage(new NotesPage());
            }
            // ...
        }
    }
    ```

    這段程式碼會加入 `System.IO` 命名空間的命名空間宣告，並加入 `string` 類型之靜態 `FolderPath` 屬性的宣告。 @No__t_0 屬性是用來將路徑儲存在要儲存附注資料的裝置上。 此外，程式碼會初始化 `App` 的函式中的 `FolderPath` 屬性，並將[`MainPage`](xref:Xamarin.Forms.Application.MainPage)屬性初始化為裝載 `NotesPage` 實例的[`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 。 如需有關導覽的詳細資訊，請參閱 Xamarin 中的[導覽](deepdive.md#navigation)[深入探討](deepdive.md)。

    按下 **CTRL+S** 以將變更儲存到 **App.xaml.cs**，然後關閉檔案。

14. 在**方案總管**的**Notes**專案中，以滑鼠右鍵按一下 [ **MainPage**]，然後選取 [**刪除**]。 在出現的對話方塊中，按下 [**確定]** 按鈕，從硬碟中移除檔案。

    這會移除不再使用的頁面。

15. 在每個平臺上建立並執行專案。 如需詳細資訊，請參閱[建立快速入門](single-page.md#building-the-quickstart)。

    在 [ **NotesPage** ] 上，按 [ **+** ] 按鈕以流覽至**NoteEntryPage**並輸入附注。 儲存便箋之後，應用程式會流覽回**NotesPage**。

    輸入一些不同長度的附注，以觀察應用程式行為。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新應用程式

1. 啟動 Visual Studio for Mac。 在 [開始] 視窗中，按一下 [**開啟**]，然後在對話方塊中選取 Notes 專案的方案檔：

    ![](multi-page-images/vsmac/open-solution.png "Open Solution")

2. 在  **Solution Pad**中，選取  **Notes**  專案，按一下滑鼠右鍵，然後選取 **加入 > 新增資料夾**：

    ![](multi-page-images/vsmac/add-new-folder.png "Add New Folder")

3. 在  **Solution Pad**中，將新的資料夾命名為**模型**：

    ![](multi-page-images/vsmac/name-folder.png "Models Folder")

4. 在  **Solution Pad**中，選取 **模型** 資料夾，以滑鼠右鍵按一下，然後選取 **新增 > 新檔案 ...** ：

    ![](multi-page-images/vsmac/add-new-models-file.png "Add New File")

5. 在 [**新增**檔案] 對話方塊中，選取 **[一般] > [空白類別**]，將新檔案命名為**Note**，然後按一下 [**新增**] 按鈕：

    ![](multi-page-images/vsmac/add-note-class.png "Add Note Class")

    這會將名為**Note**的類別新增至**Notes**專案的 [**模型**] 資料夾。

6. 在**Note.cs**中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;

    namespace Notes.Models
    {
        public class Note
        {
            public string Filename { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    這個類別會定義一個 `Note` 模型，以儲存應用程式中每個便箋的相關資料。

    選擇 [檔案] **> [儲存**] （或按 **&#8984; + S**），將變更儲存到**Note.cs** ，然後關閉檔案。

7. 在  **Solution Pad**中，選取  **Notes**  專案，按一下滑鼠右鍵，然後選取 **加入 > 新增**檔案 ...。在 **新增**檔案 對話方塊中，選取 **表單 > 表單 ContentPage XAML**，將新檔案命名為**NoteEntryPage**，然後按一下 **新增** 按鈕：

    ![](multi-page-images/vsmac/add-note-entry-page.png "Add Xamarin.Forms ContentPage")

    這會將名為**NoteEntryPage**的新頁面加入至專案的根資料夾。 此頁面將會是應用程式中的第二個頁面。

8. 在**NoteEntryPage**中，移除所有範本程式碼，並取代為下列程式碼：

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.NoteEntryPage"
                   Title="Note Entry">
          <StackLayout Margin="20">
              <Editor Placeholder="Enter your note"
                      Text="{Binding Text}"
                      HeightRequest="100" />
              <Grid>
                  <Grid.ColumnDefinitions>
                      <ColumnDefinition Width="*" />
                      <ColumnDefinition Width="*" />
                  </Grid.ColumnDefinitions>
                  <Button Text="Save"
                          Clicked="OnSaveButtonClicked" />
                  <Button Grid.Column="1"
                          Text="Delete"
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      這段程式碼會以宣告方式定義頁面的使用者介面，其中包含文字輸入的[`Editor`](xref:Xamarin.Forms.Editor) ，以及指示應用程式儲存或刪除檔案的兩個[`Button`](xref:Xamarin.Forms.Button)實例。 這兩個 `Button` 實例是以水準方式配置在[`Grid`](xref:Xamarin.Forms.Grid)中，`Editor` 和 `Grid` 會在[`StackLayout`](xref:Xamarin.Forms.StackLayout)中垂直配置。 此外，`Editor` 會使用資料系結來系結至 `Note` 模型的 `Text` 屬性。 如需資料系結的詳細資訊，請參閱 Xamarin 中的[資料](deepdive.md#data-binding)系結[快速入門深入探討](deepdive.md)。

      選擇 [檔案] **> [儲存**] （或按 **&#8984; + S**），將變更儲存到**NoteEntryPage** ，然後關閉檔案。

9. 在**NoteEntryPage.xaml.cs**中，移除所有範本程式碼，並取代為下列程式碼：

      ```csharp
      using System;
      using System.IO;
      using Xamarin.Forms;
      using Notes.Models;

      namespace Notes
      {
          public partial class NoteEntryPage : ContentPage
          {
              public NoteEntryPage()
              {
                  InitializeComponent();
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  await Navigation.PopAsync();
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  await Navigation.PopAsync();
              }
          }
      }
      ```

      此程式碼會在頁面的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)中儲存代表單一便箋的 `Note` 實例。 當按下 [**儲存** [`Button`](xref:Xamarin.Forms.Button) ] 時，就會執行 `OnSaveButtonClicked` 事件處理常式，這會將 `Editor` 的內容儲存至具有隨機產生之檔案名的新檔案，或如果正在更新便箋，則會將其儲存至現有的檔案。 在這兩種情況下，檔案都會儲存在應用程式的本機應用程式資料檔案夾中。 然後，方法會流覽回到前一頁。 當按下 **刪除**`Button` 時，就會執行 `OnDeleteButtonClicked` 事件處理常式，這會刪除檔案（如果有的話），然後再流覽回到上一頁。 如需有關導覽的詳細資訊，請參閱 Xamarin 中的[導覽](deepdive.md#navigation)[深入探討](deepdive.md)。

      選擇 [檔案] **> [儲存**] （或按 **&#8984; + S**），將變更儲存到**NoteEntryPage.xaml.cs** ，然後關閉檔案。

      > [!WARNING]
      > 此時嘗試建立應用程式將會導致在後續步驟中修正的錯誤。

10. 在  **Solution Pad**中，選取  **Notes**  專案，按一下滑鼠右鍵，然後選取 **加入 > 新增**檔案 ...。在 **新增**檔案 對話方塊中，選取 **表單 > 表單 ContentPage XAML**，將新檔案命名為**NotesPage**，然後按一下 **新增** 按鈕。

      這會將名為**NotesPage**的頁面加入至專案的根資料夾。 此頁面將會是應用程式的根頁面。

11. 在**NotesPage**中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>
        <ListView x:Name="listView"
                  Margin="20"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage>
    ```

    此程式碼會以宣告方式定義頁面的使用者介面，其中包含[`ListView`](xref:Xamarin.Forms.ListView)和[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)。 @No__t_0 會使用資料系結來顯示應用程式所取出的任何附注，而選取附注將會流覽至可修改便箋的 `NoteEntryPage`。 或者，您也可以按下 [`ToolbarItem`] 來建立新的便箋。 如需資料系結的詳細資訊，請參閱 Xamarin 中的[資料](deepdive.md#data-binding)系結[快速入門深入探討](deepdive.md)。

    選擇 [檔案] **> [儲存**] （或按 **&#8984; + S**），將變更儲存到**NotesPage** ，然後關閉檔案。

12. 在**NotesPage.xaml.cs**中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Xamarin.Forms;
    using Notes.Models;

    namespace Notes
    {
        public partial class NotesPage : ContentPage
        {
            public NotesPage()
            {
                InitializeComponent();
            }

            protected override void OnAppearing()
            {
                base.OnAppearing();

                var notes = new List<Note>();

                var files = Directory.EnumerateFiles(App.FolderPath, "*.notes.txt");
                foreach (var filename in files)
                {
                    notes.Add(new Note
                    {
                        Filename = filename,
                        Text = File.ReadAllText(filename),
                        Date = File.GetCreationTime(filename)
                    });
                }

                listView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnNoteAddedClicked(object sender, EventArgs e)
            {
                await Navigation.PushAsync(new NoteEntryPage
                {
                    BindingContext = new Note()
                });
            }

            async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
            {
                if (e.SelectedItem != null)
                {
                    await Navigation.PushAsync(new NoteEntryPage
                    {
                        BindingContext = e.SelectedItem as Note
                    });
                }
            }
        }
    }
    ```    

    此程式碼會定義 `NotesPage` 的功能。 當頁面出現時，會執行 `OnAppearing` 方法，這會將已從本機應用程式資料檔案夾中取出的任何附注填入[`ListView`](xref:Xamarin.Forms.ListView)中。 當按下[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)時，就會執行 `OnNoteAddedClicked` 事件處理常式。 這個方法會導覽至 `NoteEntryPage`，將 `NoteEntryPage` 的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)設定為新的 `Note` 實例。 選取 `ListView` 中的專案時，就會執行 `OnListViewItemSelected` 事件處理常式。 這個方法會導覽至 `NoteEntryPage`，並將 `NoteEntryPage` 的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)設定為選取的 `Note` 實例。 如需有關導覽的詳細資訊，請參閱 Xamarin 中的[導覽](deepdive.md#navigation)[深入探討](deepdive.md)。

    選擇 [檔案] **> [儲存**] （或按 **&#8984; + S**），將變更儲存到**NotesPage.xaml.cs** ，然後關閉檔案。

    > [!WARNING]
    > 此時嘗試建立應用程式將會導致在後續步驟中修正的錯誤。

13. 在  **Solution Pad**中，按兩下**App.xaml.cs**以將其開啟。 然後將現有的程式碼取代為下列程式碼：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {
            public static string FolderPath { get; private set; }

            public App()
            {
                InitializeComponent();
                FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
                MainPage = new NavigationPage(new NotesPage());
            }
            // ...
        }
    }
    ```

    這段程式碼會加入 `System.IO` 命名空間的命名空間宣告，並加入 `string` 類型之靜態 `FolderPath` 屬性的宣告。 @No__t_0 屬性是用來將路徑儲存在要儲存附注資料的裝置上。 此外，程式碼會初始化 `App` 的函式中的 `FolderPath` 屬性，並將[`MainPage`](xref:Xamarin.Forms.Application.MainPage)屬性初始化為裝載 `NotesPage` 實例的[`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 。 如需有關導覽的詳細資訊，請參閱 Xamarin 中的[導覽](deepdive.md#navigation)[深入探討](deepdive.md)。

    選擇 [檔案] > [儲存] (或按下 **&#8984; + S**) 以將變更儲存到 **App.xaml.cs**，然後關閉檔案。

14. 在**Solution Pad**的**Notes**專案中，以滑鼠右鍵按一下 [ **MainPage**]，然後選取 [**移除**]。 在出現的對話方塊中，按下 [**刪除**] 按鈕以從硬碟中移除檔案。

    這會移除不再使用的頁面。

15. 在每個平臺上建立並執行專案。 如需詳細資訊，請參閱[建立快速入門](single-page.md#building-the-quickstart)。

    在 [ **NotesPage** ] 上，按 [ **+** ] 按鈕以流覽至**NoteEntryPage**並輸入附注。 儲存便箋之後，應用程式會流覽回**NotesPage**。

    輸入一些不同長度的附注，以觀察應用程式行為。

::: zone-end

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已瞭解如何：

- 將其他頁面新增至 [Xamarin] 表單方案。
- 執行頁面之間的流覽。
- 使用資料系結來同步處理使用者介面專案與其資料來源之間的資料。

若要修改應用程式，使其將其資料儲存在本機 SQLite.NET 資料庫中，請繼續進行下一個快速入門。

> [!div class="nextstepaction"]
> [下一步](database.md)

## <a name="related-links"></a>相關連結

- [Notes (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)
- [Xamarin. 表單快速入門深入探討](deepdive.md)
