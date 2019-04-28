---
title: 跨平台應用程式案例研究：Tasky
description: 本文件說明如何設計和建置跨平台行動應用程式為 Tasky Portable 範例應用程式。 它討論應用程式的需求、 介面、 資料模型、 核心功能、 實作及更多。
ms.prod: xamarin
ms.assetid: B581B2D0-9890-C383-C654-0B0E12DAD5A6
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 23deddae61452d532a87c51cc1ec3bc53eb91c9f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61278978"
---
# <a name="cross-platform-app-case-study-tasky"></a>跨平台應用程式案例研究：Tasky

*Tasky* *可攜式*是一個簡單的待辦事項清單應用程式。 本文將探討如何的設計和建置，都要遵循的指導方針[建置跨平台應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)文件。 討論內容涵蓋下列領域：

<a name="Design_Process" />

## <a name="design-process"></a>設計程序

建議您建立的藍圖為您想要達到您開始撰寫程式碼之前。 這是特別適用於跨平台開發，您要在其中建置將會以多種方式公開的功能。 從開始清楚瞭解哪些您要建置可節省時間和精力稍後在開發週期。

 <a name="Requirements" />

### <a name="requirements"></a>需求

設計應用程式的第一個步驟是找出所需的功能。 這些可以是高階目標或是詳細的使用案例。 Tasky 具有簡單的功能性需求：

 -  檢視工作清單
 -  加入、 編輯和刪除工作
 -  將工作的狀態設為 [完成]

您應該考慮使用平台專屬功能。  可以 Tasky 利用 iOS 地理圍欄或 Windows Phone 動態磚？ 即使您未使用平台專屬功能的第一個版本中，您應該先確定您的商務和資料層級可以配合這些事先規劃。

 <a name="User_Interface_Design" />

### <a name="user-interface-design"></a>使用者介面設計

