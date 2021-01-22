---
title: 在 Xamarin 中使用 tvOS 資料表視圖
description: 本文說明如何在 tvOS 應用程式內設計及使用資料表視圖和資料表視圖控制器。
ms.prod: xamarin
ms.assetid: D8F80FA9-6400-4DB7-AFC9-A28A54AD04E8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: a3ae6b794ce94ddc5dffdb13dec53eef52b23800
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697614"
---
# <a name="working-with-tvos-table-views-in-xamarin"></a>在 Xamarin 中使用 tvOS 資料表視圖

_本文說明如何在 tvOS 應用程式內設計及使用資料表視圖和資料表視圖控制器。_

在 tvOS 中，資料表視圖會顯示為滾動列的單一資料行，可選擇性地組織成群組或區段。 當您需要以清楚易懂的方式，有效率地向使用者顯示大量資料時，應該使用資料表視圖。

資料表視圖通常會顯示在 [分割視圖](~/ios/tvos/user-interface/split-views.md) 的一端做為導覽，並顯示所選取專案的詳細資料，在相反的側邊：

[![範例資料表視圖](table-views-images/intro01.png)](table-views-images/intro01.png#lightbox)

<a name="About-Table-Views"></a>

## <a name="about-table-views"></a>關於資料表視圖

`UITableView`會將可滾動資料列的單一資料行顯示為階層式資訊清單，可選擇性地組織成群組或區段： 

[![選取的專案](table-views-images/table01.png)](table-views-images/table01.png#lightbox)

Apple 針對使用資料表有下列建議：

- 請 **留意寬度**-請嘗試在您的資料表寬度中找出正確的餘額。 如果資料表太寬，可能很難從某個距離進行掃描，而且可以離開可用的內容區域。 如果資料表太窄，它可能會造成資訊遭到截斷或換行，這可能會讓使用者難以從空間讀取。
- **快速顯示資料表內容** -針對大型資料清單，延遲載入內容，並在資料表呈現給使用者時立即開始顯示資訊。 如果資料表花了很長的時間載入，則使用者可能會失去您應用程式的興趣，或將它視為已鎖定。
- **通知使用者長時間的載入** -如果長資料表載入時間無法避免，請提供 [進度列或活動指標](~/ios/tvos/user-interface/progress-indicators.md) ，讓他們知道應用程式未鎖定。

<a name="Table-Cell-Types"></a>

## <a name="table-view-cell-types"></a>資料表視圖資料格類型

`UITableViewCell`用來代表資料表視圖中的個別資料列。 Apple 已定義數個預設資料表資料格類型：

- **預設值** -此類型會在資料格的左側顯示選項影像，右邊則是靠左對齊的標題。 
- **副標題** -此類型會在第一行顯示靠左對齊的標題，並在下一行顯示較小且靠左對齊的副標題。
- **值 1** -此類型會呈現靠左對齊的標題，並在同一行上顯示較淺顏色、靠右對齊的副標題。
- **值 2** -此類型會顯示在同一行上具有較淺彩色、靠左對齊的子標題之靠右對齊的標題。

所有的預設資料表視圖儲存格類型也支援圖形元素，例如洩漏指標或核取記號。 

此外，您可以定義 **自訂** 的資料表視圖資料格類型，並呈現 _原型資料格_，您可以在介面設計工具中或透過程式碼建立該資料格。

Apple 對於使用資料表視圖儲存格有下列建議：

- **避免文字裁剪** -將一行文字保持簡短，讓它們不會被截斷。 截斷的單字或片語很難讓使用者從房間內進行剖析。
- **請考慮焦點的資料列狀態** ，因為資料列變得更大，當焦點時有更大的圓角，您需要測試儲存格在所有狀態下的外觀。 影像或文字可能會遭到裁剪，或在焦點狀態中看起來不正確。
- 在 tvOS 上 **使用可編輯的資料表謹慎** 移動或刪除資料表資料列比 iOS 更費時。 如果這項功能會在您的 tvOS 應用程式中新增或干擾，您必須仔細決定。
- 在適當的情況下 **建立自訂資料格類型**-雖然內建資料表視圖資料格類型很適合許多情況，但請考慮為非標準的資訊建立自訂資料格類型，以提供更好的控制，並更妥善地向使用者呈現資訊。

<a name="Working-With-Table-Views"></a>

## <a name="working-with-table-views"></a>使用資料表視圖

在 tvOS 應用程式中使用資料表視圖的最簡單方式，就是在介面設計工具中建立和修改它們的外觀。

若要開始，請執行下列動作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中，啟動新的 tvOS 應用程式專案，並選取 **tvOS**  >  **應用程式**  >  **單一視圖應用程式**，然後按 [**下一步]** 按鈕： 

    [![選取單一視圖應用程式](table-views-images/table02.png)](table-views-images/table02.png#lightbox)
1. 輸入應用程式的 **名稱** ，然後按 **[下一步]**： 

    [![輸入應用程式的名稱](table-views-images/table03.png)](table-views-images/table03.png#lightbox)
1. 調整 **專案名稱** 和 **方案名稱** ，或接受預設值，然後按一下 [ **建立** ] 按鈕以建立新的方案： 

    [![專案名稱和方案名稱](table-views-images/table04.png)](table-views-images/table04.png#lightbox)
1. 在 [ **Solution Pad** 中，按兩下該檔案， `Main.storyboard` 以在 iOS 設計工具中開啟它： 

    [![主要的分鏡腳本檔案](table-views-images/table05.png)](table-views-images/table05.png#lightbox)
1. 選取並刪除 **預設的 View Controller**： 

    [![選取和刪除預設的視圖控制器](table-views-images/table06.png)](table-views-images/table06.png#lightbox)
1. 從 [**工具箱**] 選取 [**分割視圖控制器**]，並將它拖曳至 Design Surface。
1. 根據預設，您將會在左側顯示具有 **導覽視圖控制器** 和 **資料表視圖控制器** 的 [分割視圖](~/ios/tvos/user-interface/split-views.md)，並在右側 **顯示視圖控制器**。 這是 Apple 在 tvOS 中使用資料表視圖的建議用法： 

    [![新增分割視圖](table-views-images/table08.png)](table-views-images/table08.png#lightbox)
1. 您必須選取資料表視圖的每個部分，並在 **屬性瀏覽器** 的 [ **Widget** ] 索引標籤中，為它指派自訂 **類別名稱**，讓您稍後可以在 c # 程式碼中存取它。 例如， **資料表視圖控制器**： 

    [![指派類別名稱](table-views-images/table09.png)](table-views-images/table09.png#lightbox)
1. 確定您為 **資料表視圖控制器**、 **資料表視圖** 和任何 **原型資料格** 建立了自訂類別。 Visual Studio for Mac 會在建立自訂類別時，將自訂類別加入至專案樹狀結構中： 

    [![專案樹狀結構中的自訂類別](table-views-images/table10.png)](table-views-images/table10.png#lightbox)
1. 接下來，選取 Design Surface 中的資料表視圖，並視需要調整其屬性。 例如， **原型資料格** 的數目和 **樣式** (單純或分組) ： 

    [![[Widget] 索引標籤](table-views-images/table11.png)](table-views-images/table11.png#lightbox)
1. 針對每個 **原型資料格**，請選取它，並在 **屬性瀏覽器** 的 [ **Widget** ] 索引標籤中指派唯一 **識別碼**。 這個步驟 _非常重要_ ，因為稍後當您填入資料表時，將需要此識別碼。 例如：`AttrCell`： 

    [![[Widget] 索引標籤](table-views-images/table12.png)](table-views-images/table12.png#lightbox)
1. 您也可以選擇透過 [**樣式**] 下拉式清單將儲存格呈現為其中一個 [預設資料表視圖資料格類型](#table-view-cell-types)，或將它設定為 [**自訂**]，然後使用 Design Surface 從 [**工具箱**] 拖曳其他 UI widget 來配置資料格： 

    [![資料格版面配置](table-views-images/table13.png)](table-views-images/table13.png#lightbox)
1. 在 **屬性瀏覽器** 的 [ **Widget** ] 索引標籤中，為原型資料格設計中的每個 UI 專案指派唯一的 **名稱**，讓您稍後可以在 c # 程式碼中存取這些專案： 

    [![指派名稱](table-views-images/table14.png)](table-views-images/table14.png#lightbox)
1. 針對資料表視圖中的所有原型資料格重複上述步驟。
1. 接下來，將自訂類別指派給您的其他 UI 設計、配置詳細資料檢視，並將唯一 **名稱** 指派給詳細資料檢視中的每個 UI 專案，讓您也可以在 c # 中加以存取。 例如： 

    [![UI 版面配置](table-views-images/table15.png)](table-views-images/table15.png#lightbox)
1. 將您的變更儲存至腳本。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中，啟動新的 tvOS 應用程式專案，並選取 [ **tvOS**  >  **單一視圖應用程式**]，然後輸入應用程式的名稱。 按一下 [ **確定** ] 按鈕以建立新的方案： 

    [![選取單一視圖應用程式](table-views-images/table02-vs.png)](table-views-images/table02-vs.png#lightbox)
1. 在 [ **方案總管** 中，按兩下該檔案， `Main.storyboard` 以在 iOS 設計工具中開啟它： 

    [![主要的分鏡腳本檔案](table-views-images/table05-vs.png)](table-views-images/table05-vs.png#lightbox)
1. 選取並刪除 **預設的 View Controller**： 

    [![選取和刪除預設的視圖控制器](table-views-images/table06-vs.png)](table-views-images/table06-vs.png#lightbox)
1. 從 [**工具箱**] 中選取 [**分割視圖控制器**]，並將它拖曳至 Design Surface： 

    [![分割視圖控制器](table-views-images/table07-vs.png)](table-views-images/table07-vs.png#lightbox)
1. 根據預設，您將會在左側顯示具有 **導覽視圖控制器** 和 **資料表視圖控制器** 的 [分割視圖](~/ios/tvos/user-interface/split-views.md)，並在右側 **顯示視圖控制器**。 這是 Apple 在 tvOS 中使用資料表視圖的建議用法： 

    [![版面配置 UI](table-views-images/table08-vs.png)](table-views-images/table08-vs.png#lightbox)
1. 您必須選取資料表視圖的每個部分，並在 **屬性瀏覽器** 的 [ **Widget** ] 索引標籤中，為它指派自訂 **類別名稱**，讓您稍後可以在 c # 程式碼中存取它。 例如， **資料表視圖控制器**： 

    [![屬性瀏覽器的 [Widget] 索引標籤，您可以在其中指派類別名稱。](table-views-images/table09-vs.png)](table-views-images/table09-vs.png#lightbox)
1. 確定您為 **資料表視圖控制器**、 **資料表視圖** 和任何 **原型資料格** 建立了自訂類別。 Visual Studio for Mac 會在建立自訂類別時，將自訂類別加入至專案樹狀結構中： 

    [![專案樹狀結構中的自訂類別](table-views-images/table10-vs.png)](table-views-images/table10-vs.png#lightbox)
1. 接下來，選取 Design Surface 中的資料表視圖，並視需要調整其屬性。 例如， **原型資料格** 的數目和 **樣式** (單純或分組) ： 

    [![[Widget] 索引標籤，您可以在此處視需要變更屬性。](table-views-images/table11-vs.png)](table-views-images/table11-vs.png#lightbox)
1. 針對每個 **原型資料格**，請選取它，並在 **屬性瀏覽器** 的 [ **Widget** ] 索引標籤中指派唯一 **識別碼**。 這個步驟 _非常重要_ ，因為稍後當您填入資料表時，將需要此識別碼。 例如：`AttrCell`： 

    [![指派識別碼](table-views-images/table12-vs.png)](table-views-images/table12-vs.png#lightbox)
1. 您也可以選擇透過 [**樣式**] 下拉式清單將儲存格呈現為其中一個 [預設資料表視圖資料格類型](#table-view-cell-types)，或將它設定為 [**自訂**]，然後使用 Design Surface 從 [**工具箱**] 拖曳其他 UI widget 來配置資料格： 

    [![樣式下拉式清單](table-views-images/table13-vs.png)](table-views-images/table13-vs.png#lightbox)
1. 在 **屬性瀏覽器** 的 [ **Widget** ] 索引標籤中，為原型資料格設計中的每個 UI 專案指派唯一的 **名稱**，讓您稍後可以在 c # 程式碼中存取這些專案： 

    [![[Widget] 索引標籤，您可以在其中為每個 U I 元素指派名稱。](table-views-images/table14-vs.png)](table-views-images/table14-vs.png#lightbox)
1. 針對資料表視圖中的所有原型資料格重複上述步驟。
1. 接下來，將自訂類別指派給您的其他 UI 設計、配置詳細資料檢視，並將唯一 **名稱** 指派給詳細資料檢視中的每個 UI 專案，讓您也可以在 c # 中加以存取。 例如： 

    [![UI 版面配置](table-views-images/table15.png)](table-views-images/table15.png#lightbox)
1. 將您的變更儲存至腳本。

-----

<a name="Designing-a-Data-Model"></a>

## <a name="designing-a-data-model"></a>設計資料模型

若要使用資料表視圖會更容易顯示的資訊，以及簡化詳細資訊的呈現 (當使用者選取或反白顯示資料表視圖) 中的資料列時，請建立自訂類別，作為所呈現資訊的資料模型。

採用包含城市清單的旅遊預約應用程式範例，其中每個 **城市** 都包含使用者可選取的唯一 **景點** 清單。 使用者將可以將遊樂設施標示為我的 *最愛*，並選取以取得遊樂設施的 *指示* ，並將 *航班預訂* 至指定的城市。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

若要建立 **遊樂設施** 的資料模型，請在 **Solution Pad** 中的專案名稱上按一下滑鼠右鍵，然後選取 [**加入**  >  **新** 檔案]。輸入 `AttractionInformation` **名稱**，然後按一下 [**新增**] 按鈕： 

[![輸入 AttractionInformation 作為名稱](table-views-images/data01.png)](table-views-images/data01.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要建立 **遊樂設施** 的資料模型，請在 **方案總管** 中的專案名稱上按一下滑鼠右鍵，然後選取 [**加入**  >  **新專案**]。選取 [**類別**] 並輸入 `AttractionInformation` **名稱**，然後按一下 [**新增**] 按鈕： 

[![選取 [類別]，然後輸入 AttractionInformation 做為名稱](table-views-images/data01-vs.png)](table-views-images/data01-vs.png#lightbox)

-----

編輯檔案 `AttractionInformation.cs` ，使其看起來如下所示：

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

這個類別會提供屬性，以儲存指定 **遊樂設施** 的相關資訊。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

接下來，以滑鼠右鍵按一下 **Solution Pad** 中的專案名稱，**然後選取 [**  >  **新增檔案 ...**]。輸入 `CityInformation` **名稱**，然後按一下 [**新增**] 按鈕： 

[![輸入 CityInformation 作為名稱](table-views-images/data02.png)](table-views-images/data02.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

接下來，以滑鼠右鍵按一下 **方案總管** 中的專案名稱，然後選取 [**加入**  >  **新專案**]。輸入 `CityInformation` **名稱**，然後按一下 [**新增**] 按鈕： 

[![輸入 CityInformation 作為名稱](table-views-images/data02-vs.png)](table-views-images/data02-vs.png#lightbox)

-----

編輯檔案 `CityInformation.cs` ，使其看起來如下所示：

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

此類別會保存目的地 **城市** 的所有資訊、該城市的 **景點** 集合，並提供兩個 helper 方法 () ， `AddAttraction` 讓您更輕鬆地將景點新增到城市。

<a name="The-Table-Data-Source"></a>

## <a name="the-table-view-data-source"></a>資料表視圖資料來源

每個資料表視圖都需要資料來源 (`UITableViewDataSource`) 提供資料表的資料，並視資料表視圖的要求產生必要的資料列。

針對上述範例，以滑鼠右鍵按一下 **方案總管** 中的專案名稱，選取 [**加入**  >  **新** 檔案]，然後呼叫它， `AttractionTableDatasource` 然後按一下 [**新增**] 按鈕以建立。 接著，編輯檔案 `AttractionTableDatasource.cs` ，使其看起來如下所示：

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

讓我們詳細看一下類別的一些區段。

首先，我們定義了一個常數來保存原型資料格的唯一識別碼 (這是在) 的介面設計師中指派的相同識別碼，將快捷方式新增回資料表視圖控制器，並為我們的資料建立儲存體：

```csharp
const string CellID = "AttrCell";
public AttractionTableViewController Controller { get; set;}
public List<CityInformation> Cities { get; set;}
```

接下來，我們會儲存資料表視圖控制器，然後使用) 在建立類別時定義的資料模型，建立並填入資料來源 (：

```csharp
public AttractionTableDatasource (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
    this.Cities = new List<CityInformation> ();
    PopulateCities ();
}
```

例如， `PopulateCities` 方法只會在記憶體中建立資料模型物件，不過，您可以在實際的應用程式中輕鬆地從資料庫或 web 服務讀取這些物件：

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

方法會傳回 `NumberOfSections` 資料表中的區段數目：

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    // Return number of cities
    return Cities.Count;
}
```

針對 **純** 樣式的資料表視圖，一律會傳回1。

方法會傳回 `RowsInSection` 目前區段中的資料列數目：

```csharp
public override nint RowsInSection (UITableView tableView, nint section)
{
    // Return the number of attractions in the given city
    return Cities [(int)section].Attractions.Count;
}
```

同樣地，若是 **純** 資料表視圖，則會傳回資料來源中的總專案數。

`TitleForHeader`方法會傳回指定區段的標題：

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    // Get the name of the current city
    return Cities [(int)section].Name;
}
```

若為 **純** 資料表檢視類型，請將標題保留為空白 (`""`) 。

最後，當資料表視圖要求時，請使用方法來建立並填入原型資料格 `GetCell` ： 

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

如需有關使用的詳細資訊 `UITableViewDatasource` ，請參閱 Apple 的 [UITableViewDatasource](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40006941) 檔。

<a name="The-Table-View-Delegate"></a>

## <a name="the-table-view-delegate"></a>資料表視圖委派

每個資料表視圖都需要委派 (`UITableViewDelegate`) 來回應使用者互動或資料表上的其他系統事件。

針對上述範例，以滑鼠右鍵按一下 **方案總管** 中的專案名稱，選取 [**加入**  >  **新** 檔案]，然後呼叫它， `AttractionTableDelegate` 然後按一下 [**新增**] 按鈕以建立。 接著，編輯檔案 `AttractionTableDelegate.cs` ，使其看起來如下所示：

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

讓我們來看看這個類別的幾個部分，詳細資訊請參閱。

首先，我們會在建立類別時建立資料表視圖控制器的快捷方式：

```csharp
public AttractionTableViewController Controller { get; set;}
...

public AttractionTableDelegate (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
}
```

然後，當選取資料列時 (使用者按一下 Apple 遠端) 的觸控介面，我們想要將選取的資料列所代表的 **遊樂設施** 標示為我的最愛：

```csharp
public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
    attraction.IsFavorite = (!attraction.IsFavorite);

    // Update UI
    Controller.TableView.ReloadData ();
}
```

接下來，當使用者藉由使用 Apple 遠端觸控介面讓焦點放在 (時，) 我們想要在分割視圖控制器的 [詳細資料] 區段中，顯示該資料列所代表的 **遊樂設施** 詳細資料：

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

`CanFocusRow`針對即將在資料表視圖中取得焦點的每個資料列，會呼叫方法。 `true`如果資料列可以取得焦點，則傳回，否則傳回 `false` 。 在此範例中，我們建立了自訂 `AttractionHighlighted` 事件，其會在每個資料列收到焦點時引發。

如需有關使用的詳細資訊 `UITableViewDelegate` ，請參閱 Apple 的 [UITableViewDelegate](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006942) 檔。

<a name="The-Table-View-Cell"></a>

## <a name="the-table-view-cell"></a>資料表視圖儲存格

針對您在介面設計工具中加入至資料表視圖的每個原型資料格，您也會建立 [資料表視圖] 儲存格的自訂實例 (`UITableViewCell`) ，讓您在建立資料列) 時，將新資料格填入 (資料列。

針對範例應用程式，按兩下檔案 `AttractionTableCell.cs` 以開啟它進行編輯，使其看起來如下所示：

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

此類別提供遊樂設施資料模型物件的儲存 (`AttractionInformation` 如上面所定義) 顯示在指定的資料列中：

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

`UpdateUI`方法會在介面設計工具中填入加入至儲存格原型 (的 UI widget) 視需要：

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

如需有關使用的詳細資訊 `UITableViewCell` ，請參閱 Apple 的 [UITableViewCell](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewCell_Class/index.html#//apple_ref/doc/uid/TP40006938) 檔。

<a name="The-Table-View-Controller"></a>

## <a name="the-table-view-controller"></a>表格視圖控制器

資料表視圖控制器 (`UITableViewController`) 管理透過介面設計工具加入至腳本的資料表視圖。

針對範例應用程式，按兩下檔案 `AttractionTableViewController.cs` 以開啟它進行編輯，使其看起來如下所示：

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

讓我們仔細看看這個類別。 首先，我們建立了快速鍵，讓您更輕鬆地存取資料表視圖的 `DataSource` 和 `TableDelegate` 。 我們稍後會使用這些資訊，在分割視圖左側的資料表視圖和右邊的詳細資料檢視之間進行通訊。

最後，當資料表視圖載入至記憶體時，我們會建立的實例， `AttractionTableDatasource` 並 `AttractionTableDelegate` (上面建立的) ，並將它們附加至資料表視圖。

如需有關使用的詳細資訊 `UITableViewController` ，請參閱 Apple 的 [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523) 檔。

<a name="Pulling-it-All-Together"></a>

## <a name="pulling-it-all-together"></a>全部一起提取

如本檔開頭所述，資料表視圖通常會在 [分割視圖](~/ios/tvos/user-interface/split-views.md) 的一端顯示為導覽，並在相反的側邊顯示所選取專案的詳細資料。 例如： 

[![範例應用程式執行](table-views-images/intro01.png)](table-views-images/intro01.png#lightbox)

由於這是 tvOS 中的標準模式，讓我們來看看如何將所有專案整合在一起，並讓分割視圖的左邊和右邊彼此互動的最後步驟。

<a name="The-Detail-View"></a>

### <a name="the-detail-view"></a>詳細資料檢視

針對上面顯示的旅遊應用程式範例， `AttractionViewController` 會針對分割視圖右邊顯示的標準視圖控制器定義自訂類別 () 作為詳細資料檢視：

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

在這裡，我們提供了 **遊樂設施** (`AttractionInformation`) 顯示為屬性，並建立了一個 `UpdateUI` 方法，以填入在介面設計工具中新增至視圖的 UI 小工具。

我們也已將快捷方式定義回分割視圖控制器 (`SplitView`) ，我們將用它來將變更傳達回資料表視圖 (`AcctractionTableView`) 。

最後，自訂動作 (事件) 已新增至 `UIButton` 在介面設計工具中建立的三個實例，可讓使用者將遊樂設施標記為我的 _最愛_、取得遊樂設施的 _指示_ ，以及將 _航班預訂_ 給指定的城市。

<a name="The-Navigation-View-Controller"></a>

### <a name="the-navigation-view-controller"></a>流覽視圖控制器

因為資料表視圖控制器會在「分割」（Split View）左側的流覽視圖控制器中進行嵌套，所以會在介面設計工具中將自訂類別 () 指派給導覽視圖控制器， `MasterNavigationController` 並定義如下：

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

同樣地，這個類別只會定義一些快速鍵，讓您更輕鬆地在分割視圖控制器的兩端進行通訊：

- `SplitView` -是 `MainSpiltViewController` 導覽視圖控制器所屬之分割視圖控制器 () 的連結。
- `TableController` -取得 `AttractionTableViewController` 顯示為導覽視圖控制器頂端視圖的表格視圖控制器 () 。

<a name="The-Split-View-Controller"></a>

### <a name="the-split-view-controller"></a>分割視圖控制器

由於分割視圖控制器是我們應用程式的基底，因此我們在介面設計工具中建立了自訂類別 (`MasterSplitViewController`) ，並加以定義，如下所示：

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

首先，我們會建立分割視圖的 **詳細資料** 側 (`AttractionViewController`) 和 **主** 端 () 的快捷方式 `MasterNavigationController` 。 同樣地，這可讓您更輕鬆地在這兩個邊之間進行通訊。

接下來，當分割視圖載入至記憶體時，我們會將分割視圖控制器附加至分割視圖的兩端，並回應使用者在 [資料表視圖] 中反白顯示遊樂設施 (`AttractionHighlighted`) ，方法是在分割視圖的 [ **詳細資料** ] 端顯示新的遊樂設施。

請參閱 [tvTables](/samples/xamarin/ios-samples/tvos-tvtable) 範例應用程式，以取得分割視圖內資料表視圖的完整執行。

## <a name="table-views-in-detail"></a>詳細資料表格視圖

由於 tvOS 是以 iOS 為基礎，因此資料表視圖和資料表視圖控制器都是以類似的方式設計和運作。 如需在 Xamarin 應用程式中使用資料表視圖的詳細資訊，請參閱我們的 iOS 使用 [資料表和資料格](~/ios/user-interface/controls/tables/index.md) 檔。

<a name="Summary"></a>

## <a name="summary"></a>[摘要]

本文涵蓋了在 tvOS 應用程式內設計及使用資料表視圖的說明。 和已展示在分割視圖內使用資料表視圖的範例，這是 tvOS 應用程式中資料表視圖的一般使用方式。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)