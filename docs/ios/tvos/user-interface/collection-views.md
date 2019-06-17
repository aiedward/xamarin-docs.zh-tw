---
title: 使用 tvOS 在 Xamarin 中的集合檢視
description: 本文件說明如何使用 Xamarin 建置的 tvOS 應用程式中的集合檢視使用。 它涵蓋了集合檢視版面配置，建立的儲存格和增補的檢視，回應使用者事件和更多功能。
ms.prod: xamarin
ms.assetid: 5125C4C7-2DDF-4C19-A362-17BB2B079178
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: f815afa6b1abb15348019b0c53333b4acb054008
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60933720"
---
# <a name="working-with-tvos-collection-views-in-xamarin"></a>使用 tvOS 在 Xamarin 中的集合檢視

集合檢視可以使用任意的版面配置來顯示讓群組的內容。 使用內建支援，可讓您輕鬆建立類似方格或線性配置，同時也支援自訂的版面配置。

[![](collection-views-images/collection01.png "範例集合檢視")](collection-views-images/collection01.png#lightbox)

集合檢視會維護一組以提供使用者互動和集合的內容中使用委派和資料來源的項目。 因為集合檢視以獨立於本身的檢視版面配置子系統為基礎，提供不同的版面配置可以輕鬆地變更集合檢視的資料上即時的呈現方式。

<a name="About-Collection-Views" />

## <a name="about-collection-views"></a>如需集合檢視

集合檢視上方所述 (`UICollectionView`) 管理的已排序的集合的項目，並提供可自訂的版面配置中的這些項目。 集合檢視在資料表檢視類似的方式 (`UITableView`)，但它們可以使用出現的項目配置，不只是單一資料行中。

使用集合檢視 tvOS 中，您的應用程式時負責提供使用資料來源的集合相關聯的資料 (`UICollectionViewDataSource`)。 集合檢視的資料可以選擇性地組織並顯示分成不同的群組 （區段）。

集合檢視會顯示在螢幕上的資料格的個別項目 (`UICollectionViewCell`)，提供指定的一種資訊 （例如影像和標題） 集合中的呈現方式。

（選擇性） 增補的檢視可以加入到集合檢視的簡報，以作為標頭和頁尾的區段和資料格。 集合檢視的配置會負責定義這些檢視，以及個別儲存格的位置。

集合檢視可以回應使用者互動使用委派 (`UICollectionViewDelegate`)。 這個委派也會負責判斷如果給定資料格可以取得焦點時，如果已反白顯示資料格，或其中一個已選取的選項。 在某些情況下，委派會判斷個別的儲存格的大小。

<a name="Collection-View-Layouts" />

## <a name="collection-view-layouts"></a>集合檢視版面配置

集合檢視的主要功能是其它所要呈現的資料和其版面配置區隔。 集合檢視版面配置 (`UICollectionViewLayout`) 會負責提供組織和儲存格 （和任何增補的檢視） 的位置與集合檢視的螢幕上的簡報中。

個別儲存格藉由從附加的資料來源的集合檢視是然後排列並顯示指定集合檢視版面配置。

建立集合檢視時，通常會提供集合的檢視版面配置。 不過，您可以隨時變更集合的檢視版面配置，而且集合檢視的資料的螢幕上呈現方式會自動更新使用新的版面配置提供。

集合檢視版面配置提供數種方法，可用來以動畫顯示兩個不同的版面配置 （藉由任何動畫完成時，預設值） 之間的轉換。 此外，若要進一步建立動畫的版面配置中的變更會導致使用者互動的筆勢辨識器可以處理集合檢視版面配置。

<a name="Creating-Cells-and-Supplementary-Views" />

## <a name="creating-cells-and-supplementary-views"></a>建立儲存格和增補的檢視

集合檢視的資料來源不是只負責提供項目，但是也支援集合的資料用來顯示內容的資料格。

集合檢視設計用來處理大型集合的項目，因為可以從佇列中清除並重複使用來防止造成滿溢記憶體限制個別儲存格。 有兩個不同的方法，清除佇列的檢視：

- `DequeueReusableCell` -建立或傳回指定的類型 （如應用程式的分鏡腳本中指定） 的資料格。
- `DequeueReusableSupplementaryView` -建立或傳回指定的型別 （如應用程式的分鏡腳本中指定） 的增補檢視。

然後再呼叫其中一種方法，您必須註冊類別中，分鏡腳本或`.xib`檔案用來建立集合檢視儲存格的檢視。 例如：

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    ...
}
```

何處`typeof(CityCollectionViewCell)`提供類別，以支援檢視和`CityViewDatasource.CardCellId`提供資料格 （或檢視表） 已從佇列清除時所使用的識別碼。

儲存格已從佇列清除之後，您會設定它代表項目的資料，並返回 顯示的集合檢視。

<a name="About-Collection-View-Controllers" />

## <a name="about-collection-view-controllers"></a>關於集合檢視控制器

集合檢視控制器 (`UICollectionViewController`) 是特製化的檢視控制器 (`UIViewController`)，提供下列行為：

- 它會負責載入集合檢視從其分鏡腳本或`.xib`檔案和具現化檢視。 如果在程式碼中建立，它會自動建立新的、 未設定的集合檢視。
- 一旦載入集合檢視時，控制器會嘗試載入其資料來源和委派從分鏡腳本或`.xib`檔案。 如果沒有任何可用時，它會設定本身做為兩者的來源。
- 可確保資料會載入集合檢視上顯示的第一個填入之前和重新載入並清除每個後續的顯示器上選取。

此外，集合檢視控制器會提供可覆寫的方法，可用來管理的集合檢視生命週期，例如`AwakeFromNib`和`ViewWillDisplay`。

<a name="Collection-Views-and-Storyboards" />

## <a name="collection-views-and-storyboards"></a>集合檢視 」 和 「 分鏡腳本

使用集合檢視中您 Xamarin.tvOS 應用程式中，最簡單方式是將其加入至分鏡腳本。 快速的範例中，我們要建立範例應用程式所呈現影像、 標題和 [選取] 按鈕。 如果使用者按一下 [選取] 按鈕，將集合檢視隨即出現，讓使用者選擇新的映像。 映像選擇時，此集合檢視已關閉，且將會顯示新的映像及標題。

讓我們執行下列作業：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

    
1. 啟動新**單一檢視 tvOS 應用程式**在 Visual Studio for mac。
1. 在 **方案總管**，連按兩下`Main.storyboard`檔案，並在 iOS 設計工具中開啟它。
1. 將映像檢視、 標籤和按鈕新增至現有的檢視，並將其設定為如下所示： 

    [![](collection-views-images/collection02.png "範例版面配置")](collection-views-images/collection02.png#lightbox)
1. 指派**名稱**映像檢視與中的標籤**小工具 索引標籤**的**屬性總管**。 例如:  

    [![](collection-views-images/collection03.png "設定名稱")](collection-views-images/collection03.png#lightbox)
1. 接下來，拖曳到 分鏡腳本的集合檢視控制器： 

    [![](collection-views-images/collection04.png "集合檢視控制器")](collection-views-images/collection04.png#lightbox)
1. 按住 control 再拖曳 按鈕從集合檢視控制器，然後選取**推播**從快顯視窗： 

    [![](collection-views-images/collection05.png "從快顯視窗中選取 推播")](collection-views-images/collection05.png#lightbox)
1. 執行應用程式時，這會讓集合檢視會顯示，每當使用者按一下按鈕。
1. 選取集合檢視，並輸入下列值**版面配置 索引標籤**的**屬性總管**: 

    [![](collection-views-images/collection06.png "屬性總管")](collection-views-images/collection06.png#lightbox)
1. 這會控制個別儲存格和資料格與集合檢視的外部邊緣之間的框線的大小。
1. 選取的集合檢視控制器，並將其類別設定為`CityCollectionViewController`中**小工具 索引標籤**: 

    [![](collection-views-images/collection07.png "設 CityCollectionViewController 類別")](collection-views-images/collection07.png#lightbox)
1. 選取 集合 檢視，並將其類別設定為`CityCollectionView`中**小工具 索引標籤**: 

    [![](collection-views-images/collection08.png "設 CityCollectionView 類別")](collection-views-images/collection08.png#lightbox)
1. 選取集合檢視儲存格，並將其類別設定為`CityCollectionViewCell`中**小工具 索引標籤**: 

    [![](collection-views-images/collection09.png "設 CityCollectionViewCell 類別")](collection-views-images/collection09.png#lightbox)
1. 在**小工具 索引標籤**確保**版面配置**是`Flow`而**捲軸方向**是`Vertical`集合檢視： 

    [![](collection-views-images/collection10.png "小工具 索引標籤")](collection-views-images/collection10.png#lightbox)
1. 選取集合檢視儲存格，並設定其**身分識別**要`CityCell`中**小工具 索引標籤**: 

    [![](collection-views-images/collection11.png "CityCell 來設定身分識別")](collection-views-images/collection11.png#lightbox)
1. 儲存您的變更。
    

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    
1. 啟動新**單一檢視 tvOS 應用程式**Visual Studio 中。
1. 在 **方案總管**，連按兩下`Main.storyboard`檔案，並在 iOS 設計工具中開啟它。
1. 將映像檢視、 標籤和按鈕新增至現有的檢視，並將其設定為如下所示： 

    [![](collection-views-images/collection02vs.png "設定版面配置")](collection-views-images/collection02vs.png#lightbox)
1. 指派**名稱**映像檢視與中的標籤**小工具 索引標籤**的**屬性總管**。 例如： 

    [![](collection-views-images/collection03vs.png "屬性總管")](collection-views-images/collection03vs.png#lightbox)
1. 接下來，拖曳到 分鏡腳本的集合檢視控制器： 

    [![](collection-views-images/collection04vs.png "集合檢視控制器")](collection-views-images/collection04vs.png#lightbox)
1. 按住 control 再拖曳 按鈕從集合檢視控制器，然後選取**推播**從快顯視窗： 

    [![](collection-views-images/collection05vs.png "從快顯視窗中選取 推播")](collection-views-images/collection05vs.png#lightbox)
1. 執行應用程式時，這會讓集合檢視會顯示，每當使用者按一下按鈕。
1. 選取 集合 檢視並在**版面配置 索引標籤**的**屬性總管**輸入**寬度**作為_361_和**高度**做為_256_ 
1. 這會控制個別儲存格和資料格與集合檢視的外部邊緣之間的框線的大小。
1. 選取的集合檢視控制器，並將其類別設定為`CityCollectionViewController`中**小工具 索引標籤**: 

    [![](collection-views-images/collection07vs.png "設 CityCollectionViewController 類別")](collection-views-images/collection07vs.png#lightbox)
1. 選取 集合 檢視，並將其類別設定為`CityCollectionView`中**小工具 索引標籤**: 

    [![](collection-views-images/collection08vs.png "設 CityCollectionView 類別")](collection-views-images/collection08vs.png#lightbox)
1. 選取集合檢視儲存格，並將其類別設定為`CityCollectionViewCell`中**小工具 索引標籤**: 

    [![](collection-views-images/collection09vs.png "設 CityCollectionViewCell 類別")](collection-views-images/collection09vs.png#lightbox)
1. 在**小工具 索引標籤**確保**版面配置**是`Flow`而**捲軸方向**是`Vertical`集合檢視： 

    [![](collection-views-images/collection10vs.png "T 小工具 索引標籤")](collection-views-images/collection10vs.png#lightbox)
1. 選取集合檢視儲存格，並設定其**身分識別**要`CityCell`中**小工具 索引標籤**: 

    [![](collection-views-images/collection11vs.png "CityCell 來設定身分識別")](collection-views-images/collection11vs.png#lightbox)
1. 儲存您的變更。
    

-----

如果我們已選擇`Custom`集合檢視**版面配置**，我們也可以指定自訂的版面配置。 Apple 提供的內建`UICollectionViewFlowLayout`並`UICollectionViewDelegateFlowLayout`，可以輕鬆地展示方格配置中的資料 (會使用這些`flow`版面配置樣式)。 

如需有關使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Providing-Data-for-the-Collection-View" />

## <a name="providing-data-for-the-collection-view"></a>提供資料的集合檢視

既然我們已新增至我們的分鏡腳本我們集合檢視 （和集合檢視控制器），我們需要提供集合中的資料。 

<a name="The-Data-Model" />

### <a name="the-data-model"></a>資料模型

首先，我們要為我們保留要顯示標題和允許選取縣 （市） 旗標之影像的檔案名稱的資料建立模型。

建立`CityInfo`類別，並使它看起來如下：

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

### <a name="the-collection-view-cell"></a>集合檢視儲存格

現在，我們需要定義資料的每個資料格的呈現方式。 編輯`CityCollectionViewCell.cs`（自動為您建立從您的分鏡腳本檔案） 的檔案，使它看起來如下所示：

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

我們 tvOS 應用程式中，我們將要顯示的映像和選擇性的標題。 如果無法選取位於給定的城市，我們會變暗的映像檢視，使用下列程式碼：

```csharp
CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
```

當使用者包含映像的儲存格處於焦點中時，我們想要使用內建視差效果，它會設定下列屬性：

```csharp
CityView.AdjustsImageWhenAncestorFocused = true;
```

如需有關導覽和焦點的詳細資訊，請參閱我們[使用導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)並[Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md)文件。


<a name="The-Collection-View-Data-Provider" />

### <a name="the-collection-view-data-provider"></a>集合檢視的資料提供者

我們建立的資料模型和定義我們的儲存格配置，讓我們建立我們的集合檢視的資料來源。 資料來源會負責不僅提供備份資料，同時也清除佇列来顯示在螢幕上的個別儲存格的儲存格。

建立`CityViewDatasource`類別，並使它看起來如下：

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

可讓查看詳細資料中的這個類別。 首先，我們會繼承`UICollectionViewDataSource`和提供的資料格識別碼 （我們指派 iOS 設計工具中） 的捷徑：

```csharp
public static NSString CardCellId = new NSString ("CityCell");
```

接下來我們會為我們收集的資料提供儲存體，並提供類別來填入資料：

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

然後我們會覆寫`NumberOfSections`方法，並傳回集合檢視的區段 （的項目群組） 的數字。 在此情況下，有一個只有：

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    return 1;
}
```

