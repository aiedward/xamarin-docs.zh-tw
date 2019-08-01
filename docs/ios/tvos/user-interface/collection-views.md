---
title: 在 Xamarin 中使用 tvOS 收集視圖
description: 本檔說明如何在以 Xamarin 建立的 tvOS 應用程式中使用集合視圖。 其中涵蓋集合視圖版面配置、建立儲存格和輔助視圖、回應使用者事件等等。
ms.prod: xamarin
ms.assetid: 5125C4C7-2DDF-4C19-A362-17BB2B079178
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3246dcbf58a1b6dda6838b5eb81442fdbc429af5
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652356"
---
# <a name="working-with-tvos-collection-views-in-xamarin"></a>在 Xamarin 中使用 tvOS 收集視圖

集合視圖可讓您使用任意版面配置來顯示一組內容。 使用內建支援, 可讓您輕鬆建立類似格線或線性版面配置, 同時也支援自訂版面配置。

[![](collection-views-images/collection01.png "範例集合視圖")](collection-views-images/collection01.png#lightbox)

集合視圖會使用委派和資料來源來維護專案的集合, 以提供使用者互動和集合內容。 由於集合視圖是以獨立于 View 本身的版面配置子系統為基礎, 因此提供不同的版面配置可以輕鬆地即時變更集合視圖資料的呈現。

<a name="About-Collection-Views" />

## <a name="about-collection-views"></a>關於集合視圖

如上所述, 集合視圖 (`UICollectionView`) 會管理專案的已排序集合, 並以可自訂的版面配置呈現這些專案。 集合視圖的工作方式類似于資料表視圖 (`UITableView`), 不同之處在于它們可以使用版面配置來呈現不只是單一資料行中的專案。

在 tvOS 中使用集合視圖時, 您的應用程式會負責使用資料來源 (`UICollectionViewDataSource`) 提供與集合相關聯的資料。 您可以選擇性地組織集合視圖資料, 並將其呈現在不同的群組中 (區段)。

[集合] 視圖會使用資料格 (`UICollectionViewCell`) 來呈現螢幕上的個別專案, 以提供集合中指定資訊的呈現方式 (例如影像及其標題)。

選擇性地, 您可以將補充視圖加入至集合視圖的簡報, 做為區段和儲存格的頁首和頁尾。 集合視圖的版面配置會負責定義這些視圖的位置, 以及個別的儲存格。

[集合] 視圖可以使用委派 (`UICollectionViewDelegate`) 來回應使用者互動。 如果資料格已反白顯示, 或已選取一個, 則此委派也會負責判斷指定的儲存格是否可以取得焦點。 在某些情況下, 委派會決定個別資料格的大小。

<a name="Collection-View-Layouts" />

## <a name="collection-view-layouts"></a>集合視圖版面配置

集合視圖的一項重要功能, 就是它所呈現的資料與其版面配置之間的分隔。 集合視圖配置 (`UICollectionViewLayout`) 負責在集合視圖的螢幕簡報中提供組織和儲存格的位置 (以及任何補充的視圖)。

集合視圖會從其附加的資料來源建立個別的儲存格, 然後由指定的集合視圖配置來排列和顯示。

建立集合視圖時, 通常會提供集合的版面配置。 不過, 您可以隨時變更集合視圖版面配置, 並使用所提供的新版面配置, 自動更新集合視圖資料的螢幕簡報。

集合視圖配置提供數種方法, 可用來建立兩個不同版面配置之間的轉換動畫 (預設不會執行任何動畫)。 此外, 集合視圖配置可以與手勢辨識器搭配使用, 以進一步建立使用者互動的動畫, 而導致版面配置變更。

<a name="Creating-Cells-and-Supplementary-Views" />

## <a name="creating-cells-and-supplementary-views"></a>建立儲存格和輔助視圖

集合視圖的資料來源不只負責提供支援集合專案的資料, 也是用來顯示內容的儲存格。

由於集合視圖的設計是用來處理大型的專案集合, 因此可以將個別的資料格清除佇列並重複使用, 以保持 overrunning 的記憶體限制。 清除佇列 views 有兩種不同的方法:

- `DequeueReusableCell`-建立或傳回給定類型的儲存格 (如應用程式的分鏡腳本中所指定)。
- `DequeueReusableSupplementaryView`-建立或傳回給定類型的補充視圖 (如應用程式的分鏡腳本中所指定)。

呼叫其中任一種方法之前, 您必須先註冊用來以集合`.xib`視圖建立儲存格視圖的類別、分鏡腳本或檔案。 例如：

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    ...
}
```

其中`typeof(CityCollectionViewCell)`提供支援 view 的類別, 並`CityViewDatasource.CardCellId`提供資料格 (或 view) 已清除佇列時所使用的識別碼。

將資料格清除佇列之後, 您可以使用它所代表之專案的資料來設定它, 並返回 [集合] 視圖以供顯示。

<a name="About-Collection-View-Controllers" />

## <a name="about-collection-view-controllers"></a>關於集合視圖控制器

集合視圖控制器 (`UICollectionViewController`) 是專門的視圖控制器 (`UIViewController`), 可提供下列行為:

- 它負責從其分鏡腳本或`.xib`檔案載入集合視圖, 並具現化視圖。 如果是在程式碼中建立, 則會自動建立新的、未設定的集合視圖。
- 一旦載入集合視圖, 控制器就會嘗試從分鏡腳本或`.xib`檔案載入其資料來源和委派。 如果沒有可用的, 它會將本身設定為兩者的來源。
- 確保資料會在第一次顯示時填入集合視圖之前載入, 並在每次後續顯示時重載並清除選取。

此外, 集合視圖控制器會提供可覆寫的方法, 以便用來管理集合視圖的生命週期, `AwakeFromNib`例如`ViewWillDisplay`和。

<a name="Collection-Views-and-Storyboards" />

## <a name="collection-views-and-storyboards"></a>集合視圖和分鏡腳本

在您的 tvOS 應用程式中使用集合視圖的最簡單方式, 是在其分鏡腳本中加入一個。 例如, 我們將建立一個範例應用程式, 其中會顯示影像、標題和 [選取] 按鈕。 如果使用者按一下 [選取] 按鈕, 將會顯示 [集合] 視圖, 讓使用者選擇新的影像。 選擇影像時, 會關閉 [集合] 視圖, 並顯示新的影像和標題。

讓我們執行下列動作:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

    
1. 在 Visual Studio for Mac 中啟動新的**單一 View TvOS 應用程式**。
1. 在 **方案總管**中, 按兩下該`Main.storyboard`檔案, 然後在 iOS 設計工具中開啟它。
1. 將 [影像視圖]、[標籤] 和 [按鈕] 新增至現有的視圖, 並將其設定為如下所示: 

    [![](collection-views-images/collection02.png "範例版面配置")](collection-views-images/collection02.png#lightbox)
1. 在 [**屬性] Explorer**的 [ **Widget]** 索引標籤中, 將**名稱**指派給影像視圖和標籤。 例如： 

    [![](collection-views-images/collection03.png "設定名稱")](collection-views-images/collection03.png#lightbox)
1. 接下來, 將集合視圖控制器拖曳至分鏡腳本: 

    [![](collection-views-images/collection04.png "集合視圖控制器")](collection-views-images/collection04.png#lightbox)
1. 從按鈕控制項拖曳至集合視圖控制器, 然後從快顯視窗中選取 [**推送**]: 

    [![](collection-views-images/collection05.png "從快顯視窗中選取 [推送]")](collection-views-images/collection05.png#lightbox)
1. 當應用程式執行時, 每當使用者按一下按鈕時, 就會顯示集合視圖。
1. 選取 [集合] 視圖, 然後在 [**屬性] Explorer**的 [配置 **]** 索引標籤中輸入下列值: 

    [![](collection-views-images/collection06.png "屬性瀏覽器")](collection-views-images/collection06.png#lightbox)
1. 這會控制個別資料格的大小, 以及資料格和集合視圖外部邊緣之間的框線。
1. 選取 [集合視圖控制器], 並將其`CityCollectionViewController`類別設定為 [ **Widget]** 索引標籤中的: 

    [![](collection-views-images/collection07.png "將類別設定為 CityCollectionViewController")](collection-views-images/collection07.png#lightbox)
1. 選取 [集合] 視圖, 並將其`CityCollectionView`類別設定為 [ **Widget]** 索引標籤中的: 

    [![](collection-views-images/collection08.png "將類別設定為 CityCollectionView")](collection-views-images/collection08.png#lightbox)
1. 選取 [集合視圖] 資料格, 並將`CityCollectionViewCell`其類別設定為 [ **Widget]** 索引標籤中的: 

    [![](collection-views-images/collection09.png "將類別設定為 CityCollectionViewCell")](collection-views-images/collection09.png#lightbox)
1. 在 [**小工具]** 索引卷  標中`Flow` , 確定版面配置為`Vertical` , 且捲軸**方向**為 [集合視圖]: 

    [![](collection-views-images/collection10.png "[Widget] 索引標籤")](collection-views-images/collection10.png#lightbox)
1. 選取 [集合視圖] 資料格, 並在`CityCell` [ **Widget]** 索引標籤中將其**識別**設定為: 

    [![](collection-views-images/collection11.png "將身分識別設定為 CityCell")](collection-views-images/collection11.png#lightbox)
1. 儲存您的變更。
    

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    
1. 在 Visual Studio 中啟動新的**單一 View TvOS 應用程式**。
1. 在 **方案總管**中, 按兩下該`Main.storyboard`檔案, 然後在 iOS 設計工具中開啟它。
1. 將 [影像視圖]、[標籤] 和 [按鈕] 新增至現有的視圖, 並將其設定為如下所示: 

    [![](collection-views-images/collection02vs.png "設定版面配置")](collection-views-images/collection02vs.png#lightbox)
1. 在 [**屬性] Explorer**的 [ **Widget]** 索引標籤中, 將**名稱**指派給影像視圖和標籤。 例如： 

    [![](collection-views-images/collection03vs.png "屬性瀏覽器")](collection-views-images/collection03vs.png#lightbox)
1. 接下來, 將集合視圖控制器拖曳至分鏡腳本: 

    [![](collection-views-images/collection04vs.png "集合視圖控制器")](collection-views-images/collection04vs.png#lightbox)
1. 從按鈕控制項拖曳至集合視圖控制器, 然後從快顯視窗中選取 [**推送**]: 

    [![](collection-views-images/collection05vs.png "從快顯視窗中選取 [推送]")](collection-views-images/collection05vs.png#lightbox)
1. 當應用程式執行時, 每當使用者按一下按鈕時, 就會顯示集合視圖。
1. 選取 [集合 **]** 視圖, 然後在 [屬性] **Explorer**的 [配置] 索引標籤中, 將**寬度**輸入為_361_ , 而**Height**則為_256_ 
1. 這會控制個別資料格的大小, 以及資料格和集合視圖外部邊緣之間的框線。
1. 選取 [集合視圖控制器], 並將其`CityCollectionViewController`類別設定為 [ **Widget]** 索引標籤中的: 

    [![](collection-views-images/collection07vs.png "將類別設定為 CityCollectionViewController")](collection-views-images/collection07vs.png#lightbox)
1. 選取 [集合] 視圖, 並將其`CityCollectionView`類別設定為 [ **Widget]** 索引標籤中的: 

    [![](collection-views-images/collection08vs.png "將類別設定為 CityCollectionView")](collection-views-images/collection08vs.png#lightbox)
1. 選取 [集合視圖] 資料格, 並將`CityCollectionViewCell`其類別設定為 [ **Widget]** 索引標籤中的: 

    [![](collection-views-images/collection09vs.png "將類別設定為 CityCollectionViewCell")](collection-views-images/collection09vs.png#lightbox)
1. 在 [**小工具]** 索引卷  標中`Flow` , 確定版面配置為`Vertical` , 且捲軸**方向**為 [集合視圖]: 

    [![](collection-views-images/collection10vs.png "[Widget] 索引標籤")](collection-views-images/collection10vs.png#lightbox)
1. 選取 [集合視圖] 資料格, 並在`CityCell` [ **Widget]** 索引標籤中將其**識別**設定為: 

    [![](collection-views-images/collection11vs.png "將身分識別設定為 CityCell")](collection-views-images/collection11vs.png#lightbox)
1. 儲存您的變更。
    

-----

如果我們選擇`Custom`了集合視圖的**版面**配置, 我們就可以指定自訂版面配置。 Apple 提供內`UICollectionViewFlowLayout` `UICollectionViewDelegateFlowLayout`建的功能, 可讓您輕鬆地以方格為基礎的版面配置來呈現資料 ( `flow`版面配置樣式會使用這些功能)。 

如需使用分鏡腳本的詳細資訊, 請參閱我們的[Hello, tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Providing-Data-for-the-Collection-View" />

## <a name="providing-data-for-the-collection-view"></a>提供集合視圖的資料

既然我們已將集合視圖 (和集合視圖控制器) 新增至分鏡腳本, 我們需要提供集合的資料。 

<a name="The-Data-Model" />

### <a name="the-data-model"></a>資料模型

首先, 我們要為數據建立一個模型, 其中保存要顯示之影像的檔案名、標題, 以及允許選取該城市的旗標。

`CityInfo`建立類別, 使其看起來如下所示:

```csharp
using System;

