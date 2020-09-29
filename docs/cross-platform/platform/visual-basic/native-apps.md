---
title: Xamarin. Android 和 Xamarin 中的 Visual Basic
description: 本逐步解說示範如何建立原生的 Xamarin 和 Xamarin. Android 應用程式，以使用以 Visual Basic.NET 撰寫的商務邏輯。
ms.prod: xamarin
ms.assetid: 455fda67-3879-4299-8036-b12840e6a498
author: davidortinau
ms.author: daortin
ms.date: 04/24/2019
ms.openlocfilehash: 77df7d986f63861667c67b9c74b88ea6176f42ad
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458182"
---
# <a name="visual-basic-in-xamarin-android-and-ios"></a>Xamarin Android 和 iOS 中的 Visual Basic

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-taskyvb/)

[TaskyVB](/samples/xamarin/mobile-samples/visualbasic-taskyvb/)範例應用程式會示範如何將編譯成 .NET Standard 程式庫 Visual Basic 程式碼用於 Xamarin。 以下是在 Android 和 iOS 上執行的應用程式的一些螢幕擷取畫面：

 [![Android 和 iOS 正在執行以 Visual Basic 建立的應用程式](native-apps-images/simulators-sml.png)](native-apps-images/simulators.png#lightbox)

範例中的 Android 和 iOS 專案都是以 c # 撰寫。 每個應用程式的使用者介面都是以原生技術為基礎，而 `TodoItem` Visual Basic .NET Standard 程式庫會使用 (XML 檔案來提供管理，以供示範之用，而不是完整的資料庫) 。

## <a name="sample-walkthrough"></a>範例逐步解說

本指南將討論如何在適用于 iOS 和 Android 的 [TaskyVB](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyVB) Xamarin 範例中執行 Visual Basic。

> [!NOTE]
> 繼續進行本指南之前，請先參閱 [Visual Basic 和 .NET Standard](index.md) 的指示。
>
> 請參閱 [使用 Visual Basic](xamarin-forms.md) 指示的 Xamarin，以瞭解如何使用共用使用者介面 Visual Basic 程式碼來建立應用程式。

## <a name="visualbasicnetstandard"></a>VisualBasicNetStandard

Visual Basic .NET Standard 程式庫只能在 Windows 上的 Visual Studio 中建立。
範例程式庫包含應用程式在這些 Visual Basic 檔案中的基本概念：

- TodoItem .vb
- TodoItemManager .vb
- TodoItemRepositoryXML .vb
- >\adminconsole\xmlstorage\eptemplates .vb

### <a name="todoitemvb"></a>TodoItem .vb

此類別包含要在整個應用程式中使用的商務物件。 它會定義在 Visual Basic 中，並與以 c # 撰寫的 Android 和 iOS 專案共用。

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

Manager 類別提供可移植程式碼的「API」。 它提供類別的基本 CRUD 作業 `TodoItem` ，但不會執行這些作業。

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

此函式會採用 IXmlStorage 的實例作為參數。 如此一來，每個平臺都能提供自己的工作執行，同時還能讓可攜程式碼描述其他可共用的功能。

### <a name="todoitemrepositoryvb"></a>TodoItemRepository .vb

存放庫類別包含用來管理 TodoItem 物件清單的邏輯。 完整的程式碼如下所示–邏輯主要是在 TodoItems 中管理唯一的識別碼值，因為它們是從集合中加入和移除。

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
> 這段程式碼是非常基本的資料儲存機制範例。
> 這是為了示範 .NET Standard 程式庫如何針對介面進行程式碼的程式碼，以存取平臺特定功能 (在此案例中，載入和儲存 XML 檔案) 。 它不是一種生產品質資料庫的替代方案。

## <a name="android-and-ios-application-projects"></a>Android 和 iOS 應用程式專案

### <a name="ios"></a>iOS

在 iOS 應用程式中， `TodoItemManager` `XmlStorageImplementation` 會在 **AppDelegate.cs** 檔案中建立和，如下列程式碼片段所示。 前四行只是建立將儲存資料之檔案的路徑;最後兩行顯示兩個具現化的類別。

```csharp
var xmlFilename = "TodoList.xml";
string documentsPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine(documentsPath, "..", "Library"); // Library folder
var path = Path.Combine(libraryPath, xmlFilename);

TaskMgr = new TodoItemManager(path);
```

### <a name="android"></a>Android

在 Android 應用程式中， `TodoItemManager` `XmlStorageImplementation` 會在 **Application.cs** 檔案中建立和，如下列程式碼片段所示。 前三行只是建立將儲存資料之檔案的路徑;最後兩行顯示兩個具現化的類別。

```csharp
var xmlFilename = "TodoList.xml";
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
var path = Path.Combine(libraryPath, xmlFilename);

TaskMgr = new TodoItemManager(path);
```

應用程式程式碼的其餘部分主要是與使用者介面相關，並使用 `TaskMgr` 類別來載入和儲存 `TodoItem` 類別。

## <a name="visual-studio-2019-for-mac"></a>Visual Studio 2019 for Mac

> [!WARNING]
> Visual Studio for Mac 不支援編輯 Visual Basic 語言-沒有任何功能表項目可建立 Visual Basic 專案或檔案。 如果您開啟 **.vb** ，則不會反白顯示語言語法、自動完成或 IntelliSense。

Visual Studio 2019 for Mac _可以_ 編譯在 Windows 上建立的 Visual Studio .NET 標準專案，讓 iOS 應用程式可以參考這些專案。

Visual Studio 2017 完全 _無法_ 建立 Visual Basic 專案。

## <a name="summary"></a>摘要

本文示範如何使用 Visual Studio 和 .NET Standard 程式庫，在 Xamarin 應用程式中取用 Visual Basic 程式碼。 雖然 Xamarin 並不支援直接 Visual Basic，但是將 Visual Basic 編譯成 .NET Standard 程式庫，可讓以 Visual Basic 撰寫的程式碼包含在 iOS 和 Android 應用程式中。

## <a name="related-links"></a>相關連結

- [TaskyVB ( .NET Standard 範例) ](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyVB)
- [.NET Standard 的新功能](/dotnet/standard/whats-new/whats-new-in-dotnet-standard?tabs=csharp)