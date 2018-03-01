---
title: "使用集合檢視"
description: "本文涵蓋設計和集合檢視 Xamarin.tvOS 應用程式內使用。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5125C4C7-2DDF-4C19-A362-17BB2B079178
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: f0201e114f55e0610aceb68f98fae60a801afc68
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-collection-views"></a>使用集合檢視

_本文涵蓋設計和集合檢視 Xamarin.tvOS 應用程式內使用。_

集合檢視使用任意的版面配置來顯示可讓群組的內容。 使用內建支援，可讓您輕鬆建立類似格線或線性配置，同時也支援自訂版面配置。

[ ![](collection-views-images/collection01.png "設定集合檢視的範例")](collection-views-images/collection01.png)

集合檢視會維護使用委派與資料來源，以提供使用者互動和集合的內容項目的集合。 因為集合檢視的檢視表本身無關的版面配置子系統上，提供不同的版面配置可以輕鬆地變更集合檢視的資料上作業的呈現方式。

<a name="About-Collection-Views" />

## <a name="about-collection-views"></a>關於集合檢視

如上所述，集合檢視 (`UICollectionView`) 管理的項目已排序的集合並可自訂的版面配置顯示這些項目。 集合檢視在資料表檢視類似的方式 (`UITableView`)，不過它們可以使用版面配置，以顯示項目不只是單一資料行中。

使用集合檢視中時 tvOS，您的應用程式會負責提供與使用資料來源的集合相關聯的資料 (`UICollectionViewDataSource`)。 集合檢視的資料可以選擇性地組織並顯示分成不同的群組 （區段）。

集合檢視會呈現在螢幕上的資料格的個別項目 (`UICollectionViewCell`)，提供特定資訊 （例如映像和其標題） 集合中的呈現方式。

（選擇性） 補充檢視可以加入到頁首和頁尾做為區段和資料格的集合檢視的簡報。 集合檢視的配置會負責定義這些檢視，以及個別儲存格的位置。

集合檢視可以回應使用者互動使用委派 (`UICollectionViewDelegate`)。 此委派也會負責判斷是否有已反白顯示資料格，給定資料格可以取得焦點，或如果已選取其中一個。 在某些情況下，委派會決定個別資料格的大小。

<a name="Collection-View-Layouts" />

## <a name="collection-view-layouts"></a>集合檢視的版面配置

集合檢視的主要功能是它所要呈現的資料和其版面配置之間的分隔。 集合檢視配置 (`UICollectionViewLayout`) 會負責提供組織和儲存格 （以及任何補充檢視） 的位置與集合檢視的螢幕上的簡報中。

個別儲存格所建立的附加資料來源的集合檢視，會排列然後顯示指定集合檢視配置。

當建立集合檢視，通常會提供集合檢視配置。 不過，您可以隨時變更集合檢視版面配置，並呈現在螢幕上集合檢視的資料將會自動更新使用提供的新配置。

集合檢視配置會提供數種方法可以用來繪製兩個不同的版面配置 （依預設是無動畫） 之間的轉換。 此外，以進一步動畫顯示版面配置中的變更所導致的使用者互動的筆勢辨識器可以處理集合檢視配置。

<a name="Creating-Cells-and-Supplementary-Views" />

## <a name="creating-cells-and-supplementary-views"></a>正在建立儲存格和增補的檢視

集合檢視的資料來源不是只負責提供項目，但是也支援集合的資料用來顯示內容的資料格。

因為集合檢視設計以處理大型集合的項目，可以清除佇列並重複使用來防止造成滿溢記憶體限制個別儲存格。 有兩個不同的方法的 pop receipt 都檢視：

- `DequeueReusableCell` -建立，或傳回指定類型 （如應用程式的分鏡腳本中指定） 的儲存格。
- `DequeueReusableSupplementaryView` -建立或傳回指定的型別 （如應用程式的分鏡腳本中指定） 的補充檢視。

