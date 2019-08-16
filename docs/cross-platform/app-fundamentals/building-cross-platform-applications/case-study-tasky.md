---
title: 跨平臺應用程式案例研究:Tasky
description: 本檔說明如何以跨平臺行動應用程式的方式設計和建立 Tasky 可移植的範例應用程式。 其中討論應用程式的需求、介面、資料模型、核心功能、執行等。
ms.prod: xamarin
ms.assetid: B581B2D0-9890-C383-C654-0B0E12DAD5A6
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 38f4e079529bec0dfc721d0c37686a6d90533b7e
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527014"
---
# <a name="cross-platform-app-case-study-tasky"></a>跨平臺應用程式案例研究:Tasky

*Tasky* *可移植*性是簡單的待辦事項清單應用程式。 本檔將討論如何設計和建立, 遵循[建立跨平臺應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)檔的指引。 討論內容涵蓋下列領域:

<a name="Design_Process" />

## <a name="design-process"></a>設計流程

建議您在開始撰寫程式碼之前, 先建立一個您想要達到的道路圖。 這對於跨平臺開發特別適用, 因為您正在建立將以多種方式公開的功能。 從現在開始, 您可以清楚瞭解您所建立的內容, 在未來的開發週期中節省時間和工作量。

 <a name="Requirements" />

### <a name="requirements"></a>需求

設計應用程式的第一個步驟是識別所需的功能。 這些可以是高階目標或詳細的使用案例。 Tasky 具有直接的功能性需求:

- 查看工作清單
- 新增、編輯和刪除工作
- 將工作的狀態設定為「完成」

您應該考慮使用平臺特有的功能。  Tasky 可以利用 iOS 地理柵欄或 Windows Phone 動態磚嗎？ 即使您未使用第一個版本中的平臺特定功能, 您仍應事先規劃, 以確保您的企業 & 資料層可以容納它們。

 <a name="User_Interface_Design" />

### <a name="user-interface-design"></a>使用者介面設計