namespace tvCollection
{
    public class CityInfo
    {
        #region Computed Properties
        public string ImageFilename { get; set; }
        public string Title { get; set; }
        public bool CanSelect{ get; set; }
        #endregion

        #region Constructors
        public CityInfo (string filename, string title, bool canSelect)
        {
            // Initialize
            this.ImageFilename = filename;
            this.Title = title;
            this.CanSelect = canSelect;
        }
        #endregion
    }
}
```

### <a name="the-collection-view-cell"></a>[集合視圖] 資料格

現在, 我們必須定義每個資料格的資料呈現方式。 `CityCollectionViewCell.cs`編輯檔案 (自動從分鏡腳本檔案建立), 使其看起來如下所示:

```csharp
using System;
using Foundation;
using UIKit;
using CoreGraphics;

namespace tvCollection
{
    public partial class CityCollectionViewCell : UICollectionViewCell
    {
        #region Private Variables
        private CityInfo _city;
        #endregion

        #region Computed Properties
        public UIImageView CityView { get ; set; }
        public UILabel CityTitle { get; set; }

        public CityInfo City {
            get { return _city; }
            set {
                _city = value;
                CityView.Image = UIImage.FromFile (City.ImageFilename);
                CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
                CityTitle.Text = City.Title;
            }
        }
        #endregion

