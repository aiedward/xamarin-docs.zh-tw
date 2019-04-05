---
title: 在 Xamarin.iOS 中的分鏡腳本簡介
description: 本文件提供在 Xamarin.iOS 中的分鏡腳本的簡介。 它描述如何使用分鏡腳本來定義使用者介面、 segue，以及如何使用 iOS 設計工具來編輯分鏡腳本檔案。
ms.prod: xamarin
ms.assetid: A3339BD2-9F56-7965-25F5-4B7C991EB775
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: f24be635afcba181efcab85d81a984d93dae4bc8
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2019
ms.locfileid: "58071108"
---
# <a name="introduction-to-storyboards-in-xamarinios"></a>在 Xamarin.iOS 中的分鏡腳本簡介

在本指南中，我們將說明哪些分鏡腳本並檢查一些 – Segue 等重要元件。 我們將探討分鏡腳本可以如何建立和使用，而且開發人員有哪些優點。

分鏡腳本檔案格式由 Apple 引入的 iOS 應用程式 UI 的視覺表示法做之前，開發人員會建立每個檢視控制器，XIB 檔案，並以手動方式程式設計每個檢視之間導覽。  使用分鏡腳本時，可讓開發人員定義檢視控制器和其間的設計介面上的導覽，並提供 WYSIWYG 編輯的應用程式的使用者介面。

分鏡腳本可以建立、 開啟和編輯與 Xamarin iOS 設計工具。 本指南也會逐步解說如何使用設計工具時，使用 C# 進行程式設計的巡覽建立分鏡腳本。


## <a name="requirements"></a>需求

分鏡腳本可以搭配使用 iOS 設計工具在 Visual Studio for Mac 或 Visual Studio 2017 安裝 Xamarin 工作負載。

## <a name="what-is-a-storyboard"></a>什麼是分鏡腳本？

