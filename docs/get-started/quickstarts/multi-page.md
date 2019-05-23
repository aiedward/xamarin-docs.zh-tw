---
title: 在多頁 Xamarin.Forms 應用程式中執行巡覽
description: 這篇文章說明如何將單一頁面應用程式，能夠將單一的附註，儲存至多頁應用程式，可以儲存多個備忘稿。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 9DC3B3D6-6CBC-4705-BE80-3D86A9E65F92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: 886527f6d4338ffbe7026bec536716bf844e3701
ms.sourcegitcommit: 4b7a0ac33fc64516dd3d70bb68d9aa50e46b2dc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2019
ms.locfileid: "65997124"
---
# <a name="perform-navigation-in-a-multi-page-xamarinforms-application"></a>執行瀏覽多頁 Xamarin.Forms 應用程式中

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/)

在本快速入門中，您將了解如何：

- 將其他頁面新增至 Xamarin.Forms 方案中。
- 執行頁面之間瀏覽。
- 您可以使用資料繫結，同步處理使用者介面項目和其資料來源之間的資料。

快速入門逐步解說如何將單一頁面跨平台 Xamarin.Forms 應用程式，能夠將單一的附註，儲存至多頁應用程式，可以儲存多個備忘稿。 最終的應用程式如下所示：

