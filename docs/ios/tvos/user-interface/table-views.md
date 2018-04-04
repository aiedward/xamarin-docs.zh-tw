---
title: 使用 資料表檢視
description: 本文涵蓋設計和資料表檢視和資料表檢視控制器 Xamarin.tvOS 應用程式內使用。
ms.prod: xamarin
ms.assetid: D8F80FA9-6400-4DB7-AFC9-A28A54AD04E8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 8864e85e4d657fc242f6c06b21c815f62055c9f4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-table-views"></a>使用 資料表檢視

_本文涵蓋設計和資料表檢視和資料表檢視控制器 Xamarin.tvOS 應用程式內使用。_

TvOS，在資料表檢視表會顯示成捲動可以選擇性地組織到群組或章節的資料列的單一資料行。 當您需要有效率地將大量的資料顯示給使用者，以了解的方式清除時，應該使用資料表檢視。

資料表檢視通常會顯示一個方[分割檢視](~/ios/tvos/user-interface/split-views.md)為巡覽邊中顯示選取之項目的詳細資料：

[![](table-views-images/intro01.png "範例資料表檢視")](table-views-images/intro01.png#lightbox)

<a name="About-Table-Views" />

## <a name="about-table-views"></a>有關資料表檢視

A`UITableView`可以選擇性地組織到群組或章節的資訊的階層式清單以顯示可捲動的資料列的單一資料行： 

[![](table-views-images/table01.png "選取的項目")](table-views-images/table01.png#lightbox)

Apple 已使用資料表的下列建議：

- **要注意的寬度**-嘗試在表格寬度中，在正確的平衡。 如果太寬的資料表，它會很難從距離掃描，並可以在可用的內容區域強調的重點。 資料表是否太窄，就可能會導致截斷的資訊或換行、 一次這很難遙控器輕鬆讀取使用者。
- **顯示快速資料表內容**-大型資料的清單，請消極載入內容並啟動只要資料表會向使用者顯示相關資訊。 如果資料表花太長的時間載入，使用者可能會遺失在應用程式或考慮鎖定總感興趣。
- **通知使用者的完整內容負載**-如果很長的資料表載入時間是無可避免、 存在[進度列或活動指示器](~/ios/tvos/user-interface/progress-indicators.md)，使他們知道應用程式尚未鎖定。

<a name="Table-Cell-Types" />

## <a name="table-view-cell-types"></a>資料表檢視儲存格類型

A`UITableViewCell`用來代表個別的資料列的資料表檢視中的資料。 Apple 已定義數個預設資料表儲存格類型：

- **預設**-這個選項映像的右邊靠左對齊的標題與資料格左邊的類型顯示。 
- **副標題**-此類型會呈現靠左對齊的標題的第一行和較小靠左對齊子標題在下一行。
- **值 1** -此類型在同一行顯示有起來較淺色彩色、 靠右對齊的子標題靠左對齊的標題。
- **值 2** -此類型在同一行顯示有起來較淺色彩色、 靠左對齊的子標題靠右對齊的標題。

所有的預設資料表檢視儲存格類型也支援圖形化元素例如洩漏指標或核取記號。 

此外，您可以定義**自訂**表格檢視儲存格類型和存在_原型儲存格_，您可能會建立介面設計工具中或透過程式碼。

Apple 已使用 資料表檢視儲存格的下列建議：

- **避免文字和結束時間裁剪**-保持在個別的簡短文字行，讓他們不最後被截斷。 截斷的單字或片語難以從分為聊天室的使用者。
- **請考慮資料列狀態為 Focused** -因為資料列變得更大的、 具有多四捨五入角落在焦點中的，您需要測試所有狀態中的儲存格的外觀。 影像或文字可能會變成裁剪或狀態為 Focused 看起來不正確。
- **編輯資料表盡量不要使用**-移動或刪除資料表資料列會耗用更多時間在 tvOS 於 iOS。 您需要小心地決定這項功能將會加入或分散 tvOS 應用程式。
- **建立自訂儲存格類型適當處**-時內建的資料表檢視儲存格類型非常適合在許多情況下，請考慮建立自訂儲存格類型的非標準的資訊提供更好的控制，並進一步呈現的資訊目前的使用者。

<a name="Working-With-Table-Views" />

## <a name="working-with-table-views"></a>使用 資料表檢視

Xamarin.tvOS 應用程式中使用的資料表檢視的最簡單方式是建立和修改介面設計工具中的外觀。

若要開始，請執行下列動作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
    
1. 在適用於 Mac 的 Visual Studio 中啟動新的 tvOS 應用程式專案，然後選取**tvOS** > **應用程式** > **單一檢視應用程式**按一下**下一步**按鈕： 

    [![](table-views-images/table02.png "選取單一檢視應用程式")](table-views-images/table02.png#lightbox)
1. 輸入**名稱**應用程式並按一下**下一步**: 

    [![](table-views-images/table03.png "輸入應用程式的名稱")](table-views-images/table03.png#lightbox)
1. 請調整**專案名稱**和**方案名稱**或接受預設值，然後按一下**建立**按鈕以建立新的方案： 

    [![](table-views-images/table04.png "專案名稱和方案名稱")](table-views-images/table04.png#lightbox)
1. 在**方案板**，連按兩下`Main.storyboard`iOS 設計工具中開啟的檔案： 

    [![](table-views-images/table05.png "Main.storyboard 檔案")](table-views-images/table05.png#lightbox)
1. 選取並刪除**預設檢視控制器**: 

    [![](table-views-images/table06.png "選取並刪除在預設檢視控制站")](table-views-images/table06.png#lightbox)
1. 選取**分割檢視控制器**從**工具箱**並將它拖曳到設計介面上。
1. 根據預設，您會取得[分割檢視](~/ios/tvos/user-interface/split-views.md)與**瀏覽檢視控制器**和**資料表檢視控制器**在左手邊與**檢視控制器**右手邊中。 這是資料表中之檢視的 tvOS Apple 的建議的用法： 

    [![](table-views-images/table08.png "新增 分割檢視")](table-views-images/table08.png#lightbox)
1. 您必須選取 資料表 檢視的每個組件，並將它指派自訂**類別名稱**中**Widget**  索引標籤**屬性總管**，讓您可以稍後在 C# 中存取程式碼。 例如，**資料表檢視控制器**: 

    [![](table-views-images/table09.png "指定類別名稱")](table-views-images/table09.png#lightbox)
1. 請確定您建立的自訂類別**資料表檢視控制器**、**資料表檢視**和任何**原型儲存格**。 Visual Studio for Mac 將自訂類別加入專案樹狀結構建立時： 

    [![](table-views-images/table10.png "在專案樹狀結構中的自訂類別")](table-views-images/table10.png#lightbox)
1. 接下來，在設計介面中選取 [資料表] 檢視，並視需要調整它的屬性。 例如，數目**原型儲存格**和**樣式**（「 純 」 或 「 群組 」）： 

    [![](table-views-images/table11.png "[小工具] 索引標籤")](table-views-images/table11.png#lightbox)
1. 每個**原型儲存格**、 選取它，然後指派一個唯一**識別碼**中**Widget**  索引標籤**屬性總管**。 這是步驟_非常重要_因為您稍後會需要此識別碼時您填入的資料表。 例如`AttrCell`: 

    [![](table-views-images/table12.png "[小工具] 索引標籤")](table-views-images/table12.png#lightbox)
1. 您也可以選取顯示做為其中一個儲存格[資料表檢視儲存格類型](#Table-View-Cell-Types)透過**樣式**下拉式清單或將它設定為**自訂**並使用設計介面來配置儲存格從其他 UI widgets 中拖曳**工具箱**: 

    [![](table-views-images/table13.png "資料格的版面配置")](table-views-images/table13.png#lightbox)
1. 指派一個唯一**名稱**中的原型儲存格設計中的每個 UI 項目**Widget**  索引標籤**屬性總管**以便稍後在 C# 程式碼中存取： 

    [![](table-views-images/table14.png "指派的名稱")](table-views-images/table14.png#lightbox)
1. 原型中的資料格資料表檢視表的所有重複上述步驟。
1. 接下來，將自訂類別指派給 UI 設計、 版面配置詳細資料檢視和指派唯一的其餘部分**名稱**詳細資料中每個 UI 項目檢視，以便您可以同時在 C# 中存取它們。 例如： 

    [![](table-views-images/table15.png "UI 配置")](table-views-images/table15.png#lightbox)
1. 將您的變更儲存到分鏡腳本。
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. 在 Visual Studio 中，啟動新的 tvOS 應用程式專案，然後選取**tvOS** > **單一檢視應用程式**並輸入您的應用程式的名稱。 按一下**好**按鈕以建立新的方案： 

    [![](table-views-images/table02-vs.png "選取單一檢視應用程式")](table-views-images/table02-vs.png#lightbox)
1. 在**方案總管 中**，連按兩下`Main.storyboard`iOS 設計工具中開啟的檔案： 

    [![](table-views-images/table05-vs.png "Main.storyboard 檔案")](table-views-images/table05-vs.png#lightbox)
1. 選取並刪除**預設檢視控制器**: 

    [![](table-views-images/table06-vs.png "選取並刪除在預設檢視控制站")](table-views-images/table06-vs.png#lightbox)
1. 選取**分割檢視控制器**從**工具箱**並將它拖曳到設計介面上： 

    [![](table-views-images/table07-vs.png "分割檢視控制器")](table-views-images/table07-vs.png#lightbox)
1. 根據預設，您會取得[分割檢視](~/ios/tvos/user-interface/split-views.md)與**瀏覽檢視控制器**和**資料表檢視控制器**在左手邊與**檢視控制器**右手邊中。 這是資料表中之檢視的 tvOS Apple 的建議的用法： 

    [![](table-views-images/table08-vs.png "UI 的版面配置")](table-views-images/table08-vs.png#lightbox)
1. 您必須選取 資料表 檢視的每個組件，並將它指派自訂**類別名稱**中**Widget**  索引標籤**屬性總管**，讓您可以稍後在 C# 中存取程式碼。 例如，**資料表檢視控制器**: 

    [![](table-views-images/table09-vs.png "[小工具] 索引標籤")](table-views-images/table09-vs.png#lightbox)
1. 請確定您建立的自訂類別**資料表檢視控制器**、**資料表檢視**和任何**原型儲存格**。 Visual Studio for Mac 將自訂類別加入專案樹狀結構建立時： 

    [![](table-views-images/table10-vs.png "在專案樹狀結構中的自訂類別")](table-views-images/table10-vs.png#lightbox)
1. 接下來，在設計介面中選取 [資料表] 檢視，並視需要調整它的屬性。 例如，數目**原型儲存格**和**樣式**（「 純 」 或 「 群組 」）： 

    [![](table-views-images/table11-vs.png "[小工具] 索引標籤")](table-views-images/table11-vs.png#lightbox)
1. 每個**原型儲存格**、 選取它，然後指派一個唯一**識別碼**中**Widget**  索引標籤**屬性總管**。 這是步驟_非常重要_因為您稍後會需要此識別碼時您填入的資料表。 例如`AttrCell`: 

    [![](table-views-images/table12-vs.png "識別項指派")](table-views-images/table12-vs.png#lightbox)
1. 您也可以選取顯示做為其中一個儲存格[資料表檢視儲存格類型](#Table-View-Cell-Types)透過**樣式**下拉式清單或將它設定為**自訂**並使用設計介面來配置儲存格從其他 UI widgets 中拖曳**工具箱**: 

    [![](table-views-images/table13-vs.png "[樣式] 下拉式清單")](table-views-images/table13-vs.png#lightbox)
1. 指派一個唯一**名稱**中的原型儲存格設計中的每個 UI 項目**Widget**  索引標籤**屬性總管**以便稍後在 C# 程式碼中存取： 

    [![](table-views-images/table14-vs.png "[小工具] 索引標籤")](table-views-images/table14-vs.png#lightbox)
1. 原型中的資料格資料表檢視表的所有重複上述步驟。
1. 接下來，將自訂類別指派給 UI 設計、 版面配置詳細資料檢視和指派唯一的其餘部分**名稱**詳細資料中每個 UI 項目檢視，以便您可以同時在 C# 中存取它們。 例如： 

    [![](table-views-images/table15.png "UI 配置")](table-views-images/table15.png#lightbox)
1. 將您的變更儲存到分鏡腳本。
    
-----

<a name="Designing-a-Data-Model" />

## <a name="designing-a-data-model"></a>設計資料模型

請使用 [資料表] 檢視會更容易顯示的資訊，並且將可簡化將呈現的詳細資訊 （如使用者選取或反白顯示的表格檢視中的資料列），請建立自訂的類別或類別，以做為資料模型的資訊呈現.

例旅遊預約應用程式，其中包含一份**城市**，每個包含的唯一清單**吸引**，使用者可加以選取。 使用者可以將標示為引力*我的最愛*，請選取以取得*指示*至引力和*一個班機*至給定的縣 （市）。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要建立資料模型**引力**中的專案名稱上按一下滑鼠右鍵**方案板**選取**新增** > **新的檔案...**.輸入`AttractionInformation`如**名稱**按一下**新增**按鈕： 

[![](table-views-images/data01.png "請輸入名稱 AttractionInformation")](table-views-images/data01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要建立資料模型**引力**中的專案名稱上按一下滑鼠右鍵**方案總管 中**選取**新增** > **新項目...**.選取**類別**輸入`AttractionInformation`如**名稱**按一下**新增**按鈕： 

[![](table-views-images/data01-vs.png "選取類別，並輸入名稱 AttractionInformation")](table-views-images/data01-vs.png#lightbox)

-----

編輯`AttractionInformation.cs`檔案，並讓它看起來如下所示：

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

這個類別會提供有關儲存資訊的屬性指定**引力**。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

接下來，以滑鼠右鍵按一下 專案名稱中**方案板**再次選取**新增** > **新的檔案...**.輸入`CityInformation`如**名稱**按一下**新增**按鈕： 

[![](table-views-images/data02.png "請輸入名稱 CityInformation")](table-views-images/data02.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

接下來，以滑鼠右鍵按一下 [專案名稱中**方案總管] 中**再次選取**新增** > **新項目...**.輸入`CityInformation`如**名稱**按一下**新增**按鈕： 

[![](table-views-images/data02-vs.png "請輸入名稱 CityInformation")](table-views-images/data02-vs.png#lightbox)

-----

編輯`CityInformation.cs`檔案，並讓它看起來如下所示：

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

這個類別會保存所有的資訊關於目的地**縣 （市)**，集合**吸引**該城市的並提供兩個 helper 方法 (`AddAttraction`) 若要更輕鬆地新增到吸引縣 （市)。

<a name="The-Table-Data-Source" />

## <a name="the-table-view-data-source"></a>資料表檢視資料來源

每個資料表的檢視表需要資料來源 (`UITableViewDataSource`) 來提供資料表的資料，並產生必要的資料列，因為所需的資料表檢視表。

上述範例中，以滑鼠右鍵按一下專案名稱中**方案總管 中**，選取**新增** > **新的檔案...**並呼叫它`AttractionTableDatasource`按一下**新增** 按鈕來建立。 接著，編輯`AttractionTableDatasource.cs`檔案，並讓它看起來如下所示：

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

讓我們看看類別的幾個章節中詳細資料。

首先，我們已定義常數，以保存原型儲存格 （這是指派上述介面設計工具中的相同識別項） 的唯一識別碼、 回資料表檢視控制站加入快顯並建立資料的儲存體：

```csharp
const string CellID = "AttrCell";
public AttractionTableViewController Controller { get; set;}
public List<CityInformation> Cities { get; set;}
```

接下來，我們可以在這裡儲存資料表的檢視控制站，然後建立及填入資料來源 （使用以上定義的資料模型） 建立類別之後：

```csharp
public AttractionTableDatasource (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
    this.Cities = new List<CityInformation> ();
    PopulateCities ();
}
```

範例中，為了`PopulateCities`方法只會建立資料模型物件在記憶體中但這些無法輕易讀取從實際的應用程式中的資料庫或 web 服務：

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

`NumberOfSections`方法會傳回資料表中的區段數目：

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    // Return number of cities
    return Cities.Count;
}
```

如**一般**樣式的資料表檢視，一律會傳回 1。

`RowsInSection`方法會傳回目前的區段中的資料列數目：

```csharp
public override nint RowsInSection (UITableView tableView, nint section)
{
    // Return the number of attractions in the given city
    return Cities [(int)section].Attractions.Count;
}
```

同樣地，針對**一般**資料表檢視中的資料來源傳回的項目總數。

`TitleForHeader`方法會傳回標題指定區段：

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    // Get the name of the current city
    return Cities [(int)section].Name;
}
```

如**一般**資料表檢視類型，將標題保留空白 (`""`)。

最後，當要求資料表檢視表時，建立並填入原型儲存格，使用`GetCell`方法： 

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

如需有關使用`UITableViewDatasource`，請參閱 Apple [UITableViewDatasource](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40006941)文件。

<a name="The-Table-View-Delegate" />

## <a name="the-table-view-delegate"></a>資料表檢視委派

每個資料表的檢視表需要委派 (`UITableViewDelegate`) 回應使用者互動或資料表上的其他系統事件。

上述範例中，以滑鼠右鍵按一下專案名稱中**方案總管 中**，選取**新增** > **新的檔案...**並呼叫它`AttractionTableDelegate`按一下**新增** 按鈕來建立。 接著，編輯`AttractionTableDelegate.cs`檔案，並讓它看起來如下所示：

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

讓我們看看數個區段，這個類別的詳細資料。

首先，我們建立的資料表檢視控制器捷徑，建立類別時：

```csharp
public AttractionTableViewController Controller { get; set;}
...

public AttractionTableDelegate (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
}
```

然後，選取資料列時 （使用者按一下觸控介面上的 Apple 遠端） 我們想要將標示**引力**所選取的資料列做為我的最愛表示：

```csharp
public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
    attraction.IsFavorite = (!attraction.IsFavorite);

    // Update UI
    Controller.TableView.ReloadData ();
}
```

接下來，使用者會反白顯示的資料列 （藉由提供使用 Apple 遠端觸控介面的焦點） 時我們想要呈現的詳細資料**引力**我們分割檢視控制站的詳細資料 區段中該資料列代表：

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

`CanFocusRow`方法針對每個資料列在資料表檢視中取得焦點的呼叫。 傳回`true`如果資料列可以取得焦點，否則傳回`false`。 在此範例中，我們建立了自訂`AttractionHighlighted`收到焦點將每個資料列引發的事件。

如需有關使用`UITableViewDelegate`，請參閱 Apple [UITableViewDelegate](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006942)文件。

<a name="The-Table-View-Cell" />

## <a name="the-table-view-cell"></a>資料表檢視儲存格

針對您加入至資料表檢視表設計工具介面中每個原型資料格，同時也會建立資料表的檢視儲存格的自訂執行個體 (`UITableViewCell`) 可讓您建立時，填入新的儲存格 （資料列）。

範例應用程式中，按兩下`AttractionTableCell.cs`檔案開啟它進行編輯，並使其看起來如下：

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

這個類別會提供儲存體引力資料模型物件 (`AttractionInformation`定義上方) 顯示給定資料列中：

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

`UpdateUI`方法會填入 UI Widgets （針對已加入至設計工具介面中的儲存格的原型） 所需：

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

如需有關使用`UITableViewCell`，請參閱 Apple [UITableViewCell](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewCell_Class/index.html#//apple_ref/doc/uid/TP40006938)文件。

<a name="The-Table-View-Controller" />

## <a name="the-table-view-controller"></a>資料表檢視控制器

資料表檢視控制器 (`UITableViewController`) 管理已加入至分鏡腳本設計工具介面透過資料表檢視。

範例應用程式中，按兩下`AttractionTableViewController.cs`檔案開啟它進行編輯，並使其看起來如下：

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

讓我們看看這個類別。 首先，我們也可以建立快速鍵，讓您更輕鬆地存取資料表檢視`DataSource`和`TableDelegate`。 我們會使用那些稍後資料表中的檢視分割檢視的左邊和右邊的 [詳細資料] 檢視之間進行通訊。

最後，當資料表檢視載入記憶體時，我們建立的執行個體`AttractionTableDatasource`和`AttractionTableDelegate`（兩者上面所建立） 並將它們附加至資料表檢視表。

如需有關使用`UITableViewController`，請參閱 Apple [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)文件。

<a name="Pulling-it-All-Together" />

## <a name="pulling-it-all-together"></a>所有的一起提取它

資料表檢視如同本文開頭所述，通常會顯示一個方[分割檢視](~/ios/tvos/user-interface/split-views.md)為巡覽邊中顯示選取之項目的詳細資料。 例如:  

[![](table-views-images/intro01.png "執行範例應用程式")](table-views-images/intro01.png#lightbox)

因為這是 tvOS 的標準模式，讓我們看看將所有項目結合在一起的最後一個步驟，並具有彼此互動的分割檢視左右側邊。

<a name="The-Detail-View" />

### <a name="the-detail-view"></a>詳細資料檢視

針對旅遊應用程式的範例中顯示的上方的自訂類別 (`AttractionViewController`) 定義為在詳細資料檢視分割檢視的右邊顯示的標準檢視控制器：

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

在這裡，我們已提供**引力**(`AttractionInformation`) 即將顯示為屬性，並建立`UpdateUI`填入 UI Widgets 的方法加入至介面設計工具中的檢視。

我們也已定義捷徑回到分割檢視控制器 (`SplitView`)，我們將使用通訊變更回 [資料表] 檢視 (`AcctractionTableView`)。

最後，自訂動作 （事件） 已加入至三個`UIButton`介面設計工具中建立的執行個體，可讓使用者將標示為引力_我的最愛_，取得_指示_至引力和_一個班機_至給定的縣 （市）。

<a name="The-Navigation-View-Controller" />

### <a name="the-navigation-view-controller"></a>瀏覽檢視控制器

瀏覽檢視控制器在分割檢視的左側瀏覽檢視控制器中巢狀資料表檢視控制器，因為已指派為自訂類別 (`MasterNavigationController`) 介面的設計工具中定義，如下所示：

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

同樣地，這個類別只會定義幾個捷徑，讓您更輕鬆地跨兩個邊的分割檢視控制器進行通訊：

* `SplitView` -這是通往分割檢視控制器 (`MainSpiltViewController`) 瀏覽檢視控制器屬於。
* `TableController` -取得資料表檢視控制器 (`AttractionTableViewController`)，會以 [Top] 檢視中瀏覽檢視控制器。

<a name="The-Split-View-Controller" />

### <a name="the-split-view-controller"></a>分割檢視控制器

因為分割檢視控制器是我們的應用程式的基底，我們建立的自訂類別 (`MasterSplitViewController`) 介面的設計工具中，定義如下：

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

首先，我們建立的捷徑**詳細資料**這邊的分割檢視 (`AttractionViewController`) 以及**Master**端 (`MasterNavigationController`)。 同樣地，這使得您更輕鬆地更新版本的兩端之間的通訊。

接下來，當分割檢視載入記憶體時，我們的分割檢視這兩端都將分割檢視控制器並回應引力資料表檢視中的反白顯示的使用者 (`AttractionHighlighted`) 藉由顯示在新的引力**詳細資料**這邊的分割檢視。

請參閱[tvTables](https://developer.xamarin.com/samples/monotouch/tvos/tvTable/)分割檢視內的資料表檢視的完整實作的範例應用程式。

## <a name="table-views-in-detail"></a>在詳細資料的資料表檢視

TvOS 以 iOS 為依據，因為資料表檢視和資料表檢視控制器設計，並以類似的方式運作。 如需詳細資料表檢視中的 Xamarin 應用程式處理的詳細資訊，請參閱我們的 iOS[使用資料表和資料格](~/ios/user-interface/controls/tables/index.md)文件。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計和使用 Xamarin.tvOS 應用程式內的資料表檢視。 並提供使用內部分割檢視，也就是典型的用法 tvOS 應用程式中的資料表檢視的資料表檢視表的範例。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