然後再呼叫其中一種方法，您必須註冊類別分鏡腳本或`.xib`用來建立與集合檢視的儲存格的檢視檔案。 例如: 

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    ...
}
```

其中`typeof(CityCollectionViewCell)`提供支援的檢視類別和`CityViewDatasource.CardCellId`提供資料格 （或檢視表） 會從佇列清除時使用的識別碼。

資料格從佇列中清除之後，您會設定它代表項目的資料，並返回集合檢視的顯示。

<a name="About-Collection-View-Controllers" />

## <a name="about-collection-view-controllers"></a>關於集合檢視的控制器

集合檢視控制器 (`UICollectionViewController`) 是特殊的檢視控制器 (`UIViewController`) 提供下列行為：

- 它會負責載入集合檢視從其分鏡腳本或`.xib`檔案和具現化檢視。 如果建立在程式碼中，它會自動建立新的未設定集合檢視。
- 一旦載入集合檢視，控制器會嘗試從 分鏡腳本載入其資料來源和委派或`.xib`檔案。 如果都無法使用，會將本身做為這兩者的來源。
- 可確保將資料載入集合檢視上顯示，第一個填入之前和重新載入，並清除每個後續的顯示器上選取。

此外，集合檢視控制器提供可覆寫方法，可以用於管理集合檢視的生命週期，例如`AwakeFromNib`和`ViewWillDisplay`。

<a name="Collection-Views-and-Storyboards" />

## <a name="collection-views-and-storyboards"></a>集合檢視和分鏡腳本

若要使用集合中的檢視 Xamarin.tvOS 應用程式，最簡單的方式是加入至其分鏡腳本。 在簡單的範例，我們要建立範例應用程式所呈現的映像、 標題和選取的按鈕。 如果使用者按一下 [選取] 按鈕，將集合檢視隨即出現，讓使用者選擇新的映像。 選擇映像後，關閉集合檢視，而將會顯示新的映像和標題。

讓我們執行下列作業：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

    
1. 啟動新**應用程式的單一檢視 tvOS**在 Visual Studio for mac。
1. 在**方案總管 中**，連按兩下`Main.storyboard`檔案，並在 iOS 設計工具中開啟它。
1. 將影像檢視、 標籤和按鈕加入至現有的檢視，並將其設定為如下所示： 

    [ ![](collection-views-images/collection02.png "範例版面配置")](collection-views-images/collection02.png)
1. 指派**名稱**映像檢視與中的標籤**Widget 索引標籤**的**屬性總管**。 例如:  

    [ ![](collection-views-images/collection03.png "設定名稱")](collection-views-images/collection03.png)
1. 下一步，拖曳到 分鏡腳本的集合檢視控制器： 

    [ ![](collection-views-images/collection04.png "集合檢視控制器")](collection-views-images/collection04.png)
1. 控制項拖曳按鈕到集合檢視控制站，然後選取**推送**從快顯視窗： 

    [ ![](collection-views-images/collection05.png "從快顯功能表中選取 推播")](collection-views-images/collection05.png)
1. 當應用程式執行時，這會使得每當使用者按一下按鈕時，可顯示的集合檢視。
1. 選取集合檢視，並輸入下列值**配置 索引標籤**的**屬性總管**: 

    [ ![](collection-views-images/collection06.png "屬性總管")](collection-views-images/collection06.png)
1. 這會控制個別儲存格和資料格，而集合檢視的外部邊緣之間的框線的大小。
1. 選取的集合檢視的控制器，並將它的類別設定為`CityCollectionViewController`中**Widget 索引標籤**: 

    [ ![](collection-views-images/collection07.png "將類別設 CityCollectionViewController")](collection-views-images/collection07.png)
1. 選取的集合檢視，並將它的類別設定為`CityCollectionView`中**Widget 索引標籤**: 

    [ ![](collection-views-images/collection08.png "將類別設 CityCollectionView")](collection-views-images/collection08.png)
1. 選取的集合檢視的資料格，並將它的類別設定為`CityCollectionViewCell`中**Widget 索引標籤**: 

    [ ![](collection-views-images/collection09.png "將類別設 CityCollectionViewCell")](collection-views-images/collection09.png)
1. 在**Widget 索引標籤**確定**配置**是`Flow`和**捲軸方向**是`Vertical`集合檢視： 

    [ ![](collection-views-images/collection10.png "[小工具] 索引標籤")](collection-views-images/collection10.png)
1. 選取集合檢視儲存格，並設定其**識別**至`CityCell`中**Widget 索引標籤**: 

    [ ![](collection-views-images/collection11.png "將識別設定 CityCell")](collection-views-images/collection11.png)
1. 儲存您的變更。
    

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

    
1. 啟動新**應用程式的單一檢視 tvOS** Visual Studio 中。
1. 在**方案總管 中**，連按兩下`Main.storyboard`檔案，並在 iOS 設計工具中開啟它。
1. 將影像檢視、 標籤和按鈕加入至現有的檢視，並將其設定為如下所示： 

    [ ![](collection-views-images/collection02vs.png "設定版面配置")](collection-views-images/collection02vs.png)
1. 指派**名稱**映像檢視與中的標籤**Widget 索引標籤**的**屬性總管**。 例如:  

    [ ![](collection-views-images/collection03vs.png "屬性總管")](collection-views-images/collection03vs.png)
1. 下一步，拖曳到 分鏡腳本的集合檢視控制器： 

    [ ![](collection-views-images/collection04vs.png "集合檢視控制器")](collection-views-images/collection04vs.png)
1. 控制項拖曳按鈕到集合檢視控制站，然後選取**推送**從快顯視窗： 

    [ ![](collection-views-images/collection05vs.png "從快顯功能表中選取 推播")](collection-views-images/collection05vs.png)
1. 當應用程式執行時，這會使得每當使用者按一下按鈕時，可顯示的集合檢視。
1. 選取 集合 檢視和**配置 索引標籤**的**屬性總管**輸入**寬度**為_361_和**高度**為_256_ 
1. 這會控制個別儲存格和資料格，而集合檢視的外部邊緣之間的框線的大小。
1. 選取的集合檢視的控制器，並將它的類別設定為`CityCollectionViewController`中**Widget 索引標籤**: 

    [ ![](collection-views-images/collection07vs.png "將類別設 CityCollectionViewController")](collection-views-images/collection07vs.png)
1. 選取的集合檢視，並將它的類別設定為`CityCollectionView`中**Widget 索引標籤**: 

    [ ![](collection-views-images/collection08vs.png "將類別設 CityCollectionView")](collection-views-images/collection08vs.png)
1. 選取的集合檢視的資料格，並將它的類別設定為`CityCollectionViewCell`中**Widget 索引標籤**: 

    [ ![](collection-views-images/collection09vs.png "將類別設 CityCollectionViewCell")](collection-views-images/collection09vs.png)
1. 在**Widget 索引標籤**確定**配置**是`Flow`和**捲軸方向**是`Vertical`集合檢視： 

    [ ![](collection-views-images/collection10vs.png "T Widget 索引標籤")](collection-views-images/collection10vs.png)
1. 選取集合檢視儲存格，並設定其**識別**至`CityCell`中**Widget 索引標籤**: 

    [ ![](collection-views-images/collection11vs.png "將識別設定 CityCell")](collection-views-images/collection11vs.png)
1. 儲存您的變更。
    

-----

如果我們選擇`Custom`集合檢視**配置**，我們無法指定自訂版面配置。 Apple 提供的內建`UICollectionViewFlowLayout`和`UICollectionViewDelegateFlowLayout`方格配置中可以輕鬆地呈現資料 (會使用這些`flow`版面配置樣式)。 

如需使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Providing-Data-for-the-Collection-View" />

## <a name="providing-data-for-the-collection-view"></a>提供資料的集合檢視

既然我們已加入至我們的分鏡腳本我們集合檢視 （和集合檢視的控制器），我們需要提供集合中的資料。 

<a name="The-Data-Model" />

### <a name="the-data-model"></a>資料模型

首先，我們將建立保存檔案名稱指定為要顯示的標題和允許縣 （市） 選取的旗標的影像資料的模型。

建立`CityInfo`類別，並讓它看起來如下所示：

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

現在我們需要定義資料的每個資料格的呈現方式。 編輯`CityCollectionViewCell.cs`（自動建立從 分鏡腳本檔案） 的檔案並看起來如下所示：

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

我們 tvOS 應用程式中，我們將會顯示的映像和選擇性的標題。 如果無法選取指定的縣 （市），我們會變暗的影像檢視，使用下列程式碼：

```csharp
CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
```

當使用者包含映像的儲存格處於焦點中時，我們想要使用內建視差效果可設定下列屬性：

```csharp
CityView.AdjustsImageWhenAncestorFocused = true;
```

如需有關導覽和焦點的詳細資訊，請參閱我們[使用導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)和[Siri 遠端和藍芽控制器](~/ios/tvos/platform/remote-bluetooth.md)文件。


<a name="The-Collection-View-Data-Provider" />

### <a name="the-collection-view-data-provider"></a>集合檢視的資料提供者

我們的資料模型中建立和定義我們儲存格配置中，我們來建立我們集合檢視的資料來源。 資料來源會負責不僅提供備份資料，但是也 pop receipt 都顯示在螢幕上的個別資料格的資料格。

建立`CityViewDatasource`類別，並讓它看起來如下所示：

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

可讓此詳細資料中的類別。 首先，我們會繼承`UICollectionViewDataSource`和提供的資料格識別碼 （我們指派 iOS 設計工具中） 的捷徑：

```csharp
public static NSString CardCellId = new NSString ("CityCell");
```

接下來我們會為我們收集的資料提供儲存體，並提供來填入資料的類別：

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

然後我們覆寫`NumberOfSections`方法，並傳回具有集合檢視的區段 （項目群組） 的數目。 在此情況下，有一個只有：

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

最後，我們清除佇列可重複使用的儲存格集合檢視要求以下列程式碼時：

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

我們取得的集合檢視的資料格之後我們`CityCollectionViewCell`類型，我們填入指定的項目。

<a name="Responding-to-User-Events" />

## <a name="responding-to-user-events"></a>回應使用者事件

由於我們想要使用者能夠從集合中選取的項目，我們需要提供集合檢視委派以處理這種互動。 因此，需要提供一種方法讓使用者知道何種項目我們呼叫檢視所選取。

<a name="The-App-Delegate" />

### <a name="the-app-delegate"></a>應用程式委派

我們需要能夠建立來自集合檢視的目前選取的項目關聯性回到呼叫的檢視。 我們將使用自訂的屬性上我們`AppDelegate`。 編輯`AppDelegate.cs`檔案，然後加入下列程式碼：

```csharp
public CityInfo SelectedCity { get; set;} = new CityInfo("City02.jpg", "Turning Circle", true);
```

這會定義屬性，並設定一開始要顯示預設縣 （市）。 更新版本中，我們會使用這個屬性，以顯示使用者的選取項目，並允許變更選取。

<a name="The-Collection-View-Delegate" />

### <a name="the-collection-view-delegate"></a>集合檢視委派

接下來，新增`CityViewDelegate`類別至專案，並看起來如下所示：


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

讓我們看看這個類別。 首先，我們會繼承`UICollectionViewDelegateFlowLayout`。 我們繼承自這個類別的原因而非`UICollectionViewDelegate`是我們會使用內建`UICollectionViewFlowLayout`向我們的項目並不是自訂版面配置類型。

接下來，我們決定傳回個別的項目使用下列程式碼的大小：

```csharp
public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
{
    return new CGSize (361, 256);
}
```

然後，我們決定是否給定的資料格可以取得焦點，使用下列程式碼： 

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

我們會檢查給定的片段的備份資料是否有其`CanSelect`旗標設為`true`並傳回該值。 如需有關導覽和焦點的詳細資訊，請參閱我們[使用導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)和[Siri 遠端和藍芽控制器](~/ios/tvos/platform/remote-bluetooth.md)文件。

最後，我們回應使用者選取的項目。 下列程式碼：

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    var controller = collectionView as CityCollectionView;
    App.SelectedCity = controller.Source.Cities [indexPath.Row];

    // Close Collection
    controller.ParentController.DismissViewController(true,null);
}
```