        #region Constructors
        public CityCollectionViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            CityView = new UIImageView(new CGRect(22, 19, 320, 171));
            CityView.AdjustsImageWhenAncestorFocused = true;
            AddSubview (CityView);

            CityTitle = new UILabel (new CGRect (22, 209, 320, 21)) {
                TextAlignment = UITextAlignment.Center,
                TextColor = UIColor.White,
                Alpha = 0.0f
            };
            AddSubview (CityTitle);
        }
        #endregion
    

    }
}
```

針對我們的 tvOS 應用程式, 我們會顯示影像和選擇性標題。 如果無法選取指定的城市, 我們會使用下列程式碼來變暗影像視圖:

```csharp
CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
```

當使用者將包含影像的儲存格納入焦點時, 我們想要在其上使用內建的視差效果, 設定下列屬性:

```csharp
CityView.AdjustsImageWhenAncestorFocused = true;
```

如需導覽和焦點的詳細資訊, 請參閱我們[的使用導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)和[Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md)檔。


<a name="The-Collection-View-Data-Provider" />

### <a name="the-collection-view-data-provider"></a>集合視圖 Data Provider

建立資料模型並定義儲存格配置之後, 讓我們為集合視圖建立資料來源。 資料來源不僅會負責提供支援的資料, 還會清除佇列儲存格來顯示幕幕上的個別資料格。

`CityViewDatasource`建立類別, 使其看起來如下所示:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;
using ObjCRuntime;

namespace tvCollection
{
    public class CityViewDatasource : UICollectionViewDataSource
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Static Constants
        public static NSString CardCellId = new NSString ("CityCell");
        #endregion

        #region Computed Properties
        public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
        public CityCollectionView ViewController { get; set; }
        #endregion

        #region Constructors
        public CityViewDatasource (CityCollectionView controller)
        {
            // Initialize
            this.ViewController = controller;
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities() {

            // Clear existing cities
            Cities.Clear();

            // Add new cities
            Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
            Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
            Cities.Add(new CityInfo("City03.jpg", "Skyline at Night", true));
            Cities.Add(new CityInfo("City04.jpg", "Golden Gate Bridge", true));
            Cities.Add(new CityInfo("City05.jpg", "Roads by Night", true));
            Cities.Add(new CityInfo("City06.jpg", "Church Domes", true));
            Cities.Add(new CityInfo("City07.jpg", "Mountain Lights", true));
            Cities.Add(new CityInfo("City08.jpg", "City Scene", false));
            Cities.Add(new CityInfo("City09.jpg", "House in Winter", true));
            Cities.Add(new CityInfo("City10.jpg", "By the Lake", true));
            Cities.Add(new CityInfo("City11.jpg", "At the Dome", true));
            Cities.Add(new CityInfo("City12.jpg", "Cityscape", true));
            Cities.Add(new CityInfo("City13.jpg", "Model City", true));
            Cities.Add(new CityInfo("City14.jpg", "Taxi, Taxi!", true));
            Cities.Add(new CityInfo("City15.jpg", "On the Sidewalk", true));
            Cities.Add(new CityInfo("City16.jpg", "Midnight Walk", true));
            Cities.Add(new CityInfo("City17.jpg", "Lunchtime Cafe", true));
            Cities.Add(new CityInfo("City18.jpg", "Coffee Shop", true));
            Cities.Add(new CityInfo("City19.jpg", "Rustic Tavern", true));
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            return Cities.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
            var city = Cities [indexPath.Row];

            // Initialize city
            cityCell.City = city;

            return cityCell;
        }
        #endregion
    }
}
```

