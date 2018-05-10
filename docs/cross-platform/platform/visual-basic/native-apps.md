---
title: 在 Xamarin iOS 和 Android 的 visual basic.net
description: XThis 逐步解說示範如何建置原生 Xamarin.iOS 和 Xamarin.Android 應用程式利用在 Visual basic.net 所撰寫的商務邏輯。
ms.prod: xamarin
ms.assetid: 455fda67-3879-4299-8036-b12840e6a498
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 3b92442668f6bd86c1c521dd6b476e4bbb43c1ec
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="visual-basicnet-in-xamarin-ios-and-android"></a>在 Xamarin iOS 和 Android 的 visual basic.net

[TaskyPortable](/samples/mobile/VisualBasic/TaskyPortableVB/)範例應用程式示範如何使用 Visual Basic 程式碼編譯成可攜式類別庫，使用 Xamarin。 以下是一些產生在 iOS、 Android 和 Windows Phone 上執行的應用程式的螢幕擷取畫面：

 [![](native-apps-images/image5.png "iOS、 Android 和 Windows 手機執行使用 Visual Basic 建置的應用程式")](native-apps-images/image5.png#lightbox)

IOS、 Android 和 Windows Phone 專案，在範例中會寫入 C# 中。 使用原生技術所建置的每個應用程式的使用者介面 (分鏡腳本、 Xml 和 Xaml 分別)，而`TodoItem`管理由 Visual Basic 的可攜式類別庫提供使用`IXmlStorage`所提供的實作原生專案。

## <a name="sample-walkthrough"></a>範例逐步解說

本指南討論 Visual Basic 中已實作方式[TaskyPortableVB](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)適用於 iOS 和 Android 的 Xamarin 範例。

> [!NOTE]
> 檢閱上的指示[Visual Basic.NET PCLs](/guides/cross-platform/application_fundamentals/pcl/portable_visual_basic_net/)再繼續進行本指南。

## <a name="visualbasicportablelibrary"></a>VisualBasicPortableLibrary

只能在 Visual Studio 中建立 Visual Basic 可攜式類別庫。
範例程式庫包含我們四個 Visual Basic 檔案中的應用程式的基本概念：

-  IXmlStorage.vb
-  TodoItem.vb
-  TodoItemManager.vb
-  TodoItemRepositoryXML.vb


### <a name="ixmlstoragevb"></a>IXmlStorage.vb

因為檔案的存取行為因此大幅不同平台之間，不提供可攜式類別庫`System.IO`檔案中的任何設定檔的儲存體 Api。 這表示如果我們想要在我們的可攜式程式碼檔案系統直接互動，我們需要在每個平台上回撥我們的原生專案。  藉由撰寫簡單的介面可以實作在 C# 中，每個平台上，針對 Visual Basic 程式碼，我們可以有可共用的 Visual Basic 程式碼仍然可以存取檔案系統。

範例程式碼會使用這個非常簡單的介面，其中包含兩個方法： 讀取和寫入序列化的 Xml 檔案。

```vb
Public Interface IXmlStorage
    Function ReadXml(filename As String) As List(Of TodoItem)
    Sub WriteXml(tasks As List(Of TodoItem), filename As String)
End Interface
```

iOS、 Android 和 Windows Phone 實作這個介面會顯示本指南稍後。

### <a name="todoitemvb"></a>TodoItem.vb

這個類別包含要在整個應用程式的商務物件。 它會定義在 Visual Basic 中，並分享 iOS、 Android 和 Windows Phone 專案以 C# 撰寫的。

類別定義如下所示：

```vb
Public Class TodoItem
    Property ID() As Integer
    Property Name() As String
    Property Notes() As String
    Property Done() As Boolean
End Class
```

這個範例會使用 XML 序列化和還原序列化，以載入和儲存的 TodoItem 物件。

### <a name="todoitemmanagervb"></a>TodoItemManager.vb

管理員類別提供可攜式程式碼的 'API'。 它會提供基本 CRUD 作業`TodoItem`類別，但沒有這些作業的實作。

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

建構函式會將參數當成 IXmlStorage 的執行個體。 這可讓每個平台提供它自己的工作實作，同時仍讓能夠說明您可以共用其他功能的可攜式程式碼。

### <a name="todoitemrepositoryvb"></a>TodoItemRepository.vb

儲存機制類別包含的邏輯管理的 TodoItem 物件清單。 完整的程式碼如下所示 – 邏輯存在目的主要是為了管理跨 TodoItems 唯一的識別碼值，加入並移除集合中。

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
> 這個程式碼是非常基本的資料儲存機制的範例。
> 被為了示範如何可攜式類別庫可以撰寫程式碼針對介面來存取平台專屬功能 （在此情況下，載入及儲存為 Xml 檔案）。 它它不是生產品質資料庫的替代方式。

## <a name="ios-android-and-windows-phone-application-projects"></a>iOS、 Android 和 Windows Phone 應用程式專案

本章節包含 IXmlStorage 介面的平台特定實作，並示範如何使用每個應用程式。 應用程式專案是以 C# 撰寫。

### <a name="ios-and-android-ixmlstorage"></a>iOS 和 Android IXmlStorage

Xamarin.iOS 和 Xamarin.Android 提供完整`System.IO`功能，可讓您輕鬆地載入並儲存 Xml 檔案使用下列類別：

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

IOS 應用程式中`TodoItemManager`和`XmlStorageImplementation`中建立**d**檔案中此程式碼片段所示。 前四行只會建置資料的儲存位置; 檔案的路徑最後兩行顯示兩個類別執行個體化。

```csharp
var xmlFilename = "TodoList.xml";
string documentsPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine(documentsPath, "..", "Library"); // Library folder
var path = Path.Combine(libraryPath, xmlFilename);
var xmlStorage = new XmlStorageImplementation();
TaskMgr = new TodoItemManager(path, xmlStorage);
```

在 Android 應用程式中`TodoItemManager`和`XmlStorageImplementation`中建立**Application.cs**檔案中此程式碼片段所示。 前三個行只會建置資料的儲存位置; 檔案的路徑最後兩行顯示兩個類別執行個體化。

```csharp
var xmlFilename = "TodoList.xml";
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
var path = Path.Combine(libraryPath, xmlFilename);
var xmlStorage = new AndroidTodo.XmlStorageImplementation();
TaskMgr = new TodoItemManager(path, xmlStorage);
```

應用程式程式碼的其餘部分主要是考慮使用者介面，並使用`TaskMgr`類別來載入及儲存`TodoItem`類別。

### <a name="windows-phone-ixmlstorage"></a>Windows Phone IXmlStorage

Windows Phone 不提供完整存取裝置的檔案系統，改為公開 IsolatedStorage API。 `IXmlStorage`實作適用於 Windows Phone 看起來像這樣：

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

`TodoItemManager`和`XmlStorageImplementation`中建立**App.xaml.cs**檔案中此程式碼片段所示。

```csharp
var filename = "TodoList.xml";
var xmlStorage = new XmlStorageImplementation();
TodoMgr = new TodoItemManager(filename, xmlStorage);
```

Windows Phone 應用程式的其餘部分包含的 Xaml 和 C# 建立的使用者介面，並使用`TodoMgr`類別來載入及儲存`TodoItem`物件。

## <a name="visual-basic-pcl-in-visual-studio-for-mac"></a>Visual Basic 在 Visual Studio for Mac PCL

適用於 Mac 的 visual Studio 不支援 Visual Basic 語言-您無法建立或編譯 Visual Basic 專案與 Visual Studio for mac。

Visual Studio for Mac 的可攜式類別庫支援表示它可以參考 PCL 組件從 Visual Basic 所建置。

本節說明如何編譯 Visual Studio 中的 PCL 組件，然後確定 它，將會儲存在版本控制系統及其他專案所參考。

### <a name="keeping-the-pcl-output-from-visual-studio"></a>Visual Studio 中的保留 PCL 輸出

根據預設 （包括 TFS 和 Git） 的大部分版本控制系統會設定為忽略 **/bin/** 不會儲存這表示已編譯的 PCL 組件的目錄。 這表示您必須手動將它複製到執行 Visual Studio for Mac 將參考加入至它的任何電腦。

若要確保您的版本控制系統可以儲存 PCL 組件的輸出，您可以建立的建置後指令碼，將其複製到專案根目錄。 此建置後步驟，有助於確保組件可以輕鬆地加入至原始檔控制並與其他專案共用。

#### <a name="visual-studio-2017"></a>Visual Studio 2017

1. 以滑鼠右鍵按一下專案，然後選擇 **屬性 > 建置事件**> 一節。

2. 新增_建置後_從這個專案會將輸出 DLL 複製到專案根目錄的指令碼 (即外部 **/bin/**)。 根據您的版本控制設定，DLL 現在應該可以加入至原始檔控制。

  [![](native-apps-images/image6-vs-sml.png "建置事件將 VB DLL 複製後組建指令碼")](native-apps-images/image6-vs.png#lightbox)

#### <a name="visual-studio-2015"></a>Visual Studio 2015

1.  以滑鼠右鍵按一下專案，然後選擇 **屬性 > 編譯**，然後確定所有設定已都選取左上方 comb 進行方塊中。 按一下**建置事件...** 在右下方的按鈕。

    [![](native-apps-images/image6.png "專案屬性編譯區段")](native-apps-images/image6.png#lightbox)

1.  加入建置後指令碼，從這個專案會將輸出 DLL 複製到專案根目錄 (即外部 **/bin/** )。 根據您的版本控制設定，DLL 現在應該可以加入至原始檔控制。

    [![](native-apps-images/image7.png "建置事件 視窗")](native-apps-images/image7.png#lightbox)

#### <a name="all-versions"></a>所有版本

下一次建置專案，可攜式類別庫組件會複製，專案根目錄中，以及當您核取-在/認可/推入變更 DLL 將會儲存 （以便它可以下載到 Mac，以使用 Visual Studio for Mac）。

  [![](native-apps-images/image8-sml.png "輸出 Visual 基本組件的檔案位置")](native-apps-images/image8.png#lightbox)


這個組件可以再加入 Visual Studio 中的 Xamarin 專案的 Mac，即使 Xamarin iOS 或 Android 專案中不支援 Visual Basic 語言本身。

### <a name="referencing-the-pcl-in-visual-studio-for-mac"></a>參考 PCL 在 Visual Studio for Mac

因為 Xamarin 不支援 Visual Basic 它無法載入 PCL 專案 （或 Windows Phone 應用程式），如下列螢幕擷取畫面所示：

 [![](native-apps-images/image9.png "Visual Studio for Mac 方案")](native-apps-images/image9.png#lightbox)

此外，我們仍然可以包含 Visual Basic PCL 組件 DLL Xamarin.iOS 和 Xamarin.Android 專案中：

1.  以滑鼠右鍵按一下**參考**節點，然後選取**編輯參考...**

    [![](native-apps-images/image10.png "編輯參考的專案 功能表")](native-apps-images/image10.png#lightbox)

1.  選取 **.Net 組件**索引標籤上，瀏覽至 Visual Basic 專案目錄中的輸出 DLL。 即使適用於 Mac 的 Visual Studio 無法開啟專案時，所有的檔案應該有從原始檔控制。 按一下**新增**然後**確定**將這個組件新增至 iOS 和 Android 應用程式。

    [![](native-apps-images/image11-sml.png "按一下新增，再按一下 [確定] 將這個組件新增至 iOS 和 Android 應用程式")](native-apps-images/image11.png#lightbox)

1.  IOS 和 Android 應用程式現在可以包含 Visual Basic 的可攜式類別庫所提供的應用程式邏輯。 這個螢幕擷取畫面顯示參考 Visual Basic PCL 和已使用的功能，從該文件庫的程式碼的 iOS 應用程式。

    [![](native-apps-images/image12-sml.png "編輯參考加入.NET 組件視窗")](native-apps-images/image12.png#lightbox)


如果變更 Visual Basic 專案，Visual Studio 中請記得要建置專案、 原始檔控制中儲存產生的組件 DLL 和提取到您的 Mac 上的原始檔控制從該新的 DLL，讓適用於 Mac 的 Visual Studio 會建置包含最新這項功能。


## <a name="summary"></a>總結

本文示範如何使用 Visual Basic 程式碼中使用 Visual Studio 和可攜式類別庫的 Xamarin 應用程式。 雖然 Xamarin 不直接支援 Visual Basic，Visual Basic 編譯 PCL 可讓使用要包含在 iOS 和 Android 應用程式中的 Visual Basic 撰寫的程式碼。

## <a name="related-links"></a>相關連結

- [TaskyPortableVB （範例）](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [使用.NET Framework (Microsoft) 進行跨平台開發](http://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)
