---
title: 案例研究： Tasky
description: 本文件說明如何建置跨平台應用程式的原則已套用 Tasky 可攜式的範例應用程式中。 它所接觸行動應用程式的設計，撰寫一般程式碼重複使用和實作 iOS、 Android 和 Windows Phone 平台為目標的平台特定專案。
ms.prod: xamarin
ms.assetid: B581B2D0-9890-C383-C654-0B0E12DAD5A6
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 690edabd53752ff0347fdb232a4bbfcb1ba6e84d
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
---
# <a name="case-study-tasky"></a>案例研究： Tasky

_本文件說明如何建置跨平台應用程式的原則已套用 Tasky 可攜式的範例應用程式中。它所接觸行動應用程式的設計，撰寫一般程式碼重複使用和實作 iOS、 Android 和 Windows Phone 平台為目標的平台特定專案。_

*Tasky* *可攜式*是簡單的待辦事項清單應用程式。 本文將討論如何它所設計及建置，下列的指引[建置跨平台應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)文件。 討論涵蓋下列各方面：

<a name="Design_Process" />

## <a name="design-process"></a>設計程序

建議您建立的藍圖您要為您開始撰寫程式碼之前完成。 這是特別適用於跨平台開發，您要在其中建立將多個方式公開的功能。 開頭充份什麼您要建置節省時間和精力在開發週期。

 <a name="Requirements" />

### <a name="requirements"></a>需求

設計應用程式的第一個步驟是找出所需的功能。 這些可以是高層級目標或是詳細的使用案例。 Tasky 具有簡單的功能需求：

 -  檢視工作的清單
 -  加入、 編輯和刪除工作
 -  將工作的狀態設為 'done'

您應該考慮您使用的平台專屬功能。  可以 Tasky 利用 iOS 地理圍欄或 Windows Phone Live 菾拏嗎？ 即使您不使用特定平台功能中的第一個版本，您應該先確定您的商務和資料層級可以容納它們事先規劃。

 <a name="User_Interface_Design" />

### <a name="user-interface-design"></a>使用者介面設計