[![](multi-page-images/screenshots1-sml.png "備忘稿")](multi-page-images/screenshots1.png#lightbox "備忘稿")
[![](multi-page-images/screenshots2-sml.png "附註項目頁面")](multi-page-images/screenshots2.png#lightbox "附註項目頁面")

### <a name="prerequisites"></a>必要條件

您應該已成功完成[先前的快速入門](single-page.md)之前嘗試本快速入門。 或者，下載[先前的快速入門範例](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)並使用它做為起點，在本快速入門。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新應用程式

1. 啟動 Visual Studio。 在 開始 視窗中，按一下 **備忘稿**中的最新的專案/方案清單中或按一下 解決方案**開啟專案或方案**，然後在**開啟專案/方案**對話方塊選取備忘稿專案的方案檔：

    ![](multi-page-images/vs/open-solution.png "開啟專案")

2. 在 [**方案總管] 中**，以滑鼠右鍵按一下**備忘稿**專案，然後選取**新增 > 新資料夾**:

    ![](multi-page-images/vs/add-new-item.png "加入新項目")

3. 在 **方案總管**，新資料夾命名**模型**:

    ![](multi-page-images/vs/name-folder.png "Models 資料夾")

4. 在 [**方案總管] 中**，選取**模型**資料夾、 按一下滑鼠右鍵，然後選取**新增 > 新增項目...**:

    ![](multi-page-images/vs/add-new-models-file.png "加入新檔案")

5. 中**加入新項目**對話方塊中，選取**VisualC#項目 > 類別**，將新檔案命名**注意**，然後按一下**新增**按鈕：

    ![](multi-page-images/vs/add-note-class.png "新增附註類別")

    這會新增一個名為類別**附註**要**模型**資料夾**備忘稿**專案。

6. 在  **Note.cs**，移除所有範本程式碼，並取代為下列程式碼：

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

    這個類別會定義`Note`會儲存應用程式中的每一個筆記相關資料的模型。    

    變更儲存到**Note.cs**藉由按下**CTRL + S**，並關閉檔案。

7. 中**方案總管**，以滑鼠右鍵按一下**備忘稿**專案，然後選取**新增 > 新增項目...**.在**加入新項目**對話方塊中，選取**VisualC#項目 > Xamarin.Forms > 內容頁面**，將新檔案命名**NoteEntryPage**，然後按一下**新增**按鈕：

    ![](multi-page-images/vs/add-note-entry-page.png "新增 Xamarin.Forms ContentPage")

    這會新增名為的新頁面**NoteEntryPage**到專案的根資料夾。 此頁面將會是應用程式中的第二頁。

8. 在  **NoteEntryPage.xaml**，移除所有範本程式碼，並取代為下列程式碼：

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

      此程式碼以宣告方式定義頁面上，其中包含的使用者介面[ `Editor` ](xref:Xamarin.Forms.Editor)文字輸入和兩個[ `Button` ](xref:Xamarin.Forms.Button)指示儲存或刪除應用程式的執行個體此檔案。 這兩個`Button`執行個體以水平方式配置[ `Grid` ](xref:Xamarin.Forms.Grid)，使用`Editor`並`Grid`垂直配置[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)。 颾魤 ㄛ`Editor`會使用資料繫結至繫結`Text`屬性`Note`模型。 如需有關資料繫結的詳細資訊，請參閱 <<c0> [ 資料繫結](deepdive.md#data-binding)中[Xamarin.Forms 快速入門深入探討](deepdive.md)。

      變更儲存到**NoteEntryPage.xaml**藉由按下**CTRL + S**，並關閉檔案。

9. 在  **NoteEntryPage.xaml.cs**，移除所有範本程式碼，並取代為下列程式碼：

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

      此程式碼會儲存`Note`執行個體，代表單一附註，這[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的頁面。 當**儲存** [ `Button` ](xref:Xamarin.Forms.Button)按下`OnSaveButtonClicked`事件處理常式執行時，這是儲存的內容`Editor`到新的檔案，以隨機產生的檔名，或如果正在更新記下現有檔案。 在這兩種情況下，檔案會儲存在應用程式的本機應用程式資料資料夾中。 然後此方法向後巡覽至上一頁。 當**刪除**`Button`按下`OnDeleteButtonClicked`執行事件處理常式時，會刪除檔案，前提是它存在，並向後巡覽至上一頁。 如需有關瀏覽的詳細資訊，請參閱[瀏覽](deepdive.md#navigation)中[Xamarin.Forms 快速入門深入探討](deepdive.md)。

      變更儲存到**NoteEntryPage.xaml.cs**藉由按下**CTRL + S**，並關閉檔案。

      > [!WARNING]
      > 嘗試建置應用程式現在將會產生將在後續步驟中修正的錯誤。

10. 中**方案總管**，以滑鼠右鍵按一下**備忘稿**專案，然後選取**新增 > 新增項目...**.在**加入新項目**對話方塊中，選取**VisualC#項目 > Xamarin.Forms > 內容頁面**，將新檔案命名**NotesPage**，然後按一下**新增**  按鈕。

      這會新增名為的頁面**NotesPage**到專案的根資料夾。 此頁面將會是應用程式的 [根] 頁面。

11. 在  **NotesPage.xaml**，移除所有範本程式碼，並取代為下列程式碼：

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

    此程式碼以宣告方式定義頁面上，其中包含的使用者介面[ `ListView` ](xref:Xamarin.Forms.ListView)並[ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem)。 `ListView`會使用資料繫結來顯示應用程式時，所擷取的任何附註，然後選取 附註會瀏覽至`NoteEntryPage`修改附註的位置。 或者，您可以在新便箋建立按`ToolbarItem`。 如需有關資料繫結的詳細資訊，請參閱 <<c0> [ 資料繫結](deepdive.md#data-binding)中[Xamarin.Forms 快速入門深入探討](deepdive.md)。

    變更儲存到**NotesPage.xaml**藉由按下**CTRL + S**，並關閉檔案。

12. 在  **NotesPage.xaml.cs**，移除所有範本程式碼，並取代為下列程式碼：

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

    此程式碼定義的功能`NotesPage`。 頁面出現時，`OnAppearing`執行方法時，這會填入[ `ListView` ](xref:Xamarin.Forms.ListView)與任何從本機應用程式資料資料夾中擷取的資訊。 當[ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem)按下`OnNoteAddedClicked`事件處理常式會執行。 這個方法會瀏覽至`NoteEntryPage`，將[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的`NoteEntryPage`新`Note`執行個體。 中的項目`ListView`已選取`OnListViewItemSelected`事件處理常式會執行。 這個方法會瀏覽至`NoteEntryPage`，將[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的`NoteEntryPage`於所選`Note`執行個體。 如需有關瀏覽的詳細資訊，請參閱[瀏覽](deepdive.md#navigation)中[Xamarin.Forms 快速入門深入探討](deepdive.md)。

    變更儲存到**NotesPage.xaml.cs**藉由按下**CTRL + S**，並關閉檔案。

    > [!WARNING]
    > 嘗試建置應用程式現在將會產生將在後續步驟中修正的錯誤。

13. 在 [**方案總管] 中**，按兩下**App.xaml.cs**加以開啟。 然後以下列程式碼取代現有的程式碼：

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

    這個程式碼加入的命名空間宣告`System.IO`命名空間，並將宣告靜態`FolderPath`型別的屬性`string`。 `FolderPath`屬性用來將路徑儲存在裝置上附註資料的儲存位置。 此外，程式碼會初始化`FolderPath`中的屬性`App`建構函式，並初始化[ `MainPage` ](xref:Xamarin.Forms.Application.MainPage)屬性[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)裝載執行個體`NotesPage`。 如需有關瀏覽的詳細資訊，請參閱[瀏覽](deepdive.md#navigation)中[Xamarin.Forms 快速入門深入探討](deepdive.md)。

    按下 **CTRL+S** 以將變更儲存到 **App.xaml.cs**，然後關閉檔案。

14. 中**方案總管**，請在**備忘稿**專案中，以滑鼠右鍵按一下**MainPage.xaml**，然後選取**刪除**。 在對話方塊中顯示按下**確定**按鈕即可移除您的硬碟中的檔案。

    這會移除不再使用的頁面。

15. 建置並執行專案，每個平台。 如需詳細資訊，請參閱 <<c0> [ 建置快速入門](single-page.md#building-the-quickstart)。

    在上**NotesPage**按下 **+** 按鈕，巡覽至**NoteEntryPage**和輸入的附註。 儲存記事應用程式將會瀏覽回到之後**NotesPage**。

    輸入資訊，以觀察應用程式行為的不同長度的數的字。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新應用程式

1. 啟動 Visual Studio for Mac。 在 開始 視窗中按一下**開啟**，並在對話方塊中選取 備忘稿專案的方案檔：

    ![](multi-page-images/vsmac/open-solution.png "開啟方案")

2. 在  **Solution Pad**，選取**備忘稿**專案、 按一下滑鼠右鍵，然後選取**新增 > 新的資料夾**:

    ![](multi-page-images/vsmac/add-new-folder.png "加入新的資料夾")

3. 在  **Solution Pad**，將新資料夾命名**模型**:

    ![](multi-page-images/vsmac/name-folder.png "Models 資料夾")

4. 在  **Solution Pad**，選取**模型**資料夾、 按一下滑鼠右鍵，然後選取**新增 > 新的檔案...**:

    ![](multi-page-images/vsmac/add-new-models-file.png "加入新檔案")

5. 中**新的檔案**對話方塊中，選取**一般 > 空的類別**，將新檔案命名**附註**，然後按一下**新增**按鈕：

    ![](multi-page-images/vsmac/add-note-class.png "新增附註類別")

    這會新增一個名為類別**附註**要**模型**資料夾**備忘稿**專案。

6. 在  **Note.cs**，移除所有範本程式碼，並取代為下列程式碼：

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

    這個類別會定義`Note`會儲存應用程式中的每一個筆記相關資料的模型。

    變更儲存到**Note.cs**選擇**檔案 > 儲存**(或按下 **&#8984; + S**)，然後關閉檔案。

7. 在  **Solution Pad**，選取**備忘稿**專案、 按一下滑鼠右鍵，然後選取**新增 > 新的檔案...**.中**新的檔案**對話方塊中，選取**表單 > Forms ContentPage XAML**，將新檔案命名**NoteEntryPage**，然後按一下**新增**按鈕：

    ![](multi-page-images/vsmac/add-note-entry-page.png "新增 Xamarin.Forms ContentPage")

    這會新增名為的新頁面**NoteEntryPage**到專案的根資料夾。 此頁面將會是應用程式中的第二頁。

8. 在  **NoteEntryPage.xaml**，移除所有範本程式碼，並取代為下列程式碼：

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

      此程式碼以宣告方式定義頁面上，其中包含的使用者介面[ `Editor` ](xref:Xamarin.Forms.Editor)文字輸入和兩個[ `Button` ](xref:Xamarin.Forms.Button)指示儲存或刪除應用程式的執行個體此檔案。 這兩個`Button`執行個體以水平方式配置[ `Grid` ](xref:Xamarin.Forms.Grid)，使用`Editor`並`Grid`垂直配置[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)。 颾魤 ㄛ`Editor`會使用資料繫結至繫結`Text`屬性`Note`模型。 如需有關資料繫結的詳細資訊，請參閱 <<c0> [ 資料繫結](deepdive.md#data-binding)中[Xamarin.Forms 快速入門深入探討](deepdive.md)。

      變更儲存到**NoteEntryPage.xaml**選擇**檔案 > 儲存**(或按下 **&#8984; + S**)，然後關閉檔案。

9. 在  **NoteEntryPage.xaml.cs**，移除所有範本程式碼，並取代為下列程式碼：

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

      此程式碼會儲存`Note`執行個體，代表單一附註，這[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的頁面。 當**儲存** [ `Button` ](xref:Xamarin.Forms.Button)按下`OnSaveButtonClicked`事件處理常式執行時，這是儲存的內容`Editor`到新的檔案，以隨機產生的檔名，或如果正在更新記下現有檔案。 在這兩種情況下，檔案會儲存在應用程式的本機應用程式資料資料夾中。 然後此方法向後巡覽至上一頁。 當**刪除**`Button`按下`OnDeleteButtonClicked`執行事件處理常式時，會刪除檔案，前提是它存在，並向後巡覽至上一頁。 如需有關瀏覽的詳細資訊，請參閱[瀏覽](deepdive.md#navigation)中[Xamarin.Forms 快速入門深入探討](deepdive.md)。

      變更儲存到**NoteEntryPage.xaml.cs**選擇**檔案 > 儲存**(或按下 **&#8984; + S**)，然後關閉檔案。

      > [!WARNING]
      > 嘗試建置應用程式現在將會產生將在後續步驟中修正的錯誤。

10. 在  **Solution Pad**，選取**備忘稿**專案、 按一下滑鼠右鍵，然後選取**新增 > 新的檔案...**.中**新的檔案**對話方塊中，選取**表單 > Forms ContentPage XAML**，將新檔案命名**NotesPage**，然後按一下**新增**按鈕。

      這會新增名為的頁面**NotesPage**到專案的根資料夾。 此頁面將會是應用程式的 [根] 頁面。

11. 在  **NotesPage.xaml**，移除所有範本程式碼，並取代為下列程式碼：

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

    此程式碼以宣告方式定義頁面上，其中包含的使用者介面[ `ListView` ](xref:Xamarin.Forms.ListView)並[ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem)。 `ListView`會使用資料繫結來顯示應用程式時，所擷取的任何附註，然後選取 附註會瀏覽至`NoteEntryPage`修改附註的位置。 或者，您可以在新便箋建立按`ToolbarItem`。 如需有關資料繫結的詳細資訊，請參閱 <<c0> [ 資料繫結](deepdive.md#data-binding)中[Xamarin.Forms 快速入門深入探討](deepdive.md)。

    變更儲存到**NotesPage.xaml**選擇**檔案 > 儲存**(或按下 **&#8984; + S**)，然後關閉檔案。

12. 在  **NotesPage.xaml.cs**，移除所有範本程式碼，並取代為下列程式碼：

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

    此程式碼定義的功能`NotesPage`。 頁面出現時，`OnAppearing`執行方法時，這會填入[ `ListView` ](xref:Xamarin.Forms.ListView)與任何從本機應用程式資料資料夾中擷取的資訊。 當[ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem)按下`OnNoteAddedClicked`事件處理常式會執行。 這個方法會瀏覽至`NoteEntryPage`，將[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的`NoteEntryPage`新`Note`執行個體。 中的項目`ListView`已選取`OnListViewItemSelected`事件處理常式會執行。 這個方法會瀏覽至`NoteEntryPage`，將[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的`NoteEntryPage`於所選`Note`執行個體。 如需有關瀏覽的詳細資訊，請參閱[瀏覽](deepdive.md#navigation)中[Xamarin.Forms 快速入門深入探討](deepdive.md)。

    變更儲存到**NotesPage.xaml.cs**選擇**檔案 > 儲存**(或按下 **&#8984; + S**)，然後關閉檔案。

    > [!WARNING]
    > 嘗試建置應用程式現在將會產生將在後續步驟中修正的錯誤。

13. 在  **Solution Pad**，按兩下**App.xaml.cs**加以開啟。 然後以下列程式碼取代現有的程式碼：

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

    這個程式碼加入的命名空間宣告`System.IO`命名空間，並將宣告靜態`FolderPath`型別的屬性`string`。 `FolderPath`屬性用來將路徑儲存在裝置上附註資料的儲存位置。 此外，程式碼會初始化`FolderPath`中的屬性`App`建構函式，並初始化[ `MainPage` ](xref:Xamarin.Forms.Application.MainPage)屬性[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)裝載執行個體`NotesPage`。 如需有關瀏覽的詳細資訊，請參閱[瀏覽](deepdive.md#navigation)中[Xamarin.Forms 快速入門深入探討](deepdive.md)。

    選擇 [檔案] > [儲存] (或按下 **&#8984; + S**) 以將變更儲存到 **App.xaml.cs**，然後關閉檔案。

14. 在**Solution Pad**，請在**備忘稿**專案，以滑鼠右鍵按一下**MainPage.xaml**，然後選取**移除**。 在對話方塊中顯示按下**刪除**按鈕即可移除您的硬碟中的檔案。

    這會移除不再使用的頁面。

15. 建置並執行專案，每個平台。 如需詳細資訊，請參閱 <<c0> [ 建置快速入門](single-page.md#building-the-quickstart)。

    在上**NotesPage**按下 **+** 按鈕，巡覽至**NoteEntryPage**和輸入的附註。 儲存記事應用程式將會瀏覽回到之後**NotesPage**。

    輸入資訊，以觀察應用程式行為的不同長度的數的字。

::: zone-end

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

- 將其他頁面新增至 Xamarin.Forms 方案中。
- 執行頁面之間瀏覽。
- 您可以使用資料繫結，同步處理使用者介面項目和其資料來源之間的資料。

若要修改應用程式，以便將其資料儲存在本機的 SQLite.NET 資料庫，請繼續下一個快速入門。

> [!div class="nextstepaction"]
> [下一步](database.md)

## <a name="related-links"></a>相關連結

- [Notes (範例)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/)
- [Xamarin.Forms 快速入門深入探討](deepdive.md)
