---
title: 在 Xamarin. Android 和 Xamarin 中的 Visual Basic
description: 本逐步解說示範如何建立原生的 Xamarin. iOS 和 Xamarin 應用程式，以使用以 Visual Basic.NET 撰寫的商務邏輯。
ms.prod: xamarin
ms.assetid: 455fda67-3879-4299-8036-b12840e6a498
author: conceptdev
ms.author: crdun
ms.date: 04/24/2019
ms.openlocfilehash: ea4dc91b262c2ae153088f6e1a8416cc01cb0fa9
ms.sourcegitcommit: f8583585c501607fdfa061b95e9a9f385ed1d591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2019
ms.locfileid: "72959117"
---
# <a name="visual-basic-in-xamarin-android-and-ios"></a>Xamarin Android 和 iOS 中的 Visual Basic

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-taskyvb/)

[TaskyVB](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-taskyvb/)範例應用程式會示範如何將編譯成 .NET Standard 程式庫的 Visual Basic 程式碼與 Xamarin 搭配使用。 以下是在 Android 和 iOS 上執行之應用程式的一些螢幕擷取畫面：

 [![Android 和 iOS 執行以 Visual Basic 建立的應用程式](native-apps-images/simulators-sml.png)](native-apps-images/simulators.png#lightbox)

範例中的 Android 和 iOS 專案都是以C#撰寫。 每個應用程式的使用者介面都是以原生技術為基礎，而 `TodoItem` 管理是由 Visual Basic .NET Standard 程式庫使用 XML 檔案（用於示範，而不是完整資料庫）提供。

## <a name="sample-walkthrough"></a>範例逐步解說

本指南討論如何在適用于 iOS 和 Android 的[TaskyVB](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyVB) Xamarin 範例中執行 Visual Basic。

> [!NOTE]
> 繼續閱讀本指南之前，請先參閱[Visual Basic 和 .NET Standard](index.md)上的指示。
>
> 請參閱[使用 Visual Basic](xamarin-forms.md)指示的 Xamarin，以瞭解如何使用共用的使用者介面 Visual Basic 程式碼來建立應用程式。

## <a name="visualbasicnetstandard"></a>VisualBasicNetStandard

Visual Basic .NET Standard 程式庫只能在 Windows 上的 Visual Studio 中建立。
範例程式庫包含我們在這些 Visual Basic 檔案中的應用程式基本概念：

- TodoItem .vb
- TodoItemManager .vb
- TodoItemRepositoryXML .vb
- XmlStorage .vb

### <a name="todoitemvb"></a>TodoItem .vb

這個類別包含要在整個應用程式中使用的商務物件。 它會在 Visual Basic 中定義，並與以C#撰寫的 Android 和 iOS 專案共用。

類別定義如下所示：

```vb
Public Class TodoItem
    Property ID() As Integer
    Property Name() As String
    Property Notes() As String
    Property Done() As Boolean
End Class
```

此範例會使用 XML 序列化和還原序列化來載入和儲存 TodoItem 物件。

### <a name="todoitemmanagervb"></a>TodoItemManager .vb

Manager 類別會呈現可移植程式碼的「API」。 它提供 `TodoItem` 類別的基本 CRUD 作業，但不會執行這些作業。

```vb
Public Class TodoItemManager
    Private _repository As TodoItemRepositoryXML
    Public Sub New(filename As String)
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

此函式會採用 IXmlStorage 的實例做為參數。 這可讓每個平臺提供自己的工作執行，同時仍然讓可攜的程式碼描述可共用的其他功能。

### <a name="todoitemrepositoryvb"></a>TodoItemRepository .vb

存放庫類別包含用來管理 TodoItem 物件清單的邏輯。 完整的程式碼如下所示–邏輯主要是用來在 TodoItems 中管理唯一的識別碼值，並在集合中新增和移除它們。

```vb
Public Class TodoItemRepositoryXML
    Private _filename As String
    Private _storage As IXmlStorage
    Private _tasks As List(Of TodoItem)

    ''' <summary>Constructor</summary>
    Public Sub New(filename As String)
        _filename = filename
        _storage = New XmlStorage
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
> 這段程式碼是非常基本的資料儲存機制的範例。
> 它是用來示範 .NET Standard 程式庫如何針對介面撰寫程式碼，以存取平臺特定功能（在此案例中，載入和儲存 XML 檔案）。 其目的不是生產品質的資料庫替代方案。

## <a name="android-and-ios-application-projects"></a>Android 和 iOS 應用程式專案

### <a name="ios"></a>iOS

在 iOS 應用程式中，會在**AppDelegate.cs**檔案中建立 `TodoItemManager` 和 `XmlStorageImplementation`，如下列程式碼片段所示。 前四行只是建立將儲存資料的檔案路徑;最後兩行顯示要具現化的兩個類別。

```csharp
var xmlFilename = "TodoList.xml";
string documentsPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine(documentsPath, "..", "Library"); // Library folder
var path = Path.Combine(libraryPath, xmlFilename);

TaskMgr = new TodoItemManager(path);
```

### <a name="android"></a>Android

在 Android 應用程式中，會在**Application.cs**檔案中建立 `TodoItemManager` 和 `XmlStorageImplementation`，如下列程式碼片段所示。 前三行只是建立將儲存資料的檔案路徑;最後兩行顯示要具現化的兩個類別。

```csharp
var xmlFilename = "TodoList.xml";
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
var path = Path.Combine(libraryPath, xmlFilename);

TaskMgr = new TodoItemManager(path);
```

應用程式程式碼的其餘部分主要是與使用者介面有關，並使用 `TaskMgr` 類別來載入和儲存 `TodoItem` 類別。

## <a name="visual-studio-2019-for-mac"></a>Visual Studio 2019 for Mac

> [!WARNING]
> Visual Studio for Mac 不支援編輯 Visual Basic 語言–沒有任何功能表項目可用於建立 Visual Basic 專案或檔案。 如果您開啟 **.vb** ，則不會有語言語法反白顯示、自動完成或 IntelliSense。

適用于 Mac 的 Visual Studio 2019_可以_編譯在 Windows 上建立 Visual Studio .NET Standard 專案，因此 iOS 應用程式可以參考這些專案。

Visual Studio 2017_無法_完全建立 Visual Basic 專案。

## <a name="summary"></a>總結

本文已示範如何使用 Visual Studio 和 .NET Standard 程式庫，在 Xamarin 應用程式中使用 Visual Basic 程式碼。 雖然 Xamarin 不支援直接 Visual Basic，但將 Visual Basic 編譯成 .NET Standard 程式庫可讓以 Visual Basic 撰寫的程式碼包含在 iOS 和 Android 應用程式中。

## <a name="related-links"></a>相關連結

- [TaskyVB （.NET Standard 範例）](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyVB)
- [.NET Standard 的新功能](https://docs.microsoft.com/dotnet/standard/whats-new/whats-new-in-dotnet-standard?tabs=csharp)