接下來，我們會使用下列程式碼集合中傳回的項目數：

```csharp
public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    return Cities.Count;
}
```

最後，我們從佇列中清除可重複使用的儲存格集合檢視要求為下列程式碼時：

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

我們取得的集合檢視儲存格後我們`CityCollectionViewCell`類型，我們填入指定的項目。

<a name="Responding-to-User-Events" />

## <a name="responding-to-user-events"></a>回應使用者事件

因為我們想讓使用者能夠從我們要在集合中選取項目，我們需要提供集合檢視委派以處理這種互動。 我們需要提供一種方法讓我們知道何種項目使用者呼叫的檢視已選取。

<a name="The-App-Delegate" />

### <a name="the-app-delegate"></a>應用程式委派

我們需要用來在目前選取的項目從集合檢視往回關聯到呼叫的檢視。 我們將使用自訂的屬性上我們`AppDelegate`。 編輯`AppDelegate.cs`檔案，並新增下列程式碼：

```csharp
public CityInfo SelectedCity { get; set;} = new CityInfo("City02.jpg", "Turning Circle", true);
```

這會定義屬性，並設定一開始會顯示預設縣 （市）。 稍後，我們將使用這個屬性，以顯示使用者的選取項目，並允許變更選取。

<a name="The-Collection-View-Delegate" />

