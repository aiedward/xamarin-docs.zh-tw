---
title: 跨平臺應用程式案例研究： Tasky
description: 本檔說明如何以跨平臺行動應用程式的方式設計和建立 Tasky 可移植範例應用程式。 它會討論應用程式的需求、介面、資料模型、核心功能、執行等。
ms.prod: xamarin
ms.assetid: B581B2D0-9890-C383-C654-0B0E12DAD5A6
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 803b42cfcc27dc86b0d4bc78fc4745af5565e8cb
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457545"
---
# <a name="cross-platform-app-case-study-tasky"></a>跨平臺應用程式案例研究： Tasky

*Tasky* *可移植* 性是簡單的待辦事項清單應用程式。 本檔將討論如何設計和建立它，並遵循 [建立跨平臺應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) 檔的指導方針。 討論內容涵蓋下列領域：

<a name="Design_Process"></a>

## <a name="design-process"></a>設計程序

建議您在開始撰寫程式碼之前，先建立想要達成的道路地圖。 這特別適用于跨平臺開發，您所建立的功能將會以多種方式公開。 從現在開始，清楚瞭解您所建立的內容，可以在開發週期中更節省時間和精力。

 <a name="Requirements"></a>

### <a name="requirements"></a>需求

設計應用程式的第一個步驟是識別所需的功能。 這些可以是高階目標或詳細的使用案例。 Tasky 具有直接的功能性需求：

- 查看工作清單
- 新增、編輯和刪除工作
- 將工作的狀態設定為 [已完成]

您應該考慮使用平臺特定功能。  Tasky 可以利用 iOS 地理柵欄或 Windows Phone 動態磚嗎？ 即使您未在第一版中使用平臺特定功能，您還是應該事先規劃，以確定您的企業 & 資料層可以容納它們。

 <a name="User_Interface_Design"></a>

### <a name="user-interface-design"></a>使用者介面設計