從可在目標平臺上執行的高階設計開始。 請小心注意平臺特定的 UI 條件約束。 例如, iOS 中`TabBarController`的可以顯示五個以上的按鈕, 而 Windows Phone 對等的則最多可以顯示四個。
使用您選擇的工具 (紙張工作) 繪製螢幕流程。

 [![](case-study-tasky-images/taskydesign.png "使用您選擇的工具繪製螢幕流程工作")](case-study-tasky-images/taskydesign.png#lightbox)

 <a name="Data_Model" />

### <a name="data-model"></a>資料模型

知道需要儲存哪些資料將有助於判斷要使用的持續性機制。 如需可用儲存機制的詳細資訊, 以及協助在兩者之間做出決定, 請參閱[跨平臺資料存取](~/cross-platform/app-fundamentals/index.md)。 針對此專案, 我們將使用 SQLite.NET。

Tasky 需要為每個 ' TaskItem ' 儲存三個屬性:

- **名稱**–字串
- **附注**–字串
- **完成**-布林值

 <a name="Core_Functionality" />

### <a name="core-functionality"></a>核心功能

請考慮使用者介面需要使用的 API, 以符合需求。 待辦事項清單需要下列功能:

- **列出所有**工作–顯示所有可用工作的主要畫面清單
- **取得一項**工作–觸及工作資料列時
- **儲存一項**工作–編輯工作時
- **刪除一項**工作–刪除工作時
- **建立空白**工作-建立新工作時

若要讓程式碼重複使用, 此 API 應該在*可移植類別庫*中執行一次。

 <a name="Implementation" />

### <a name="implementation"></a>實作

一旦應用程式設計經過同意, 請考慮如何將其實作為跨平臺應用程式。 這會變成應用程式的架構。 遵循[建立跨平臺應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)檔中的指導方針, 應用程式代碼應細分為下列部分:

- **通用程式碼**–包含可重複使用的程式碼以儲存工作資料的通用專案;公開模型類別和 API, 以管理資料的儲存和載入。
- **平臺**專屬的程式碼–平臺特定的專案, 為每個作業系統執行原生 UI, 並利用通用程式碼做為「後端」。

[![](case-study-tasky-images/taskypro-architecture.png "平臺特定專案會針對每個作業系統執行原生 UI, 並利用通用程式碼做為後端")](case-study-tasky-images/taskypro-architecture.png#lightbox)

下列各節將說明這兩個部分。

 <a name="Common_(PCL)_Code" />

## <a name="common-pcl-code"></a>一般 (PCL) 程式碼

Tasky 可攜性會使用可移植的類別庫策略來共用通用程式碼。 如需程式碼共用選項的說明, 請參閱[共用程式碼選項](~/cross-platform/app-fundamentals/code-sharing.md)檔。

所有通用程式碼 (包括資料存取層、資料庫程式碼和合約) 都會放在程式庫專案中。

完整的 PCL 專案如下所示。 便攜媒體櫃中的所有程式碼都與每個目標平臺相容。 部署時, 每個原生應用程式都會參考該程式庫。

![](case-study-tasky-images/portable-project.png "部署時, 每個原生應用程式都會參考該程式庫")

下列類別圖顯示依圖層分組的類別。 `SQLiteConnection`類別是 Sqlite 網路套件中的重複程式碼。 其餘的類別是 Tasky 的自訂程式碼。 `TaskItemManager` 和`TaskItem`類別代表對平臺特定應用程式公開的 API。

 [![](case-study-tasky-images/classdiagram-core.png "TaskItemManager 和 TaskItem 類別代表對平臺特定應用程式公開的 API。")](case-study-tasky-images/classdiagram-core.png#lightbox)

使用命名空間來分隔圖層有助於管理每個圖層之間的參考。 平臺特定專案應該只需要包含`using`適用于商務層的語句。 資料存取層和資料層應封裝`TaskItemManager`在商務層中公開的 API。

 <a name="References" />

### <a name="references"></a>參考

可移植的類別庫必須可在多個平臺上使用, 而且各有不同的平臺和架構功能支援層級。 因此, 可以使用的套件和架構程式庫有一些限制。 例如, Xamarin 不支援 c # `dynamic`關鍵字, 因此可移植的類別庫不能使用相依于動態程式碼的任何套件, 即使這類程式碼可以在 Android 上運作也一樣。 Visual Studio for Mac 將會讓您無法新增不相容的套件和參考, 但您會想要保留限制以避免日後發生意外。

注意:您會看到您的專案參考您尚未使用的架構程式庫。 這些參考會納入為 Xamarin 專案範本的一部分。 當應用程式進行編譯時, 連結進程將會移除未參考的程式`System.Xml`代碼, 因此即使已被參考, 也不會包含在最終的應用程式中, 因為我們不會使用任何 Xml 函數。

 <a name="Data_Layer_(DL)" />

### <a name="data-layer-dl"></a>資料層 (DL)

資料層包含執行資料實體儲存的程式碼–不論是資料庫、一般檔案或其他機制。 Tasky 資料層是由兩個部分組成: SQLite 程式庫和新增的自訂程式碼, 以連線到網路。

Tasky 依賴 Sqlite-net nuget 套件 (由 Frank Kreuger 發行) 來內嵌 SQLite .NET 程式碼, 以提供物件關聯式對應 (ORM) 資料庫介面。 類別繼承自`SQLiteConnection` , 並新增必要的 Create、read、Update、Delete (CRUD) 方法來讀取和寫入資料至 SQLite。 `TaskItemDatabase` 這是可在其他專案中重複使用之一般 CRUD 方法的簡單方式。

`TaskItemDatabase`是單一的, 確保所有存取都是針對相同的實例進行。 鎖定是用來防止平行存取多個執行緒。

 <a name="SQLite_on_WIndows_Phone" />

#### <a name="sqlite-on-windows-phone"></a>Windows Phone 上的 SQLite

雖然 iOS 和 Android 都隨附于 SQLite 做為作業系統的一部分, 但 Windows Phone 並不包含相容的資料庫引擎。 若要跨三個平臺共用程式碼, 必須要有 Windows phone 原生版本的 SQLite。 如需為 Sqlite 設定 Windows Phone 專案的詳細資訊, 請參閱[使用本機資料庫](~/xamarin-forms/data-cloud/data/databases.md)。

 <a name="Using_an_Interface_to_Generalize_Data_Access" />

#### <a name="using-an-interface-to-generalize-data-access"></a>使用介面將資料存取一般化

資料層會相依于`BL.Contracts.IBusinessIdentity` , 使其可以執行需要主鍵的抽象資料存取方法。 任何執行介面的商務層級類別, 都可以保存在資料層中。

介面只會指定要做為主鍵的整數屬性:

```csharp
public interface IBusinessEntity {
    int ID { get; set; }
}
```

基類會執行介面並新增 SQLite-NET 屬性, 以將其標示為自動遞增的主鍵。 商務層中任何可實作為此基類的類別, 都可以保存在資料層中:

```csharp
public abstract class BusinessEntityBase : IBusinessEntity {
    public BusinessEntityBase () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
}
```

在使用介面的資料層中, 泛型方法的範例是這個`GetItem<T>`方法:

```csharp
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

 <a name="Locking_to_prevent_Concurrent_Access" />

#### <a name="locking-to-prevent-concurrent-access"></a>鎖定以防止平行存取

[鎖定](https://msdn.microsoft.com/library/c5kehkcz(v=vs.100).aspx)會在`TaskItemDatabase`類別內執行, 以防止平行存取資料庫。 這是為了確保會序列化來自不同執行緒的平行存取 (否則, 當背景執行緒正在更新時, UI 元件可能會嘗試讀取資料庫)。 如何執行鎖定的範例如下所示:

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

大部分的資料層程式碼都可以在其他專案中重複使用。 圖層中唯一的應用程式特定程式碼是`CreateTable<TaskItem>`在此`TaskItemDatabase`函式中的呼叫。

 <a name="Data_Access_Layer_(DAL)" />

### <a name="data-access-layer-dal"></a>資料存取層 (DAL)

類別會使用強型別 API 來封裝資料儲存機制, 讓`TaskItem`物件能夠建立、刪除、抓取和更新。 `TaskItemRepository`

 <a name="Using_Conditional_Compilation" />

#### <a name="using-conditional-compilation"></a>使用條件式編譯

類別會使用條件式編譯來設定檔案位置, 這是執行平臺分歧的範例。 傳回路徑的屬性會編譯為每個平臺上的不同程式碼。 程式碼和平臺特定的編譯器指示詞如下所示:

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

視平臺而定, 輸出將會是 "<app
path>/Library/TaskDB.db3" (適用于 iOS<app
path>)、"/Documents/TaskDB.db3" (適用于 Android), 或僅針對 Windows Phone 的 "TaskDB"。

### <a name="business-layer-bl"></a>商務層 (BL)

商務層會執行模型類別和外觀來管理它們。
在 Tasky 中, 此模型`TaskItem`是類別`TaskItemManager` , 並會執行面板模式來提供用於管理`TaskItems`的 API。

 <a name="Façade" />

#### <a name="faade"></a>外觀

 `TaskItemManager`包裝, `DAL.TaskItemRepository`以提供應用程式和 UI 層將參考的 Get、Save 和 Delete 方法。

如有需要, 商務規則和邏輯會放在這裡-例如, 在儲存物件之前必須滿足的任何驗證規則。

 <a name="API_for_Platform-Specific_Code" />

### <a name="api-for-platform-specific-code"></a>平臺特定程式碼的 API

撰寫通用程式碼之後, 必須建立使用者介面來收集並顯示其所公開的資料。 `TaskItemManager`類別會執行面板模式, 以提供簡單的 API 讓應用程式程式碼存取。

在每個平臺特定專案中撰寫的程式碼, 通常會與該裝置的原生 SDK 緊密結合, 而且只會使用所定義`TaskItemManager`的 API 來存取通用程式碼。 這包括其公開的方法和商業類別, 例如`TaskItem`。

映射不會跨平臺共用, 而是獨立新增至每個專案。 這很重要, 因為每個平臺會使用不同的檔案名、目錄和解析度, 以不同的方式處理影像。

其餘各節將討論 Tasky UI 的平臺專屬的執行詳細資料。

 <a name="iOS_App" />

## <a name="ios-app"></a>iOS 應用程式

使用一般 PCL 專案來儲存和抓取資料時, 只需要少數幾個類別就可以執行 iOS Tasky 應用程式。 完整的 iOS Xamarin. iOS 專案如下所示:

 ![](case-study-tasky-images/taskyios-solution.png "iOS 專案顯示在這裡")

此圖表中會顯示類別, 並分組為圖層。

 [![](case-study-tasky-images/classdiagram-android.png "此圖表中會顯示類別, 並分組為圖層")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>參考

IOS 應用程式參考平臺特定的 SDK 程式庫–例如 [Xamarin] 和 [MonoTouch]。對話方塊-1。

它也必須參考`TaskyPortableLibrary` PCL 專案。
[參考] 清單如下所示:

 ![](case-study-tasky-images/taskyios-references.png "參考清單如下所示")

應用層和使用者介面層會在此專案中使用這些參考來執行。

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>應用層 (AL)

應用層包含將 PCL 公開的物件「系結」至 UI 所需的平臺特定類別。 IOS 特定應用程式有兩個類別可協助顯示工作:

- **EditingSource** –這個類別是用來將工作清單系結至使用者介面。 因為`MonoTouch.Dialog`已用於工作清單, 所以我們需要執行此協助程式, 以便`UITableView`在中啟用「刪除滑動」功能。 在 iOS (而不是 Android 或 Windows Phone) 上, 輕輕鬆刪除是常見的, 因此 iOS 特定專案是唯一可進行的工作。
- **TaskDialog** –這個類別是用來將單一工作系結至 UI。 它會使用`MonoTouch.Dialog`反映 API, 以包含正確屬性`TaskItem`的類別來「包裝」物件, 以允許正確格式化輸入畫面。

類別會使用`MonoTouch.Dialog`屬性, 根據類別的屬性來建立畫面。 `TaskDialog` 類別看起來像這樣:

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

請注意`OnTap` , 屬性需要方法名稱–這些方法必須存在於建立的`MonoTouch.Dialog.BindingContext`類別中 (在此案例中為下一`HomeScreen`節所討論的類別)。

 <a name="User_Interface_Layer_(UI)" />

### <a name="user-interface-layer-ui"></a>使用者介面層 (UI)

使用者介面層包含下列類別:

1. **AppDelegate** –包含對外觀 API 的呼叫, 以樣式應用程式中所使用的字型和色彩。 Tasky 是簡單的應用程式, 因此不會在中`FinishedLaunching`執行其他初始化工作。
2. **畫面**–定義每`UIViewController`個畫面和其行為的子類別。 畫面會將 UI 與應用層類別和通用 API ( `TaskItemManager` ) 結合在一起。 在此範例中, 畫面是以程式碼建立, 但它們可以使用 Xcode 的 Interface Builder 或分鏡腳本設計工具來設計。
3. **影像**–視覺元素是每個應用程式中很重要的一部分。 Tasky 具有啟動顯示畫面和圖示影像, 適用于 iOS 的一般和 Retina 解析必須提供。

 <a name="Home_Screen" />

#### <a name="home-screen"></a>主畫面

[首頁] 畫面是`MonoTouch.Dialog`一個畫面, 其中顯示 SQLite 資料庫中的工作清單。 它繼承自`DialogViewController`並執行程式碼, 以`Root`將設定為包含要`TaskItem`顯示之物件的集合。

 [![](case-study-tasky-images/ios-taskylist.png "它繼承自 DialogViewController 並執行程式碼, 以將根設定為包含要顯示的 TaskItem 物件集合")](case-study-tasky-images/ios-taskylist.png#lightbox)

與工作清單顯示和互動的兩個主要方法如下:

1. **PopulateTable** –使用商務層的`TaskManager.GetTasks`方法來取出要顯示的`TaskItem`物件集合。
2. 已**選取**–觸及資料列時, 會在新畫面中顯示工作。

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>工作詳細資料畫面

工作詳細資料是允許編輯或刪除工作的輸入畫面。

Tasky 會`MonoTouch.Dialog`使用的反映 API 來顯示畫面, 因此不會執行`UIViewController`任何動作。 相反地, `HomeScreen`類別會具現化`DialogViewController`並顯示`TaskDialog` , 並使用來自應用層的類別。

這個螢幕擷取畫面顯示一個空白畫面, 示範`Entry`在 [**名稱**] 和 [**附注**] 欄位中設定浮水印文字的屬性:

 [![](case-study-tasky-images/ios-taskydetail.png "這個螢幕擷取畫面顯示一個空白畫面, 其中示範專案屬性設定 [名稱] 和 [附注] 欄位中的浮水印文字")](case-study-tasky-images/ios-taskydetail.png#lightbox)

[工作**詳細資料**] 畫面的功能 (例如儲存或刪除工作) 必須在`HomeScreen`類別中執行, 因為這是建立的`MonoTouch.Dialog.BindingContext`位置。 下列`HomeScreen`方法支援 [工作詳細資料] 畫面:

1. **ShowTaskDetails** –建立`MonoTouch.Dialog.BindingContext`以呈現螢幕。 它會使用反映建立輸入畫面, 以從`TaskDialog`類別取出屬性名稱和類型。 其他資訊 (例如輸入方塊的浮水印文字) 會實作為屬性上的屬性。
2. **SaveTask** –這個方法是在`TaskDialog`類別`OnTap`中透過屬性參考。 當按下 [**儲存**] 時, 會呼叫它`MonoTouch.Dialog.BindingContext` , 並使用來取出使用者輸入的資料, 然後再`TaskItemManager`使用儲存變更。
3. **DeleteTask** –這個方法是在`TaskDialog`類別`OnTap`中透過屬性參考。 它會`TaskItemManager`使用, 以使用 primary key (ID 屬性) 來刪除資料。

 <a name="Android_App" />

## <a name="android-app"></a>Android 應用程式

完整的 Xamarin. Android 專案如下圖所示:

 ![](case-study-tasky-images/taskyandroid-solution.png "Android 專案的說明如下所示")

類別圖, 其中的類別依圖層分組:

 [![](case-study-tasky-images/classdiagram-android.png "類別圖, 其中的類別依圖層分組")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>參考

Android 應用程式專案必須參考平臺特定的 Xamarin. Android 元件, 才能從 Android SDK 存取類別。

它也必須參考 PCL 專案 (例如 TaskyPortableLibrary) 存取通用資料和商務層程式碼。

 ![](case-study-tasky-images/taskyandroid-references.png "存取通用資料和商務層程式碼的 TaskyPortableLibrary")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>應用層 (AL)

類似于稍早所探討的 iOS 版本, Android 版本中的應用層包含將核心公開的物件「系結」至 UI 所需的平臺特定類別。

 **TaskListAdapter** -若要顯示物件\<的清單 T > 我們需要執行介面卡, 以便在中顯示`ListView`自訂物件。 介面卡會控制清單中每個專案所使用的版面配置, 在此情況下, 程式碼會使用 Android 內`SimpleListItemChecked`建版面配置。

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>使用者介面 (UI)

Android 應用程式的使用者介面層是程式碼和 XML 標記的組合。

- **資源/** 配置-螢幕佈局和實作為 AXML 檔案的資料列儲存格設計。 AXML 可以手動撰寫, 或使用適用于 Android 的 Xamarin UI 設計工具以視覺化方式呈現。
- **資源/繪製**–影像 (圖示) 和自訂按鈕。
- **畫面**–定義每個畫面和其行為的活動子類別。 將 UI 與應用層類別和通用 API (`TaskItemManager`) 結合在一起。

 <a name="Home_Screen" />

#### <a name="home-screen"></a>主畫面

[首頁] 畫面包含一個活動子`HomeScreen`類別`HomeScreen.axml` , 以及一個定義版面配置 (按鈕和工作清單的位置) 的檔案。 畫面看起來像這樣:

 [![](case-study-tasky-images/android-taskylist.png "畫面看起來像這樣")](case-study-tasky-images/android-taskylist.png#lightbox)

主畫面程式碼會定義按一下按鈕並按一下清單中的專案, 以及在`OnResume`方法中填入清單 (以便反映在 [工作詳細資料] 畫面中所做的變更) 的處理常式。 資料是使用商務層`TaskItemManager`和應用層的`TaskListAdapter`來載入。

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>工作詳細資料畫面

[工作詳細資料] 畫面也包含`Activity`子類別和 AXML 配置檔案。 版面配置會決定輸入控制項的位置, 而C#類別會定義載入和儲存`TaskItem`物件的行為。

 [![](case-study-tasky-images/android-taskydetail.png "類別會定義載入和儲存 TaskItem 物件的行為")](case-study-tasky-images/android-taskydetail.png#lightbox)

PCL 程式庫的所有參考都是透過`TaskItemManager`類別。

 <a name="Windows_Phone_App" />

## <a name="windows-phone-app"></a>Windows Phone 應用程式
完整的 Windows Phone 專案:

 ![](case-study-tasky-images/taskywp7-solution.png "Windows Phone 應用程式完成完整的 Windows Phone 專案")

下圖顯示分組為圖層的類別:

 [![](case-study-tasky-images/classdiagram-wp7.png "此圖顯示分組為圖層的類別")](case-study-tasky-images/classdiagram-wp7.png#lightbox)

 <a name="References" />

### <a name="references"></a>參考

平臺特定專案必須參考所需的平臺特定程式庫 (例如`Microsoft.Phone`和`System.Windows`), 才能建立有效的 Windows Phone 應用程式。

它也必須參考 PCL 專案 (例如 `TaskyPortableLibrary`) 以利用`TaskItem`類別和資料庫。

 ![](case-study-tasky-images/taskywp7-references.png "要利用 TaskItem 類別和資料庫的 TaskyPortableLibrary")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>應用層 (AL)

同樣地, 如同 iOS 和 Android 版本, 應用層是由可協助您將資料系結至使用者介面的非視覺元素所組成。

 <a name="ViewModels" />

#### <a name="viewmodels"></a>ViewModels

Viewmodel 會從 PCL ( `TaskItemManager`) 包裝資料, 並以可供 Silverlight/XAML 資料系結使用的方式呈現。 這是平臺特定行為的範例 (如跨平臺應用程式檔中所討論)。

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>使用者介面 (UI)

XAML 具有唯一的資料系結功能, 可在標記中宣告, 並減少顯示物件所需的程式碼數量:

1. **頁面**– XAML 檔案和其後置的定義使用者介面, 並參考 VIEWMODEL 和 PCL 專案來顯示和收集資料。
2. **影像**–啟動顯示畫面、背景和圖示影像是使用者介面的重要部分。

 <a name="MainPage" />

#### <a name="mainpage"></a>MainPage

MainPage 類別會使用`TaskListViewModel` , 以使用 XAML 的資料系結功能來顯示資料。 頁面的`DataContext`會設定為以非同步方式填入的視圖模型。 XAML `{Binding}`中的語法會決定資料的顯示方式。

 <a name="TaskDetailsPage" />

#### <a name="taskdetailspage"></a>TaskDetailsPage

藉由`TaskViewModel`將系結至 TaskDetailsPage 中定義的 xaml, 即可顯示每項工作。 工作資料是透過商務層中`TaskItemManager`的來抓取。

 <a name="Results" />

## <a name="results"></a>結果

產生的應用程式在每個平臺上看起來像這樣:

 <a name="iOS" />

#### <a name="ios"></a>iOS

應用程式會使用 iOS 標準使用者介面設計, 例如定位在導覽列中的 [新增] 按鈕, 並使用內建的**加號 (+)** 圖示。 它也會使用預設`UINavigationController`的 [返回] 按鈕行為, 並支援資料表中的「滑動到刪除」。

 [![](case-study-tasky-images/ios-taskylist.png "它也會使用預設 UINavigationController 上一頁按鈕行為，並支援資料表撥動至刪除")](case-study-tasky-images/ios-taskylist.png#lightbox) [![](case-study-tasky-images/ios-taskylist.png "它也會使用預設 UINavigationController備份按鈕的行為，並支援表撥動-delete")](case-study-tasky-images/ios-taskylist.png#lightbox)

 <a name="Android" />

#### <a name="android"></a>Android

Android 應用程式會使用內建控制項, 包括需要顯示「滴答」的資料列內建版面配置。 除了螢幕上的 [上一頁] 按鈕, 也支援硬體/系統返回行為。

 [![](case-study-tasky-images/android-taskylist.png "除了螢幕上的上一頁按鈕")](case-study-tasky-images/android-taskylist.png#lightbox)[![]以外, 還支援硬體/系統返回行為(case-study-tasky-images/android-taskylist.png ", 除了螢幕上的 [上一頁] 按鈕以外, 還支援硬體/系統返回行為")](case-study-tasky-images/android-taskylist.png#lightbox)

 <a name="Windows_Phone" />

#### <a name="windows-phone"></a>Windows Phone

Windows Phone 應用程式會使用標準版面配置, 在畫面底部填入應用程式行, 而不是頂端的導覽列。

 [Windows Phone 應用程式會使用標準版面配置, 在畫面底部填入應用程式行, 而不是頂端的導覽列。 ![(case-study-tasky-images/wp-taskylist.png "")] ](case-study-tasky-images/wp-taskylist.png#lightbox)[Windows Phone 應用(case-study-tasky-images/wp-taskylist.png "程式會使用標準版面配置, 在畫面底部填入應用程式行, 而不是頂端")的導覽列。 ![]](case-study-tasky-images/wp-taskylist.png#lightbox)

 <a name="Summary" />

## <a name="summary"></a>總結

本檔提供詳細說明如何將分層應用程式設計的原則套用至簡單的應用程式, 以加速跨三個行動平臺 (iOS、Android 和 Windows Phone) 使用程式碼。

其中已說明用來設計應用層的程式, 並討論每個&amp;層級中已執行的程式碼功能。

您可以從[github](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)下載程式代碼。

## <a name="related-links"></a>相關連結

- [建立跨平臺應用程式 (主要檔)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Tasky 可移植範例應用程式 (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