開始使用可實作目標平台的高階設計。 請小心注意 UI 平台特定的條件約束。 比方說，`TabBarController`在 iOS 中可以顯示五個按鈕，而 Windows Phone 對等項目可以顯示最多四個。
繪製畫面 flow 使用您的選擇 （文件適用於） 的工具。

 [![](case-study-tasky-images/taskydesign.png "繪製畫面 flow 使用您選擇的紙張運作的工具")](case-study-tasky-images/taskydesign.png#lightbox)

 <a name="Data_Model" />

### <a name="data-model"></a>資料模型

了解哪些資料需要儲存，可協助判斷要使用哪一個持續性機制。 請參閱[跨平台的資料存取](~/cross-platform/app-fundamentals/index.md)如需可用的儲存機制和它們之間做決定的說明資訊。 此專案中，我們將使用 SQLite.NET。

Tasky 需要針對每個 'TaskItem' 儲存三個屬性：

 -  **名稱**– 字串
 -  **附註**– 字串
 -  **完成**– 布林值

 <a name="Core_Functionality" />

### <a name="core-functionality"></a>核心功能

請考慮使用者介面必須取用以符合需求的 API。 待辦事項清單需要下列功能：

 -   **列出所有工作**– 顯示所有可用工作的主畫面都清單
 -  **取得一個工作**– 當工作的資料列都會被接觸到
 -  **儲存一項工作**– 編輯工作時
 -  **刪除一個工作**– 當工作被刪除時
 -  **建立空白的工作**– 建立新的工作時

若要達到重複使用程式碼，此 API 應該一次在實作*可攜式類別庫*。

 <a name="Implementation" />

### <a name="implementation"></a>實作

一旦應用程式的設計都同意，請考慮可能會將它實作為一個跨平台應用程式的方式。 這會成為應用程式的架構。 中的指導方針[建置跨平台應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)文件中，應用程式程式碼應該會中斷清單分成下列部分：

 -   **常見的程式碼**– 常見的專案，包含要儲存的工作資料的重複使用程式碼; 公開模型類別和 API，以管理儲存及載入資料。
 -   **平台特定程式碼**– 實作原生 UI 每個作業系統，請使用通用的程式碼，做為後端' 的平台特定專案。

 [![](case-study-tasky-images/taskypro-architecture.png "平台專屬專案實作原生 UI 每個作業系統，請使用通用的程式碼，做為後端")](case-study-tasky-images/taskypro-architecture.png#lightbox)

這兩個部分是由下列各節所述。

 <a name="Common_(PCL)_Code" />

## <a name="common-pcl-code"></a>通用 (PCL) 程式碼

可攜式 tasky 使用可攜式類別庫策略來進行共用通用程式碼。 請參閱[共用程式碼選項](~/cross-platform/app-fundamentals/code-sharing.md)文件的程式碼共用選項的說明。

所有的通用程式碼，包括資料存取層、 資料庫程式碼和合約，會放在程式庫專案。

完整的 PCL 專案如下所示。 只與每個目標平台相容的可攜式程式庫中的程式碼。 部署時，每個原生應用程式將會參考該程式庫。

![](case-study-tasky-images/portable-project.png "每個原生應用程式部署時，會參考該程式庫")

類別圖會顯示依層的類別。 `SQLiteConnection`類別是從 Sqlite NET 封裝的未定案程式碼。 類別的其餘部分是 Tasky 的自訂程式碼。 `TaskItemManager`和`TaskItem`類別代表特定平台應用程式公開 API。

 [![](case-study-tasky-images/classdiagram-core.png "TaskItemManager 和 TaskItem 類別代表特定平台應用程式公開 API")](case-study-tasky-images/classdiagram-core.png#lightbox)

至個別的圖層中使用命名空間可協助管理每個圖層之間的參考。 平台專屬專案應該只需要包含`using`商務圖層的陳述式。 資料存取層和資料層應該封裝所公開的 api`TaskItemManager`商務層中。

 <a name="References" />

### <a name="references"></a>參考

可攜式類別庫需要可以跨多個平台，各有不同層級的支援平台和架構的功能。 因此，有的限制的套件和 framework 程式庫可用。 例如，Xamarin.iOS 不支援 c#`dynamic`關鍵字，因此可攜式類別庫無法使用任何動態程式碼，而定的封裝，即使這類程式碼會在 Android 上運作。 Visual Studio for Mac 會妨礙您將不相容的套件及參考資料，但您會想要保留在心，以避免意外更新版本上的限制。

注意:您會看到您的專案參考您未曾使用過的 framework 程式庫。 這些參考所包含的 Xamarin 專案範本。 當應用程式會編譯時，連結的程序會移除未參考的程式碼，因此即使`System.Xml`已參考，它將不會包含在最終的應用程式因為我們不使用任何 Xml 函式。

 <a name="Data_Layer_(DL)" />

### <a name="data-layer-dl"></a>資料層 (DL)

資料層包含執行的資料 – 實體儲存到資料庫、 一般檔案或其他機制的程式碼。 Tasky 資料層是由兩個部分所組成： SQLite NET 程式庫及自訂的程式碼加入至連接它。

Tasky 依賴 Sqlite.net nuget 套件 （由 Frank Kreuger 發行） 將提供的物件關聯式對應 (ORM) 資料庫介面的 SQLite NET 程式碼內嵌。 `TaskItemDatabase`類別繼承自`SQLiteConnection`，並將需要的建立、 讀取、 更新、 刪除 (CRUD) 方法來讀取和寫入資料至 SQLite。 它是簡單的未定案泛型方法的實作 CRUD 可在其他專案中重複使用。

`TaskItemDatabase`是單一值，確保所有存取，都會針對相同的執行個體。 鎖定用來防止從多個執行緒並行存取。

 <a name="SQLite_on_WIndows_Phone" />

#### <a name="sqlite-on-windows-phone"></a>在 Windows Phone 上的 SQLite

IOS 和 Android 都提供使用 SQLite 作為作業系統的一部分，而 Windows Phone 不包含相容的資料庫引擎。 跨所有的三個平台共用程式碼的 SQLite 的 Windows phone 原生版本是必要的。 請參閱[使用本機資料庫](~/xamarin-forms/app-fundamentals/databases.md)如需有關設定 Windows Phone 專案，sqlite。

 <a name="Using_an_Interface_to_Generalize_Data_Access" />

#### <a name="using-an-interface-to-generalize-data-access"></a>使用介面來一般化資料存取

資料層依存於`BL.Contracts.IBusinessIdentity`以便它可以實作需要主索引鍵的抽象資料存取方法。 實作介面的任何商務層類別接著可以保存資料層。

介面只會指定整數作為主索引鍵屬性：

```csharp
public interface IBusinessEntity {
    int ID { get; set; }
}
```

基底類別實作介面，並將 SQLite NET 屬性，將它標示為 自動遞增的主索引鍵。 商務層實作這個基底類別中的任何類別接著可以保存在資料層：

```csharp
public abstract class BusinessEntityBase : IBusinessEntity {
    public BusinessEntityBase () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
}
```

資料層中的使用介面的泛型方法的範例如下`GetItem<T>`方法：

```csharp
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

 <a name="Locking_to_prevent_Concurrent_Access" />

#### <a name="locking-to-prevent-concurrent-access"></a>鎖定以防止並行存取

A[鎖定](https://msdn.microsoft.com/library/c5kehkcz(v=vs.100).aspx)內實作`TaskItemDatabase`類別避免並行存取資料庫。 這是為了確保序列化從不同的執行緒並行存取 （否則 UI 元件可能會嘗試在背景執行緒正在更新的同時讀取資料庫）。 鎖定的實作方式的範例如下所示：

```csharp
static object locker = new object ();
public IEnumerable<T> GetItems<T> () where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return (from i in Table<T> () select i).ToList ();
    }
}
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

大部分的資料層程式碼無法在其他專案中重複使用。 只有特定應用程式中的程式碼層`CreateTable<TaskItem>`呼叫中`TaskItemDatabase`建構函式。

 <a name="Data_Access_Layer_(DAL)" />

### <a name="data-access-layer-dal"></a>資料存取層 (DAL)

`TaskItemRepository`類別會封裝資料儲存機制，提供強類型的 api，可讓`TaskItem`物件來建立、 刪除、 擷取和更新。

 <a name="Using_Conditional_Compilation" />

#### <a name="using-conditional-compilation"></a>使用條件式編譯

類別會使用條件式編譯到設定檔的位置-這是實作平台分歧的範例。 傳回路徑的屬性會編譯成不同的程式碼，在每個平台上。 如下所示的程式碼和特定平台編譯器指示詞：

```csharp
public static string DatabaseFilePath {
    get {
        var sqliteFilename = "TaskDB.db3";
#if SILVERLIGHT
        // Windows Phone expects a local path, not absolute
        var path = sqliteFilename;
#else
#if __ANDROID__
        // Just use whatever directory SpecialFolder.Personal returns
        string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
        // we need to put in /Library/ on iOS5.1+ to meet Apple's iCloud terms
        // (they don't want non-user-generated data in Documents)
        string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
        string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder
#endif
        var path = Path.Combine (libraryPath, sqliteFilename);
                #endif
                return path;
    }
}
```

輸出將會根據平台，「<app
path>/Library/TaskDB.db3 」 適用於 iOS、 「<app
path>/Documents/TaskDB.db3"適用於 Android 或只是 「 TaskDB.db3 「 適用於 Windows Phone。

### <a name="business-layer-bl"></a>商務層 (BL)

商務層會實作模型類別，來管理它們的外觀。
模型是在 Tasky`TaskItem`類別及`TaskItemManager`實作外觀模式提供 API 管理`TaskItems`。

 <a name="Façade" />

#### <a name="faade"></a>Façade

 `TaskItemManager` 包裝`DAL.TaskItemRepository`可提供 Get、 儲存及刪除用來在應用程式和 UI 層參考的方法。

商務規則和邏輯會放在此視需要 – 例如在儲存物件之前，必須滿足任何驗證規則。

 <a name="API_for_Platform-Specific_Code" />

### <a name="api-for-platform-specific-code"></a>適用於平台特定程式碼的 API

一旦已撰寫一般程式碼，必須建置使用者介面，可收集並顯示它所公開的資料。 `TaskItemManager`類別會實作外觀模式，可提供簡單的 API 應用程式程式碼存取。

每個平台專屬專案中撰寫的程式碼會通常緊密結合至該裝置的原生 SDK，而且只能存取一般的程式碼使用 API 所定義`TaskItemManager`。 這包括方法和商務它會公開，這類類別`TaskItem`。

映像並不跨平台共用，而個別加入每個專案。 這很重要，因為每個平台映像以不同方式處理，使用不同的檔案名稱、 目錄和解決方式。

其餘各節將討論 Tasky UI 的平台特定實作詳細資料。

 <a name="iOS_App" />

## <a name="ios-app"></a>iOS 應用程式

有只有少數幾個實作 iOS Tasky 使用一般的 PCL 專案，來儲存和擷取資料的應用程式所需的類別。 如下所示完成 iOS 的 Xamarin.iOS 專案：

 ![](case-study-tasky-images/taskyios-solution.png "如下所示的 iOS 專案")

在此圖中，分組為圖層顯示的類別。

 [![](case-study-tasky-images/classdiagram-android.png "在此圖中，分組為圖層顯示的類別")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>參考

IOS 應用程式會參考特定平台 SDK 程式庫 – 例如。 Xamarin.iOS 和 MonoTouch.Dialog-1。

它也必須參考`TaskyPortableLibrary`PCL 專案。
參考清單如下所示：

 ![](case-study-tasky-images/taskyios-references.png "如下所示的參考清單")

使用者介面層與應用程式層會在此專案中使用這些參考實作。

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>應用程式層 (AL)

應用程式層包含 '' 將物件繫結公開至 UI PCL 的所需的平台特定類別。 IOS 專屬的應用程式具有可顯示工作的兩個類別：

 -   **EditingSource** – 這個類別用來繫結的使用者介面工作的清單。 因為`MonoTouch.Dialog`曾針對工作清單中，我們必須實作才能啟用撥動--刪除功能，在此協助程式`UITableView`。 撥動--刪除是在 iOS，但不是 Android 或 Windows Phone 上常見的因此 iOS 特定專案是唯一實作它。
 -   **TaskDialog** – 這個類別用來繫結至 UI 的單一工作。 它會使用`MonoTouch.Dialog`反映 API，以 '包裝'`TaskItem`包含正確的屬性，來允許輸入的螢幕格式正確的類別的物件。

`TaskDialog`類別會使用`MonoTouch.Dialog`類別的屬性為基礎的屬性，以建立螢幕。 此類別看起來像這樣：

```csharp
public class TaskDialog {
    public TaskDialog (TaskItem task)
    {
        Name = task.Name;
        Notes = task.Notes;
        Done = task.Done;
    }
    [Entry("task name")]
    public string Name { get; set; }
    [Entry("other task info")]
    public string Notes { get; set; }
    [Entry("Done")]
    public bool Done { get; set; }
    [Section ("")]
    [OnTap ("SaveTask")]    // method in HomeScreen
    [Alignment (UITextAlignment.Center)]
    public string Save;
    [Section ("")]
    [OnTap ("DeleteTask")]  // method in HomeScreen
    [Alignment (UITextAlignment.Center)]
    public string Delete;
}
```

請注意`OnTap`屬性需要一個方法名稱，這些方法必須存在於類別所在`MonoTouch.Dialog.BindingContext`建立 (在此情況下，`HomeScreen`下一節所述的類別)。

 <a name="User_Interface_Layer_(UI)" />

### <a name="user-interface-layer-ui"></a>使用者介面層級 (UI)

使用者介面層級包含下列類別：

1.   **AppDelegate** – 包含外觀 API 的呼叫來設定應用程式中使用的色彩與字型的樣式。 Tasky 是一個簡單的應用程式，因此會有其他執行的初始設定工作`FinishedLaunching`。
2.   **畫面**-的子類別`UIViewController`可定義每個畫面和它的行為。 畫面結合在一起的 UI 應用程式層的類別和通用的 API ( `TaskItemManager` )。 在此範例程式碼中，建立畫面，但它們可能已設計成使用 Xcode 的 Interface Builder] 或 [分鏡腳本設計工具。
3.   **映像**– 視覺項目是每個應用程式中很重要的一部分。 Tasky 已啟動顯示畫面和圖示映像，這適用於 iOS 必須按照標準與 Retina 解析度來提供。

 <a name="Home_Screen" />

#### <a name="home-screen"></a>主畫面

[首頁] 畫面是`MonoTouch.Dialog`畫面會顯示一份工作之 SQLite 資料庫。 它繼承自`DialogViewController`並實作程式碼以設定`Root`若要包含集合的`TaskItem`顯示的物件。

 [![](case-study-tasky-images/ios-taskylist.png "它繼承自 DialogViewController 和實作程式碼以設定根包含顯示 TaskItem 物件的集合")](case-study-tasky-images/ios-taskylist.png#lightbox)

若要顯示，並與其互動的工作清單相關的兩個主要方法如下：

1.   **PopulateTable** – 會使用商務層`TaskManager.GetTasks`方法來擷取集合`TaskItem`来顯示的物件。
2.   **選取**– 當資料列都會被接觸到時，在新的畫面會顯示工作。

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>工作詳細資料畫面

工作詳細資料是允許編輯或刪除工作的輸入的畫面。

使用 tasky`MonoTouch.Dialog`的反映 API，以顯示畫面中，因此會有任何`UIViewController`實作。 相反地，`HomeScreen`類別會具現化，並顯示`DialogViewController`使用`TaskDialog`應用程式層的類別。

此螢幕擷取畫面顯示空白畫面，示範`Entry`屬性中設定浮水印文字**名稱**並**備忘稿**欄位：

 [![](case-study-tasky-images/ios-taskydetail.png "此螢幕擷取畫面顯示空白畫面，示範在 [名稱] 和 附註欄位中設定浮水印文字的項目屬性")](case-study-tasky-images/ios-taskydetail.png#lightbox)

功能**工作詳細資料**中，必須實作 （例如儲存或刪除工作） 畫面`HomeScreen`類別，因為這正是`MonoTouch.Dialog.BindingContext`建立。 下列`HomeScreen`方法支援工作詳細資料畫面：

1.   **ShowTaskDetails** – 建立`MonoTouch.Dialog.BindingContext`呈現的畫面。 它會建立輸入的畫面使用反映來擷取屬性的名稱，並從型別`TaskDialog`類別。 其他資訊，例如浮水印文字輸入方塊中，是使用屬性的屬性實作。
2.   **SaveTask** – 此方法中參考`TaskDialog`類別透過`OnTap`屬性。 它時，會呼叫**儲存**按下時，並使用`MonoTouch.Dialog.BindingContext`來擷取使用者輸入的資料，然後儲存變更，使用`TaskItemManager`。
3.   **DeleteTask** – 此方法中參考`TaskDialog`類別透過`OnTap`屬性。 它會使用`TaskItemManager`刪除使用的主索引鍵 （[識別碼] 屬性） 的資料。

 <a name="Android_App" />

## <a name="android-app"></a>Android App

完整的 Xamarin.Android 專案，如圖如下所示：

 ![](case-study-tasky-images/taskyandroid-solution.png "在此圖顯示 android 專案")

類別圖表中，依層的類別：

 [![](case-study-tasky-images/classdiagram-android.png "類別圖表中，依層的類別")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>參考

Android 應用程式專案必須參考從 Android SDK 平台專屬 Xamarin.Android 組件存取類別。

它也必須 （例如參考 PCL 專案。 TaskyPortableLibrary) 存取的常見的資料和商務層程式碼所示。

 ![](case-study-tasky-images/taskyandroid-references.png "若要存取的常見的資料和商務層程式碼的 TaskyPortableLibrary")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>應用程式層 (AL)

類似於我們討論過的 iOS 版本更早版本，應用程式層中的 Android 版本包含 '' 將物件繫結公開至 UI 的核心的所需的平台特定類別。

 **TaskListAdapter** – 顯示清單<T>我們需要實作一個配接器，以顯示自訂物件中的物件`ListView`。 配接器可讓您控制哪些的版面配置，用於清單中的每個項目 – 程式碼會在此情況下使用 Android 的內建版面配置`SimpleListItemChecked`。

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>使用者介面 (UI)

Android 應用程式的使用者介面層是結合程式碼和 XML 標記。

 -   **資源/配置**– 畫面版面配置和資料列儲存格實作為 AXML 檔案的設計。 AXML 可以是以手動的方式，撰寫或配置不足以視覺化方式使用適用於 Android 的 Xamarin UI 設計工具。
 -   **資源/Drawable** – 影像 （圖示） 和自訂的按鈕。
 -   **畫面**– 定義每個畫面和它的行為的活動子類別。 繫結在一起的 UI 應用程式層的類別和通用的 API (`TaskItemManager`)。

 <a name="Home_Screen" />

#### <a name="home-screen"></a>主畫面

組成活動子類別的 [首頁] 畫面`HomeScreen`而`HomeScreen.axml`檔案可定義版面配置 （按鈕和工作清單的位置）。 畫面看起來像這樣：

 [![](case-study-tasky-images/android-taskylist.png "畫面看起來像這樣")](case-study-tasky-images/android-taskylist.png#lightbox)

主畫面的程式碼會定義按一下按鈕，按一下在清單中，項目，以及填入的清單中的處理常式`OnResume`方法，因此它會反映在 工作詳細資料畫面中所做的變更）。 使用商務圖層的載入資料`TaskItemManager`而`TaskListAdapter`應用程式層。

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>工作詳細資料畫面

也包含 工作詳細資料畫面`Activity`子類別和 AXML 版面配置檔案。 版面配置判斷輸入控制項的位置和C#類別會定義載入及儲存行為`TaskItem`物件。

 [![](case-study-tasky-images/android-taskydetail.png "此類別會定義載入及儲存 TaskItem 物件行為")](case-study-tasky-images/android-taskydetail.png#lightbox)

所有參考 PCL 程式庫則都是透過`TaskItemManager`類別。

 <a name="Windows_Phone_App" />

## <a name="windows-phone-app"></a>Windows Phone App
完整的 Windows Phone 專案中：

 ![](case-study-tasky-images/taskywp7-solution.png "Windows Phone 應用程式的完整的 Windows Phone 專案")

下圖顯示分組為圖層的類別：

 [![](case-study-tasky-images/classdiagram-wp7.png "此圖顯示分組為圖層的類別")](case-study-tasky-images/classdiagram-wp7.png#lightbox)

 <a name="References" />

### <a name="references"></a>參考

平台專屬專案必須參考所需的平台專屬程式庫 (例如`Microsoft.Phone`和`System.Windows`) 來建立有效的 Windows Phone 應用程式。

它也必須 （例如參考 PCL 專案。 `TaskyPortableLibrary`) 利用`TaskItem`類別和資料庫。

 ![](case-study-tasky-images/taskywp7-references.png "TaskyPortableLibrary 利用 TaskItem 類別與資料庫")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>應用程式層 (AL)

同樣地，如 iOS 和 Android 版本，應用程式層都包含協助將資料繫結至使用者介面的非視覺項目。

 <a name="ViewModels" />

#### <a name="viewmodels"></a>ViewModels

Viewmodel 包裝 PCL 中的資料 ( `TaskItemManager`)，並呈現可供 Silverlight/XAML 資料繫結的方式。 （如跨平台應用程式文件所述），這會是平台專屬行為的範例。

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>使用者介面 (UI)

XAML 有獨特的資料繫結功能，可以在標記中宣告，並減少顯示的物件所需的程式碼數量：

1.   **頁面**– XAML 檔案和其程式碼後置定義的使用者介面，並參考 Viewmodel 和 PCL 專案，來顯示和收集資料。
2.   **映像**– 啟動顯示畫面、 背景和圖示的影像是使用者介面的重要部分。

 <a name="MainPage" />

#### <a name="mainpage"></a>MainPage

MainPage 類別會使用`TaskListViewModel`顯示使用 XAML 的資料繫結功能的資料。 在頁面的`DataContext`設為 「 檢視模型，其中會以非同步方式填入。 `{Binding}`中 XAML 語法可讓您決定如何顯示資料。

 <a name="TaskDetailsPage" />

#### <a name="taskdetailspage"></a>TaskDetailsPage

會顯示每個工作，藉由繫結`TaskViewModel`至 TaskDetailsPage.xaml 中定義的 XAML。 工作擷取的資料是透過`TaskItemManager`商務層中。

 <a name="Results" />

## <a name="results"></a>結果

每個平台上，產生的應用程式看起來像這樣：

 <a name="iOS" />

#### <a name="ios"></a>iOS

應用程式使用 iOS 標準使用者介面的設計，例如 新增 按鈕被放置在導覽列中，並使用內建**加號 （+）** 圖示。 它也會使用預設值`UINavigationController`[上一頁] 按鈕的行為，並支援 ' 撥動-delete' 在資料表中的。

 [![](case-study-tasky-images/ios-taskylist.png "它也會使用預設 UINavigationController 上一頁按鈕行為，並支援資料表撥動至刪除")](case-study-tasky-images/ios-taskylist.png#lightbox) [![](case-study-tasky-images/ios-taskylist.png "它也會使用預設 UINavigationController備份按鈕的行為，並支援表撥動-delete")](case-study-tasky-images/ios-taskylist.png#lightbox)

 <a name="Android" />

#### <a name="android"></a>Android

Android 應用程式會使用內建控制項，包括需要顯示 '刻度' 的資料列的內建版面配置。 除了螢幕返回按鈕支援硬體/系統後的行為。

 [![](case-study-tasky-images/android-taskylist.png "除了螢幕返回按鈕支援硬體/系統後行為")](case-study-tasky-images/android-taskylist.png#lightbox)[![](case-study-tasky-images/android-taskylist.png "除了支援硬體/系統後行為螢幕上上一步按鈕")](case-study-tasky-images/android-taskylist.png#lightbox)

 <a name="Windows_Phone" />

#### <a name="windows-phone"></a>Windows Phone

Windows Phone 應用程式會使用標準的版面配置，填入應用程式列中，而不是在上方瀏覽列在螢幕的底部。

 [![](case-study-tasky-images/wp-taskylist.png "Windows Phone 應用程式使用的標準版面配置，填入應用程式列中，而不是在上方瀏覽列在螢幕的底部")](case-study-tasky-images/wp-taskylist.png#lightbox) [ ![](case-study-tasky-images/wp-taskylist.png "Windows Phone 應用程式使用標準版面配置，填入應用程式列中的，而不是在上方瀏覽列螢幕底部")](case-study-tasky-images/wp-taskylist.png#lightbox)

 <a name="Summary" />

## <a name="summary"></a>總結

本文件提供的多層式應用程式的設計原則如何套用至簡單的應用程式，以促進跨三個行動平台的 重複使用程式碼的詳細的說明： iOS、 Android 和 Windows Phone。

它描述用來設計應用程式層級的程序，並討論哪些程式碼&amp;每個圖層中，已實作功能。

您可以從下載的程式碼[github](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)。

## <a name="related-links"></a>相關連結

- [建置跨平台應用程式 （主要的文件）](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Tasky 可攜式範例應用程式 (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