讓我們詳細查看此類別。 首先, 我們會繼承`UICollectionViewDataSource`自並提供資料格識別碼的快捷方式 (我們在 iOS 設計工具中指派):

```csharp
public static NSString CardCellId = new NSString ("CityCell");
```

接下來, 我們會提供集合資料的儲存體, 並提供類別來填入資料:

```csharp
public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
...

public void PopulateCities() {

    // Clear existing cities
    Cities.Clear();

    // Add new cities
    Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
    Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
    ...
}
```

然後, 我們會`NumberOfSections`覆寫方法, 並傳回我們的集合視圖所擁有的區段數 (專案群組)。 在此情況下, 只有一個:

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    return 1;
}
```

接下來, 我們會使用下列程式碼, 傳回集合中的專案數:

```csharp
public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    return Cities.Count;
}
```

最後, 當集合視圖要求具有下列程式碼時, 我們會將可重複使用的資料格清除佇列:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
    var city = Cities [indexPath.Row];

    // Initialize city
    cityCell.City = city;

    return cityCell;
}
```

取得`CityCollectionViewCell`類型的 [集合視圖] 資料格之後, 我們會在其中填入指定的專案。

<a name="Responding-to-User-Events" />

## <a name="responding-to-user-events"></a>回應使用者事件

因為我們想要讓使用者能夠從集合中選取一個專案, 所以我們需要提供集合視圖委派來處理此互動。 我們需要提供一個方法, 讓我們的呼叫視圖知道使用者所選取的專案。