從可在目標平臺上執行的高階設計開始著手。 請留意平臺特定 UI 條件約束。 例如， `TabBarController` 在 iOS 中，可顯示五個以上的按鈕，而 Windows Phone 的相等最多可顯示四個。
使用您選擇的工具繪製螢幕流程 (紙張) 。

 [![使用您選擇的工具來繪製螢幕流程的工作](case-study-tasky-images/taskydesign.png)](case-study-tasky-images/taskydesign.png#lightbox)

 <a name="Data_Model"></a>

### <a name="data-model"></a>資料模型

瞭解需要儲存的資料將有助於判斷要使用的持續性機制。 請參閱 [跨平臺資料存取](~/cross-platform/app-fundamentals/index.md) ，以取得可用儲存機制的相關資訊，並協助您決定兩者之間的差異。 針對此專案，我們將使用 SQLite.NET。

Tasky 需要為每個 ' TaskItem ' 儲存三個屬性：

- **Name** – 字串
- **附注** -字串
- **完成** –布林值

 <a name="Core_Functionality"></a>

### <a name="core-functionality"></a>核心功能

請考慮使用者介面需要取用以符合需求的 API。 待辦事項清單需要下列功能：

- **列出所有** 工作–顯示所有可用工作的主要螢幕清單
- **取得一項** 工作-觸及工作資料列時
- **儲存一項** 工作-編輯工作時
- **刪除一項** 工作–刪除工作時
- **建立空白** 工作-建立新工作時

為了讓程式碼重複使用，此 API 應該在 *便攜類別庫*中實作為。

 <a name="Implementation"></a>

### <a name="implementation"></a>實作

一旦應用程式設計獲得同意之後，請考慮如何將其實作為跨平臺應用程式。 這會成為應用程式的架構。 遵循 [建立跨平臺應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) 檔中的指引，應用程式程式碼應細分為下列部分：

- **通用程式碼** -包含可重複使用的程式碼以儲存工作資料的通用專案;公開模型類別和 API，以管理資料的儲存和載入。
- **平臺專屬的程式碼** –為每個作業系統執行原生 UI 的平臺特定專案，並利用通用程式碼做為「後端」。

[![平臺特定專案會針對每個作業系統執行原生 UI，並利用通用程式碼作為後端](case-study-tasky-images/taskypro-architecture.png)](case-study-tasky-images/taskypro-architecture.png#lightbox)

下列各節將說明這兩個部分。

 <a name="Common_(PCL)_Code"></a>

## <a name="common-pcl-code"></a>常見的 (PCL) 碼

Tasky 便攜使用可移植的類別庫策略來共用通用程式碼。 如需程式碼共用選項的說明，請參閱 [共用程式碼選項](~/cross-platform/app-fundamentals/code-sharing.md) 檔。

所有常見的程式碼（包括資料存取層、資料庫程式碼和合約）都會放置在程式庫專案中。

完整的 PCL 專案如下所示。 便攜程式庫中的所有程式碼都與每個目標平臺相容。 部署時，每個原生應用程式都會參考該程式庫。

![部署時，每個原生應用程式都會參考該程式庫](case-study-tasky-images/portable-project.png)

下圖顯示依圖層分組的類別。 `SQLiteConnection`類別是來自 Sqlite-NET 封裝的未定案程式碼。 其餘的類別是 Tasky 的自訂程式碼。 `TaskItemManager`和 `TaskItem` 類別代表對平臺特定應用程式公開的 API。

 [![TaskItemManager 和 TaskItem 類別代表對平臺特定應用程式公開的 API](case-study-tasky-images/classdiagram-core.png)](case-study-tasky-images/classdiagram-core.png#lightbox)

使用命名空間分隔各層有助於管理每個圖層之間的參考。 平臺特定專案只需要包含 `using` 商務層的語句。 資料存取層和資料層應該由商務層中公開的 API 封裝 `TaskItemManager` 。

 <a name="References"></a>

### <a name="references"></a>參考資料

便攜類別庫必須可跨多個平臺使用，每個平臺各有不同的平臺和架構功能支援層級。 因此，可以使用封裝和架構程式庫的限制。 例如，Xamarin 不支援 c # `dynamic` 關鍵字，因此便攜類別庫無法使用相依于動態程式碼的任何套件，即使這類程式碼可在 Android 上運作也一樣。 Visual Studio for Mac 將會讓您無法新增不相容的套件和參考，但您會想要保留限制，以避免日後發生意外狀況。

注意：您會看到您的專案參考了尚未使用的 framework 程式庫。 這些參考包含在 Xamarin 專案範本中。 當應用程式進行編譯時，連結程式將會移除未參考的程式碼，因此即使已被 `System.Xml` 參考，也不會包含在最終的應用程式中，因為我們不會使用任何 Xml 函數。

 <a name="Data_Layer_(DL)"></a>

### <a name="data-layer-dl"></a>資料層 (DL) 

資料層包含執行資料實體儲存的程式碼，無論是資料庫、一般檔案或其他機制都一樣。 Tasky 資料層是由兩個部分所組成：已新增 SQLite 程式庫和自訂程式碼以連接到網路。

Tasky 依賴由 Frank Krueger) 發佈的 Sqlite (NuGet 封裝，以內嵌可提供物件關聯式對應 (ORM) 資料庫介面的 SQLite .net 程式碼。 `TaskItemDatabase`類別繼承自， `SQLiteConnection` 並新增必要的建立、讀取、更新、刪除 (CRUD) 方法，以將資料讀取和寫入至 SQLite。 它是可在其他專案中重複使用之一般 CRUD 方法的簡單未定案執行。

`TaskItemDatabase`是單一的，可確保所有存取都會針對相同的實例進行。 鎖定是用來防止多個執行緒的平行存取。

 <a name="SQLite_on_WIndows_Phone"></a>

#### <a name="sqlite-on-windows-phone"></a>Windows Phone 上的 SQLite

雖然 iOS 和 Android 都隨附于 SQLite 作為作業系統的一部分，但 Windows Phone 不包含相容的 database engine。 若要在所有三個平臺之間共用程式碼，必須要有 Windows phone 原生版本的 SQLite。 如需有關為 Sqlite 設定 Windows Phone 專案的詳細資訊，請參閱使用 [本機資料庫](~/xamarin-forms/data-cloud/data/databases.md) 。

 <a name="Using_an_Interface_to_Generalize_Data_Access"></a>

#### <a name="using-an-interface-to-generalize-data-access"></a>使用介面將資料存取一般化

資料層會相依于， `BL.Contracts.IBusinessIdentity` 讓它可以執行需要主鍵的抽象資料存取方法。 然後，任何可執行介面的商務層級類別都會保存在資料層中。

介面只會指定要作為主鍵的整數屬性：

```csharp
public interface IBusinessEntity {
    int ID { get; set; }
}
```

基類會執行介面並新增 SQLite 屬性，將它標示為自動遞增的主鍵。 在商務層中，任何可實作為此基類的類別，就可以保存在資料層中：

```csharp
public abstract class BusinessEntityBase : IBusinessEntity {
    public BusinessEntityBase () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
}
```

使用介面的資料層中的泛型方法範例 `GetItem<T>` 如下：

```csharp
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

 <a name="Locking_to_prevent_Concurrent_Access"></a>

#### <a name="locking-to-prevent-concurrent-access"></a>鎖定以防止平行存取

[鎖定](/previous-versions/visualstudio/visual-studio-2010/c5kehkcz(v=vs.100))會在類別內實作為 `TaskItemDatabase` ，以防止資料庫的平行存取。 這是為了確保會序列化不同執行緒的平行存取 (否則，UI 元件可能會在背景執行緒) 更新時，嘗試讀取資料庫。 如何實作為鎖定的範例如下所示：

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

大部分的資料層程式碼都可以在其他專案中重複使用。 該層中唯一的應用程式特定程式碼是在函式 `CreateTable<TaskItem>` 中呼叫 `TaskItemDatabase` 。

 <a name="Data_Access_Layer_(DAL)"></a>

### <a name="data-access-layer-dal"></a>資料存取層 (DAL) 

`TaskItemRepository`類別會使用強型別 API 封裝資料儲存機制， `TaskItem` 以允許建立、刪除、取出和更新物件。

 <a name="Using_Conditional_Compilation"></a>

#### <a name="using-conditional-compilation"></a>使用條件式編譯

類別會使用條件式編譯來設定檔案位置，這是執行平臺分歧的範例。 傳回路徑的屬性會在每個平臺上編譯為不同的程式碼。 程式碼和平臺特定的編譯器指示詞如下所示：

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

視平臺而定，iOS 的輸出會是 " <app
path> /Library/TaskDB.db3"，適用于 Android 為 "/Documents/TaskDB.db3"，而針對 Windows Phone 則為 " <app
path> TaskDB. db3"。

### <a name="business-layer-bl"></a>商務層 (BL) 

商務層會執行模型類別和外觀來管理它們。
在 Tasky 中，模型是 `TaskItem` 類別並會執行 `TaskItemManager` 面板模式，以提供用於管理的 API `TaskItems` 。

 <a name="Façade"></a>

#### <a name="faade"></a>門面

 `TaskItemManager` 會包裝， `DAL.TaskItemRepository` 以提供應用程式和 UI 層所參考的 Get、Save 和 Delete 方法。

如有必要，商務規則和邏輯會置於此處-例如，在儲存物件之前必須滿足的任何驗證規則。

 <a name="API_for_Platform-Specific_Code"></a>

### <a name="api-for-platform-specific-code"></a>平臺特定程式碼的 API

撰寫通用程式碼之後，必須建立使用者介面，以收集和顯示其所公開的資料。 類別會實作為 `TaskItemManager` 面板模式，以提供簡單的 API 讓應用程式程式碼存取。

在每個平臺特定專案中撰寫的程式碼通常會緊密地結合到該裝置的原生 SDK，而且只會使用所定義的 API 來存取通用程式碼 `TaskItemManager` 。 這包括其公開的方法和商業類別，例如 `TaskItem` 。

映射不會跨平臺共用，而是個別新增至每個專案。 這點很重要，因為每個平臺都會使用不同的檔案名、目錄和解析度，以不同的方式處理影像。

其餘各節將討論 Tasky UI 的平臺特定執行詳細資料。

 <a name="iOS_App"></a>

## <a name="ios-app"></a>iOS 應用程式

使用一般 PCL 專案來儲存和取出資料時，只有少數幾個類別需要執行 iOS Tasky 應用程式。 完整的 iOS Xamarin. iOS 專案如下所示：

 ![iOS 專案如下所示](case-study-tasky-images/taskyios-solution.png)

這些類別會顯示在此圖中，並依圖層分組。

 [![這些類別會顯示在此圖中，並依圖層分組](case-study-tasky-images/classdiagram-android.png)](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References"></a>

### <a name="references"></a>參考資料

IOS 應用程式會參考平臺專屬的 SDK 程式庫–例如 Xamarin 和 Monotouch.dll。對話方塊-1。

它也必須參考 `TaskyPortableLibrary` PCL 專案。
[參考] 清單如下所示：

 ![[參考] 清單如下所示](case-study-tasky-images/taskyios-references.png)

應用層和消費者介面層會在此專案中使用這些參考來執行。

 <a name="Application_Layer_(AL)"></a>

### <a name="application-layer-al"></a>應用層 (AL) 

應用層包含必要的平臺特定類別，以將 PCL 公開的物件「系結」至 UI。 IOS 特定的應用程式有兩個類別可協助顯示工作：

- **EditingSource** –此類別可用來將工作清單系結至使用者介面。 因為 `MonoTouch.Dialog` 是用於工作清單中，所以我們需要執行這項協助程式，以啟用中的滑動至刪除功能  `UITableView` 。 在 iOS （但不是 Android 或 Windows Phone）上，輕輕刪除很常見，因此 iOS 特定專案是唯一可執行檔專案。
- **TaskDialog** –此類別可用來將單一工作系結至 UI。 它會使用 `MonoTouch.Dialog` 反映 API，以包含正確屬性的類別來「包裝」 `TaskItem` 物件，以允許正確格式化輸入畫面。

`TaskDialog`類別會使用 `MonoTouch.Dialog` 屬性，根據類別的屬性來建立畫面。 類別看起來像這樣：

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

請注意， `OnTap` 屬性需要方法名稱，這些方法必須存在於建立 (的類別中 `MonoTouch.Dialog.BindingContext` ，在此情況下， `HomeScreen` 下一節中所討論的類別) 。

 <a name="User_Interface_Layer_(UI)"></a>

### <a name="user-interface-layer-ui"></a>消費者介面層 (UI) 

消費者介面層包含下列類別：

1. **AppDelegate** –包含對外觀 API 的呼叫，以針對應用程式中使用的字型和色彩進行樣式。 Tasky 是一個簡單的應用程式，因此不會在中執行其他的初始化工作  `FinishedLaunching` 。
2. **畫面** - `UIViewController` 定義每個畫面及其行為的子類別。 畫面會將 UI 與應用層類別和一般 API ( ) 結合在一起 `TaskItemManager` 。 在此範例中，畫面是在程式碼中建立的，但它們可以使用 Xcode 的 Interface Builder 或分鏡腳本設計工具來設計。
3. **影像** –視覺元素是每個應用程式的重要部分。 Tasky 具有啟動顯示畫面和圖示影像，適用于 iOS 的一般和 Retina 解決方案必須提供這些影像。

 <a name="Home_Screen"></a>

#### <a name="home-screen"></a>主畫面

主畫面是 `MonoTouch.Dialog` 顯示來自 SQLite 資料庫之工作清單的螢幕。 它繼承自 `DialogViewController` 並執行程式碼，以將設定 `Root` 為包含要 `TaskItem` 顯示的物件集合。

 [![它繼承自 DialogViewController 並執行程式碼，以設定根目錄以包含要顯示的 TaskItem 物件集合](case-study-tasky-images/ios-taskylist.png)](case-study-tasky-images/ios-taskylist.png#lightbox)

與工作清單顯示和互動的兩個主要方法如下：

1. **PopulateTable** –使用商務層的  `TaskManager.GetTasks` 方法來取得  `TaskItem` 要顯示的物件集合。
2. 已**選取**–觸及某個資料列時，會在新畫面中顯示該工作。

 <a name="Task_Details_Screen"></a>

#### <a name="task-details-screen"></a>工作詳細資料畫面

工作詳細資料是輸入畫面，可讓您編輯或刪除工作。

Tasky `MonoTouch.Dialog` 會使用的反映 API 來顯示畫面，因此不會執行任何動作 `UIViewController` 。 相反地，類別會具現 `HomeScreen` 化，並 `DialogViewController` 使用 `TaskDialog` 應用層中的類別來顯示。

這個螢幕擷取畫面顯示一個空白畫面，示範在 `Entry` [ **名稱** ] 和 [ **附注** ] 欄位中設定浮水印文字的屬性：

 [![這個螢幕擷取畫面顯示一個空白畫面，示範在 [名稱] 和 [附注] 欄位中設定浮水印文字的專案屬性。](case-study-tasky-images/ios-taskydetail.png)](case-study-tasky-images/ios-taskydetail.png#lightbox)

工作 **詳細資料** 畫面 (的功能（例如儲存或刪除工作) ）必須在類別中執行 `HomeScreen` ，因為這是建立的位置 `MonoTouch.Dialog.BindingContext` 。 下列 `HomeScreen` 方法支援 [工作詳細資料] 畫面：

1. **ShowTaskDetails** –建立  `MonoTouch.Dialog.BindingContext` 以呈現螢幕。 它會使用反映來建立輸入畫面，以從類別取出屬性名稱和類型  `TaskDialog` 。 其他資訊（例如輸入方塊的浮水印文字）會使用屬性（attribute）來實作為屬性（property）。
2. **SaveTask** –此方法會透過屬性在  `TaskDialog` 類別中參考  `OnTap` 。 當按下 [  **儲存** ] 時，會呼叫它，並使用在  `MonoTouch.Dialog.BindingContext` 儲存變更之前，先取出使用者輸入的資料  `TaskItemManager` 。
3. **DeleteTask** –此方法會透過屬性在  `TaskDialog` 類別中參考  `OnTap` 。 它用  `TaskItemManager` 來刪除使用 primary key (ID 屬性) 的資料。

 <a name="Android_App"></a>

## <a name="android-app"></a>Android 應用程式

完整的 Xamarin. Android 專案如下圖所示：

 ![Android 專案如下圖所示](case-study-tasky-images/taskyandroid-solution.png)

類別圖表，其中類別依圖層分組：

 [![類別圖表，其中類別依圖層分組](case-study-tasky-images/classdiagram-android.png)](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References"></a>

### <a name="references"></a>參考資料

Android 應用程式專案必須參考平臺特定的 Xamarin. Android 元件，才能從 Android SDK 存取類別。

它也必須參考 PCL 專案 (例如 TaskyPortableLibrary) 存取通用資料和商務層程式碼。

 ![存取 common data 和 business layer 程式碼的 TaskyPortableLibrary](case-study-tasky-images/taskyandroid-references.png)

 <a name="Application_Layer_(AL)"></a>

### <a name="application-layer-al"></a>應用層 (AL) 

類似于稍早所探討的 iOS 版本，Android 版中的應用層級包含將核心公開的物件「系結」至 UI 所需的平臺特定類別。

 **TaskListAdapter** –用來顯示 \<T> 在中顯示自訂物件所需的物件清單 `ListView` 。 介面卡會控制清單中的每個專案所使用的版面配置，在此案例中，程式碼會使用 Android 內建的版面配置 `SimpleListItemChecked` 。

 <a name="User_Interface_(UI)"></a>

### <a name="user-interface-ui"></a>使用者介面 (UI)

Android 應用程式的消費者介面圖層是程式碼和 XML 標記的組合。

- **資源/** 配置–將螢幕版面配置和資料列儲存格設計實作為 .axml 檔。 .AXML 可以手動撰寫，或是使用適用于 Android 的 Xamarin UI 設計工具以視覺化方式進行配置。
- **資源/繪製** -影像 (圖示) 和自訂按鈕。
- **畫面** -定義每個畫面及其行為的活動子類別。 將 UI 與應用層類別和一般 API () 系結在一起 `TaskItemManager` 。

 <a name="Home_Screen"></a>

#### <a name="home-screen"></a>主畫面

主畫面包含活動子類別和檔案，該檔案會 `HomeScreen` `HomeScreen.axml` 定義按鈕和工作清單) 的版面配置 (位置。 畫面看起來像這樣：

 [![畫面看起來像這樣](case-study-tasky-images/android-taskylist.png)](case-study-tasky-images/android-taskylist.png#lightbox)

主畫面程式碼會定義用於按一下按鈕並按一下清單中專案的處理常式，並在方法 (中填入清單， `OnResume` 以反映在 [工作詳細資料] 畫面) 中所做的變更。 `TaskItemManager`從應用層使用商務層和來載入資料 `TaskListAdapter` 。

 <a name="Task_Details_Screen"></a>

#### <a name="task-details-screen"></a>工作詳細資料畫面

[工作詳細資料] 畫面也包含子 `Activity` 類別和 .axml 版面配置檔案。 版面配置會決定輸入控制項的位置，而 c # 類別會定義載入和儲存物件的行為 `TaskItem` 。

 [![類別定義載入和儲存 TaskItem 物件的行為](case-study-tasky-images/android-taskydetail.png)](case-study-tasky-images/android-taskydetail.png#lightbox)

PCL 程式庫的所有參考都是透過 `TaskItemManager` 類別。

 <a name="Windows_Phone_App"></a>

## <a name="windows-phone-app"></a>Windows Phone 應用程式
完整的 Windows Phone 專案：

 ![Windows Phone 應用程式完成 Windows Phone 專案](case-study-tasky-images/taskywp7-solution.png)

下圖顯示群組為圖層的類別：

 [![下圖顯示群組為圖層的類別](case-study-tasky-images/classdiagram-wp7.png)](case-study-tasky-images/classdiagram-wp7.png#lightbox)

 <a name="References"></a>

### <a name="references"></a>參考資料

平臺特定專案必須參考所需的平臺特定程式庫 (例如 `Microsoft.Phone` 和) ， `System.Windows` 才能建立有效的 Windows Phone 應用程式。

它也必須參考 PCL 專案 (例如 `TaskyPortableLibrary`) 利用 `TaskItem` 類別和資料庫。

 ![利用 TaskItem 類別和資料庫的 TaskyPortableLibrary](case-study-tasky-images/taskywp7-references.png)

 <a name="Application_Layer_(AL)"></a>

### <a name="application-layer-al"></a>應用層 (AL) 

同樣地，如同 iOS 和 Android 版本，應用層是由可協助將資料系結至使用者介面的非視覺元素所組成。

 <a name="ViewModels"></a>

#### <a name="viewmodels"></a>ViewModels

Viewmodel 會將來自 PCL)  ( 的資料換行 `TaskItemManager` ，並以可供 Silverlight/XAML 資料系結使用的方式呈現。 這是平臺特定行為的範例， (如跨平臺應用程式檔) 中所述。

 <a name="User_Interface_(UI)"></a>

### <a name="user-interface-ui"></a>使用者介面 (UI)

XAML 具有唯一的資料系結功能，可在標記中宣告並減少顯示物件所需的程式碼數量：

1. **Pages** – XAML 檔案和其後置定義使用者介面，並參考 VIEWMODEL 和 PCL 專案，以顯示並收集資料。
2. **影像** –啟動顯示畫面、背景和圖示影像是使用者介面的重要部分。

 <a name="MainPage"></a>

#### <a name="mainpage"></a>MainPage

MainPage 類別會使用， `TaskListViewModel` 以使用 XAML 的資料系結功能來顯示資料。 頁面 `DataContext` 會設定為以非同步方式填入的視圖模型。 `{Binding}`XAML 中的語法會決定如何顯示資料。

 <a name="TaskDetailsPage"></a>

#### <a name="taskdetailspage"></a>TaskDetailsPage

每項工作都是藉由將系結 `TaskViewModel` 至 TaskDetailsPage 中定義的 xaml 來顯示。 工作資料會透過 `TaskItemManager` 商務層中的來抓取。

 <a name="Results"></a>

## <a name="results"></a>結果

產生的應用程式在每個平臺上看起來像這樣：

 <a name="iOS"></a>

### <a name="ios"></a>iOS

應用程式會使用 iOS 標準的使用者介面設計，例如在導覽列中放置 [新增] 按鈕，並使用內建的 **和 (+) ** 圖示。 它也會使用預設的「 `UINavigationController` 上一頁」按鈕行為，並支援資料表中的「滑動至刪除」。

 [ ![ 它也會使用預設的 UINavigationController 上一頁按鈕行為，並支援資料表中的滑動至刪除](case-study-tasky-images/ios-taskylist.png)](case-study-tasky-images/ios-taskylist.png#lightbox)， [ ![ 也會使用預設的 UINavigationController 上一頁按鈕行為，並支援在資料表中切換到刪除](case-study-tasky-images/ios-taskylist.png)](case-study-tasky-images/ios-taskylist.png#lightbox)

 <a name="Android"></a>

### <a name="android"></a>Android

Android 應用程式使用內建控制項，包括需要顯示「刻度」之資料列的內建版面配置。 除了螢幕上的 [上一頁] 按鈕之外，還支援硬體/系統回撥行為。

 除了螢幕上的 [上一頁] 按鈕之外，還[ ![ 支援硬體/系統回溯行為](case-study-tasky-images/android-taskylist.png)](case-study-tasky-images/android-taskylist.png#lightbox)，除了螢幕上的 [[ ![ 上一頁] 按鈕之外，還支援硬體/系統回撥行為](case-study-tasky-images/android-taskylist.png)](case-study-tasky-images/android-taskylist.png#lightbox)。

 <a name="Windows_Phone"></a>

### <a name="windows-phone"></a>Windows Phone

Windows Phone 應用程式會使用標準版面配置，並在畫面底部填入應用程式行，而不是頂端的導覽列。

 [ ![ Windows Phone 應用程式會使用標準版面配置，並在畫面底部填入應用程式行，而不是](case-study-tasky-images/wp-taskylist.png)](case-study-tasky-images/wp-taskylist.png#lightbox)在[ ![ Windows Phone 應用程式使用標準版面配置的導覽列上，于畫面底部填入應用程式行，而不是頂端](case-study-tasky-images/wp-taskylist.png)](case-study-tasky-images/wp-taskylist.png#lightbox)的導覽列

 <a name="Summary"></a>

## <a name="summary"></a>摘要

本檔已詳細說明如何將多層式應用程式設計原則套用至簡單的應用程式，以協助程式碼重複使用於三個行動平臺： iOS、Android 和 Windows Phone。

它已說明用來設計應用層的程式，並討論 &amp; 每個圖層中已執行的程式碼功能。

您可以從 [github](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)下載程式代碼。

## <a name="related-links"></a>相關連結

- [ (主檔案建立跨平臺應用程式) ](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [ (github) 的 Tasky 可移植範例應用程式 ](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)