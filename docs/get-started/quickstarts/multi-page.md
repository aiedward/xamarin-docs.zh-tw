---
title: 在多頁 Xamarin.Forms 應用程式中執行巡覽
description: 本文說明如何將單頁應用程式 (能夠儲存單一備註) 變成多頁應用程式 (能夠儲存多個備註)。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 9DC3B3D6-6CBC-4705-BE80-3D86A9E65F92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: 9ce02b4c6412eab1f4b1003b262573c59940286c
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "68653788"
---
# <a name="perform-navigation-in-a-multi-page-xamarinforms-application"></a>在多頁 Xamarin.Forms 應用程式中執行導覽

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)

在本快速入門中，您將了解如何：

- 將其他頁面新增至 Xamarin.Forms 方案。
- 執行頁面與頁面間的導覽。
- 使用資料繫結以同步處理使用者介面元素及其資料來源間的資料。

快速入門會逐步說明如何將單頁跨平台 Xamarin.Forms 應用程式 (能夠儲存單一備註) 變成多頁應用程式 (能夠儲存多個備註)。 最終的應用程式如下所示：

[![](multi-page-images/screenshots1-sml.png "Notes Page")](multi-page-images/screenshots1.png#lightbox "Notes Page")
[![](multi-page-images/screenshots2-sml.png "Note Entry Page")](multi-page-images/screenshots2.png#lightbox "Note Entry Page")

### <a name="prerequisites"></a>必要條件

您應該先成功完成[先前的快速入門](single-page.md)，再嘗試本快速入門。 或者，下載[先前的快速入門範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/)，並使用此範例作為本快速入門的起點。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新應用程式

1. 啟動 Visual Studio。 在開始視窗中，按一下最近使用的專案/方案清單中的 **Notes** 方案，或按一下 [Open a project or solution] \(開啟專案或方案\)  ，然後在 [開啟專案/方案]  對話方塊中，選取 Notes 專案的方案檔：

    ![](multi-page-images/vs/open-solution.png "Open Project")

2. 在 [方案總管]  中，以滑鼠右鍵按一下 **Notes** 專案，然後選取 [新增] > [新增資料夾]  ：

    ![](multi-page-images/vs/add-new-item.png "Add New Item")

3. 在 [方案總管]  中，將新資料夾命名為 **Models**：

    ![](multi-page-images/vs/name-folder.png "Models Folder")

4. 在 [方案總管]  中，選取 **Models** 資料夾，按一下滑鼠右鍵，然後選取 [新增] > [新增項目]  ：

    ![](multi-page-images/vs/add-new-models-file.png "Add New File")

5. 在 [新增項目]  對話方塊中，選取 [Visual C# 項目] > [類別]  ，將新檔案命名為 **Note**，然後按一下 [新增]  按鈕：

    ![](multi-page-images/vs/add-note-class.png "Add Note Class")

    這會將名為 **Note** 的類別新增至 **Notes** 專案的 **Models** 資料夾。

6. 在 **Note.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

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

    此類別會定義 `Note` 模型，以儲存應用程式中每個備註的相關資料。    

    按下 **CTRL+S**，將變更儲存到 **Note.cs**，然後關閉檔案。

7. 在 [方案總管]  中，以滑鼠右鍵按一下 **Notes** 專案，然後選取 [新增] > [新增項目]  。在 [新增項目]  對話方塊中，選取 [Visual C# 項目] > [Xamarin.Forms] > [內容頁面]  ，將新檔案命名為 **NoteEntryPage**，然後按一下 [新增]  按鈕：

    ![](multi-page-images/vs/add-note-entry-page.png "Add Xamarin.Forms ContentPage")

    這會將名為 **NoteEntryPage** 的新頁面新增至專案根資料夾。 此頁面會是應用程式的第二個頁面。

8. 在 **NoteEntryPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

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

      此程式碼以宣告方式定義頁面的使用者介面，包含用於文字輸入的 [`Editor`](xref:Xamarin.Forms.Editor)，以及指示應用程式要儲存或刪除檔案的二個 [`Button`](xref:Xamarin.Forms.Button) 執行個體。 兩個 `Button` 執行個體以水平方式配置在 [`Grid`](xref:Xamarin.Forms.Grid) 中，而 `Editor` 和 `Grid` 以垂直方式配置在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中。 此外，`Editor` 使用資料繫結以繫結至 `Note` 模型的 `Text` 屬性。 如需資料繫結的詳細資訊，請參閱 [Xamarin.Forms 快速入門深入探討](deepdive.md)中的[資料繫結](deepdive.md#data-binding)。

      按下 **CTRL+S** 將變更儲存到 **NoteEntryPage.xaml**，然後關閉檔案。

9. 在 **NoteEntryPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

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

      此程式碼會在頁面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 中，儲存代表單一備註的 `Note` 執行個體。 按下 [儲存]  [`Button`](xref:Xamarin.Forms.Button) 即會執行 `OnSaveButtonClicked` 事件處理常式，這會將 `Editor` 內容儲存至具有隨機產生檔名的新檔案，如果正在更新備註，則會儲存至現有的檔案。 在這兩種情況下，檔案都會儲存在應用程式深入探討本機應用程式資料資料夾中。 然後，方法則巡覽回上一頁。 按下 [刪除]  `Button` 即會執行 `OnDeleteButtonClicked` 事件處理常式，這會刪除檔案 (若檔案存在)，並巡覽回上一頁。 如需導覽的詳細資訊，請參閱 [Xamarin.Forms 快速入門深入探討](deepdive.md)中的[導覽](deepdive.md#navigation)。

      按下 **CTRL+S** 將變更儲存到 **NoteEntryPage.xaml.cs**，然後關閉檔案。

      > [!WARNING]
      > 此時若嘗試建置應用程式，將導致會在後續步驟中修正的錯誤。

10. 在 [方案總管]  中，以滑鼠右鍵按一下 **Notes** 專案，然後選取 [新增] > [新增項目]  。在 [新增項目]  對話方塊中，選取 [Visual C# 項目] > [Xamarin.Forms] > [內容頁面]  ，將新檔案命名為 **NotesPage**，然後按一下 [新增]  按鈕。

      這會將名為 **NotesPage** 的頁面新增至專案根資料夾。 此頁面會成為應用程式的根頁面。

11. 在 **NotesPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

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

    此程式碼會以宣告方式定義頁面的使用者介面，其包含一個 [`ListView`](xref:Xamarin.Forms.ListView) 和一個 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)。 `ListView` 使用資料繫結顯示應用程式擷取的任何備註，而選取備註將會巡覽至可修改備註的 `NoteEntryPage`。 或者，您也可以按下 `ToolbarItem` 建立新的備註。 如需資料繫結的詳細資訊，請參閱 [Xamarin.Forms 快速入門深入探討](deepdive.md)中的[資料繫結](deepdive.md#data-binding)。

    按下 **CTRL+S** 將變更儲存到 **NotesPage.xaml**，然後關閉檔案。

12. 在 **NotesPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

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

    此程式碼會定義 `NotesPage` 的功能。 當頁面出現時，即會執行 `OnAppearing` 方法，這會將擷取自本機應用程式資料資料夾的任何備註填入 [`ListView`](xref:Xamarin.Forms.ListView)。 按下 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 即會執行 `OnNoteAddedClicked` 事件處理常式。 此方法會巡覽至 `NoteEntryPage`，將 `NoteEntryPage` 的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 設為新的 `Note` 執行個體。 選取 `ListView` 中的項目即會執行 `OnListViewItemSelected` 事件處理常式。 此方法會巡覽至 `NoteEntryPage`，將 `NoteEntryPage` 的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 設為選取的 `Note` 執行個體。 如需導覽的詳細資訊，請參閱 [Xamarin.Forms 快速入門深入探討](deepdive.md)中的[導覽](deepdive.md#navigation)。

    按下 **CTRL+S** 將變更儲存到 **NotesPage.xaml.cs**，然後關閉檔案。

    > [!WARNING]
    > 此時若嘗試建置應用程式，將導致會在後續步驟中修正的錯誤。

13. 在 [方案總管]  中，按兩下 **App.xaml.cs** 以開啟此檔案。 將現有程式碼取代成下列程式碼：

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

    這段程式碼會新增 `System.IO` 命名空間的命名空間宣告，並新增 `string` 類型的靜態 `FolderPath` 屬性宣告。 `FolderPath` 屬性用於儲存裝置路徑，其為儲存備註資料的路徑。 此外，程式碼會初始化 `App` 建構函式中的 `FolderPath` 屬性，並將 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) 屬性初始化為可裝載 `NotesPage` 執行個體的 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)。 如需導覽的詳細資訊，請參閱 [Xamarin.Forms 快速入門深入探討](deepdive.md)中的[導覽](deepdive.md#navigation)。

    按下 **CTRL+S** 以將變更儲存到 **App.xaml.cs**，然後關閉檔案。

14. 在 [方案總管]  的 **Notes** 專案中，以滑鼠右鍵按一下 **MainPage.xaml**，然後選取 [刪除]  。 在出現的對話方塊中，按下 [確定]  按鈕，以從硬碟中移除檔案。

    這會移除不再使用的頁面。

15. 在每個平台上建置並執行專案。 如需詳細資訊，請參閱[建置快速入門](single-page.md#building-the-quickstart)。

    在 **NotesPage** 上按 **+** 按鈕，以巡覽至 **NoteEntryPage** 並輸入備註。 儲存備註之後，應用程式會巡覽回 **NotesPage**。

    輸入多個長短不一的備註，以觀察應用程式的行為。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新應用程式

1. 啟動 Visual Studio for Mac。 在起始視窗中，按一下 [開啟]  ，然後在對話方塊中選取適用於 Notes 專案的方案檔：

    ![](multi-page-images/vsmac/open-solution.png "Open Solution")

2. 在 **Solution Pad** 中，選取 **Notes** 專案，按一下滑鼠右鍵，然後選取 [新增] > [新增資料夾]  ：

    ![](multi-page-images/vsmac/add-new-folder.png "Add New Folder")

3. 在 **Solution Pad** 中，將新資料夾命名為 **Models**：

    ![](multi-page-images/vsmac/name-folder.png "Models Folder")

4. 在 **Solution Pad** 中，選取 **Models** 資料夾，按一下滑鼠右鍵，然後選取 [新增] > [新增檔案]  ：

    ![](multi-page-images/vsmac/add-new-models-file.png "Add New File")

5. 在 [新增檔案]  對話方塊中，選取 [一般] > [空的類別]  ，將新檔案命名為 **Note**，然後按一下 [新增]  按鈕：

    ![](multi-page-images/vsmac/add-note-class.png "Add Note Class")

    這會將名為 **Note** 的類別新增至 **Notes** 專案 **Models** 資料夾。

6. 在 **Note.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

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

    此類別會定義 `Note` 模型，以儲存應用程式中每個備註的相關資料。

    選擇 [檔案] > [儲存]  (或按下 **&#8984; + S**)，將變更儲存到 **Note.cs**，然後關閉檔案。

7. 在 **Solution Pad** 中，選取 **Notes** 專案，按一下滑鼠右鍵，然後選取 [新增] > [新增檔案]  。在 [新增檔案]  對話方塊中，選取 [表單] > [Forms ContentPage XAML] \(建立 ContentPage XAML 表單\)  ，將新檔案命名為 **NoteEntryPage**，然後按一下 [新增]  按鈕：

    ![](multi-page-images/vsmac/add-note-entry-page.png "Add Xamarin.Forms ContentPage")

    這會將名為 **NoteEntryPage** 的新頁面新增至專案根資料夾。 此頁面會是應用程式的第二個頁面。

8. 在 **NoteEntryPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

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

      此程式碼以宣告方式定義頁面的使用者介面，包含用於文字輸入的 [`Editor`](xref:Xamarin.Forms.Editor)，以及指示應用程式要儲存或刪除檔案的二個 [`Button`](xref:Xamarin.Forms.Button) 執行個體。 兩個 `Button` 執行個體以水平方式配置在 [`Grid`](xref:Xamarin.Forms.Grid) 中，而 `Editor` 和 `Grid` 以垂直方式配置在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中。 此外，`Editor` 使用資料繫結以繫結至 `Note` 模型的 `Text` 屬性。 如需資料繫結的詳細資訊，請參閱 [Xamarin.Forms 快速入門深入探討](deepdive.md)中的[資料繫結](deepdive.md#data-binding)。

      選擇 [檔案] > [儲存]  (或按下 **&#8984; + S**) 將變更儲存到 **NoteEntryPage.xaml**，然後關閉檔案。

9. 在 **NoteEntryPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

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

      此程式碼會在頁面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 中，儲存代表單一備註的 `Note` 執行個體。 按下 [儲存]  [`Button`](xref:Xamarin.Forms.Button) 即會執行 `OnSaveButtonClicked` 事件處理常式，這會將 `Editor` 內容儲存至具有隨機產生檔名的新檔案，如果正在更新備註，則會儲存至現有的檔案。 在這兩種情況下，檔案都會儲存在應用程式深入探討本機應用程式資料資料夾中。 然後，方法則巡覽回上一頁。 按下 [刪除]  `Button` 即會執行 `OnDeleteButtonClicked` 事件處理常式，這會刪除檔案 (若檔案存在)，並巡覽回上一頁。 如需導覽的詳細資訊，請參閱 [Xamarin.Forms 快速入門深入探討](deepdive.md)中的[導覽](deepdive.md#navigation)。

      選擇 [檔案] > [儲存]  (或按下 **&#8984; + S**) 將變更儲存到 **NoteEntryPage.xaml.cs**，然後關閉檔案。

      > [!WARNING]
      > 此時若嘗試建置應用程式，將導致會在後續步驟中修正的錯誤。

10. 在 **Solution Pad** 中，選取 **Notes** 專案，按一下滑鼠右鍵，然後選取 [新增] > [新增檔案]  。在 [新增檔案]  對話方塊中，選取 [表單] > [Forms ContentPage XAML] \(建立 ContentPage XAML 表單\)  ，將新檔案命名為 **NotesPage**，然後按一下 [新增]  按鈕。

      這會將名為 **NotesPage** 的頁面新增至專案根資料夾。 此頁面會成為應用程式的根頁面。

11. 在 **NotesPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

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

    此程式碼會以宣告方式定義頁面的使用者介面，其包含一個 [`ListView`](xref:Xamarin.Forms.ListView) 和一個 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)。 `ListView` 使用資料繫結顯示應用程式擷取的任何備註，而選取備註將會巡覽至可修改備註的 `NoteEntryPage`。 或者，您也可以按下 `ToolbarItem` 來建立新的備註。 如需資料繫結的詳細資訊，請參閱 [Xamarin.Forms 快速入門深入探討](deepdive.md)中的[資料繫結](deepdive.md#data-binding)。

    選擇 [檔案] > [儲存]  (或按下 **&#8984; + S**) 將變更儲存到 **NotesPage.xaml**，然後關閉檔案。

12. 在 **NotesPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

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

    此程式碼會定義 `NotesPage` 的功能。 當頁面出現時，即會執行 `OnAppearing` 方法，這會將擷取自本機應用程式資料資料夾的任何備註填入 [`ListView`](xref:Xamarin.Forms.ListView)。 按下 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 即會執行 `OnNoteAddedClicked` 事件處理常式。 此方法會巡覽至 `NoteEntryPage`，將 `NoteEntryPage` 的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 設為新的 `Note` 執行個體。 選取 `ListView` 中的項目即會執行 `OnListViewItemSelected` 事件處理常式。 此方法會巡覽至 `NoteEntryPage`，將 `NoteEntryPage` 的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 設為選取的 `Note` 執行個體。 如需導覽的詳細資訊，請參閱 [Xamarin.Forms 快速入門深入探討](deepdive.md)中的[導覽](deepdive.md#navigation)。

    選擇 [檔案] > [儲存]  (或按下 **&#8984; + S**) 將變更儲存到 **NotesPage.xaml.cs**，然後關閉檔案。

    > [!WARNING]
    > 此時若嘗試建置應用程式，將導致會在後續步驟中修正的錯誤。

13. 在 **Solution Pad** 中，按兩下 **App.xaml.cs** 以開啟此檔案。 將現有程式碼取代成下列程式碼：

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

    這段程式碼會新增 `System.IO` 命名空間的命名空間宣告，並新增 `string` 類型的靜態 `FolderPath` 屬性宣告。 `FolderPath` 屬性用於儲存裝置路徑，其為儲存備註資料的路徑。 此外，程式碼會初始化 `App` 建構函式中的 `FolderPath` 屬性，並將 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) 屬性初始化為可裝載 `NotesPage` 執行個體的 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)。 如需導覽的詳細資訊，請參閱 [Xamarin.Forms 快速入門深入探討](deepdive.md)中的[導覽](deepdive.md#navigation)。

    選擇 [檔案] > [儲存]  (或按下 **&#8984; + S**) 以將變更儲存到 **App.xaml.cs**，然後關閉檔案。

14. 在 **Solution Pad** 的 **Notes** 專案中，以滑鼠右鍵按一下 **MainPage.xaml**，然後選取 [移除]  。 在出現的對話方塊中，按下 [刪除]  按鈕，以從硬碟中移除檔案。

    這會移除不再使用的頁面。

15. 在每個平台上建置並執行專案。 如需詳細資訊，請參閱[建置快速入門](single-page.md#building-the-quickstart)。

    在 **NotesPage** 上按 **+** 按鈕，以巡覽至 **NoteEntryPage** 並輸入備註。 儲存備註之後，應用程式會巡覽回 **NotesPage**。

    輸入多個長短不一的備註，以觀察應用程式的行為。

::: zone-end

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何：

- 將其他頁面新增至 Xamarin.Forms 方案。
- 執行頁面與頁面間的導覽。
- 使用資料繫結以同步處理使用者介面元素及其資料來源間的資料。

若要修改應用程式，使其能將資料儲存在本機 SQLite.NET 資料庫中，則請繼續下一個快速入門。

> [!div class="nextstepaction"]
> [下一步](database.md)

## <a name="related-links"></a>相關連結

- [Notes (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)
- [Xamarin.Forms 快速入門深入探討](deepdive.md)
