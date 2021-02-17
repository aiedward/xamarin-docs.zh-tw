---
title: 在應用程式中執行導覽 Xamarin.Forms
description: 本文說明如何將 Xamarin.Forms Shell 應用程式（能夠儲存單一附注）轉換成可儲存多個附注的應用程式。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 030204F7-E9E4-4AE3-B9F7-915B697D0171
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.custom: contperf-fy21q3
ms.date: 01/28/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c218c7108ec70ab1ccd1b048e1655dba02c48660
ms.sourcegitcommit: 66ca2680c8fde18a55ce5daa3818edeeb9ba219b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/17/2021
ms.locfileid: "100569705"
---
# <a name="perform-navigation-in-a-xamarinforms-application"></a>在應用程式中執行導覽 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/getstarted-notes-navigation/)

在本快速入門中，您將了解如何：

- 將其他頁面新增至 Xamarin.Forms Shell 應用程式。
- 執行頁面與頁面間的導覽。
- 使用資料繫結以同步處理使用者介面元素及其資料來源間的資料。

本快速入門會逐步解說如何將跨平臺 Xamarin.Forms Shell 應用程式（能夠儲存單一附注）轉換成能夠儲存多個備註的應用程式。 最終的應用程式如下所示：

[ ![ 附注頁面](navigation-images/screenshots1-sml.png)](navigation-images/screenshots1.png#lightbox "[備註] 頁面") 
 [ ![ 筆記輸入頁面](navigation-images/screenshots2-sml.png)](navigation-images/screenshots2.png#lightbox "[備註輸入] 頁面")

### <a name="prerequisites"></a>必要條件

您應該先成功完成[先前的快速入門](app.md)，再嘗試本快速入門。 或者，下載[先前的快速入門範例](/samples/xamarin/xamarin-forms-samples/getstarted-notes-app/)，並使用此範例作為本快速入門的起點。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新應用程式

1. 啟動 Visual Studio。 在開始視窗中，按一下最近使用的專案/方案清單中的 **Notes** 方案，或按一下 [Open a project or solution] \(開啟專案或方案\)，然後在 [開啟專案/方案] 對話方塊中，選取 Notes 專案的方案檔：

    ![開啟方案](navigation-images/vs/open-solution.png)

2. 在 **方案總管** 中，以滑鼠右鍵按一下 **Notes** 專案，然後選取 [ **加入 > 新資料夾**：

    ![新增資料夾](navigation-images/vs/add-new-folder.png)

3. 在 [方案總管] 中，將新資料夾命名為 **Models**：

    ![Models 資料夾](navigation-images/vs/name-folder.png)

4. 在 **方案總管** 中，選取 [ **模型** ] 資料夾、按一下滑鼠右鍵，然後選取 [ **新增 > 類別 ...**]：

    ![新增檔案](navigation-images/vs/add-new-models-file.png)

5. 在 [新增項目] 對話方塊中，選取 [Visual C# 項目] > [類別]，將新檔案命名為 **Note**，然後按一下 [新增] 按鈕：

    ![新增備註類別](navigation-images/vs/add-note-class.png)

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

    按 **CTRL + S**，將變更儲存至 **Note.cs** 。  

7. 在 **方案總管** 的 **Notes** 專案中，選取 [ **Views** ] 資料夾，按一下滑鼠右鍵，然後選取 [ **加入 > 新專案**...]。在 [ **加入新專案** ] 對話方塊中，選取 [ **Visual c # 專案] > Xamarin.Forms > 內容] 頁面**，將新的檔案命名為 **>noteentrypage.xaml.cs**，然後按一下 [ **新增** ] 按鈕：

    ![Add：：：非 loc (Xamarin. Forms) ：：： ContentPage](navigation-images/vs/add-note-entry-page.png)

    這會將名為 **>noteentrypage.xaml.cs** 的新頁面新增至專案的 **Views** 資料夾中。 此頁面將用於附注專案。

8. 在 **NoteEntryPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.Views.NoteEntryPage"
                   Title="Note Entry">
          <!-- Layout children vertically -->
          <StackLayout Margin="20">
              <Editor Placeholder="Enter your note"
                      Text="{Binding Text}"
                      HeightRequest="100" />
              <!-- Layout children in two columns -->
              <Grid ColumnDefinitions="*,*">
                  <Button Text="Save"
                          Clicked="OnSaveButtonClicked" />
                  <Button Grid.Column="1"
                          Text="Delete"
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      這段程式碼會以宣告方式定義頁面的使用者介面，其中包含 [`Editor`](xref:Xamarin.Forms.Editor) 用於文字輸入的，以及指示 [`Button`](xref:Xamarin.Forms.Button) 應用程式儲存或刪除檔案的兩個物件。 這兩個 `Button` 實例會以水準方式配置在中 [`Grid`](xref:Xamarin.Forms.Grid) ， `Editor` 並在 `Grid` 中垂直配置和 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 此外，`Editor` 使用資料繫結以繫結至 `Note` 模型的 `Text` 屬性。 如需資料系結的詳細資訊，請參閱[ Xamarin.Forms 快速入門深入探討](deepdive.md)中的[資料](deepdive.md#data-binding)系結。

      按 **CTRL + S**，將變更儲存至 **>noteentrypage.xaml.cs。**

9. 在 **NoteEntryPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

      ```csharp
      using System;
      using System.IO;
      using Notes.Models;
      using Xamarin.Forms;

      namespace Notes.Views
      {
          [QueryProperty(nameof(ItemId), nameof(ItemId))]
          public partial class NoteEntryPage : ContentPage
          {
              public string ItemId
              {
                  set
                  {
                      LoadNote(value);
                  }
              }

              public NoteEntryPage()
              {
                  InitializeComponent();

                  // Set the BindingContext of the page to a new Note.
                  BindingContext = new Note();
              }

              void LoadNote(string filename)
              {
                  try
                  {
                      // Retrieve the note and set it as the BindingContext of the page.
                      Note note = new Note
                      {
                          Filename = filename,
                          Text = File.ReadAllText(filename),
                          Date = File.GetCreationTime(filename)
                      };
                      BindingContext = note;
                  }
                  catch (Exception)
                  {
                      Console.WriteLine("Failed to load note.");
                  }
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save the file.
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update the file.
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  // Navigate backwards
                  await Shell.Current.GoToAsync("..");
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  // Delete the file.
                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  // Navigate backwards
                  await Shell.Current.GoToAsync("..");
              }
          }
      }
      ```

      此程式碼會 `Note` 在頁面的中儲存代表單一附注的實例 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。 類別的裝飾是，可 `QueryPropertyAttribute` 讓資料透過查詢參數傳遞至頁面中。 的第一個引數 `QueryPropertyAttribute` 會指定將接收資料之屬性的名稱，而第二個引數則指定查詢參數識別碼。因此， `QueryParameterAttribute` 上述程式碼中的 `ItemId` 會指定屬性將 `ItemId` 從方法呼叫中指定的 URI 接收查詢參數中所傳遞的資料 `GoToAsync` 。 `ItemId`然後，屬性會呼叫 `LoadNote` 方法 `Note` ，從裝置上的檔案建立物件，並將頁面的設定 `BindingContext` 為 `Note` 物件。

      當按下 [ **儲存**] 時 [`Button`](xref:Xamarin.Forms.Button) `OnSaveButtonClicked` ，就會執行事件處理常式，這會將的內容儲存 `Editor` 至具有隨機產生之檔案名的新檔案，或儲存至現有檔案（如果正在更新備註）。 在這兩種情況下，檔案都會儲存在應用程式深入探討本機應用程式資料資料夾中。 然後，方法則巡覽回上一頁。 按下 [刪除] `Button` 即會執行 `OnDeleteButtonClicked` 事件處理常式，這會刪除檔案 (若檔案存在)，並巡覽回上一頁。 如需導覽的詳細資訊，請參閱[ Xamarin.Forms Shell 快速入門](deepdive.md)中的[導覽](deepdive.md#navigation)深入探討。

      按 **CTRL + S**，將變更儲存至 **NoteEntryPage.xaml.cs** 。

      > [!WARNING]
      > 應用程式目前不會因為將在後續步驟中修正的錯誤而建立。

10. 在 **方案總管** 的 **Notes** 專案中，在 [ **Views** ] 資料夾中開啟 **>notespage.xaml.cs。**

11. 在 **NotesPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NotesPage"
                 Title="Notes">
        <!-- Add an item to the toolbar -->
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="Add"
                         Clicked="OnAddClicked" />
        </ContentPage.ToolbarItems>

        <!-- Display notes in a list -->
        <CollectionView x:Name="collectionView"
                        Margin="20"
                        SelectionMode="Single"
                        SelectionChanged="OnSelectionChanged">
            <CollectionView.ItemsLayout>
                <LinearItemsLayout Orientation="Vertical"
                                   ItemSpacing="10" />
            </CollectionView.ItemsLayout>
            <!-- Define the appearance of each item in the list -->
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <StackLayout>
                        <Label Text="{Binding Text}"
                               FontSize="Medium"/>
                        <Label Text="{Binding Date}"
                               TextColor="Silver"
                               FontSize="Small" />
                    </StackLayout>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </ContentPage>
    ```

    這段程式碼會以宣告方式定義頁面的使用者介面，其中包含 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 和 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 。 `CollectionView`會使用資料系結來顯示應用程式所取出的任何附注。 選取便箋會流覽至 `NoteEntryPage` 可修改附注的位置。 或者，您也可以按下 `ToolbarItem` 來建立新的備註。 如需資料系結的詳細資訊，請參閱[ Xamarin.Forms 快速入門深入探討](deepdive.md)中的[資料](deepdive.md#data-binding)系結。

    按 **CTRL + S**，將變更儲存至 **>notespage.xaml.cs。**

12. 在 **方案總管** 的 **Notes** 專案中，展開 **Views** 資料夾中的 **>notespage.xaml.cs** ，然後開啟 **NotesPage.xaml.cs**。

13. 在 **NotesPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Notes.Models;
    using Xamarin.Forms;

    namespace Notes.Views
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

                // Create a Note object from each file.
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

                // Set the data source for the CollectionView to a
                // sorted collection of notes.
                collectionView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnAddClicked(object sender, EventArgs e)
            {
                // Navigate to the NoteEntryPage, without passing any data.
                await Shell.Current.GoToAsync(nameof(NoteEntryPage));
            }

            async void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
            {
                if (e.CurrentSelection != null)
                {
                    // Navigate to the NoteEntryPage, passing the filename as a query parameter.
                    Note note = (Note)e.CurrentSelection.FirstOrDefault();
                    await Shell.Current.GoToAsync($"{nameof(NoteEntryPage)}?{nameof(NoteEntryPage.ItemId)}={note.Filename}");
                }
            }
        }
    }
    ```

    此程式碼會定義 `NotesPage` 的功能。 當頁面出現時， `OnAppearing` 會執行方法，該方法會在中填入 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 已從本機應用程式資料檔案夾中取出的任何附注。 當 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 按下時， `OnAddClicked` 就會執行事件處理常式。 這個方法會導覽至 `NoteEntryPage` 。 選取 `CollectionView` 中的項目即會執行 `OnSelectionChanged` 事件處理常式。 `NoteEntryPage`如果已選取中的專案，這個方法會導覽至， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 並將 `Filename` 選取之的屬性傳遞 `Note` 至頁面。 如需導覽的詳細資訊，請參閱[ Xamarin.Forms 快速入門深入探討](deepdive.md)中的[導覽](deepdive.md#navigation)。

    按 **CTRL + S**，將變更儲存至 **NotesPage.xaml.cs** 。

    > [!WARNING]
    > 應用程式目前不會因為將在後續步驟中修正的錯誤而建立。

14. 在 **方案總管** 的 **Notes** 專案中，展開 [ **AppShell** ]，然後開啟 [ **AppShell.xaml.cs**]。 將現有程式碼取代成下列程式碼：

    ```csharp
    using Notes.Views;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class AppShell : Shell
        {
            public AppShell()
            {
                InitializeComponent();
                Routing.RegisterRoute(nameof(NoteEntryPage), typeof(NoteEntryPage));
            }
        }
    }
    ```

    此程式碼會註冊的路由 `NoteEntryPage` ，此路由不會在 Shell 視覺階層中表示 (**AppShell**) 。 然後，您可以使用方法，將此頁面導覽至使用以 URI 為基礎的導覽 `GoToAsync` 。

    按 **CTRL + S**，將變更儲存至 **AppShell.xaml.cs** 。

15. 在 **方案總管** 的 **Notes** 專案中，展開 [ **app.xaml** ] 並開啟 **App.xaml.cs**。 將現有程式碼取代成下列程式碼：

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
                MainPage = new AppShell();
            }

            protected override void OnStart()
            {
            }

            protected override void OnSleep()
            {
            }

            protected override void OnResume()
            {
            }
        }
    }

    ```

    這段程式碼會新增 `System.IO` 命名空間的命名空間宣告，並新增 `string` 類型的靜態 `FolderPath` 屬性宣告。 `FolderPath` 屬性用於儲存裝置路徑，其為儲存備註資料的路徑。 此外，程式碼會初始化函式 `FolderPath` 中的屬性 `App` ，並將屬性初始化為子類別化 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) 的 `Shell` 物件。

    按 **CTRL + S**，將變更儲存至 **App.xaml.cs** 。

16. 在每個平台上建置並執行專案。 如需詳細資訊，請參閱[建置快速入門](app.md#building-the-quickstart)。

    在 **>notespage.xaml.cs** 上，按下 [ **新增** ] 按鈕以流覽至 **>noteentrypage.xaml.cs** 並輸入備註。 儲存備註之後，應用程式會巡覽回 **NotesPage**。

    輸入數個不同長度的附注，以觀察應用程式行為。 關閉應用程式並重新啟動，以確定您輸入的附注已儲存至裝置。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新應用程式

1. 啟動 Visual Studio for Mac。 在起始視窗中，按一下 [開啟]，然後在對話方塊中選取適用於 Notes 專案的方案檔：

    ![開啟方案](navigation-images/vsmac/open-solution.png)

2. 在 [ **Solution Pad** 中，以滑鼠右鍵按一下 [ **Notes** ] 專案，然後選取 [ **加入 > 新資料夾**：

    ![新增資料夾](navigation-images/vsmac/add-new-folder.png)

3. 在 [ **新增資料夾** ] 對話方塊中，將新的資料夾 **模型** 命名為：

    ![Models 資料夾](navigation-images/vsmac/name-folder.png)

4. 在 [ **Solution Pad** 中，選取 [ **模型** ] 資料夾、按一下滑鼠右鍵，然後選取 [ **新增類別] >**[...]：

    ![新增檔案](navigation-images/vsmac/add-new-models-file.png)

5. 在 [新增檔案] 對話方塊中，選取 [一般] > [空的類別]，將新檔案命名為 **Note**，然後按一下 [新增] 按鈕：

    ![新增備註類別](navigation-images/vsmac/add-note-class.png)

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

    選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 **Note.cs** 的變更。

7. 在 [ **Solution Pad** 中，選取 [ **附注** ] 專案，按一下滑鼠右鍵，然後選取 [ **加入 > 新** 檔案 ...]。在 [ **新增** 檔案] 對話方塊中，選取 **表單 > form ContentPage XAML**、將新檔案命名為 **>noteentrypage.xaml.cs**，然後按一下 [ **新增** ] 按鈕：

    ![Add：：：非 loc (Xamarin. Forms) ：：： ContentPage](navigation-images/vsmac/add-note-entry-page.png)

    這會將名為 **>noteentrypage.xaml.cs** 的新頁面新增至專案的 **Views** 資料夾中。 此頁面將用於附注專案。

8. 在 **NoteEntryPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.Views.NoteEntryPage"
                   Title="Note Entry">
          <!-- Layout children vertically -->
          <StackLayout Margin="20">
              <Editor Placeholder="Enter your note"
                      Text="{Binding Text}"
                      HeightRequest="100" />
              <!-- Layout children in two columns -->
              <Grid ColumnDefinitions="*,*">
                  <Button Text="Save"
                          Clicked="OnSaveButtonClicked" />
                  <Button Grid.Column="1"
                          Text="Delete"
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      這段程式碼會以宣告方式定義頁面的使用者介面，其中包含 [`Editor`](xref:Xamarin.Forms.Editor) 用於文字輸入的，以及指示 [`Button`](xref:Xamarin.Forms.Button) 應用程式儲存或刪除檔案的兩個物件。 這兩個 `Button` 實例會以水準方式配置在中 [`Grid`](xref:Xamarin.Forms.Grid) ， `Editor` 並在 `Grid` 中垂直配置和 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 此外，`Editor` 使用資料繫結以繫結至 `Note` 模型的 `Text` 屬性。 如需資料系結的詳細資訊，請參閱[ Xamarin.Forms 快速入門深入探討](deepdive.md)中的[資料](deepdive.md#data-binding)系結。

      選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 >noteentrypage.xaml.cs 的變更 **。**

9. 在 **NoteEntryPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

      ```csharp
      using System;
      using System.IO;
      using Notes.Models;
      using Xamarin.Forms;

      namespace Notes.Views
      {
          [QueryProperty(nameof(ItemId), nameof(ItemId))]
          public partial class NoteEntryPage : ContentPage
          {
              public string ItemId
              {
                  set
                  {
                      LoadNote(value);
                  }
              }

              public NoteEntryPage()
              {
                  InitializeComponent();

                  // Set the BindingContext of the page to a new Note.
                  BindingContext = new Note();
              }

              void LoadNote(string filename)
              {
                  try
                  {
                      // Retrieve the note and set it as the BindingContext of the page.
                      Note note = new Note
                      {
                          Filename = filename,
                          Text = File.ReadAllText(filename),
                          Date = File.GetCreationTime(filename)
                      };
                      BindingContext = note;
                  }
                  catch (Exception)
                  {
                      Console.WriteLine("Failed to load note.");
                  }
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save the file.
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update the file.
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  // Navigate backwards
                  await Shell.Current.GoToAsync("..");
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  // Delete the file.
                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  // Navigate backwards
                  await Shell.Current.GoToAsync("..");
              }
          }
      }
      ```

      此程式碼會 `Note` 在頁面的中儲存代表單一附注的實例 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。 類別的裝飾是，可 `QueryPropertyAttribute` 讓資料透過查詢參數傳遞至頁面中。 的第一個引數 `QueryPropertyAttribute` 會指定將接收資料之屬性的名稱，而第二個引數則指定查詢參數識別碼。因此， `QueryParameterAttribute` 上述程式碼中的 `ItemId` 會指定屬性將 `ItemId` 從方法呼叫中指定的 URI 接收查詢參數中所傳遞的資料 `GoToAsync` 。 `ItemId`然後，屬性會呼叫 `LoadNote` 方法 `Note` ，從裝置上的檔案建立物件，並將頁面的設定 `BindingContext` 為 `Note` 物件。

      當按下 [ **儲存**] 時 [`Button`](xref:Xamarin.Forms.Button) `OnSaveButtonClicked` ，就會執行事件處理常式，這會將的內容儲存 `Editor` 至具有隨機產生之檔案名的新檔案，或儲存至現有檔案（如果正在更新備註）。 在這兩種情況下，檔案都會儲存在應用程式深入探討本機應用程式資料資料夾中。 然後，方法則巡覽回上一頁。 按下 [刪除] `Button` 即會執行 `OnDeleteButtonClicked` 事件處理常式，這會刪除檔案 (若檔案存在)，並巡覽回上一頁。 如需導覽的詳細資訊，請參閱[ Xamarin.Forms Shell 快速入門](deepdive.md)中的[導覽](deepdive.md#navigation)深入探討。

      選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 **NoteEntryPage.xaml.cs** 的變更。

      > [!WARNING]
      > 應用程式目前不會因為將在後續步驟中修正的錯誤而建立。

10. 在 [ **Solution Pad**] 的 [**附注**] 專案中，在 [ **Views** ] 資料夾中開啟 **>notespage.xaml.cs。**

11. 在 **NotesPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NotesPage"
                 Title="Notes">
        <!-- Add an item to the toolbar -->
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="Add"
                         Clicked="OnAddClicked" />
        </ContentPage.ToolbarItems>

        <!-- Display notes in a list -->
        <CollectionView x:Name="collectionView"
                        Margin="20"
                        SelectionMode="Single"
                        SelectionChanged="OnSelectionChanged">
            <CollectionView.ItemsLayout>
                <LinearItemsLayout Orientation="Vertical"
                                   ItemSpacing="10" />
            </CollectionView.ItemsLayout>
            <!-- Define the appearance of each item in the list -->
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <StackLayout>
                        <Label Text="{Binding Text}"
                               FontSize="Medium"/>
                        <Label Text="{Binding Date}"
                               TextColor="Silver"
                               FontSize="Small" />
                    </StackLayout>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </ContentPage>
    ```

    這段程式碼會以宣告方式定義頁面的使用者介面，其中包含 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 和 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 。 `CollectionView`會使用資料系結來顯示應用程式所取出的任何附注。 選取便箋會流覽至 `NoteEntryPage` 可修改附注的位置。 或者，您也可以按下 `ToolbarItem` 來建立新的備註。 如需資料系結的詳細資訊，請參閱[ Xamarin.Forms 快速入門深入探討](deepdive.md)中的[資料](deepdive.md#data-binding)系結。

    選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 >notespage.xaml.cs 的變更 **。**

12. 在 **Solution Pad** 的 **Notes** 專案中，展開 **Views** 資料夾中的 **>notespage.xaml.cs** ，然後開啟 **NotesPage.xaml.cs**。

13. 在 **NotesPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Notes.Models;
    using Xamarin.Forms;

    namespace Notes.Views
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

                // Create a Note object from each file.
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

                // Set the data source for the CollectionView to a
                // sorted collection of notes.
                collectionView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnAddClicked(object sender, EventArgs e)
            {
                // Navigate to the NoteEntryPage, without passing any data.
                await Shell.Current.GoToAsync(nameof(NoteEntryPage));
            }

            async void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
            {
                if (e.CurrentSelection != null)
                {
                    // Navigate to the NoteEntryPage, passing the filename as a query parameter.
                    Note note = (Note)e.CurrentSelection.FirstOrDefault();
                    await Shell.Current.GoToAsync($"{nameof(NoteEntryPage)}?{nameof(NoteEntryPage.ItemId)}={note.Filename}");
                }
            }
        }
    }
    ```

    此程式碼會定義 `NotesPage` 的功能。 當頁面出現時， `OnAppearing` 會執行方法，該方法會在中填入 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 已從本機應用程式資料檔案夾中取出的任何附注。 當 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 按下時， `OnAddClicked` 就會執行事件處理常式。 這個方法會導覽至 `NoteEntryPage` 。 選取 `CollectionView` 中的項目即會執行 `OnSelectionChanged` 事件處理常式。 `NoteEntryPage`如果已選取中的專案，這個方法會導覽至， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 並將 `Filename` 選取之的屬性傳遞 `Note` 至頁面。 如需導覽的詳細資訊，請參閱[ Xamarin.Forms 快速入門深入探討](deepdive.md)中的[導覽](deepdive.md#navigation)。

    選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 **NotesPage.xaml.cs** 的變更。

    > [!WARNING]
    > 應用程式目前不會因為將在後續步驟中修正的錯誤而建立。

14. 在 [ **Solution Pad**] 的 [ **附注** ] 專案中，展開 [ **AppShell** ]，然後開啟 [ **AppShell.xaml.cs**]。 將現有程式碼取代成下列程式碼：

    ```csharp
    using Notes.Views;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class AppShell : Shell
        {
            public AppShell()
            {
                InitializeComponent();
                Routing.RegisterRoute(nameof(NoteEntryPage), typeof(NoteEntryPage));
            }
        }
    }
    ```

    這段程式碼會註冊的路由 `NoteEntryPage` ，而不會在 Shell 視覺階層中表示。 然後，您可以使用方法，將此頁面導覽至使用以 URI 為基礎的導覽 `GoToAsync` 。

    選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 **AppShell.xaml.cs** 的變更。

15. 在 [ **Solution Pad**] 的 **Notes** 專案中，展開 [ **app.xaml** ] 並開啟 **App.xaml.cs**。 將現有程式碼取代成下列程式碼：

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
                MainPage = new AppShell();
            }

            protected override void OnStart()
            {
            }

            protected override void OnSleep()
            {
            }

            protected override void OnResume()
            {
            }
        }
    }

    ```

    這段程式碼會新增 `System.IO` 命名空間的命名空間宣告，並新增 `string` 類型的靜態 `FolderPath` 屬性宣告。 `FolderPath` 屬性用於儲存裝置路徑，其為儲存備註資料的路徑。 此外，程式碼會初始化函式 `FolderPath` 中的屬性 `App` ，並將屬性初始化為子類別化 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) 的 `Shell` 物件。

    選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 **App.xaml.cs** 的變更。

16. 在每個平台上建置並執行專案。 如需詳細資訊，請參閱[建置快速入門](app.md#building-the-quickstart)。

    在 **>notespage.xaml.cs** 上，按下 [ **新增** ] 按鈕以流覽至 **>noteentrypage.xaml.cs** 並輸入備註。 儲存備註之後，應用程式會巡覽回 **NotesPage**。

    輸入數個不同長度的附注，以觀察應用程式行為。 關閉應用程式並重新啟動，以確定您輸入的附注已儲存至裝置。

::: zone-end

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

- 將其他頁面新增至 Xamarin.Forms Shell 應用程式。
- 執行頁面與頁面間的導覽。
- 使用資料繫結以同步處理使用者介面元素及其資料來源間的資料。

繼續進行下一個快速入門，以修改應用程式，使其將其資料儲存在本機 SQLite.NET 資料庫中。

> [!div class="nextstepaction"]
> [下一步](database.md)

## <a name="related-links"></a>相關連結

- [Notes (範例)](/samples/xamarin/xamarin-forms-samples/getstarted-notes-navigation/)
- [Xamarin.Forms Shell 快速入門深入探討](deepdive.md)