以可實作目標平台的高階設計開頭。 請注意注意平台以特定 UI 的條件約束。 例如，`TabBarController`在 iOS 中可以顯示五個以上的按鈕，而 Windows Phone 對等項目可以顯示最多四個。
繪製畫面流程使用 （紙張適） 您選擇的工具。

 [![](case-study-tasky-images/taskydesign.png "繪製畫面流程使用的工具選擇紙張的運作方式")](case-study-tasky-images/taskydesign.png#lightbox)

 <a name="Data_Model" />

### <a name="data-model"></a>資料模型

了解哪些資料需要儲存，可協助判斷要使用的持續性機制。 請參閱[跨平台的資料存取](~/cross-platform/app-fundamentals/index.md)取得可用的儲存機制與協助以決定它們之間的資訊。 此專案中，我們將使用 SQLite.NET。

Tasky 需要針對每個 'TaskItem' 儲存三個屬性：

 -  **名稱**– 字串
 -  **附註**– 字串
 -  **完成**– 布林

 <a name="Core_Functionality" />

### <a name="core-functionality"></a>核心功能

請考慮使用者介面必須使用以符合需求的 API。 待辦事項清單需要下列功能：

 -   **列出所有工作**– 顯示所有可用工作的主畫面都清單
 -  **取得一個工作**– 當工作資料列會接觸到
 -  **儲存一項工作**– 編輯工作時
 -  **刪除一個工作**– 當工作被刪除時
 -  **建立空白工作**– 建立新的工作時

若要達到重複使用程式碼，此應用程式開發介面應該一次在實作*可攜式類別庫*。

 <a name="Implementation" />

### <a name="implementation"></a>實作

一旦已經同意應用程式的設計，請考慮可能會將它實作為跨平台應用程式的方式。 這會成為應用程式的架構。 下列中的指導方針[建置跨平台應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)文件，應用程式程式碼應該是中斷向下列部分：

 -   **常見的程式碼**– 常見的專案，包含要儲存的工作資料的重複使用程式碼，則為公開模型類別和應用程式開發介面來管理儲存及載入的資料。
 -   **平台專屬的程式碼**– 實作原生 UI 的每個作業系統，利用一般的程式碼做為後端' 的平台專屬專案。

 [![](case-study-tasky-images/taskypro-architecture.png "平台專屬專案中實作利用做為後端的通用程式碼的原生 UI 的每個作業系統，")](case-study-tasky-images/taskypro-architecture.png#lightbox)

下列各節將說明這兩個部分。

 <a name="Common_(PCL)_Code" />

## <a name="common-pcl-code"></a>一般 (PCL) 程式碼

Tasky Portable 共用通用程式碼使用可攜式類別庫策略。 請參閱[共用程式碼選項](~/cross-platform/app-fundamentals/code-sharing.md)文件的程式碼共用選項的描述。

所有的通用程式碼，包括資料存取層、 資料庫程式碼和合約會在 程式庫專案中。

完成 PCL 專案，如下所示。 所有的可攜式程式庫中的程式碼是與每個目標平台相容。 部署時，每個原生應用程式將會參考該文件庫。

![](case-study-tasky-images/portable-project.png "每個原生應用程式部署時，將會參考該文件庫")

類別圖顯示依圖層的類別。 `SQLiteConnection`類別是從 Sqlite NET 封裝未定案程式碼。 類別的其餘部分是用於 Tasky 自訂程式碼。 `TaskItemManager`和`TaskItem`類別則表示應用程式開發介面公開至特定平台應用程式。

 [![](case-study-tasky-images/classdiagram-core.png "為 TaskItemManager 和 TaskItem 類別代表應用程式開發介面公開至特定平台應用程式")](case-study-tasky-images/classdiagram-core.png#lightbox)

使用命名空間至個別圖層，可幫助管理每個圖層之間的參考。 平台專屬專案應該只需要包含`using`商務圖層的陳述式。 資料存取層和資料層應該封裝所公開的 api`TaskItemManager`商務圖層中。

 <a name="References" />

### <a name="references"></a>參考

可攜式類別庫需要跨多平台，每個都有不同層級的支援平台與 framework 功能都無法使用。 因此，會在其上封裝和 framework 程式庫可以用的限制。 例如，Xamarin.iOS 不支援 c#`dynamic`關鍵字，所以可攜式類別庫不能使用任何動態程式碼，而定的封裝，即使這類程式碼會在 Android 上運作。 Visual Studio for Mac 會導致您無法加入不相容的套件和參考，但您會想要記住限制以避免稍後出現意外的狀況。

注意： 您會看到您的專案參考未使用過的 framework 程式庫。 這些參考會納入為 Xamarin 專案範本的一部分。 當應用程式編譯時，連結的程序將會移除未參考的程式碼，因此即使`System.Xml`已被參照，它將不會包含在最終的應用程式因為我們不使用任何 Xml 函式。

 <a name="Data_Layer_(DL)" />

### <a name="data-layer-dl"></a>資料層 (DL)

資料層包含的程式碼的資料 – 實體儲存到資料庫、 一般檔案或其他機制。 Tasky 資料層是由兩個部分所組成： SQLite NET 程式庫和自訂程式碼加入至連接它。

Tasky 依賴 Sqlite net nuget 封裝 （Frank Kreuger 所發佈） 提供的物件關聯式對應 (ORM) 資料庫介面的 SQLite NET 程式碼內嵌。 `TaskItemDatabase`類別繼承自`SQLiteConnection`，並將需要的建立、 讀取、 更新、 刪除 (CRUD) 方法來讀取及寫入資料至 SQLite。 簡單的未定案泛型方法的實作 CRUD 可能在其他專案中重複使用它。

`TaskItemDatabase`是一個 singleton，確保所有存取針對相同的執行個體就會都發生。 鎖定用於防止從多個執行緒並行存取。

 <a name="SQLite_on_WIndows_Phone" />

#### <a name="sqlite-on-windows-phone"></a>在 Windows Phone 上 SQLite

IOS 和 Android 兩者出貨的 SQLite 做為作業系統的一部分，而 Windows Phone 不包含相容的資料庫引擎。 若要跨所有三個平台共用程式碼 SQLite 的 Windows phone 原生版本。 請參閱[使用本機資料庫](~/xamarin-forms/app-fundamentals/databases.md)如需有關設定 Sqlite Windows Phone 專案。

 <a name="Using_an_Interface_to_Generalize_Data_Access" />

#### <a name="using-an-interface-to-generalize-data-access"></a>使用介面來一般化資料存取

資料層，不需要相依性`BL.Contracts.IBusinessIdentity`，讓它可以實作需要主索引鍵的抽象資料存取方法。 實作介面的任何商業層類別接著可以保存在資料層。

介面只會指定要作為主要索引鍵的整數屬性：

```csharp
public interface IBusinessEntity {
    int ID { get; set; }
}
```

基底類別實作的介面，並將 SQLite NET 屬性，將它標示為自動遞增主索引鍵。 商業層實作這個基底類別中的任何類別接著可以保存在資料層：

```csharp
public abstract class BusinessEntityBase : IBusinessEntity {
    public BusinessEntityBase () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
}
```

這是在資料層使用介面的泛型方法的範例`GetItem<T>`方法：

```csharp
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

 <a name="Locking_to_prevent_Concurrent_Access" />

#### <a name="locking-to-prevent-concurrent-access"></a>鎖定來防止並行存取

A[鎖定](http://msdn.microsoft.com/library/c5kehkcz(v=vs.100).aspx)內實作`TaskItemDatabase`類別，以防止並行存取資料庫。 這是為了確保從不同執行緒同時存取序列化 （否則 UI 元件可能會嘗試在背景執行緒正在更新的同時讀取資料庫）。 鎖定的實作方式的範例如下所示：

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

大部分的資料層程式碼無法重新用於其他專案。 只有特定應用程式中的程式碼的圖層`CreateTable<TaskItem>`呼叫中`TaskItemDatabase`建構函式。

 <a name="Data_Access_Layer_(DAL)" />

### <a name="data-access-layer-dal"></a>資料存取層 (DAL)

`TaskItemRepository`類別會封裝資料儲存機制，以強類型的 API，可讓`TaskItem`物件建立、 刪除、 擷取和更新。

 <a name="Using_Conditional_Compilation" />

#### <a name="using-conditional-compilation"></a>使用條件式編譯

類別會使用條件式編譯設定的檔案位置-這是實作平台的不一致的範例。 傳回路徑的屬性會在每個平台上的不同程式碼編譯。 如下所示的程式碼和特定平台編譯器指示詞：

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

根據平台，則輸出會是"<app
path>/Library/TaskDB.db3"ios 平台，"<app
path>/Documents/TaskDB.db3"Android 或只"TaskDB.db3"適用於 Windows Phone。

### <a name="business-layer-bl"></a>商業層 （bl。）

商業層實作的模型類別，來管理它們的外觀。
模型是在 Tasky`TaskItem`類別和`TaskItemManager`實作來提供用來管理應用程式開發介面的外觀模式`TaskItems`。

 <a name="Façade" />

#### <a name="faade"></a>外觀

 `TaskItemManager` 包裝`DAL.TaskItemRepository`提供 Get，請儲存並刪除應用程式和 UI 層所參考的方法。

商務規則和邏輯會將放置此處如果需要 – 例如任何物件會在儲存之前，必須滿足的驗證規則。

 <a name="API_for_Platform-Specific_Code" />

### <a name="api-for-platform-specific-code"></a>平台專屬的程式碼的 API

一旦已撰寫的通用程式碼，必須建立使用者介面來收集和顯示它所公開的資料。 `TaskItemManager`類別實作的外觀模式來提供應用程式程式碼的簡單 API 來存取。

每個平台專屬專案中撰寫的程式碼會通常緊密結合至該裝置的原生的 SDK 和只能存取一般使用 API 所定義的程式碼`TaskItemManager`。 這包括方法和商務之類的類別就會公開， `TaskItem`。

映像並不跨平台共用，而個別加入每個專案。 這是很重要，因為每個平台映像以不同方式處理，使用不同的檔案名稱、 目錄和解決方式。

其餘各節討論 Tasky UI 的平台特定實作詳細資料。

 <a name="iOS_App" />

## <a name="ios-app"></a>iOS 應用程式

有少數幾個實作 iOS Tasky 使用通用的 PCL 專案來儲存和擷取資料的應用程式所需的類別。 完成 iOS Xamarin.iOS 專案如下所示：

 ![](case-study-tasky-images/taskyios-solution.png "如下所示的 iOS 專案")

類別會顯示在此圖中，分組成圖層。

 [![](case-study-tasky-images/classdiagram-android.png "類別會顯示在此圖中，分組成圖層")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>參考

IOS 應用程式會參考特定平台 SDK 程式庫-例如。 Xamarin.iOS 和 MonoTouch.Dialog-1。

它也必須參考`TaskyPortableLibrary`PCL 專案。
參考清單如下所示：

 ![](case-study-tasky-images/taskyios-references.png "參考清單如下所示")

使用者介面層與應用程式層會在此專案中使用這些參考實作。

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>應用程式層級 (AL)

應用程式層包含 'bind' PCL UI 以公開的物件所需的平台特定類別。 IOS 專屬的應用程式具有可顯示工作的兩個類別：

 -   **EditingSource** – 這個類別用來繫結的使用者介面的工作清單。 因為`MonoTouch.Dialog`使用了針對工作清單中，我們必須實作此協助程式，以啟用撥動與刪除功能在`UITableView`。 撥動至刪除是通用 Io，但不是 Android 或 Windows Phone 上，因此 iOS 特定專案是唯一其實作。
 -   **TaskDialog** – 這個類別用來將單一工作繫結至 UI。 它會使用`MonoTouch.Dialog`反映 API，來包裝'`TaskItem`物件，包含要允許輸入的螢幕格式正確的正確屬性的類別。

`TaskDialog`類別會使用`MonoTouch.Dialog`類別的內容為基礎的屬性，以建立螢幕。 此類別看起來像這樣：

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

請注意`OnTap`屬性需要的方法名稱 – 這些方法必須存在於類別其中`MonoTouch.Dialog.BindingContext`建立 (在此情況下，`HomeScreen`類別下節中討論)。

 <a name="User_Interface_Layer_(UI)" />

### <a name="user-interface-layer-ui"></a>使用者介面層 (UI)

使用者介面層級包含下列類別：

1.   **AppDelegate** – 包含外觀 API 呼叫，以設定應用程式中使用的色彩與字型的樣式。 Tasky 是簡單的應用程式，因此沒有其他執行的初始設定工作`FinishedLaunching`。
2.   **螢幕**– 的子類別`UIViewController`，定義每個畫面和它的行為。 螢幕繫結在一起以 UI 應用程式層的類別和常見的應用程式開發介面 ( `TaskItemManager` )。 在此範例程式碼中，建立螢幕，但無法設計使用 Xcode 的介面產生器或分鏡腳本設計工具。
3.   **映像**– 視覺項目是每個應用程式中很重要的一部分。 Tasky 有啟動顯示畫面和圖示映像，也適用於 iOS 必須提供一般和 Retina 解析度。

 <a name="Home_Screen" />

#### <a name="home-screen"></a>主畫面

首頁畫面是`MonoTouch.Dialog`畫面，顯示一份 SQLite 資料庫中的工作。 它繼承自`DialogViewController`和實作程式碼以設定`Root`包含的集合`TaskItem`顯示的物件。

 [![](case-study-tasky-images/ios-taskylist.png "它繼承自 DialogViewController 和實作程式碼以設定根包含顯示 TaskItem 物件的集合")](case-study-tasky-images/ios-taskylist.png#lightbox)

顯示，並與其互動的工作清單與相關的兩個主要方法如下：

1.   **PopulateTable** – 使用商業層`TaskManager.GetTasks`方法來擷取集合`TaskItem`来顯示的物件。
2.   **選取**– 當會接觸到一個資料列時，在新的畫面中顯示的工作。

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>工作詳細資料螢幕

工作詳細資料不允許編輯或刪除工作的輸入的畫面。

Tasky 使用`MonoTouch.Dialog`的反映 API，以顯示螢幕，因此會有任何`UIViewController`實作。 相反地，`HomeScreen`類別具現化，並顯示`DialogViewController`使用`TaskDialog`從應用程式層的類別。

這個螢幕擷取畫面顯示示範空白螢幕`Entry`屬性中設定浮水印文字**名稱**和**備忘稿**欄位：

 [![](case-study-tasky-images/ios-taskydetail.png "這個螢幕擷取畫面顯示示範設定浮水印文字的名稱和附註欄位中的項目屬性的空白螢幕")](case-study-tasky-images/ios-taskydetail.png#lightbox)

功能**工作詳細資料**必須中實作 （例如儲存或刪除工作） 的螢幕`HomeScreen`類別，因為這是 where`MonoTouch.Dialog.BindingContext`建立。 下列`HomeScreen`方法支援工作詳細資料螢幕：

1.   **ShowTaskDetails** – 建立`MonoTouch.Dialog.BindingContext`來轉譯螢幕。 它會建立輸入的螢幕使用反映來擷取屬性的名稱及類型`TaskDialog`類別。 其他資訊，例如浮水印文字輸入方塊中，是使用屬性的屬性來實作。
2.   **SaveTask** – 此方法會參考`TaskDialog`類別透過`OnTap`屬性。 它時，會呼叫**儲存**按下時，並使用`MonoTouch.Dialog.BindingContext`擷取使用者輸入的資料儲存使用變更前`TaskItemManager`。
3.   **DeleteTask** – 此方法會參考`TaskDialog`類別透過`OnTap`屬性。 它會使用`TaskItemManager`若要刪除的資料，使用主索引鍵 （ID 屬性）。

 <a name="Android_App" />

## <a name="android-app"></a>Android 應用程式

完整的 Xamarin.Android 專案，如圖如下所示：

 ![](case-study-tasky-images/taskyandroid-solution.png "示 android 專案")

類別圖表中，依圖層的類別：

 [![](case-study-tasky-images/classdiagram-android.png "類別圖表中，依圖層的類別")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>參考

在 Android 應用程式專案必須參考從 Android SDK 平台專屬 Xamarin.Android 組件存取的類別。

它也必須 （例如參考 PCL 專案 TaskyPortableLibrary) 來存取通用資料和商務層程式碼。

 ![](case-study-tasky-images/taskyandroid-references.png "若要存取通用資料和商務層程式碼 TaskyPortableLibrary")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>應用程式層級 (AL)

類似於我們看的 iOS 版本更舊版本，應用程式中的圖層的 Android 版本包含 'bind' ui 核心所公開的物件所需的平台特定類別。

 **TaskListAdapter** – 顯示清單<T>我們需要實作配接器以顯示自訂物件中的物件`ListView`。 配接器可讓您控制哪些版面配置，用於清單中的每個項目 – 在本例中的程式碼會使用 Android 的內建配置`SimpleListItemChecked`。

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>使用者介面 (UI)

Android 應用程式使用者介面層級是程式碼與 XML 標記的組合。

 -   **資源/配置**– 螢幕配置和資料列儲存格實作為 AXML 檔案的設計。 相反地，寫入的或配置不足以視覺化方式使用適用於 Android 的 Xamarin UI 設計工具，可以是 AXML。
 -   **資源/Drawable** – 影像 （圖示） 和自訂按鈕。
 -   **螢幕**– 活動子類別，定義每個畫面和它的行為。 繫結在一起以 UI 應用程式層的類別和常見的應用程式開發介面 (`TaskItemManager`)。

 <a name="Home_Screen" />

#### <a name="home-screen"></a>主畫面

主畫面組成活動子類別`HomeScreen`和`HomeScreen.axml`檔案定義的配置 （按鈕和工作清單的位置）。 螢幕看起來像這樣：

 [![](case-study-tasky-images/android-taskylist.png "螢幕看起來像這樣")](case-study-tasky-images/android-taskylist.png#lightbox)

主畫面的程式碼會定義按一下按鈕和按一下在清單中的項目，以及填入該份清單中的處理常式`OnResume`方法 （因此它會反映在 工作詳細資料螢幕所做的變更）。 資料載入使用商業層`TaskItemManager`和`TaskListAdapter`從應用程式層。

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>工作詳細資料螢幕

也包含工作詳細資料螢幕`Activity`子類別和 AXML 配置檔案。 版面配置判斷輸入控制項的位置，以及 C# 類別定義以載入和儲存行為`TaskItem`物件。

 [![](case-study-tasky-images/android-taskydetail.png "類別會定義載入及儲存 TaskItem 物件的行為")](case-study-tasky-images/android-taskydetail.png#lightbox)

所有參考 PCL 程式庫都是透過`TaskItemManager`類別。

 <a name="Windows_Phone_App" />

## <a name="windows-phone-app"></a>Windows Phone 應用程式
完整的 Windows Phone 專案：

 ![](case-study-tasky-images/taskywp7-solution.png "Windows Phone 應用程式完整的 Windows Phone 專案")

下圖顯示分組成圖層的類別：

 [![](case-study-tasky-images/classdiagram-wp7.png "此圖表顯示分組成圖層的類別")](case-study-tasky-images/classdiagram-wp7.png#lightbox)

 <a name="References" />

### <a name="references"></a>參考

平台專屬專案必須參考所需的平台特定程式庫 (例如`Microsoft.Phone`和`System.Windows`) 來建立有效的 Windows Phone 應用程式。

它也必須 （例如參考 PCL 專案 `TaskyPortableLibrary`) 利用`TaskItem`類別和資料庫。

 ![](case-study-tasky-images/taskywp7-references.png "TaskyPortableLibrary 以便利用 TaskItem 類別和資料庫")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>應用程式層級 (AL)

同樣地，使用 iOS 和 Android 的版本中，應用程式層包含非視覺項目，協助將資料繫結至使用者介面。

 <a name="ViewModels" />

#### <a name="viewmodels"></a>ViewModels

ViewModels 換行的資料從 PCL ( `TaskItemManager`)，並提供可供 Silverlight/XAML 資料繫結的方式。 （如跨平台應用程式文件所述），這是平台專屬行為的範例。

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>使用者介面 (UI)

XAML 中具有唯一的資料繫結功能，可在標記中宣告，並減少顯示的物件所需的程式碼數量：

1.   **頁面**– XAML 檔案和其程式碼後置定義使用者介面，並參考 ViewModels 和 PCL 專案，以顯示並收集資料。
2.   **映像**– 啟動顯示畫面、 背景和圖示的影像會在使用者介面的主要部分。

 <a name="MainPage" />

#### <a name="mainpage"></a>MainPage

MainPage 類別會使用`TaskListViewModel`使用 XAML 的資料繫結功能顯示資料。 在頁面的`DataContext`設為非同步地擴展的檢視模型。 `{Binding}`在 XAML 中的語法會決定資料的顯示方式。

 <a name="TaskDetailsPage" />

#### <a name="taskdetailspage"></a>TaskDetailsPage

透過繫結，就會顯示每個工作`TaskViewModel`TaskDetailsPage.xaml 中定義的 xaml。 工作擷取的資料是透過`TaskItemManager`商務圖層中。

 <a name="Results" />

## <a name="results"></a>結果

每個平台上，產生的應用程式看起來像這樣：

 <a name="iOS" />

#### <a name="ios"></a>iOS

應用程式使用 iOS 標準使用者介面的設計，例如 'add' 按鈕放在導覽列中，使用內建**加號 （+）**圖示。 它也會使用預設`UINavigationController`[上一頁] 按鈕的行為，並支援 ' 撥動-delete' 資料表中的。

 [![](case-study-tasky-images/ios-taskylist.png "它也會使用預設 UINavigationController 上一頁按鈕行為，並支援資料表撥動至刪除")](case-study-tasky-images/ios-taskylist.png#lightbox) [ ![ ](case-study-tasky-images/ios-taskylist.png "它也會使用預設 UINavigationController備份按鈕的行為，並支援表撥動-delete")](case-study-tasky-images/ios-taskylist.png#lightbox)

 <a name="Android" />

#### <a name="android"></a>Android

Android 應用程式會使用內建控制項，包括內建的配置需要 '刻度' 顯示的資料列。 系統硬體/回復行為支援除了螢幕上一步 按鈕。

 [![](case-study-tasky-images/android-taskylist.png "除了螢幕上一步 按鈕支援硬體/系統回復行為")](case-study-tasky-images/android-taskylist.png#lightbox)[![](case-study-tasky-images/android-taskylist.png "除了支援硬體/系統回復行為螢幕上一頁按鈕")](case-study-tasky-images/android-taskylist.png#lightbox)

 <a name="Windows_Phone" />

#### <a name="windows-phone"></a>Windows Phone

Windows Phone 應用程式使用的標準版面配置，填入應用程式列，而不是在上方導覽列螢幕的底部。

 [![](case-study-tasky-images/wp-taskylist.png "Windows Phone 應用程式中使用的標準版面配置，填入應用程式列，而不是在上方導覽列螢幕底部")](case-study-tasky-images/wp-taskylist.png#lightbox) [ ![ ] (case-study-tasky-images/wp-taskylist.png "Windows Phone 應用程式會使用標準版面配置，填入應用程式列，而不是在上方導覽列螢幕底部")](case-study-tasky-images/wp-taskylist.png#lightbox)

 <a name="Summary" />

## <a name="summary"></a>總結

本文件提供的多層式應用程式的設計原則如何套用至簡單的應用程式，以加速程式碼重複使用三個行動平台的詳細的說明： iOS、 Android 和 Windows Phone。

它具有說明用於設計應用程式層級的程序，並討論何種程式碼&amp;已經每個圖層中實作功能。

您可以從下載的程式碼[github](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)。

## <a name="related-links"></a>相關連結

- [建置跨平台應用程式 （主要的文件）](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Tasky 可攜式範例應用程式 (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