<a name="The-App-Delegate" />

### <a name="the-app-delegate"></a>應用程式委派

我們需要一種方法, 將目前選取的專案從集合視圖中的相關聯回到呼叫視圖。 我們將在上`AppDelegate`使用自訂屬性。 `AppDelegate.cs`編輯檔案, 並新增下列程式碼:

```csharp
public CityInfo SelectedCity { get; set;} = new CityInfo("City02.jpg", "Turning Circle", true);
```

這會定義屬性, 並設定一開始要顯示的預設城市。 稍後, 我們將使用這個屬性來顯示使用者的選取專案, 並允許變更選取。

<a name="The-Collection-View-Delegate" />

### <a name="the-collection-view-delegate"></a>集合視圖委派

接下來, 將新`CityViewDelegate`的類別新增至專案, 使其看起來如下所示:


```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace tvCollection
{
    public class CityViewDelegate : UICollectionViewDelegateFlowLayout
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public CityViewDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
        {
            return new CGSize (361, 256);
        }

        public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
        {
            if (indexPath == null) {
                return false;
            } else {
                var controller = collectionView as CityCollectionView;
                return controller.Source.Cities[indexPath.Row].CanSelect;
            }
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var controller = collectionView as CityCollectionView;
            App.SelectedCity = controller.Source.Cities [indexPath.Row];

            // Close Collection
            controller.ParentController.DismissViewController(true,null);
        }
        #endregion
    }
}
```