### <a name="the-collection-view-delegate"></a>集合檢視委派

接下來，新增 新`CityViewDelegate`類別至專案，並使它看起來如下：


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

讓我們看看這個類別。 首先，我們會繼承`UICollectionViewDelegateFlowLayout`。 我們從此類別繼承的原因而非`UICollectionViewDelegate`是，我們會使用內建`UICollectionViewFlowLayout`顯示我們的項目並不是自訂的版面配置的型別。

接下來，我們會傳回個別的項目使用下列程式碼的大小：

```csharp
public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
{
    return new CGSize (361, 256);
}
```

然後，我們決定是否指定的資料格可以取得焦點，使用下列程式碼： 

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

我們會檢查指定的一種支援的資料是否有其`CanSelect`旗標設為`true`並傳回值。 如需有關導覽和焦點的詳細資訊，請參閱我們[使用導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)並[Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md)文件。

最後，我們會回應使用者選取為下列程式碼項目：

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    var controller = collectionView as CityCollectionView;
    App.SelectedCity = controller.Source.Cities [indexPath.Row];

    // Close Collection
    controller.ParentController.DismissViewController(true,null);
}
```

我們在這裡設定`SelectedCity`屬性的我們`AppDelegate`，選取的使用者，我們關閉集合檢視控制器，將傳回給呼叫我們的檢視項目。 我們尚未定義`ParentController`尚未我們集合檢視的屬性，我們會下一步。

<a name="Configuring-the-Collection-View" />

## <a name="configuring-the-collection-view"></a>設定集合檢視

現在我們需要編輯集合檢視，並將我們的資料來源和委派的指派。 編輯`CityCollectionView.cs`（為我們自動建立從我們的分鏡腳本） 的檔案，使它看起來如下所示：

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

首先，我們提供捷徑，即可存取我們`AppDelegate`: 

```csharp
public static AppDelegate App {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
```

接下來，我們提供捷徑來存取集合檢視控制器 （由上述我們委派的使用者進行選取時，關閉集合） 的集合檢視的資料來源和屬性：

```csharp
public CityViewDatasource Source {
    get { return DataSource as CityViewDatasource;}
}

public CityCollectionViewController ParentController { get; set;}
```

然後，我們會使用下列程式碼來初始化集合檢視，並將我們的儲存格類別、 資料來源和委派指派：

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    DataSource = new CityViewDatasource (this);
    Delegate = new CityViewDelegate ();
}
```

最後，我們希望下只會顯示當使用者擁有它反白顯示的影像標題 （焦點）。 我們這麼做，以下列程式碼：

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

我們將失去焦點設為零 (0) 的上一個項目 transparence 和 transparence 的下一個項目取得焦點移到 100%。 這些轉換取得以及動畫。


## <a name="configuring-the-collection-view-controller"></a>設定集合檢視控制器

現在我們需要進行最後的設定，在我們的集合檢視，並允許設定屬性，我們定義，所以使用者進行選擇之後，就可以關閉此集合檢視控制器。

編輯`CityCollectionViewController.cs`（從我們的分鏡腳本的自動建立） 的檔案，使它看起來如下所示：

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

## <a name="putting-it-all-together"></a>總結 

既然我們已經擁有所有組合的組件來填入和控制集合檢視中，我們需要進行的最後一個編輯到我們的主要檢視，以將所有項目結合在一起。

編輯`ViewController.cs`（從我們的分鏡腳本的自動建立） 的檔案，使它看起來如下所示：

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

下列程式碼一開始會顯示選取的項目，從`SelectedCity`屬性`AppDelegate`並重新將使用者所做的選取項目從集合檢視時顯示：

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

所需的一切就緒時，如果您建置並執行應用程式，主要檢視會顯示預設城市：

[![](collection-views-images/run01.png "主畫面")](collection-views-images/run01.png#lightbox)

如果使用者按一下**選取 [檢視**集合檢視] 按鈕，將會顯示：

[![](collection-views-images/run02.png "集合檢視")](collection-views-images/run02.png#lightbox)

有任何縣 （市) 其`CanSelect`屬性設定為`false`將會顯示呈現灰色，而使用者不能將焦點設定至它。 當使用者反白顯示項目 (讓單元的焦點) 顯示標題，供他們用視差效果可微妙的地方傾斜影像 3D 中。

當使用者按一下選取的映像時，此集合檢視已關閉，且在主要檢視會重新顯示新的映像：

[![](collection-views-images/run03.png "在 [首頁] 螢幕上的新映像")](collection-views-images/run03.png#lightbox)

<a name="Creating-Custom-Layout-and-Reordering-Items" />

## <a name="creating-custom-layout-and-reordering-items"></a>建立自訂的版面配置和重新排序項目

使用集合檢視的主要功能之一是能夠建立自訂的版面配置。 因為 tvOS 繼承自 iOS，建立自訂的版面配置的程序都相同。 請參閱我們[集合檢視簡介](~/ios/user-interface/controls/uicollectionview.md)文件的詳細資訊。

最近加入到集合檢視 iOS 9 是能夠輕鬆地進行重新排列集合中的項目。 同樣地，由於 tvOS 9 是 iOS 9 的子集，這是這些相同的方式。 請參閱我們[集合檢視變更](~/ios/user-interface/controls/uicollectionview.md)文件，如需詳細資訊。


<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計和操作集合檢視內 Xamarin.tvOS 應用程式。 首先，它討論的所有元素組成的集合檢視。 接下來，它會示範如何設計和實作集合檢視使用分鏡腳本。 最後，提供資訊的連結上建立自訂的版面配置和重新排列項目。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
