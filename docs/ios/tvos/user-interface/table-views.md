---
title: 在 Xamarin 中使用 tvOS 資料表視圖
description: 本文涵蓋在 tvOS 應用程式內設計和使用資料表視圖和資料表視圖控制器。
ms.prod: xamarin
ms.assetid: D8F80FA9-6400-4DB7-AFC9-A28A54AD04E8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 0d93978d6f7b3dff6d0d7ebf7c9f9afbe3572079
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199917"
---
# <a name="working-with-tvos-table-views-in-xamarin"></a>在 Xamarin 中使用 tvOS 資料表視圖

_本文涵蓋在 tvOS 應用程式內設計和使用資料表視圖和資料表視圖控制器。_

在 tvOS 中, 資料表視圖會以單一資料行的方式呈現, 可以選擇性地組織成群組或區段。 當您需要以清楚易懂的方式將大量資料有效率地顯示給使用者時, 應該使用資料表的 Views。

資料表視圖通常會在[分割視圖](~/ios/tvos/user-interface/split-views.md)的一側顯示為導覽, 而選取專案的詳細資料則會顯示在相反的位置:

[![](table-views-images/intro01.png "範例資料表視圖")](table-views-images/intro01.png#lightbox)

<a name="About-Table-Views" />

## <a name="about-table-views"></a>關於資料表視圖

會以階層式清單的形式顯示可滾動資料列的單一資料行,以選擇性地組織成群組或區段:`UITableView` 

[![](table-views-images/table01.png "選取的專案")](table-views-images/table01.png#lightbox)

Apple 具有下列使用資料表的建議:

- 請**留意寬度**-嘗試在您的資料表寬度中刪除正確的餘額。 如果資料表太寬, 可能很難從距離進行掃描, 也可能會從可用的內容區域中消失。 如果資料表太窄, 這可能會造成資訊遭到截斷或換行, 這可能會讓使用者難以在房間內讀取。
- **快速顯示資料表內容**-針對大型資料清單, 延遲載入內容, 並在資料表呈現給使用者時立即開始顯示資訊。 如果資料表需要花費很長的時間才能載入, 則使用者可能會在您的應用程式中失去興趣, 或認為它已被鎖定。
- **通知使用者長時間進行內容載入**-如果長時間的資料表載入時間無法避免, 則會顯示[進度列或活動指示器](~/ios/tvos/user-interface/progress-indicators.md), 讓他們知道應用程式尚未鎖定。

<a name="Table-Cell-Types" />

## <a name="table-view-cell-types"></a>資料表視圖資料格類型

`UITableViewCell`是用來代表資料表視圖中的個別資料列。 Apple 定義了數個預設的資料表資料格類型:

- **Default** -此類型會在資料格的左邊顯示一個選項影像, 並在右側提供靠左對齊的標題。 
- **副標題**-此類型會在第一行顯示靠左對齊的標題, 以及下一行中較小的左對齊副標題。
- **值 1** -此類型會呈現靠左對齊的標題, 並在同一行上顯示較淺顏色、靠右對齊的副標題。
- **值 2** -此類型會呈現靠右對齊的標題, 並在同一行上顯示較淺顏色、靠左對齊的副標題。

所有的預設資料表視圖資料格類型也支援圖形元素, 例如洩漏指標或核取記號。 

此外, 您可以定義**自訂**資料表視圖資料格類型, 並呈現_原型資料格_, 您可以在介面設計工具中或透過程式碼來建立。

Apple 對於使用資料表視圖儲存格有下列建議:

- **避免文字裁剪**-讓個別的文字行簡短, 讓它們不會被截斷。 截斷的單字或片語難以讓使用者在房間內進行剖析。
- **請考慮焦點的資料列狀態**-因為某個資料列變大, 在焦點時有更多的圓角, 您需要在所有狀態中測試儲存格的外觀。 影像或文字可能會被裁剪, 或在焦點狀態中看起來不正確。
- **謹慎使用可編輯的資料表**-移動或刪除資料表資料列在 tvOS 上比 iOS 更耗時。 如果這項功能會在您的 tvOS 應用程式中新增或干擾, 您必須謹慎決定。
- **適當地建立自訂資料格類型**, 而內建資料表視圖資料格類型很適合許多情況, 請考慮為非標準的資訊建立自訂資料格類型, 以提供更佳的控制, 並將資訊呈現給使用者.

<a name="Working-With-Table-Views" />

## <a name="working-with-table-views"></a>使用資料表視圖

在 tvOS 應用程式中使用資料表視圖的最簡單方式, 是在介面設計工具中建立和修改其外觀。

若要開始，請執行下列動作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中, 啟動新的 tvOS 應用程式專案, 然後選取 [ **tvOS**  >  **app**  > **單一視圖應用程式**], 然後按 [**下一步]** 按鈕: 

    [![](table-views-images/table02.png "選取單一視圖應用程式")](table-views-images/table02.png#lightbox)
1. 輸入應用程式的**名稱**, 然後按 **[下一步]** : 

    [![](table-views-images/table03.png "輸入應用程式的名稱")](table-views-images/table03.png#lightbox)
1. 請調整 [**專案名稱**] 和 [**方案名稱**] 或接受預設值, 然後按一下 [**建立**] 按鈕以建立新的方案: 

    [![](table-views-images/table04.png "[專案名稱] 和 [方案名稱]")](table-views-images/table04.png#lightbox)
1. 在  **Solution Pad**中, 按兩下`Main.storyboard`檔案以在 iOS 設計工具中開啟檔案: 

    [![](table-views-images/table05.png "主要的分鏡腳本檔案")](table-views-images/table05.png#lightbox)
1. 選取並刪除**預設的 View Controller**: 

    [![](table-views-images/table06.png "選取並刪除預設視圖控制器")](table-views-images/table06.png#lightbox)
1. 從 [**工具箱**] 中選取 [**分割視圖控制器**], 並將它拖曳至 [Design Surface]。
1. 根據預設, 您會在左側看到具有**流覽視圖控制器**的[分割視圖](~/ios/tvos/user-interface/split-views.md), 並在右側顯示 [**資料表視圖控制器**], 並在右手邊取得**視圖控制器**。 這是 Apple 在 tvOS 中使用資料表視圖的建議用法: 

    [![](table-views-images/table08.png "新增分割視圖")](table-views-images/table08.png#lightbox)
1. 您必須選取資料表視圖的每個部分, 並在 [**屬性] 瀏覽器**的 [ **Widget** ] 索引標籤中指派自訂**類別名稱**, 讓您可以C#稍後在程式碼中存取它。 例如,**資料表視圖控制器**: 

    [![](table-views-images/table09.png "指派類別名稱")](table-views-images/table09.png#lightbox)
1. 請確定您建立的是**資料表視圖控制器**、**資料表視圖**和任何**原型資料格**的自訂類別。 Visual Studio for Mac 會在建立自訂類別時, 將其加入至專案樹狀結構中: 

    [![](table-views-images/table10.png "專案樹狀結構中的自訂類別")](table-views-images/table10.png#lightbox)
1. 接下來, 選取 Design Surface 中的資料表視圖, 並視需要調整它的屬性。 例如,**原型資料格**的數目和**樣式**(一般或群組): 

    [![](table-views-images/table11.png "[Widget] 索引標籤")](table-views-images/table11.png#lightbox)
1. 針對每個**原型資料格**, 選取它, 然後在 [屬性] **Explorer**的 [ **Widget** ] 索引標籤中指派唯一**識別碼**。 此步驟_非常重要_, 因為您稍後會在填入資料表時需要此識別碼。 `AttrCell`例如: 

    [![](table-views-images/table12.png "[Widget] 索引標籤")](table-views-images/table12.png#lightbox)
1. 您也可以選擇透過 [**樣式**] 下拉式清單將資料格呈現為其中一個[預設資料表視圖資料格類型](#table-view-cell-types), 或將其設定為 [**自訂**], 並使用 [Design Surface], 從 [**工具箱**] 中拖曳其他 UI widget 來配置資料格: 

    [![](table-views-images/table13.png "資料格版面配置")](table-views-images/table13.png#lightbox)
1. 在 [**屬性] 瀏覽器**的 [ **Widget** ] 索引標籤中, 為原型資料格設計中的每個 UI 專案指派唯一C#的**名稱**, 讓您可以稍後在程式碼中存取它們: 

    [![](table-views-images/table14.png "指派名稱")](table-views-images/table14.png#lightbox)
1. 針對資料表視圖中的所有原型資料格, 重複上述步驟。
1. 接下來, 將自訂類別指派給您的 UI 設計的其餘部分、配置詳細資料檢視, 並將唯一**名稱**指派給詳細資料檢視中的每個 UI C#元素, 讓您也可以在中存取它們。 例如： 

    [![](table-views-images/table15.png "UI 版面配置")](table-views-images/table15.png#lightbox)
1. 將您的變更儲存至分鏡腳本。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中, 啟動新的 tvOS 應用程式專案, 然後選取 [ **tvOS**  > **單一視圖應用程式**], 並輸入您的應用程式名稱。 按一下 [確定] 按鈕以建立新的解決方案: 

    [![](table-views-images/table02-vs.png "選取單一視圖應用程式")](table-views-images/table02-vs.png#lightbox)
1. 在 **方案總管**中, 按兩下`Main.storyboard`檔案以在 iOS 設計工具中開啟檔案: 

    [![](table-views-images/table05-vs.png "主要的分鏡腳本檔案")](table-views-images/table05-vs.png#lightbox)
1. 選取並刪除**預設的 View Controller**: 

    [![](table-views-images/table06-vs.png "選取並刪除預設視圖控制器")](table-views-images/table06-vs.png#lightbox)
1. 從 **工具箱** 中選取 **分割視圖控制器**, 並將它拖曳至 Design Surface: 

    [![](table-views-images/table07-vs.png "分割視圖控制器")](table-views-images/table07-vs.png#lightbox)
1. 根據預設, 您會在左側看到具有**流覽視圖控制器**的[分割視圖](~/ios/tvos/user-interface/split-views.md), 並在右側顯示 [**資料表視圖控制器**], 並在右手邊取得**視圖控制器**。 這是 Apple 在 tvOS 中使用資料表視圖的建議用法: 

    [![](table-views-images/table08-vs.png "版面配置 UI")](table-views-images/table08-vs.png#lightbox)
1. 您必須選取資料表視圖的每個部分, 並在 [**屬性] 瀏覽器**的 [ **Widget** ] 索引標籤中指派自訂**類別名稱**, 讓您可以C#稍後在程式碼中存取它。 例如,**資料表視圖控制器**: 

    [![](table-views-images/table09-vs.png "[Widget] 索引標籤")](table-views-images/table09-vs.png#lightbox)
1. 請確定您建立的是**資料表視圖控制器**、**資料表視圖**和任何**原型資料格**的自訂類別。 Visual Studio for Mac 會在建立自訂類別時, 將其加入至專案樹狀結構中: 

    [![](table-views-images/table10-vs.png "專案樹狀結構中的自訂類別")](table-views-images/table10-vs.png#lightbox)
1. 接下來, 選取 Design Surface 中的資料表視圖, 並視需要調整它的屬性。 例如,**原型資料格**的數目和**樣式**(一般或群組): 

    [![](table-views-images/table11-vs.png "[Widget] 索引標籤")](table-views-images/table11-vs.png#lightbox)
1. 針對每個**原型資料格**, 選取它, 然後在 [屬性] **Explorer**的 [ **Widget** ] 索引標籤中指派唯一**識別碼**。 此步驟_非常重要_, 因為您稍後會在填入資料表時需要此識別碼。 `AttrCell`例如: 

    [![](table-views-images/table12-vs.png "指派識別碼")](table-views-images/table12-vs.png#lightbox)
1. 您也可以選擇透過 [**樣式**] 下拉式清單將資料格呈現為其中一個[預設資料表視圖資料格類型](#table-view-cell-types), 或將其設定為 [**自訂**], 並使用 [Design Surface], 從 [**工具箱**] 中拖曳其他 UI widget 來配置資料格: 

    [![](table-views-images/table13-vs.png "樣式下拉式清單")](table-views-images/table13-vs.png#lightbox)
1. 在 [**屬性] 瀏覽器**的 [ **Widget** ] 索引標籤中, 為原型資料格設計中的每個 UI 專案指派唯一C#的**名稱**, 讓您可以稍後在程式碼中存取它們: 

    [![](table-views-images/table14-vs.png "[Widget] 索引標籤")](table-views-images/table14-vs.png#lightbox)
1. 針對資料表視圖中的所有原型資料格, 重複上述步驟。
1. 接下來, 將自訂類別指派給您的 UI 設計的其餘部分、配置詳細資料檢視, 並將唯一**名稱**指派給詳細資料檢視中的每個 UI C#元素, 讓您也可以在中存取它們。 例如： 

    [![](table-views-images/table15.png "UI 版面配置")](table-views-images/table15.png#lightbox)
1. 將您的變更儲存至分鏡腳本。

-----

<a name="Designing-a-Data-Model" />

## <a name="designing-a-data-model"></a>設計資料模型

若要使用資料表視圖會更容易顯示的資訊, 以及輕鬆呈現詳細資訊 (當使用者選取或反白顯示資料表視圖中的資料列) 時, 請建立自訂類別或類別, 做為所呈現資訊的資料模型.

採用包含**城市**清單的旅遊預約應用程式範例, 其中每一個都包含使用者可以選取的唯一**景點**清單。 使用者可以將遊樂設施標示為我的*最愛*, 選取以取得遊樂設施的*路線*, 並將*航班預訂*給指定的城市。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要建立**遊樂設施**的資料模型, 請以滑鼠右鍵按一下**Solution Pad**中的專案名稱, 然後選取 [**加入** > **新**檔案 ...]。輸入`AttractionInformation`作為**名稱**, 然後按一下 [**新增**] 按鈕: 

[![](table-views-images/data01.png "在 [名稱] 中輸入 AttractionInformation")](table-views-images/data01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要建立**遊樂設施**的資料模型, 請以滑鼠右鍵按一下**方案總管**中的專案名稱, 然後選取 [**加入** > **新專案**]。選取 [**類別**] `AttractionInformation`並輸入作為**名稱**, 然後按一下 [**新增**] 按鈕: 

[![](table-views-images/data01-vs.png "選取 [類別], 然後輸入 AttractionInformation 作為 [名稱]")](table-views-images/data01-vs.png#lightbox)

-----

`AttractionInformation.cs`編輯檔案, 使其看起來如下所示:

```csharp
using System;
using Foundation;

namespace tvTable
{
    public class AttractionInformation : NSObject
    {
        #region Computed Properties
        public CityInformation City { get; set;}
        public string Name { get; set;}
        public string Description { get; set;}
        public string ImageName { get; set;}
        public bool IsFavorite { get; set;}
        public bool AddDirections { get; set;}
        #endregion

        #region Constructors
        public AttractionInformation (string name, string description, string imageName)
        {
            // Initialize
            this.Name = name;
            this.Description = description;
            this.ImageName = imageName;
        }
        #endregion
    }
}
```

這個類別會提供屬性, 以儲存指定**遊樂設施**的相關資訊。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

接下來, 再次以滑鼠右鍵按一下  **Solution Pad**中的專案名稱,  > 然後選取 **新增檔案 ...** 。輸入`CityInformation`作為**名稱**, 然後按一下 **新增** 按鈕: 

[![](table-views-images/data02.png "在 [名稱] 中輸入 CityInformation")](table-views-images/data02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

接下來, 再次以滑鼠右鍵按一下 **方案總管**中的專案名稱, 然後選取 **加入** > **新專案**。輸入`CityInformation`作為**名稱**, 然後按一下 **新增** 按鈕: 

[![](table-views-images/data02-vs.png "在 [名稱] 中輸入 CityInformation")](table-views-images/data02-vs.png#lightbox)

-----

`CityInformation.cs`編輯檔案, 使其看起來如下所示:

```csharp
using System;
using System.Collections.Generic;
using Foundation;

namespace tvTable
{
    public class CityInformation : NSObject
    {
        #region Computed Properties
        public string Name { get; set; }
        public List<AttractionInformation> Attractions { get; set;}
        public bool FlightBooked { get; set;}
        #endregion

        #region Constructors
        public CityInformation (string name)
        {
            // Initialize
            this.Name = name;
            this.Attractions = new List<AttractionInformation> ();
        }
        #endregion

        #region Public Methods
        public void AddAttraction (AttractionInformation attraction)
        {
            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }

        public void AddAttraction (string name, string description, string imageName)
        {
            // Create attraction
            var attraction = new AttractionInformation (name, description, imageName);

            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }
        #endregion
    }
}
```

這個類別會保存關於目的地**城市**的所有資訊、該城市的**景點**集合, 並提供兩個 helper 方法 (`AddAttraction`), 讓您更輕鬆地將景點新增至城市。

<a name="The-Table-Data-Source" />

## <a name="the-table-view-data-source"></a>資料表視圖資料來源

每個資料表視圖都需要一個資料`UITableViewDataSource`源 () 來提供資料表的資料, 並根據資料表視圖的需求產生必要的資料列。

在上述範例中, 以滑鼠右鍵按一下**方案總管**中的專案名稱, 選取 [**加入** > **新檔案 ...** ] 並加以`AttractionTableDatasource`呼叫, 然後按一下 [**新增**] 按鈕來建立。 接著, 編輯`AttractionTableDatasource.cs`檔案, 使其看起來如下所示:

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDatasource : UITableViewDataSource
    {
        #region Constants
        const string CellID = "AttrCell";
        #endregion

        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        public List<CityInformation> Cities { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDatasource (AttractionTableViewController controller)
        {
            // Initialize
            this.Controller = controller;
            this.Cities = new List<CityInformation> ();
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities ()
        {
            // Clear existing
            Cities.Clear ();

            // Define cities and attractions
            var Paris = new CityInformation ("Paris");
            Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
            Paris.AddAttraction ("Musée du Louvre", "is one of the world's largest museums and a historic monument in Paris, France.", "Louvre");
            Paris.AddAttraction ("Moulin Rouge", "French for 'Red Mill', is a cabaret in Paris, France.", "MoulinRouge");
            Paris.AddAttraction ("La Seine", "Is a 777-kilometre long river and an important commercial waterway within the Paris Basin.", "RiverSeine");
            Cities.Add (Paris);

            var SanFran = new CityInformation ("San Francisco");
            SanFran.AddAttraction ("Alcatraz Island", "Is located in the San Francisco Bay, 1.25 miles (2.01 km) offshore from San Francisco.", "Alcatraz");
            SanFran.AddAttraction ("Golden Gate Bridge", "Is a suspension bridge spanning the Golden Gate strait between San Francisco Bay and the Pacific Ocean", "GoldenGateBridge");
            SanFran.AddAttraction ("San Francisco", "Is the cultural, commercial, and financial center of Northern California.", "SanFrancisco");
            SanFran.AddAttraction ("Telegraph Hill", "Is primarily a residential area, much quieter than adjoining North Beach.", "TelegraphHill");
            Cities.Add (SanFran);

            var Houston = new CityInformation ("Houston");
            Houston.AddAttraction ("City Hall", "It was constructed in 1938-1939, and is located in Downtown Houston.", "CityHall");
            Houston.AddAttraction ("Houston", "Is the most populous city in Texas and the fourth most populous city in the US.", "Houston");
            Houston.AddAttraction ("Texas Longhorn", "Is a breed of cattle known for its characteristic horns, which can extend to over 6 ft.", "LonghornCattle");
            Houston.AddAttraction ("Saturn V Rocket", "was an American human-rated expendable rocket used by NASA between 1966 and 1973.", "Rocket");
            Cities.Add (Houston);
        }
        #endregion

        #region Override Methods
        public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Get cell
            var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

            // Populate cell
            cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

            // Return new cell
            return cell;
        }

        public override nint NumberOfSections (UITableView tableView)
        {
            // Return number of cities
            return Cities.Count;
        }

        public override nint RowsInSection (UITableView tableView, nint section)
        {
            // Return the number of attractions in the given city
            return Cities [(int)section].Attractions.Count;
        }

        public override string TitleForHeader (UITableView tableView, nint section)
        {
            // Get the name of the current city
            return Cities [(int)section].Name;
        }
        #endregion
    }
}
```

讓我們詳細探討類別的幾個區段。

首先, 我們定義了常數來保存原型資料格的唯一識別碼 (這是在上述介面設計工具中指派的相同識別碼), 並將快捷方式新增至資料表視圖控制器, 並為我們的資料建立儲存體:

```csharp
const string CellID = "AttrCell";
public AttractionTableViewController Controller { get; set;}
public List<CityInformation> Cities { get; set;}
```

接下來, 我們會儲存資料表視圖控制器, 然後建立並填入我們在建立類別時所定義的資料來源 (使用上述的資料模型):

```csharp
public AttractionTableDatasource (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
    this.Cities = new List<CityInformation> ();
    PopulateCities ();
}
```

例如, `PopulateCities`方法只會在記憶體中建立資料模型物件, 不過, 您可以輕鬆地從實際應用程式中的資料庫或 web 服務讀取這些物件:

```csharp
public void PopulateCities ()
{
    // Clear existing
    Cities.Clear ();

    // Define cities and attractions
    var Paris = new CityInformation ("Paris");
    Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
    ...
}
```

`NumberOfSections`方法會傳回資料表中的區段數目:

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    // Return number of cities
    return Cities.Count;
}
```

針對**純**樣式的資料表視圖, 一律會傳回1。

`RowsInSection`方法會傳回目前區段中的資料列數目:

```csharp
public override nint RowsInSection (UITableView tableView, nint section)
{
    // Return the number of attractions in the given city
    return Cities [(int)section].Attractions.Count;
}
```

同樣地, 針對**純**資料表視圖, 會傳回資料來源中的總專案數。

`TitleForHeader`方法會傳回指定區段的標題:

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    // Get the name of the current city
    return Cities [(int)section].Name;
}
```

如果是**純**資料表檢視類型, 請將標題保留空白`""`()。

最後, 當資料表視圖要求時, 使用`GetCell`方法來建立並填入原型資料格: 

```csharp
public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Get cell
    var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

    // Populate cell
    cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

    // Return new cell
    return cell;
}
```

如需使用的`UITableViewDatasource`詳細資訊, 請參閱 Apple 的[UITableViewDatasource](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40006941)檔。

<a name="The-Table-View-Delegate" />

## <a name="the-table-view-delegate"></a>資料表視圖委派

每個資料表視圖都需要委派`UITableViewDelegate`() 來回應使用者互動或資料表上的其他系統事件。

在上述範例中, 以滑鼠右鍵按一下**方案總管**中的專案名稱, 選取 [**加入** > **新檔案 ...** ] 並加以`AttractionTableDelegate`呼叫, 然後按一下 [**新增**] 按鈕來建立。 接著, 編輯`AttractionTableDelegate.cs`檔案, 使其看起來如下所示:

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDelegate : UITableViewDelegate
    {
        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDelegate (AttractionTableViewController controller)
        {
            // Initializw
            this.Controller = controller;
        }
        #endregion

        #region Override Methods
        public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
            attraction.IsFavorite = (!attraction.IsFavorite);

            // Update UI
            Controller.TableView.ReloadData ();
        }

        public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Inform caller of highlight change
            RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
            return true;
        }
        #endregion

        #region Events
        public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
        public event AttractionHighlightedDelegate AttractionHighlighted;

        internal void RaiseAttractionHighlighted (AttractionInformation attraction)
        {
            // Inform caller
            if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
        }
        #endregion
    }
}
```

讓我們來看一下此類別的幾個區段, 詳細資訊請參閱。

首先, 我們會在建立類別時, 建立資料表視圖控制器的快捷方式:

```csharp
public AttractionTableViewController Controller { get; set;}
...

public AttractionTableDelegate (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
}
```

然後, 當選取資料列 (使用者按一下 Apple 遠端的觸控介面) 時, 我們想要將選取的資料列所代表的**遊樂設施**標示為我的最愛:

```csharp
public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
    attraction.IsFavorite = (!attraction.IsFavorite);

    // Update UI
    Controller.TableView.ReloadData ();
}
```

接下來, 當使用者反白顯示資料列時 (藉由將焦點放在使用 Apple 遠端觸控介面), 我們想要在分割視圖控制器的 [詳細資料] 區段中, 呈現該資料列所代表之**遊樂設施**的詳細資訊:

```csharp
public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Inform caller of highlight change
    RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
    return true;
}
...

public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
public event AttractionHighlightedDelegate AttractionHighlighted;

internal void RaiseAttractionHighlighted (AttractionInformation attraction)
{
    // Inform caller
    if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
}
```

會針對即將在資料表視圖中取得焦點的每個資料列呼叫方法。`CanFocusRow` 如果資料列可以取得焦點, 則傳回, `false`否則傳回。 `true` 在此範例中, 我們建立了一個自訂`AttractionHighlighted`事件, 它會在每個資料列收到焦點時引發。

如需使用的`UITableViewDelegate`詳細資訊, 請參閱 Apple 的[UITableViewDelegate](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006942)檔。

<a name="The-Table-View-Cell" />

## <a name="the-table-view-cell"></a>資料表視圖儲存格

針對您在介面設計工具中加入至資料表的每個原型資料格, 您也會建立資料表視圖資料格 (`UITableViewCell`) 的自訂實例, 讓您在建立新的資料格 (資料列) 時將其填入。

針對範例應用程式, 按兩下`AttractionTableCell.cs`檔案將其開啟以進行編輯, 使其看起來如下所示:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableCell : UITableViewCell
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public AttractionTableCell (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Trap all errors
            try {
                Title.Text = Attraction.Name;
                Favorite.Hidden = (!Attraction.IsFavorite);
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion
    }
}
```

這個類別會提供遊樂設施資料模型物件 (`AttractionInformation`如上述所定義) 的儲存體, 並顯示在給定的資料列中:

```csharp
private AttractionInformation _attraction = null;
...

public AttractionInformation Attraction {
    get { return _attraction; }
    set {
        _attraction = value;
        UpdateUI ();
    }
}
```

`UpdateUI`方法會視需要填入 UI widget (已在介面設計工具中加入至資料格的原型):

```csharp
private void UpdateUI ()
{
    // Trap all errors
    try {
        Title.Text = Attraction.Name;
        Favorite.Hidden = (!Attraction.IsFavorite);
    } catch {
        // Since the UI might not be fully loaded, ignore
        // all errors at this point
    }
}
```

如需使用的`UITableViewCell`詳細資訊, 請參閱 Apple 的[UITableViewCell](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewCell_Class/index.html#//apple_ref/doc/uid/TP40006938)檔。

<a name="The-Table-View-Controller" />

## <a name="the-table-view-controller"></a>資料表視圖控制器

資料表視圖控制器 (`UITableViewController`) 會管理已透過介面設計工具加入至分鏡腳本的資料表視圖。

針對範例應用程式, 按兩下`AttractionTableViewController.cs`檔案將其開啟以進行編輯, 使其看起來如下所示:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableViewController : UITableViewController
    {
        #region Computed Properties
        public AttractionTableDatasource Datasource {
            get { return TableView.DataSource as AttractionTableDatasource; }
        }

        public AttractionTableDelegate TableDelegate {
            get { return TableView.Delegate as AttractionTableDelegate; }
        }
        #endregion

        #region Constructors
        public AttractionTableViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Setup table
            TableView.DataSource = new AttractionTableDatasource (this);
            TableView.Delegate = new AttractionTableDelegate (this);
            TableView.ReloadData ();
        }
        #endregion
    }
}
```

讓我們仔細看一下這個類別。 首先, 我們建立了快捷方式, 讓您更輕鬆地存取資料表視圖`DataSource`的`TableDelegate`和。 我們稍後會使用這些資訊, 在分割視圖左側的資料表視圖和右邊的詳細資料檢視之間進行通訊。

最後, 當資料表視圖載入至記憶體時, 我們會建立`AttractionTableDatasource`和`AttractionTableDelegate`的實例 (兩者都是在上面建立), 並將它們附加至資料表視圖。

如需使用的`UITableViewController`詳細資訊, 請參閱 Apple 的[UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)檔。

<a name="Pulling-it-All-Together" />

## <a name="pulling-it-all-together"></a>一起提取

如本檔開頭所述, 資料表視圖通常會顯示在[分割視圖](~/ios/tvos/user-interface/split-views.md)的一側做為導覽, 而選取專案的詳細資料則會顯示在相反的一邊。 例如： 

[![](table-views-images/intro01.png "範例應用程式執行")](table-views-images/intro01.png#lightbox)

由於這是 tvOS 中的標準模式, 因此讓我們來看一下將所有專案結合在一起的最後步驟, 並讓分割視圖的左邊和右邊彼此互動。

<a name="The-Detail-View" />

### <a name="the-detail-view"></a>詳細資料檢視

在上述的旅遊應用程式範例中, 自訂類別 (`AttractionViewController`) 是針對分割視圖右側所顯示的標準視圖控制器定義, 做為詳細資料檢視:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionViewController : UIViewController
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }

        public MasterSplitView SplitView { get; set;}
        #endregion

        #region Constructors
        public AttractionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void UpdateUI ()
        {
            // Trap all errors
            try {
                City.Text = Attraction.City.Name;
                Title.Text = Attraction.Name;
                SubTitle.Text = Attraction.Description;

                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
                IsFavorite.Hidden = (!Attraction.IsFavorite);
                IsDirections.Hidden = (!Attraction.AddDirections);
                BackgroundImage.Image = UIImage.FromBundle (Attraction.ImageName);
                AttractionImage.Image = BackgroundImage.Image;
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Ensure the UI Updates
            UpdateUI ();
        }
        #endregion

        #region Actions
        partial void BookFlight (NSObject sender)
        {
            // Ask user to book flight
            AlertViewController.PresentOKCancelAlert ("Book Flight",
                                                      string.Format ("Would you like to book a flight to {0}?", Attraction.City.Name),
                                                      this,
                                                      (ok) => {
                Attraction.City.FlightBooked = ok;
                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
            });
        }

        partial void GetDirections (NSObject sender)
        {
            // Ask user to add directions
            AlertViewController.PresentOKCancelAlert ("Add Directions",
                                                     string.Format ("Would you like to add directions to {0} to you itinerary?", Attraction.Name),
                                                     this,
                                                     (ok) => {
                                                         Attraction.AddDirections = ok;
                                                         IsDirections.Hidden = (!Attraction.AddDirections);
                                                     });
        }

        partial void MarkFavorite (NSObject sender)
        {
            // Flip favorite state
            Attraction.IsFavorite = (!Attraction.IsFavorite);
            IsFavorite.Hidden = (!Attraction.IsFavorite);

            // Reload table
            SplitView.Master.TableController.TableView.ReloadData ();
        }
        #endregion
    }
}
```

在這裡, 我們提供了以屬性`AttractionInformation`顯示的遊樂設施 (), 並建立了`UpdateUI`一個方法, 可在介面設計工具中填入加入至視圖的 UI widget。

我們也將快捷方式定義回分割視圖控制器 (`SplitView`), 以用來將變更傳回到資料表視圖 (`AcctractionTableView`)。

最後, 自訂動作 (事件) 已加入至介面`UIButton`設計工具中建立的三個實例, 可讓使用者將遊樂設施標示為我的_最愛_、取得遊樂設施的_路線_, 並將_航班預訂_給指定的鹽湖城.

<a name="The-Navigation-View-Controller" />

### <a name="the-navigation-view-controller"></a>流覽視圖控制器

因為資料表視圖控制器是在分割視圖左側的導覽視圖控制器中進行嵌套, 所以導覽視圖控制器已在介面設計工具中指派自訂類別`MasterNavigationController`(), 並定義如下:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterNavigationController : UINavigationController
    {
        #region Computed Properties
        public MasterSplitView SplitView { get; set;}
        public AttractionTableViewController TableController {
            get { return TopViewController as AttractionTableViewController; }
        }
        #endregion

        #region Constructors
        public MasterNavigationController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

同樣地, 這個類別只會定義幾個快捷方式, 讓您更輕鬆地在分割視圖控制器的兩邊進行通訊:

- `SplitView`-這是流覽視圖控制器所屬之分割視圖`MainSpiltViewController`控制器 () 的連結。
- `TableController`-取得顯示為導覽視圖控制器`AttractionTableViewController`中頂端視圖的資料表視圖控制器 ()。

<a name="The-Split-View-Controller" />

### <a name="the-split-view-controller"></a>分割視圖控制器

因為分割視圖控制器是我們應用程式的基礎, 所以我們在介面設計工具中`MasterSplitViewController`為它建立了自訂類別 (), 並將其定義如下:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterSplitView : UISplitViewController
    {
        #region Computed Properties
        public AttractionViewController Details {
            get { return ViewControllers [1] as AttractionViewController; }
        }

        public MasterNavigationController Master {
            get { return ViewControllers [0] as MasterNavigationController; }
        }
        #endregion

        #region Constructors
        public MasterSplitView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            Master.SplitView = this;
            Details.SplitView = this;

            // Wire-up events
            Master.TableController.TableDelegate.AttractionHighlighted += (attraction) => {
                // Display new attraction
                Details.Attraction = attraction;
            };
        }
        #endregion
    }
}
```

首先, 我們會建立分割視圖 (`AttractionViewController`) 和**主要**端 (`MasterNavigationController`) 之**詳細資料**端的快捷方式。 同樣地, 這可讓您更輕鬆地在這兩個邊之間進行通訊。

接下來, 當分割視圖載入至記憶體時, 我們會將分割視圖控制器附加至分割視圖的兩邊, 並藉由在的**詳細資料**端顯示新的遊樂設施,`AttractionHighlighted`以回應顯示資料表視圖 () 中游樂設施的使用者分割視圖。

請參閱[tvTables](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-tvtable)範例應用程式, 以取得分割視圖內部資料表視圖的完整執行。

## <a name="table-views-in-detail"></a>資料表流覽詳細資料

由於 tvOS 是以 iOS 為基礎, 因此會以類似的方式設計資料表視圖和資料表視圖控制器。 如需在 Xamarin 應用程式中使用資料表視圖的詳細資訊, 請參閱我們[的 IOS 使用資料表和儲存格](~/ios/user-interface/controls/tables/index.md)檔。

<a name="Summary" />

## <a name="summary"></a>總結

本文涵蓋在 tvOS 應用程式內設計和使用資料表的視圖。 和已提供一個範例, 說明如何使用分割視圖內的資料表視圖, 這是 tvOS 應用程式中資料表視圖的一般用法。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