讓我們仔細看一下這個類別。 首先, 我們會繼承`UICollectionViewDelegateFlowLayout`自。 我們繼承自這個類別而不`UICollectionViewDelegate`是的原因是, 我們使用內`UICollectionViewFlowLayout`建的來呈現專案, 而不是自訂的版面配置類型。

接下來, 我們會使用下列程式碼來傳回個別專案的大小:

```csharp
public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
{
    return new CGSize (361, 256);
}
```

然後, 我們會使用下列程式碼來決定指定的儲存格是否可以取得焦點: 

```csharp
public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
{
    if (indexPath == null) {
        return false;
    } else {
        var controller = collectionView as CityCollectionView;
        return controller.Source.Cities[indexPath.Row].CanSelect;
    }
}
```

我們會檢查指定的支援資料片段是否將其`CanSelect`旗標設定為`true` , 並傳回該值。 如需導覽和焦點的詳細資訊, 請參閱我們[的使用導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)和[Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md)檔。

最後, 我們會使用下列程式碼來回應使用者選取一個專案:

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    var controller = collectionView as CityCollectionView;
    App.SelectedCity = controller.Source.Cities [indexPath.Row];

    // Close Collection
    controller.ParentController.DismissViewController(true,null);
}
```

在此, 我們`SelectedCity`會將的`AppDelegate`屬性設定為使用者選取的專案, 並關閉集合視圖控制器, 並返回呼叫我們的視圖。 我們尚未定義集合`ParentController`視圖的屬性, 接下來我們將執行此動作。

<a name="Configuring-the-Collection-View" />

## <a name="configuring-the-collection-view"></a>設定集合視圖

現在, 我們需要編輯集合視圖, 並指派我們的資料來源和委派。 `CityCollectionView.cs`編輯檔案 (從我們的腳本中自動為我們建立), 使其看起來如下所示:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionView : UICollectionView
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public CityViewDatasource Source {
            get { return DataSource as CityViewDatasource;}
        }

        public CityCollectionViewController ParentController { get; set;}
        #endregion

        #region Constructors
        public CityCollectionView (IntPtr handle) : base (handle)
        {
            // Initialize
            RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
            DataSource = new CityViewDatasource (this);
            Delegate = new CityViewDelegate ();
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections ()
        {
            return 1;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
            if (previousItem != null) {
                Animate (0.2, () => {
                    previousItem.CityTitle.Alpha = 0.0f;
                });
            }

            var nextItem = context.NextFocusedView as CityCollectionViewCell;
            if (nextItem != null) {
                Animate (0.2, () => {
                    nextItem.CityTitle.Alpha = 1.0f;
                });
            }
        }
        #endregion
    }
}
```

首先, 我們會提供快捷方式來存取`AppDelegate`我們的: 

```csharp
public static AppDelegate App {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
```

接下來, 我們會提供集合視圖之資料來源的快捷方式, 以及存取集合視圖控制器的屬性 (上面的委派會在使用者進行選取時, 用來關閉集合):

```csharp
public CityViewDatasource Source {
    get { return DataSource as CityViewDatasource;}
}

public CityCollectionViewController ParentController { get; set;}
```

然後, 我們會使用下列程式碼來初始化集合視圖, 並指派我們的儲存格類別、資料來源和委派:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    DataSource = new CityViewDatasource (this);
    Delegate = new CityViewDelegate ();
}
```

最後, 我們希望影像底下的標題只有在使用者反白顯示時才會顯示 (焦點)。 我們使用下列程式碼來執行這項操作:

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
    if (previousItem != null) {
        Animate (0.2, () => {
            previousItem.CityTitle.Alpha = 0.0f;
        });
    }

    var nextItem = context.NextFocusedView as CityCollectionViewCell;
    if (nextItem != null) {
        Animate (0.2, () => {
            nextItem.CityTitle.Alpha = 1.0f;
        });
    }
}
```