分鏡腳本是應用程式中的所有畫面的視覺表示法。 它包含一系列的場景，與每個都代表場景*檢視控制器*及其*檢視*。 這些檢視可能包含物件和[控制項](~/ios/user-interface/controls/index.md)，可讓您與您的應用程式互動的使用者。 這個檢視和控制項的集合 (或*子檢視*) 稱為*內容檢視階層*。 連接場景的 segue 代表檢視控制器之間轉換的物件。 這通常是藉由建立初始的檢視中，物件與連接的檢視之間的 segue 來達成。 在下圖說明在設計介面上的關聯性：

 [![](images/storyboardsview.png "在設計介面上的關聯性如下所示此映像")](images/storyboardsview.png#lightbox)

如所示，將分鏡腳本將使用已轉譯的內容來配置每個您場景，並說明其間的連線。  值得注意的是到目前為止，當我們談到場景在 iPhone 上，它安全假設一*場景*分鏡腳本上是等於一*螢幕*在裝置上的內容。 不過，的 iPad 是可以有多個場景，會出現一次 – 比方說，使用 Popover 檢視控制器。

有許多優點，特別是在使用 Xamarin 建立您的應用程式 UI 中，使用分鏡腳本。 首先，它是在 ui 中，為 – 包括的所有物件的視覺表示法[自訂控制項](~/ios/user-interface/designer/ios-designable-controls-overview.md)– 會在設計階段進行轉譯。 這表示，然後再建置或部署其外觀和流程，您可以視覺化您的應用程式。 上圖中，為例。 我們可以告訴從快速查看有多少場景介面是設計、 每個檢視的配置和所有項目相關聯的方式。 這就是使分鏡腳本如此強大。

特別是當使用 iOS 設計工具時，事件是以分鏡腳本，更容易管理。 大部分的 UI 控制項在 Properties Pad 中，會有一份可能的事件。 事件處理常式可以在此處加入，並在檢視控制器類別中的部分方法中完成...

分鏡腳本的內容會儲存為 XML 檔案。 在建置階段，任何`.storyboard`檔案會編譯成 nibs 為已知的二進位檔案。 在執行階段，會初始化這些 nibs，並將其具現化以建立新的檢視中。

## <a name="segues"></a>Segue

A *Segue*，或*Segue 物件*，iOS 開發用來代表場景之間的轉換。 若要建立的 segue，按住**Ctrl**索引鍵並按一下 從拖曳一個場景之間。 當我們拖曳我們滑鼠時，會出現藍色的連接器，指出其中的 segue 會導致在下圖所示：

 [![](images/createsegue.png "藍色的連接器會出現，指出其中的 segue 會導致此映像所示")](images/createsegue.png#lightbox)

在滑鼠向上功能表隨即顯示讓我們選擇我們 segue 的動作。 它可能會看起來會類似下列影像： 

**前 iOS 8 和大小類別**:

[![](images/segue1.png "[動作 Segue] 下拉式清單沒有大小類別")](images/segue1.png#lightbox)

**當使用大小類別和調適性 Segue**:

[![](images/16new.png "[動作 Segue] 下拉式清單使用大小類別")](images/16new.png#lightbox)

> [!IMPORTANT]
> 如果您使用 VMWare 的 Windows 虛擬機器，做為對應 Ctrl + 按一下_上按一下滑鼠右鍵_預設情況下的滑鼠按鈕。 若要建立的 Segue，編輯您的鍵盤喜好設定，透過**喜好設定** > **鍵盤與滑鼠** > **滑鼠捷徑**並重新對應程式**次要按鈕**如下所示：
> 
> [![](images/image22.png "鍵盤和滑鼠的喜好設定")](images/image22.png#lightbox)
> 
> 您現在應該能夠如往常一樣檢視控制器之間新增一個 segue。

有不同類型的轉換，而每個指定的控制項，向使用者呈現新的檢視控制器的方式，以及它如何與其他分鏡腳本中的檢視控制器互動。 以下說明這些。 此外，也可以子類別來實作自訂的轉換的 segue 物件：

-  **顯示 / 推送**– 推送 segue 將檢視控制器加入至導覽堆疊上。 它會假設原始推播檢視控制器會為已加入至堆疊的檢視控制器相同的瀏覽控制器的一部分。 這會與相同的工作`pushViewController`，並在螢幕上的資料之間的某種關聯性時，通常使用。 使用推入 segue 可讓您的需要上一頁按鈕及標題新增至在堆疊上，允許向下鑽研導覽的檢視階層的每個檢視中瀏覽列。
-  **強制回應**– 強制回應的 segue 會建立專案，並可顯示動畫轉換中任何兩個檢視控制器之間的關聯性。 子檢視控制器會難理解帶入檢視時，才會進行父檢視控制器。 不同於推播的 segue，為我們; 加入上一頁按鈕當使用強制回應 segue`DismissViewController`必須使用，以返回上一個檢視控制器。
-  **自訂**– 任何自訂的子類別可以在建立 segue ` UIStoryboardSegue`。
-  **回溯**-回溯的 segue 可用來向後巡覽的推播 」 或 「 強制回應 segue – 比方說，藉由關閉強制回應所呈現的檢視控制器。 此外，您可以回溯到不只一個，但一系列的推播和強制回應 segue，並返回瀏覽具有單一階層中的多個步驟回溯動作。 若要了解如何使用回溯程式在 iOS 中，讀取的 segue[建立回溯 Segue](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/unwind_segue)配方。
-  **無來源**– 無來源的 segue 會指出包含的初始檢視控制器場景，因此之檢視的使用者會看到第一次。 它表示如下所示的 segue:  

    [![](images/sourcelesssegue.png "無來源的 segue")](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>自適性 Segue 類型

 iOS 8 引入[大小類別](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes)以允許使用所有可用的螢幕大小，讓開發人員建立適用於所有的 iOS 裝置的單一 UI iOS 分鏡腳本檔案。 根據預設，所有新的 Xamarin.iOS 應用程式會使用大小類別。 若要使用大小類別，從較舊的專案，請參閱[統一的分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)指南。 
 
使用大小類別的任何應用程式也會使用新[*調適性 Segue*](~/ios/user-interface/storyboards/unified-storyboards.md)。 當您使用大小類別，請記住，我們不直接指定是否我們使用 iPhone 或 iPad。 換句話說，我們會建立一個永遠看起來相同，不論它必須能夠使用多少不動產的 UI。 自適性 Segue 工作，因為環境中，並決定最佳方式來呈現內容。 調適性的 Segue 會如下所示： 

[![](images/adaptivesegue.png "自適性 Segue 的下拉式清單中")](images/adaptivesegue.png#lightbox)

|Segue|描述|
|--- |--- |
|顯示|這是非常類似的 segue 推播，但會將帳戶的畫面內容。|
|顯示詳細資料|如果應用程式會顯示主要和詳細的檢視 （例如，在 iPad 上分割檢視控制器），內容會取代 [詳細資料] 檢視。 如果只有 master 或詳細資料，則會顯示應用程式，內容會取代檢視控制器堆疊的頂端。|
|Presentation|這類似於的強制回應的 segue，並允許的簡報和轉換樣式選取範圍。|
|Popover 簡報|這顯示為 popover 的內容|

### <a name="transferring-data-with-segues"></a>傳輸資料使用 Segue

Segue 的優點不以轉換為結尾。 它們也可以用來管理資料的檢視控制器之間的傳輸。 這藉由覆寫中達成`PrepareForSegue`上初始檢視控制器，並自行處理資料的方法。 時，就會觸發 segue – 比方說，與按下按鈕 – 應用程式會呼叫這個方法，提供準備新的檢視控制器的機會*之前*任何瀏覽，就會發生。 下列程式碼，從[Phoneword](https://developer.xamarin.com/samples/monotouch/Hello_iOS/)範例，示範這項功能： 


```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, 
NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    var callHistoryController = segue.DestinationViewController 
                                  as CallHistoryController;

    if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
}
```

在此範例中， `PrepareForSegue` segue 觸發使用者時，就會呼叫方法。 首先我們需要建立 '接收' 的檢視控制器的執行個體，並將此設為 segue 的目的地檢視控制器。 這是由下列程式碼行：

```csharp
var callHistoryController = segue.DestinationViewController as CallHistoryController;
```

方法現在已設定屬性的能力`DestinationViewController`。 在此範例中我們已利用這藉由傳遞清單，稱為`PhoneNumbers`至`CallHistoryController`並將它指派至具有相同名稱的物件：

```csharp
if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
```

完成轉換之後，使用者會看到`CallHistoryController`使用填入的清單。

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>將分鏡腳本新增至非分鏡腳本專案

在某些情況下，您可能需要將分鏡腳本新增至先前非腳本檔案。 一次這麼做的 Visual Studio for Mac 可以簡化藉由遵循下列步驟：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 建立新的分鏡腳本檔案，瀏覽至**檔案 > 新的檔案 > iOS > 分鏡腳本**，如下所示： 
    
    [![](images/new-storyboard-xs.png "新的 [檔案] 對話方塊")](images/new-storyboard-xs.png#lightbox)

2. 新增名稱至分鏡腳本**主要介面**一節**Info.plist**，如下所示：
    
    [![](images/infoplist.png "Info.plist 編輯器")](images/infoplist.png#lightbox)
    
    這會相當於具現化中的初始檢視控制器`FinishedLaunching`應用程式委派中的方法。 設定此選項，應用程式具現化的視窗 （如下所示）、 載入主要分鏡腳本，並將分鏡腳本的初始檢視控制器 （無來源的 Segue 旁邊的那一個） 的執行個體為`RootViewController`屬性 視窗，然後讓在畫面上看到的視窗。

3. 在  `AppDelegate`，覆寫預設`Window`方法，來實作的視窗中屬性的下列程式碼：
        
        public override UIWindow Window {
            get;
            set;
            }
            
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 以滑鼠右鍵按一下專案，以建立新的分鏡腳本檔案**新增 > 新的檔案 > iOS > 空白分鏡腳本**，如下所示： 
    
    [![](images/new-storyboard-vs.png "新的項目 對話方塊")](images/new-storyboard-vs.png#lightbox)

2. 新增名稱至分鏡腳本**主要介面**區段的 iOS 應用程式，如下所示：
    
    [![](images/ios-app.png "Info.plist 編輯器")](images/ios-app.png#lightbox)
    
    這會相當於具現化中的初始檢視控制器`FinishedLaunching`應用程式委派中的方法。 設定此選項，應用程式具現化的視窗 （如下所示）、 載入主要分鏡腳本，並將分鏡腳本的初始檢視控制器 （無來源的 Segue 旁邊的那一個） 的執行個體為`RootViewController`屬性 視窗，然後讓在畫面上看到的視窗。

3. 在  `AppDelegate`，覆寫預設`Window`方法，來實作的視窗中屬性的下列程式碼：

        public override UIWindow Window {
            get;
            set;
            }
            
-----

## <a name="creating-a-storyboard-with-the-ios-designer"></a>使用 iOS 設計工具中建立分鏡腳本

可以使用適用於 iOS、 已整合順暢地使用 Visual Studio for Mac 和 Visual Studio 的 Xamarin 設計工具建立分鏡腳本。

若要開始使用 iOS 設計工具來建立分鏡腳本，請遵循[Hello，iOS 多重畫面](~/ios/get-started/hello-ios-multiscreen/index.md)指南。 在本逐步解說中，您將探索瀏覽使用 Segue，以及如何處理您的控制項事件的檢視控制器之間。

## <a name="instantiate-storyboards-manually"></a>具現化分鏡腳本手動

分鏡腳本完全取代您的專案中的個別 XIB 檔案，不過個別的檢視控制器分鏡腳本中可以仍具現化使用`Storyboard.InstantiateViewController`。

有時候應用程式有特殊需求，無法處理的設計工具所提供的內建的分鏡腳本轉換。 比方說，如果我們要建立的應用程式會啟動在同一個按鈕中，根據應用程式的目前狀態不同的畫面，我們可能會想要以手動的方式，將檢視控制器具現化，並自行程式轉換。

以下螢幕擷取畫面顯示兩個檢視控制器在我們的設計介面上沒有它們之間的 segue。 下一節將逐步引導如何轉換的模式可以設定在程式碼中。

 [![](images/viewcontrollerspink.png "此螢幕擷取畫面顯示兩個檢視控制器設計介面上的沒有它們之間的 segue")](images/viewcontrollerspink.png#lightbox)

1. 新增_空白 iPhone 分鏡腳本_加入現有專案的專案：
    
    [![](images/add-storyboard1.png "加入分鏡腳本")](images/add-storyboard1.png#lightbox)

2. 按兩下以開啟它，新建立的分鏡腳本，並新增一個新**巡覽控制器**至設計介面。 因為瀏覽控制器無 UI，根據預設，它會與根檢視控制器，如下所示：

    [![](images/uinavigationcontroller.png "檢視控制器使用 Segue")](images/uinavigationcontroller.png#lightbox)

3. 選取 _檢視控制器_按一下底部的黑色列。 在設計工具中的**屬性 Pad**下方**識別**我們可以指定檢視控制器的自訂類別，以及唯一識別碼。 設定**類別名稱**並**分鏡腳本識別碼**到`MainViewController`。

    [![](images/identitypanelnew.png "指定自訂的類別")](images/identitypanelnew.png#lightbox)

4. 稍後，我們必須具現化從分鏡腳本中，我們檢視控制器會使用分鏡腳本識別碼，來在我們的程式碼中參考它們。 設定以符合分鏡腳本識別碼的還原作業識別碼，可確保如果需要還原狀態的檢視控制器取得正確地重新建立。

5. 我們目前只能有一個檢視控制器。 將另一個檢視控制器拖曳至設計介面。 在 **屬性板**，身分識別，可設定的類別和分鏡腳本識別碼來`PinkViewController`，如下所示：

    [![](images/pinkvcnew.png "[屬性] 面板")](images/pinkvcnew.png#lightbox)
    
    IDE 會建立這些自訂檢視控制器類別。 您可以在檢視這些**Solution Pad**，如以下螢幕擷取畫面所示：
    
    [![](images/solution-pad.png "Solution Pad")](images/solution-pad.png#lightbox)

6. 在  `PinkViewController`，朝控制器的畫面格的中央按一下選取的檢視。 在 [屬性] 面板中，以檢視變更**背景**洋紅色：
    
    [![](images/pinkcontroller.png "設定背景色彩")](images/pinkcontroller.png#lightbox)

7. 最後，將從按鈕**工具箱**拖曳至`MainViewController`。 在 [屬性] 面板中，將它命名`PinkButton`和標題 GoToPink，如下所示：

    [![](images/pinkbutton.png "設定按鈕的名稱")](images/pinkbutton.png#lightbox)

分鏡腳本完成，但如果我們現在部署專案時，我們會收到螢幕一片空白。 這是因為我們還是需要指示 IDE 使用我們的分鏡腳本，並設定根檢視控制器，做為第一個檢視。 通常這可以透過我們的專案選項 中，如上所示。 不過在此範例中我們將會達成相同的結果，在程式碼，將下列內容加入**AppDelegate**:

```csharp
public partial class AppDelegate : UIApplicationDelegate
    {
        UIWindow window;
        public static UIStoryboard Storyboard = UIStoryboard.FromName ("MainStoryboard", null);
        public static UIViewController initialViewController;

        public override bool FinishedLaunching (UIApplication app, NSDictionary options)
        {
            window = new UIWindow (UIScreen.MainScreen.Bounds);

            initialViewController = Storyboard.InstantiateInitialViewController () as UIViewController;

            window.RootViewController = initialViewController;
            window.MakeKeyAndVisible ();
            return true;
        }

    }
```

這是許多程式碼，但不熟悉只需幾行。 首先，我們會註冊我們的分鏡腳本與**AppDelegate**藉由將分鏡腳本名稱傳入**MainStoryboard**。 接下來，我們會告訴應用程式來呼叫具現化從分鏡腳本的初始檢視控制器`InstantiateInitialViewController`在我們的分鏡腳本中，我們將該檢視控制器設定為我們的應用程式根檢視控制器。 這個方法會判斷使用者所見，第一個畫面，並建立該檢視控制器的新執行個體。

請注意，在 [解決方案] 窗格建立 IDE`MainViewcontroller.cs`類別，並將其`corresponding designer.cs`當我們的類別名稱加入至在步驟 4 中的 [屬性] 面板。 我們可以看到這個建立特殊的建構函式，其中包含基底類別的類別：

```csharp
public MainViewController (IntPtr handle) : base (handle) 
{
}
```


建立使用設計工具的分鏡腳本，IDE 會自動加入[[註冊]](xref:Foundation.RegisterAttribute)頂端屬性`designer.cs`類別，並傳入的字串識別項，這等同於中指定的分鏡腳本識別碼上一個步驟。 這會連結的 C# 到分鏡腳本中相關的場景。

您可能想要加入現有的類別，也就是有些時候**不**在設計工具中建立。 在此情況下，您會註冊這個類別，如往常一樣：

```csharp
[Register ("MainViewController")]
public partial class MainViewController : UIViewController
{
public MainViewController (IntPtr handle) : base (handle) 
{
}

...
}
```

如需有關如何註冊類別和方法的詳細資訊，請參閱[型別註冊機構](http://docs.xamarin.com/guides/ios/advanced_topics/registrar/)文件。

此類別中的最後一個步驟是連接 按鈕，並轉換到的粉紅色的檢視控制器。 我們將會具現化`PinkViewController`從分鏡腳本; 然後，我們將進行程式設計的推播使用 segue `PushViewController`，如下列範例程式碼所示：

```csharp
public partial class MainViewController : UIViewController
{
    UIViewController pinkViewController;

    public MainViewController (IntPtr handle) : base (handle)
    {

    }

    public override void AwakeFromNib ()
    {
    // Called when loaded from xib or storyboard.

    this.Initialize ();
    }

    public void Initialize(){

    //Instantiating View Controller with Storyboard ID 'PinkViewController'
    pinkViewController = Storyboard.InstantiateViewController ("PinkViewController") as PinkViewController;
    }

    public override void ViewDidLoad ()
    {
    base.ViewDidLoad ();

    //When we push the button, we will push the pinkViewController onto our current Navigation Stack
    PinkButton.TouchUpInside += (o, e) =&gt; {
        this.NavigationController.PushViewController (pinkViewController, true);
    };
    }

}
```

執行應用程式會產生 2 畫面應用程式：

![](images/finishedstoryboard.png "執行畫面的範例應用程式")

## <a name="conditional-segues"></a>條件式 Segue

通常，從一個檢視控制器移至下一步是取決於特定條件。 例如，如果我們提出一個簡單的登入畫面我們只想移至下一個畫面*如果*已驗證的使用者名稱和密碼。

在下一個範例中，我們將新增密碼欄位以上述範例。 使用者將只能存取*PinkViewController*如果他們輸入正確的密碼，否則將會顯示錯誤。

我們開始之前，請遵循步驟 1 – 8 以上。 在這些步驟中我們建立我們的分鏡腳本，開始建立我們的 UI，並告訴我們的應用程式委派来使用哪一個檢視控制器 RootViewController 原狀。

1. 現在，讓我們建立我們的 UI，並新增額外的檢視，列出`MainViewController`，看起來類似下面的螢幕擷取畫面中：

    - UITextField
        - 名稱：PasswordTextField
        - 預留位置：「 輸入祕密 」
    - UILabel
        - 文字：' 時發生錯誤：錯誤的密碼。 您不應該傳遞 ！ '
        - 色彩：紅色
        - 對齊方式：置中
        - 程式行：2
        - 'Hidden' 核取的核取方塊 
        
    [![](images/passwordvc.png "Center 行")](images/passwordvc.png#lightbox)
    
2. 建立至粉紅色移的按鈕和 Ctrl 拖曳從檢視控制器之間的 Segue *PinkButton*要*PinkViewController*，然後選取**推播**上滑鼠向上. 

3. 按一下 Segue，並為它提供*識別碼* `SegueToPink`:

    [![](images/namesegue.png "按一下 Segue，並為它提供識別碼 SegueToPink")](images/namesegue.png#lightbox)  
    

4. 最後，新增下列 ShouldPerformSegue 方法`MainViewController`類別：

    ```csharp
    public override bool ShouldPerformSegue (string segueIdentifier, NSObject sender)
    {
        
        if(segueIdentifier == "SegueToPink"){
            if (PasswordTextField.Text == "password") {
                PasswordTextField.ResignFirstResponder ();
                return true;
            }
            else{
                ErrorLabel.Hidden = false;
                return false;
            }
        }
        return base.ShouldPerformSegue (segueIdentifier, sender);
    }
    ```

此程式碼中我們有比對到 segueIdentifier 我們`SegueToPink`segue，然後我們可以測試條件; 因此在此情況下有效的密碼。 如果我們的條件傳回`true`，的 Segue 會執行，並提供`PinkViewController`。 如果`false`，不會提供新的檢視控制器。

我們可以套用到此檢視控制器上的任何 Segue 的這種方法，藉由檢查 ShouldPerformSegue 方法 segueIdentifier 引數。 在此情況下我們只需要一個 Segue 識別碼 – `SegueToPink`。

中的 Storyboards.Conditional 方案，請參閱[手動分鏡腳本範例](https://developer.xamarin.com/samples/monotouch/ManualStoryboard/)如需實用範例。

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>使用分鏡腳本的參考

分鏡腳本參考可讓您取得大型且複雜的分鏡腳本設計，並將它分割成較小的分鏡腳本取得參考從原始，因此移除複雜度，並且讓產生個別的分鏡腳本設計和維護變得更加容易。

此外，分鏡腳本參考可提供_錨點_至相同的分鏡腳本或不同的特定場景中的另一個場景。

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>參考的外部的分鏡腳本

若要新增的外部的分鏡腳本的參考，執行下列作業：

1. 在 **方案總管**，以滑鼠右鍵按一下專案名稱，然後選取**新增** > **新檔案...**  >  **iOS** > **分鏡腳本**。 請輸入**名稱**新分鏡腳本，然後按一下 **新增**按鈕：
    
    [![](images/ref01.png "新的 [檔案] 對話方塊")](images/ref01.png#lightbox)
    
2. 設計新分鏡腳本場景的版面配置，以及您通常會儲存您的變更： 
    
    [![](images/ref02.png "新的場景的版面配置")](images/ref02.png#lightbox)
    
3. 開啟分鏡腳本，您要在 iOS 設計工具中新增的參考。

4. 拖曳**參考分鏡腳本**從**工具箱**拖曳至設計介面： 
    
    [![](images/ref03.png "分鏡腳本參考")](images/ref03.png#lightbox)
    
5. 在  **Widget**索引標籤**屬性總管**，選取的名稱**分鏡腳本**您上面所建立： 

    [![](images/ref04.png "小工具 索引標籤")](images/ref04.png#lightbox)
    
6. UI 上的小工具 （例如按鈕） 現有的場景 control + 按一下，並建立以新的 Segue**分鏡腳本參考**您剛建立： 

    [![](images/ref05.png "建立的 segue")](images/ref05.png#lightbox) 
    
7. 從快顯功能表中選取**顯示**完成 Segue: 

    [![](images/ref06.png "選取顯示完成的 Segue")](images/ref06.png#lightbox) 
    
8. 將您的變更儲存到分鏡腳本。

在執行應用程式，並在使用者按一下 UI 項目上您從初始檢視控制器從外部指定分鏡腳本參考中的分鏡腳本建立的 Segue 時將會顯示。

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>參考特定的場景中的外部的分鏡腳本

若要將參考加入特定的場景的外部的分鏡腳本 （並沒有初始檢視控制器），執行下列作業：

1. 在 [**方案總管] 中**，連按兩下以開啟它進行編輯外部的分鏡腳本。

2. 加入新場景，並像平常一樣，設計其版面配置： 

    [![](images/ref07.png "新的場景版面配置")](images/ref07.png#lightbox)
    
3. 在  **Widget**索引標籤**屬性總管**，輸入**分鏡腳本識別碼**新場景的檢視控制器： 

    [![](images/ref08.png "輸入新的場景檢視控制器分鏡腳本識別碼")](images/ref08.png#lightbox)
    
3. 開啟分鏡腳本，您要在 iOS 設計工具中新增的參考。

4. 拖曳**參考分鏡腳本**從**工具箱**拖曳至設計介面： 

    [![](images/ref03.png "分鏡腳本參考")](images/ref03.png#lightbox)
    
5. 在**Widget**索引標籤**屬性總管**，選取的名稱**分鏡腳本**而**參考識別碼**（分鏡腳本識別碼） 的您先前建立的場景： 

    [![](images/ref09.png "小工具 索引標籤 ")](images/ref09.png#lightbox)
    
6. UI 上的小工具 （例如按鈕） 現有的場景 control + 按一下，並建立以新的 Segue**分鏡腳本參考**您剛建立： 

    [![](images/ref10.png "建立的 segue")](images/ref10.png#lightbox) 
    
7. 從快顯功能表中選取**顯示**完成 Segue: 

    [![](images/ref06.png "選取顯示完成的 Segue")](images/ref06.png#lightbox) 
    
8. 將您的變更儲存到分鏡腳本。

當應用程式執行且使用者按下的 UI 項目，您所建立的 Segue 場景中填入從給定**分鏡腳本識別碼**將顯示從外部指定分鏡腳本參考中的分鏡腳本。

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>參考特定的場景中相同的分鏡腳本

若要新增至特定的場景相同的分鏡腳本的參考，執行下列作業：

1. 在 [**方案總管] 中**，連按兩下以開啟它進行編輯分鏡腳本。

2. 加入新場景，並像平常一樣，設計其版面配置： 

    [![](images/ref11.png "新的場景版面配置")](images/ref11.png#lightbox)

3. 在  **Widget**索引標籤**屬性總管**，輸入**分鏡腳本識別碼**新場景的檢視控制器： 

    [![](images/ref12.png "小工具 索引標籤")](images/ref12.png#lightbox)
    
3. 拖曳**參考分鏡腳本**從**工具箱**拖曳至設計介面： 

    [![](images/ref03.png "分鏡腳本參考")](images/ref03.png#lightbox)
    
5. 在**Widget**索引標籤**屬性總管**，選取**參考識別碼**（分鏡腳本識別碼） 您先前建立的場景： 

    [![](images/ref13.png "小工具 索引標籤")](images/ref13.png#lightbox)
    
6. UI 上的小工具 （例如按鈕） 現有的場景 control + 按一下，並建立以新的 Segue**分鏡腳本參考**您剛建立： 

    [![](images/ref14.png "建立的 segue")](images/ref14.png#lightbox) 
    
7. 從快顯功能表中選取**顯示**完成 Segue: 

    [![](images/ref06.png "選取顯示完成的 Segue")](images/ref06.png#lightbox) 
    
8. 將您的變更儲存到分鏡腳本。

當應用程式執行且使用者按下的 UI 項目，您所建立的 Segue 場景中填入從給定**分鏡腳本識別碼**將顯示同一個分鏡腳本參考中所指定的分鏡腳本中。

## <a name="summary"></a>總結

這篇文章介紹的概念，分鏡腳本，以及如何讓您看看開發 iOS 應用程式中。 它討論場景、 檢視控制器、 檢視和檢視階層以及如何搭配不同類型的 Segue 連結場景。  它也會探討具現化的檢視控制器手動從分鏡腳本，並建立條件式 Segue。



## <a name="related-links"></a>相關連結

- [手動分鏡腳本 （範例）](https://developer.xamarin.com/samples/ManualStoryboard/)
- [IOS 設計工具簡介](~/ios/user-interface/designer/introduction.md)
- [轉換到分鏡腳本](https://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [UIStoryboard 類別參考](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
