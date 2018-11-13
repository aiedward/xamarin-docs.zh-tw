---
title: 在 Xamarin iOS 和 Android 的 visual Basic.NET
description: 本逐步解說示範如何建置原生 Xamarin.iOS 和 Xamarin.Android 應用程式使用 Visual Basic.NET 中撰寫商務邏輯。
ms.prod: xamarin
ms.assetid: 455fda67-3879-4299-8036-b12840e6a498
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: affebab9bb6b07f204beef24cce2b57444d45e49
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527296"
---
# <a name="visual-basicnet-in-xamarin-ios-and-android"></a>在 Xamarin iOS 和 Android 的 visual Basic.NET

[TaskyPortable](/samples/mobile/VisualBasic/TaskyPortableVB/)範例應用程式示範如何搭配 Xamarin 使用 Visual Basic 程式碼編譯成可攜式類別庫。 以下是產生在 iOS、 Android 和 Windows Phone 上執行的應用程式的一些螢幕擷取畫面：

 [![](native-apps-images/image5.png "iOS、 Android 及 Windows 手機執行使用 Visual Basic 所建置的應用程式")](native-apps-images/image5.png#lightbox)

IOS、 Android 和 Windows Phone 專案，在範例中會寫入在C#。 每個應用程式的使用者介面以原生技術建置 (分鏡腳本、 Xml 和 Xaml 分別)，雖然`TodoItem`管理由 Visual Basic 的可攜式類別庫提供使用`IXmlStorage`所提供的實作原生專案中。

## <a name="sample-walkthrough"></a>範例逐步解說

本指南將討論 Visual Basic 中已實作如何[TaskyPortableVB](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)適用於 iOS 和 Android 的 Xamarin 範例。

> [!NOTE]
> 檢閱上的指示[Visual Basic.NET Pcl](/guides/cross-platform/application_fundamentals/pcl/portable_visual_basic_net/)再繼續進行本指南。

## <a name="visualbasicportablelibrary"></a>VisualBasicPortableLibrary

只能在 Visual Studio 中建立 Visual Basic 的可攜式類別庫。
範例程式庫包含我們在四個 Visual Basic 檔案中的應用程式的基本概念：

-  IXmlStorage.vb
-  TodoItem.vb
-  TodoItemManager.vb
-  TodoItemRepositoryXML.vb


### <a name="ixmlstoragevb"></a>IXmlStorage.vb

可攜式類別庫檔案的存取行為能夠大幅不同的平台，因為未提供`System.IO`檔案中的任何設定檔的儲存體 Api。 這表示如果我們想要直接與我們的可攜式程式碼中的檔案系統互動，我們需要在每個平台上回撥原生專案。  藉由撰寫我們的 Visual Basic 程式碼，針對簡單的介面，可實作於C#每個平台上，我們仍然可以存取檔案系統的可共用 Visual Basic 程式碼。

範例程式碼會使用這個非常簡單的介面，其中包含兩個方法： 讀取和寫入序列化的 Xml 檔案。

```vb
Public Interface IXmlStorage
    Function ReadXml(filename As String) As List(Of TodoItem)
    Sub WriteXml(tasks As List(Of TodoItem), filename As String)
End Interface
```

iOS、 Android 和 Windows Phone 實作這個介面會顯示此指南中之後。

### <a name="todoitemvb"></a>TodoItem.vb

這個類別包含用於整個應用程式的商務物件。 它將會在 Visual Basic 中的方式來定義和共用的 ios、 Android 和 Windows Phone 專案所撰寫的C#。

類別定義如下所示：

```vb
Public Class TodoItem
    Property ID() As Integer
    Property Name() As String
    Property Notes() As String
    Property Done() As Boolean
End Class
```

此範例會使用 XML 序列化和還原序列化，以載入和儲存的 TodoItem 物件。

### <a name="todoitemmanagervb"></a>TodoItemManager.vb

管理員類別提供 'API' 可攜式的程式碼。 它提供基本的 CRUD 作業，以`TodoItem`類別，但沒有實作這些作業。

```vb
Public Class TodoItemManager
    Private _repository As TodoItemRepositoryXML
    Public Sub New(filename As String, storage As IXmlStorage)
        _repository = New TodoItemRepositoryXML(filename, storage)
    End Sub
    Public Function GetTask(id As Integer) As TodoItem
        Return _repository.GetTask(id)
    End Function
    Public Function GetTasks() As List(Of TodoItem)
        Return New List(Of TodoItem)(_repository.GetTasks())
    End Function
    Public Function SaveTask(item As TodoItem) As Integer
        Return _repository.SaveTask(item)
    End Function
    Public Function DeleteTask(item As TodoItem) As Integer
        Return _repository.DeleteTask(item.ID)
    End Function
End Class
```

建構函式會接受 IXmlStorage 的執行個體，做為參數。 這可讓每個平台提供它自己的工作實作，同時仍可讓可攜式的程式碼，描述可以共用其他功能。

### <a name="todoitemrepositoryvb"></a>TodoItemRepository.vb

儲存機制類別包含的邏輯管理的 TodoItem 物件清單。 完整的程式碼如下所示-邏輯存在主要是為了跨 TodoItems 管理是唯一的識別碼值，加入並移除集合中。

```vb
Public Class TodoItemRepositoryXML
    Private _filename As String
    Private _storage As IXmlStorage
    Private _tasks As List(Of TodoItem)

    ''' <summary>Constructor</summary>
    Public Sub New(filename As String, storage As IXmlStorage)
        _filename = filename
        _storage = storage
        _tasks = _storage.ReadXml(filename)
    End Sub
    ''' <summary>Inefficient search for a Task by ID</summary>
    Public Function GetTask(id As Integer) As TodoItem
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                Return _tasks(t)
            End If
        Next
        Return New TodoItem() With {.ID = id}
    End Function
    ''' <summary>List all the Tasks</summary>
    Public Function GetTasks() As IEnumerable(Of TodoItem)
        Return _tasks
    End Function
    ''' <summary>Save a Task to the Xml file
    ''' Calculates the ID as the max of existing IDs</summary>
    Public Function SaveTask(item As TodoItem) As Integer
        Dim max As Integer = 0
        If _tasks.Count > 0 Then
            max = _tasks.Max(Function(t As TodoItem) t.ID)
        End If
        If item.ID = 0 Then
            item.ID = ++max
            _tasks.Add(item)
        Else
            Dim j = _tasks.Where(Function(t) t.ID = item.ID).First()
            j = item
        End If
        _storage.WriteXml(_tasks, _filename)
        Return max
    End Function
    ''' <summary>Removes the task from the XMl file</summary>
    Public Function DeleteTask(id As Integer) As Integer
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                _tasks.RemoveAt(t)
                _storage.WriteXml(_tasks, _filename)
                Return 1
            End If
        Next
        Return -1
    End Function
End Class
```

> [!NOTE]
> 此程式碼是非常基本的資料儲存機制的範例。
> 它可示範如何可攜式類別庫可對撰寫程式碼來存取 （在此情況下，在載入和儲存的 Xml 檔案） 的平台專屬功能的介面。 它就不適合是實際執行品質資料庫的替代方案。

## <a name="ios-android-and-windows-phone-application-projects"></a>iOS、 Android 和 Windows Phone 應用程式專案

本章節包含 IXmlStorage 介面的平台特定實作，並示範如何使用每個應用程式。 應用程式專案會寫入C#。

### <a name="ios-and-android-ixmlstorage"></a>iOS 和 Android 的 IXmlStorage

Xamarin.iOS 和 Xamarin.Android 提供完整`System.IO`功能，可讓您可以輕鬆地載入並儲存 Xml 檔案中，使用下列類別：

```csharp
public class XmlStorageImplementation : IXmlStorage
{
    public XmlStorageImplementation(){}
    public List<TodoItem> ReadXml(string filename)
    {
        if (File.Exists(filename))
        {
            var serializer = new XmlSerializer(typeof(List<TodoItem>));
            using (var stream = new FileStream(filename, FileMode.Open))
            {
                return (List<TodoItem>)serializer.Deserialize(stream);
            }
        }
        return new List<TodoItem>();
    }
    public void WriteXml(List<TodoItem> tasks, string filename)
    {
        var serializer = new XmlSerializer(typeof(List<TodoItem>));
        using (var writer = new StreamWriter(filename))
        {
            serializer.Serialize(writer, tasks);
        }
    }
}
```

在 iOS 應用程式中`TodoItemManager`而`XmlStorageImplementation`中建立**AppDelegate.cs**檔案中此程式碼片段所示。 前四行只會建置資料的儲存位置; 檔案的路徑最後兩行顯示要具現化的兩個類別。

```csharp
var xmlFilename = "TodoList.xml";
string documentsPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine(documentsPath, "..", "Library"); // Library folder
var path = Path.Combine(libraryPath, xmlFilename);
var xmlStorage = new XmlStorageImplementation();
TaskMgr = new TodoItemManager(path, xmlStorage);
```

在 Android 應用程式中`TodoItemManager`而`XmlStorageImplementation`中建立**Application.cs**檔案中此程式碼片段所示。 前三行只要建置資料的儲存位置; 檔案的路徑最後兩行顯示要具現化的兩個類別。

```csharp
var xmlFilename = "TodoList.xml";
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
var path = Path.Combine(libraryPath, xmlFilename);
var xmlStorage = new AndroidTodo.XmlStorageImplementation();
TaskMgr = new TodoItemManager(path, xmlStorage);
```

關心的使用者介面及使用應用程式程式碼的其餘部分是主要`TaskMgr`類別來載入及儲存`TodoItem`類別。

### <a name="windows-phone-ixmlstorage"></a>Windows Phone IXmlStorage

Windows Phone 不提供完整存取裝置的檔案系統，而公開 IsolatedStorage API。 `IXmlStorage`實作適用於 Windows Phone 看起來像這樣：

```csharp
public class XmlStorageImplementation : IXmlStorage
{
    public XmlStorageImplementation(){}
    public List<TodoItem> ReadXml(string filename)
    {
        IsolatedStorageFile fileStorage = IsolatedStorageFile.GetUserStoreForApplication();
        if (fileStorage.FileExists(filename))
        {
            var serializer = new XmlSerializer(typeof(List<TodoItem>));
            using (var stream = new StreamReader(new IsolatedStorageFileStream(filename, FileMode.Open, fileStorage)))
            {
                return (List<TodoItem>)serializer.Deserialize(stream);
            }
        }
        return new List<TodoItem>();
    }
    public void WriteXml(List<TodoItem> tasks, string filename)
    {
        IsolatedStorageFile fileStorage = IsolatedStorageFile.GetUserStoreForApplication();
        var serializer = new XmlSerializer(typeof(List<TodoItem>));
        using (var writer = new StreamWriter(new IsolatedStorageFileStream(filename, FileMode.OpenOrCreate, fileStorage)))
        {
            serializer.Serialize(writer, tasks);
        }
    }
}
```

`TodoItemManager`而`XmlStorageImplementation`中建立**App.xaml.cs**檔案中此程式碼片段所示。

```csharp
var filename = "TodoList.xml";
var xmlStorage = new XmlStorageImplementation();
TodoMgr = new TodoItemManager(filename, xmlStorage);
```

Windows Phone 應用程式的其餘部分包含 Xaml 和C#建立的使用者介面，並使用`TodoMgr`類別來載入及儲存`TodoItem`物件。

## <a name="visual-basic-pcl-in-visual-studio-for-mac"></a>Visual Basic 在 Visual Studio for Mac 的 PCL

Visual Studio for Mac 不支援 Visual Basic 語言-您無法建立或編譯 Visual Basic 專案中的使用 Visual Studio for mac。

Visual Studio for Mac 的支援可攜式類別庫的表示它可以參考 PCL 從 Visual Basic 所建置的組件。

本節說明如何編譯 Visual Studio 中的 PCL 組件，然後確定 它，將會儲存在版本控制系統及其他專案所參考。

### <a name="keeping-the-pcl-output-from-visual-studio"></a>Visual Studio 中的保留 PCL 輸出

根據預設 （包括 TFS 和 Git） 的大部分版本控制系統會設定為忽略 **/bin/** 將不會儲存這表示已編譯的 PCL 組件的目錄。 這表示您必須手動將它複製到執行 Visual Studio for Mac 將會新增其參考的任何電腦。

若要確保您的版本控制系統可以儲存 PCL 組件輸出，您可以建立建置後指令碼，以將它複製到專案根目錄。 此建置後步驟可協助確保組件，可以輕鬆地加入原始檔控制和與其他專案共用。

#### <a name="visual-studio-2017"></a>Visual Studio 2017

1. 以滑鼠右鍵按一下專案，然後選擇 **屬性 > 建置事件**一節。

2. 新增_post-build_將從這個專案的輸出 DLL 複製到專案根目錄的指令碼 (這是外部 **/bin/**)。 根據您的版本控制的設定，DLL 現在應該能夠加入至原始檔控制。

  [![](native-apps-images/image6-vs-sml.png "建置後組建指令碼複製 VB DLL 的事件")](native-apps-images/image6-vs.png#lightbox)

#### <a name="visual-studio-2015"></a>Visual Studio 2015

1.  以滑鼠右鍵按一下專案，然後選擇 **屬性 > 編譯**，然後確認 所有設定已都選取在左上方 comb 進行中。 按一下 **建置事件...** 右下角的按鈕。

    [![](native-apps-images/image6.png "專案屬性 [編譯] 區段")](native-apps-images/image6.png#lightbox)

1.  新增建置後指令碼，將從這個專案的輸出 DLL 複製到專案根目錄 (這是外部 **/bin/** )。 根據您的版本控制的設定，DLL 現在應該能夠加入至原始檔控制。

    [![](native-apps-images/image7.png "建置事件 視窗")](native-apps-images/image7.png#lightbox)

#### <a name="all-versions"></a>所有版本

下一次建置專案，可攜式類別庫組件會複製的專案根目錄，以及當您核取-在/認可/推送變更 DLL 將會儲存 （以便它可以下載到 Mac 以使用 Visual Studio for Mac）。

  [![](native-apps-images/image8-sml.png "檔案的輸出視覺化的基本組件的位置")](native-apps-images/image8.png#lightbox)


這個組件可以再加入 Visual Studio 中的 Xamarin 專案 for Mac，即使在 Xamarin iOS 或 Android 專案中不支援 Visual Basic 語言本身。

### <a name="referencing-the-pcl-in-visual-studio-for-mac"></a>參考 PCL 中的，在 Visual Studio for Mac

因為 Xamarin 不支援 Visual Basic 而無法載入的 PCL 專案 （或 Windows Phone 應用程式），如以下螢幕擷取畫面所示：

 [![](native-apps-images/image9.png "Visual Studio for Mac 解決方案")](native-apps-images/image9.png#lightbox)

Xamarin.iOS 和 Xamarin.Android 專案中，我們仍然可以包含 Visual Basic PCL 組件 DLL:

1.  以滑鼠右鍵按一下**參考**節點，然後選取**編輯參考...**

    [![](native-apps-images/image10.png "專案編輯參考 功能表")](native-apps-images/image10.png#lightbox)

1.  選取  **.Net 組件**索引標籤，然後瀏覽至 Visual Basic 專案目錄中的輸出 DLL。 即使 Visual Studio for Mac 無法開啟專案時，所有檔案都應該有從原始檔控制。 按一下 **新增**再**確定**將這個組件新增至 iOS 和 Android 應用程式。

    [![](native-apps-images/image11-sml.png "按一下 [新增] 和 [確定] 即可將這個組件新增至 iOS 和 Android 應用程式")](native-apps-images/image11.png#lightbox)

1.  IOS 和 Android 應用程式現在可以包含 Visual Basic 的可攜式類別庫所提供的應用程式邏輯。 此螢幕擷取畫面顯示參考 Visual Basic PCL，並已使用的功能，從該程式庫的程式碼的 iOS 應用程式。

    [![](native-apps-images/image12-sml.png "編輯參考加入.NET 組件視窗")](native-apps-images/image12.png#lightbox)


如果變更 Visual Basic 專案，Visual Studio 中請記得以建置專案、 原始檔控制中儲存產生的組件 DLL，然後再取出該新的 DLL，從您的 Mac 上的原始檔控制，使 Visual Studio for Mac 建置包含最新版本功能。


## <a name="summary"></a>總結

這篇文章已示範如何使用 Visual Basic 程式碼中使用 Visual Studio 和可攜式類別庫的 Xamarin 應用程式。 雖然 Xamarin 不直接支援 Visual Basic，編譯成 PCL 的 Visual Basic 可讓使用要包含在 iOS 和 Android 的應用程式中的 Visual Basic 撰寫的程式碼。

## <a name="related-links"></a>相關連結

- [TaskyPortableVB （範例）](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [使用.NET Framework (Microsoft) 的跨平台開發](http://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)