我們將前一個專案的 transparence 設定為零 (0), 而下一個專案的 transparence 會獲得焦點到 100%。 這些轉換也會以動畫顯示。


## <a name="configuring-the-collection-view-controller"></a>設定集合視圖控制器

現在, 我們需要在集合視圖上執行最後的設定, 並允許控制器設定我們定義的屬性, 如此一來, 在使用者進行選取之後, 集合視圖就可以關閉。

`CityCollectionViewController.cs`編輯檔案 (從我們的腳本自動建立), 使其看起來如下所示:

```csharp
// This file has been autogenerated from a class added in the UI designer.

using System;

using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionViewController : UICollectionViewController
    {
        #region Computed Properties
        public CityCollectionView Collection {
            get { return CollectionView as CityCollectionView; }
        }
        #endregion

        #region Constructors
        public CityCollectionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Save link to controller
            Collection.ParentController = this;
        }
        #endregion
    }
}

```

## <a name="putting-it-all-together"></a>將它全部放在一起 

既然我們已將所有元件放在一起, 以填入並控制我們的集合視圖, 我們必須對主要視圖進行最後的編輯, 才能將所有專案都整合在一起。

`ViewController.cs`編輯檔案 (從我們的腳本自動建立), 使其看起來如下所示:

```csharp
using System;
using Foundation;
using UIKit;
using tvCollection;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update image with the currently selected one
            CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
            BackgroundView.Image = CityView.Image;
            CityTitle.Text = App.SelectedCity.Title;
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion
    }
}
```

下列程式碼一開始會從`SelectedCity`的屬性`AppDelegate`顯示選取的專案, 並在使用者從集合視圖中進行選取時重新顯示它:

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update image with the currently selected one
    CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
    BackgroundView.Image = CityView.Image;
    CityTitle.Text = App.SelectedCity.Title;
}
```

<a name="Testing-the-app" />

## <a name="testing-the-app"></a>測試應用程式

當所有專案都備妥時, 如果您建立並執行應用程式, 主視圖會顯示為預設城市:

[![](collection-views-images/run01.png "主畫面")](collection-views-images/run01.png#lightbox)

如果使用者按一下 [**選取視圖**] 按鈕, 將會顯示 [集合] 視圖:

[![](collection-views-images/run02.png "集合視圖")](collection-views-images/run02.png#lightbox)

其`CanSelect`屬性設為`false`的任何城市都會顯示為灰色, 使用者將無法對其設定焦點。 當使用者反白顯示專案時 (將其設為焦點), 會顯示標題, 而且可以使用視差效果奧妙以3D 傾斜影像。

當使用者按一下選取影像時, 會關閉 [集合] 視圖, 並以新的影像重新顯示主視圖:

[![](collection-views-images/run03.png "主畫面上的新影像")](collection-views-images/run03.png#lightbox)

<a name="Creating-Custom-Layout-and-Reordering-Items" />

## <a name="creating-custom-layout-and-reordering-items"></a>建立自訂版面配置和重新排列專案

使用集合視圖的其中一個重要功能是建立自訂版面配置的能力。 因為 tvOS 繼承自 iOS, 所以建立自訂配置的程式是相同的。 如需詳細資訊, 請參閱[集合視圖簡介](~/ios/user-interface/controls/uicollectionview.md)檔。

最近新增至 iOS 9 的集合視圖, 能夠輕鬆地允許重新排序集合中的專案。 同樣地, 因為 tvOS 9 是 iOS 9 的子集, 所以這是以同樣的方式完成。 如需詳細資訊, 請參閱[集合視圖變更](~/ios/user-interface/controls/uicollectionview.md)檔。


<a name="Summary" />

## <a name="summary"></a>總結

本文涵蓋在 tvOS 應用程式內設計和使用集合視圖。 首先, 它會討論群組成集合視圖的所有元素。 接下來, 它會示範如何使用分鏡腳本來設計和執行集合視圖。 最後, 會提供有關建立自訂版面配置和重新排列專案之資訊的連結。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