這裡我們設定`SelectedCity`屬性我們`AppDelegate`至項目，選取的使用者，我們關閉集合檢視控制站，返回呼叫我們的檢視。 我們尚未定義`ParentController`尚未我們集合檢視的屬性，我們將會執行下, 一步。

<a name="Configuring-the-Collection-View" />

## <a name="configuring-the-collection-view"></a>設定集合檢視

現在我們需要編輯我們集合檢視，並將我們的資料來源和委派的指派。 編輯`CityCollectionView.cs`檔案 （為我們自動建立從我們的分鏡腳本） 並使其看起來如下：

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

接下來，我們提供捷徑來存取集合檢視控制器 （由上述我們委派使用者進行選取時，關閉集合） 的集合檢視的資料來源和屬性：

```csharp
public CityViewDatasource Source {
    get { return DataSource as CityViewDatasource;}
}

public CityCollectionViewController ParentController { get; set;}
```

然後，可以使用下列程式碼來初始化集合檢視，並指定我們的儲存格類別、 資料來源和委派：

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    DataSource = new CityViewDatasource (this);
    Delegate = new CityViewDelegate ();
}
```

最後，我們想要標題下，才會出現時的使用者具有它反白顯示的影像 （焦點）。 這是以下列程式碼：

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

我們設定的一個項目失焦為零 (0) transparence 和下一個項目的 transparence 取得焦點移到 100%。 這些轉換取得以及動畫。


## <a name="configuring-the-collection-view-controller"></a>設定集合檢視控制器

現在我們需要上我們集合檢視的最終設定，並允許設定屬性，因此使用者做的選擇之後，就可以關閉集合檢視，我們定義控制站。

編輯`CityCollectionViewController.cs`（我們分鏡腳本從自動建立） 的檔案並看起來如下所示：

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

## <a name="putting-it-all-together"></a>全部整合 

既然我們已經有所有的組件放在一起以填入和控制集合檢視中，我們需要進行最終編輯到我們的主要檢視，以將所有項目結合在一起。

編輯`ViewController.cs`（我們分鏡腳本從自動建立） 的檔案並看起來如下所示：

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

下列程式碼一開始會顯示選取的項目從`SelectedCity`屬性`AppDelegate`並重新顯示它時使用者已從集合檢視進行選取：

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

與任何進行中的項目，如果您建置並執行應用程式時，主要檢視會顯示包含預設縣 （市）：

[ ![](collection-views-images/run01.png "主畫面")](collection-views-images/run01.png)

如果使用者按一下**選取檢視**集合檢視 按鈕，將會顯示：

[ ![](collection-views-images/run02.png "集合檢視")](collection-views-images/run02.png)

任何縣 （市) 具有其`CanSelect`屬性設定為`false`將顯示暗灰色，而使用者不能將焦點設定給它。 當使用者反白顯示項目 (讓它在焦點) 顯示標題，供他們用視差效果可微妙的地方傾斜影像以 3D。

當使用者按一下選取的映像時，集合檢視已關閉，且主要檢視會重新顯示新的映像：

[ ![](collection-views-images/run03.png "在 [首頁] 螢幕上新的映像")](collection-views-images/run03.png)

<a name="Creating-Custom-Layout-and-Reordering-Items" />

## <a name="creating-custom-layout-and-reordering-items"></a>建立自訂版面配置和重新排列項目

使用集合檢視的主要功能是能夠建立自訂版面配置。 IOS tvOS 繼承，因為建立自訂的版面配置的程序都相同。 請參閱我們[集合檢視簡介](~/ios/user-interface/controls/uicollectionview.md)文件的詳細資訊。

最近新增到集合檢視適用於 iOS 9 是能夠輕易地允許重新排列集合中的項目。 同樣地，由於 tvOS 9 iOS 9 的子集，這是這些相同的方式。 請參閱我們[集合檢視變更](~/ios/user-interface/controls/uicollectionview.md)文件以取得詳細資料。


<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計和集合檢視 Xamarin.tvOS 應用程式內使用。 首先，它討論的所有項目組成集合檢視。 接下來，它會示範如何設計及實作集合檢視使用分鏡腳本。 最後，提供資訊的連結上建立自訂版面配置和重新排列項目。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
