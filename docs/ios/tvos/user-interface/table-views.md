---
title: 使用 tvOS 在 Xamarin 中的資料表檢視
description: 本文章涵蓋設計與 Xamarin.tvOS 應用程式內使用的資料表檢視和資料表檢視控制器。
ms.prod: xamarin
ms.assetid: D8F80FA9-6400-4DB7-AFC9-A28A54AD04E8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3e7fc3d627b5d7a1dc73caa395a9181efb0b5f08
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2019
ms.locfileid: "58677998"
---
# <a name="working-with-tvos-table-views-in-xamarin"></a>使用 tvOS 在 Xamarin 中的資料表檢視

_本文章涵蓋設計與 Xamarin.tvOS 應用程式內使用的資料表檢視和資料表檢視控制器。_

TvOS，在資料表檢視依現狀捲動可以選擇性地組織到群組或區段的資料列的單一資料行。 當您需要有效率地將大量的資料顯示給使用者，以純文字，以了解的方式時，應該使用資料表檢視。

資料表檢視通常會顯示在一端[分割檢視](~/ios/tvos/user-interface/split-views.md)為導覽中，選取的項目顯示在另一端的詳細資料：

[![](table-views-images/intro01.png "範例資料表檢視")](table-views-images/intro01.png#lightbox)

<a name="About-Table-Views" />

## <a name="about-table-views"></a>如需資料表檢視

A`UITableView`為階層式清單，可以選擇性地組織到群組或章節的資訊顯示可捲動的資料列的單一資料行： 

[![](table-views-images/table01.png "選取的項目")](table-views-images/table01.png#lightbox)

Apple 已處理資料表的下列建議：

- **了解的寬度**-試圖在忠於傳統的正確平衡點，在您的表格寬度。 如果資料表太寬，它可能很難從距離掃描，並可能需要與可用的內容區域。 如果資料表太窄，它可能會造成截斷資訊或換行，一次這可能很難讀取房間對面的使用者。
- **顯示快速資料表內容**-大型的資料的詳細清單，請消極式載入內容並顯示資訊，只要資料表呈現給使用者。 如果資料表為長而無法載入時，使用者可能會遺失您的應用程式或考慮它鎖定的興趣。
- **通知使用者的完整內容載入**-如果很長的資料表載入時間是無法避免，存在[進度列或活動指示器](~/ios/tvos/user-interface/progress-indicators.md)，使他們知道應用程式尚未鎖定。

<a name="Table-Cell-Types" />

## <a name="table-view-cell-types"></a>資料表檢視儲存格類型

A`UITableViewCell`用來代表個別的資料表檢視中的資料列。 Apple 已定義數個預設資料表資料格類型：

- **預設**-這種型別行為左下方儲存格和靠左對齊的標題右邊的選項映像。 
- **副標題**-這種型別行為，靠左對齊的標題的第一行並小於靠左對齊子標題下一行。
- **值 1** -此類型在同一行顯示更輕便彩色、 靠右對齊的子標題靠左對齊的標題。
- **值 2** -此類型在同一行顯示更輕便彩色、 靠左對齊的子標題靠右對齊的標題。

所有的預設資料表檢視儲存格類型也支援圖形化的項目，例如洩漏指標或核取記號。 

此外，您可以定義**自訂**資料表檢視儲存格類型和存在_原型的儲存格_，您可能會建立在介面設計工具或透過程式碼。

Apple 已使用 資料表檢視儲存格的下列建議：

- **避免文字裁剪**-保留個別的簡短的文字行，讓它們不會得到截斷。 截斷的單字或片語難以從剖析房間對面的使用者。
- **請考慮資料列狀態為 Focused** -因為資料列會變成更大，捨入的更多有關角落在焦點中的，您需要測試所有狀態中的儲存格的外觀。 影像或文字可能會變成裁剪，或處於對焦狀態看起來不正確。
- **編輯資料表盡量不要使用**-移動或刪除資料表的資料列會更耗時 tvOS 上於 iOS。 您需要小心決定這項功能將會加入或偏離您的 tvOS 應用程式。
- **建立自訂資料格類型適當處**-雖然內建的資料表檢視儲存格類型很適合許多情況下，請考慮建立非標準的資訊來提供更好的控制，並進一步呈現之資訊的自訂資料格類型目前的使用者。

<a name="Working-With-Table-Views" />

## <a name="working-with-table-views"></a>使用 「 資料表檢視

Xamarin.tvOS 應用程式中使用 「 資料表檢視工作的最簡單方式是建立和修改它們出現在介面設計工具。

若要開始，請執行下列動作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)
    
1. 在 Visual Studio for Mac，請啟動新的 tvOS 應用程式專案，然後選取**tvOS** > **應用程式** > **單一檢視應用程式**按一下**下一步**按鈕： 

    [![](table-views-images/table02.png "選取 單一檢視應用程式")](table-views-images/table02.png#lightbox)
1. 請輸入**名稱**應用程式，然後按一下 [**下一步]**: 

    [![](table-views-images/table03.png "輸入應用程式的名稱")](table-views-images/table03.png#lightbox)
1. 請調整**專案名稱**並**方案名稱**或接受預設值，然後按一下**建立**按鈕，以建立新的方案： 

    [![](table-views-images/table04.png "方案名稱與專案名稱")](table-views-images/table04.png#lightbox)
1. 在  **Solution Pad**，連按兩下`Main.storyboard`在 iOS 設計工具中開啟的檔案： 

    [![](table-views-images/table05.png "Main.storyboard 檔案")](table-views-images/table05.png#lightbox)
1. 選取並刪除**預設檢視控制器**: 

    [![](table-views-images/table06.png "選取並刪除預設檢視控制器")](table-views-images/table06.png#lightbox)
1. 選取 **分割檢視控制器**從**工具箱**並將它拖曳至設計介面。
1. 根據預設，您會收到[分割檢視](~/ios/tvos/user-interface/split-views.md)使用**瀏覽檢視控制器**並**資料表檢視控制器**在左手邊和**檢視控制器**在右手邊。 這是 Apple 的建議的用法 tvOS 的資料表檢視： 

    [![](table-views-images/table08.png "新增 分割檢視")](table-views-images/table08.png#lightbox)
1. 您必須選取 [資料表] 檢視的每個部分，並將它指派自訂**類別名稱**中**Widget**索引標籤**屬性總管**，讓您可以稍後C#程式碼。 例如，**資料表檢視控制器**: 

    [![](table-views-images/table09.png "將指定的類別名稱")](table-views-images/table09.png#lightbox)
1. 請確定您建立的自訂類別**資料表檢視控制器**，則**表格檢視**以及任何**原型的儲存格**。 Visual Studio for Mac 會將自訂類別新增至專案樹狀結構，在建立： 

    [![](table-views-images/table10.png "在專案樹狀結構中的自訂類別")](table-views-images/table10.png#lightbox)
1. 接下來，在設計介面中選取 [資料表] 檢視，並視需要調整它的屬性。 例如數目**原型的儲存格**並**樣式**（「 純 」 或 「 分組 」）： 

    [![](table-views-images/table11.png "小工具 索引標籤")](table-views-images/table11.png#lightbox)
1. 每個**原型的儲存格**，加以選取，然後指派一個唯一**識別項**中**Widget**索引標籤**屬性總管**。 這是步驟_非常重要_因為您稍後需要此識別碼時您填入的資料表。 例如`AttrCell`: 

    [![](table-views-images/table12.png "小工具 索引標籤")](table-views-images/table12.png#lightbox)
1. 您也可以選取來做為其中一個儲存格的呈現[預設資料表檢視儲存格類型](#table-view-cell-types)透過**樣式**下拉式清單或將它設定為**自訂**，並使用設計介面，以儲存格的版面配置從其他 UI widgets 中拖曳**工具箱**: 

    [![](table-views-images/table13.png "資料格的版面配置")](table-views-images/table13.png#lightbox)
1. 指派一個唯一**名稱**中的原型的儲存格設計每個 UI 項目的**小工具**索引標籤**屬性總管**讓您可以存取這些稍後在C#的程式碼： 

    [![](table-views-images/table14.png "指派的名稱")](table-views-images/table14.png#lightbox)
1. 針對所有原型的儲存格在資料表檢視中都重複上述的步驟。
1. 接下來，將自訂類別指派給您的 UI 設計、 版面配置詳細資料檢視和指派唯一的其餘部分**名稱**詳細資料中的每個 UI 項目檢視，以便您可以存取在C#以及。 例如： 

    [![](table-views-images/table15.png "UI 配置")](table-views-images/table15.png#lightbox)
1. 將您的變更儲存到分鏡腳本。
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. 在 Visual Studio 中，啟動新的 tvOS 應用程式專案，然後選取**tvOS** > **單一檢視應用程式**並輸入您的應用程式的名稱。 按一下 **好**按鈕，以建立新的方案： 

    [![](table-views-images/table02-vs.png "選取 單一檢視應用程式")](table-views-images/table02-vs.png#lightbox)
1. 在 [**方案總管] 中**，連按兩下`Main.storyboard`在 iOS 設計工具中開啟的檔案： 

    [![](table-views-images/table05-vs.png "Main.storyboard 檔案")](table-views-images/table05-vs.png#lightbox)
1. 選取並刪除**預設檢視控制器**: 

    [![](table-views-images/table06-vs.png "選取並刪除預設檢視控制器")](table-views-images/table06-vs.png#lightbox)
1. 選取 **分割檢視控制器**從**工具箱**並將它拖曳至設計介面： 

    [![](table-views-images/table07-vs.png "分割檢視控制器")](table-views-images/table07-vs.png#lightbox)
1. 根據預設，您會收到[分割檢視](~/ios/tvos/user-interface/split-views.md)使用**瀏覽檢視控制器**並**資料表檢視控制器**在左手邊和**檢視控制器**在右手邊。 這是 Apple 的建議的用法 tvOS 的資料表檢視： 

    [![](table-views-images/table08-vs.png "UI 的版面配置")](table-views-images/table08-vs.png#lightbox)
1. 您必須選取 [資料表] 檢視的每個部分，並將它指派自訂**類別名稱**中**Widget**索引標籤**屬性總管**，讓您可以稍後C#程式碼。 例如，**資料表檢視控制器**: 

    [![](table-views-images/table09-vs.png "小工具 索引標籤")](table-views-images/table09-vs.png#lightbox)
1. 請確定您建立的自訂類別**資料表檢視控制器**，則**表格檢視**以及任何**原型的儲存格**。 Visual Studio for Mac 會將自訂類別新增至專案樹狀結構，在建立： 

    [![](table-views-images/table10-vs.png "在專案樹狀結構中的自訂類別")](table-views-images/table10-vs.png#lightbox)
1. 接下來，在設計介面中選取 [資料表] 檢視，並視需要調整它的屬性。 例如數目**原型的儲存格**並**樣式**（「 純 」 或 「 分組 」）： 

    [![](table-views-images/table11-vs.png "小工具 索引標籤")](table-views-images/table11-vs.png#lightbox)
1. 每個**原型的儲存格**，加以選取，然後指派一個唯一**識別項**中**Widget**索引標籤**屬性總管**。 這是步驟_非常重要_因為您稍後需要此識別碼時您填入的資料表。 例如`AttrCell`: 

    [![](table-views-images/table12-vs.png "指派的識別項")](table-views-images/table12-vs.png#lightbox)
1. 您也可以選取來做為其中一個儲存格的呈現[預設資料表檢視儲存格類型](#table-view-cell-types)透過**樣式**下拉式清單或將它設定為**自訂**，並使用設計介面，以儲存格的版面配置從其他 UI widgets 中拖曳**工具箱**: 

    [![](table-views-images/table13-vs.png "[樣式] 下拉式清單")](table-views-images/table13-vs.png#lightbox)
1. 指派一個唯一**名稱**中的原型的儲存格設計每個 UI 項目的**小工具**索引標籤**屬性總管**讓您可以存取這些稍後在C#的程式碼： 

    [![](table-views-images/table14-vs.png "小工具 索引標籤")](table-views-images/table14-vs.png#lightbox)
1. 針對所有原型的儲存格在資料表檢視中都重複上述的步驟。
1. 接下來，將自訂類別指派給您的 UI 設計、 版面配置詳細資料檢視和指派唯一的其餘部分**名稱**詳細資料中的每個 UI 項目檢視，以便您可以存取在C#以及。 例如： 

    [![](table-views-images/table15.png "UI 配置")](table-views-images/table15.png#lightbox)
1. 將您的變更儲存到分鏡腳本。
    
-----

<a name="Designing-a-Data-Model" />

## <a name="designing-a-data-model"></a>設計資料模型

若要便於使用的資料表檢視將顯示更簡單的資訊，以簡化 （因為使用者選取或反白顯示 [資料表] 檢視中的資料列） 呈現的詳細資訊，請建立自訂類別或類別，以做為資料模型的資訊呈現.

包含一份旅遊預約應用程式為例**城市**，每個包含唯一的清單**準備贏得大奬**，使用者可以選取。 使用者將能夠將標示為引力*我的最愛*，選取以取得*方向*引力來和*一個班機*到指定的城市。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要建立資料模型**引力**中的專案名稱上按一下滑鼠右鍵**Solution Pad** ，然後選取**新增** > **新檔...**.請輸入`AttractionInformation`for**名稱**，按一下 **新增**按鈕： 

[![](table-views-images/data01.png "輸入名稱 AttractionInformation")](table-views-images/data01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要建立資料模型**引力**中的專案名稱上按一下滑鼠右鍵**方案總管**，然後選取**新增** > **新項目...**.選取 **類別**，然後輸入`AttractionInformation`如**名稱**，按一下 **新增**按鈕： 

[![](table-views-images/data01-vs.png "選取類別，並輸入名稱 AttractionInformation")](table-views-images/data01-vs.png#lightbox)

-----

編輯`AttractionInformation.cs`檔案，並使它看起來如下：

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

這個類別提供屬性，以儲存資訊有關給定**引力**。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

接下來中的專案名稱上按一下滑鼠右鍵**Solution Pad**一次，然後選取**新增** > **新檔...**.請輸入`CityInformation`for**名稱**，按一下 **新增**按鈕： 

[![](table-views-images/data02.png "輸入名稱 CityInformation")](table-views-images/data02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

接下來，以滑鼠右鍵按一下 專案名稱，在**方案總管**一次，然後選取**新增** > **新項目...**.請輸入`CityInformation`for**名稱**，按一下 **新增**按鈕： 

[![](table-views-images/data02-vs.png "輸入名稱 CityInformation")](table-views-images/data02-vs.png#lightbox)

-----

編輯`CityInformation.cs`檔案，並使它看起來如下：

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

這個類別包含的所有資訊有關目的地**城市**的集合**準備贏得大奬**該城市的並提供兩個 helper 方法 (`AddAttraction`) 讓您更輕鬆地新增至的準備贏得大奬縣 （市)。

<a name="The-Table-Data-Source" />

## <a name="the-table-view-data-source"></a>資料表檢視資料來源

每個資料表檢視表需要資料來源 (`UITableViewDataSource`) 來提供資料表的資料，並產生所需的資料列所需的資料表檢視。

上述範例中，以滑鼠右鍵按一下專案名稱，在**方案總管**，選取**新增** > **新的檔案...** ，並稱之為`AttractionTableDatasource`，按一下 [**新增**] 按鈕來建立。 接著，編輯`AttractionTableDatasource.cs`檔案，並使它看起來如下：

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

在詳細資料，讓我們看看幾個區段的類別。

首先，我們已定義常數，以保留 （這是上述介面設計工具中指派相同的識別項） 的 [原型] 儲存格的唯一識別碼、 回資料表檢視控制器中加入快顯並為我們的資料建立的儲存體：

```csharp
const string CellID = "AttrCell";
public AttractionTableViewController Controller { get; set;}
public List<CityInformation> Cities { get; set;}
```

接下來，我們可以在這裡儲存資料表檢視控制器，然後建立及填入我們的資料來源 （使用以上定義的資料模型） 建立類別時：

```csharp
public AttractionTableDatasource (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
    this.Cities = new List<CityInformation> ();
    PopulateCities ();
}
```

範例中，為了`PopulateCities`方法只會建立資料模型物件在記憶體中但這些可以輕鬆地從資料庫或 web 服務中實際的應用程式讀取：

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

針對**純**自訂樣式表檢視，一律會傳回 1。

`RowsInSection`方法會傳回在目前這一節中的資料列數目：

```csharp
public override nint RowsInSection (UITableView tableView, nint section)
{
    // Return the number of attractions in the given city
    return Cities [(int)section].Attractions.Count;
}
```

同樣地，如**純**資料表檢視中的資料來源傳回的項目總數。

`TitleForHeader`方法會傳回標題指定區段：

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    // Get the name of the current city
    return Cities [(int)section].Name;
}
```

針對**純**資料表檢視類型，將標題保留空白 (`""`)。

最後，當要求資料表檢視，建立並填入原型資料格，使用`GetCell`方法： 

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

每個資料表檢視表需要委派 (`UITableViewDelegate`) 來回應使用者互動或在資料表上的其他系統事件。

上述範例中，以滑鼠右鍵按一下專案名稱，在**方案總管**，選取**新增** > **新的檔案...** ，並稱之為`AttractionTableDelegate`，按一下 [**新增**] 按鈕來建立。 接著，編輯`AttractionTableDelegate.cs`檔案，並使它看起來如下：

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

讓我們看看幾個區段，這個類別的詳細資料。

首先，我們建立資料表檢視控制器的捷徑，建立類別時：

```csharp
public AttractionTableViewController Controller { get; set;}
...

public AttractionTableDelegate (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
}
```

然後，在選取的資料列時才 （使用者點按的觸控介面上遠端 Apple） 我們想要將標示**引力**所選取的資料列，為我的最愛表示：

```csharp
public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
    attraction.IsFavorite = (!attraction.IsFavorite);

    // Update UI
    Controller.TableView.ReloadData ();
}
```

接下來，當使用者會反白顯示一個資料列 （藉由提供使用 Apple 遠端觸控介面的焦點） 我們想要呈現的詳細資料**引力**由我們的分割檢視控制器的 [詳細資料] 區段中的該資料列：

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

`CanFocusRow`方法會針對每個資料列在資料表檢視中取得焦點的呼叫。 傳回`true`如果資料列可以取得焦點，否則會傳回`false`。 在此範例中，我們已經建立自訂`AttractionHighlighted`系統在收到焦點在每個資料列引發的事件。

如需有關使用`UITableViewDelegate`，請參閱 Apple [UITableViewDelegate](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006942)文件。

<a name="The-Table-View-Cell" />

## <a name="the-table-view-cell"></a>資料表檢視儲存格

每個原型資料格加入至介面設計工具中的 [資料表] 檢視，也會建立資料表檢視儲存格的自訂執行個體 (`UITableViewCell`) 可讓您建立時，填入新的資料格 （資料列）。

範例應用程式中，按兩下`AttractionTableCell.cs`檔案，以開啟它進行編輯，使它看起來如下所示：

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

這個類別會提供儲存體引力資料模型物件 (`AttractionInformation`如上面所定義) 指定的資料列中顯示：

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

`UpdateUI`方法會填入 UI Widgets （針對已加入至介面設計工具中的儲存格的原型） 所需：

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

資料表檢視控制器 (`UITableViewController`) 管理已新增透過介面設計工具的分鏡腳本的資料表檢視。

範例應用程式中，按兩下`AttractionTableViewController.cs`檔案，以開啟它進行編輯，使它看起來如下所示：

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

讓我們看看這個類別。 首先，我們建立了讓您更輕鬆地存取 [資料表] 檢視的捷徑`DataSource`和`TableDelegate`。 我們將使用那些更新版本中的資料表檢視分割檢視的左邊和右邊的 [詳細資料] 檢視之間進行通訊。

最後，當資料表檢視載入記憶體時，我們建立的執行個體`AttractionTableDatasource`和`AttractionTableDelegate`（兩者上面所建立），並將它們附加至資料表檢視。

如需有關使用`UITableViewController`，請參閱 Apple [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)文件。

<a name="Pulling-it-All-Together" />

## <a name="pulling-it-all-together"></a>所有的一起提取它

如這份文件開頭所述，資料表檢視通常會顯示在一端[分割檢視](~/ios/tvos/user-interface/split-views.md)為導覽中，選取的項目顯示在另一端的詳細資料。 例如:  

[![](table-views-images/intro01.png "執行範例應用程式")](table-views-images/intro01.png#lightbox)

由於這是在 tvOS 的標準模式，讓我們看看集結了各種的最後一個步驟，並有與彼此互動左右側邊的分割檢視。

<a name="The-Detail-View" />

### <a name="the-detail-view"></a>詳細資料檢視

以上的自訂類別所顯示的旅遊應用程式範例 (`AttractionViewController`) 定義標準右側為 詳細資料檢視的 分割 檢視中呈現的檢視控制器：

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

在這裡，我們有提供**引力**(`AttractionInformation`) 所顯示的屬性，並建立`UpdateUI`填入 UI Widgets 的方法加入至介面設計工具中的檢視。

我們也已定義的快顯回到分割檢視控制器 (`SplitView`)，我們會使用通訊變回 [資料表] 檢視 (`AcctractionTableView`)。

最後，自訂動作 （事件） 已新增至三個`UIButton`介面設計工具中建立的執行個體，可讓使用者將標示為引力_我的最愛_，取得_方向_至引力並_一個班機_到指定的城市。

<a name="The-Navigation-View-Controller" />

### <a name="the-navigation-view-controller"></a>瀏覽檢視控制器

因為資料表檢視控制器巢狀瀏覽檢視控制器左邊的 [分割] 檢視中，瀏覽檢視控制器已指派的自訂類別 (`MasterNavigationController`) 介面設計工具中定義，如下所示：

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

同樣地，這個類別只會定義幾個捷徑，讓您更輕鬆地跨兩個側邊的分割檢視控制器進行通訊：

* `SplitView` -這是分割檢視控制器的連結 (`MainSpiltViewController`) 所屬瀏覽檢視控制器。
* `TableController` -取得資料表檢視控制器 (`AttractionTableViewController`)，都會呈現為 [Top] 檢視中瀏覽檢視控制器。

<a name="The-Split-View-Controller" />

### <a name="the-split-view-controller"></a>分割檢視控制器

由於分割檢視控制器是我們的應用程式的基底，我們建立的自訂類別 (`MasterSplitViewController`) 介面設計工具中定義它，如下所示：

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

首先，我們建立的捷徑**詳細資料**這邊的分割檢視 (`AttractionViewController`) 及**Master**端 (`MasterNavigationController`)。 同樣地，這使得更新版本的兩端之間進行通訊的工作變得更容易。

接下來，當分割檢視載入記憶體時，我們附加至分割檢視的兩邊的分割檢視控制器，並回應引力資料表檢視中的反白顯示的使用者 (`AttractionHighlighted`) 會顯示在新的引力**詳細資料**這邊的分割檢視。

請參閱[tvTables](https://developer.xamarin.com/samples/monotouch/tvos/tvTable/)內分割檢視的資料表檢視的完整實作的範例應用程式。

## <a name="table-views-in-detail"></a>在 詳細資料的資料表檢視

TvOS 是以 iOS 為基礎，因為資料表檢視和資料表檢視控制器設計，並以類似的方式運作。 如需詳細的 Xamarin 應用程式中使用資料表檢視的詳細資訊，請參閱我們的 iOS[使用資料表和資料格](~/ios/user-interface/controls/tables/index.md)文件。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計和使用 Xamarin.tvOS 應用程式內的資料表檢視。 並提供有關使用分割檢視，也就是典型的用法的 tvOS 應用程式中的資料表 檢視內的資料表檢視的範例。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
