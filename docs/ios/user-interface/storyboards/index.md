---
title: "分鏡腳本的簡介"
description: "外觀的視覺表示法，而應用程式的流程分鏡腳本。 Xamarin 已經導入的設計工具可讓 Xamarin.iOS 應用程式，以善用分鏡腳本，讓您以視覺化方式設計您的應用程式畫面及存取檢視，控制站和使用 C# segues 獲得更多控制。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A3339BD2-9F56-7965-25F5-4B7C991EB775
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 342e8189d9dec6eaa60a999d56a7891da845d247
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="introduction-to-storyboards"></a>分鏡腳本的簡介

_外觀的視覺表示法，而應用程式的流程分鏡腳本。Xamarin 已經導入的設計工具可讓 Xamarin.iOS 應用程式，以善用分鏡腳本，讓您以視覺化方式設計您的應用程式畫面及存取檢視，控制站和使用 C# segues 獲得更多控制。_

在本指南中，我們將說明分鏡腳本並檢查一些重要元件 – 例如 Segues。 我們將探討分鏡腳本可以如何建立和使用，而且開發人員有哪些優點。

分鏡腳本檔案格式由 Apple 引入的 iOS 應用程式 UI 的視覺表示法做之前，開發人員建立的每個檢視控制站，XIB 檔案，並手動撰寫每個檢視之間導覽。  使用分鏡腳本時，可讓開發人員定義檢視控制器和瀏覽不同的設計介面上進行，並提供 WYSIWYG 編輯應用程式的使用者介面。

分鏡腳本可以建立、 開啟和編輯與 Xamarin iOS 設計工具。 本指南也會逐步解說如何使用設計工具時，使用 C# 程式瀏覽建立分鏡腳本。


## <a name="requirements"></a>需求

分鏡腳本可以搭配 iOS 適用於 Mac 的 Visual Studio 中的設計工具或 Visual Studio 2015 及 2017年安裝 Xamarin 工作負載。

## <a name="what-is-a-storyboard"></a>分鏡腳本是什麼？

分鏡腳本是應用程式中的所有畫面的視覺表示法。 它包含一連串的場景，每個場景表示*檢視控制器*及其*檢視*。 這些檢視可能會包含物件和[控制項](~/ios/user-interface/controls/index.md)，可讓您與您的應用程式互動的使用者。 這個檢視表和控制項的集合 (或*subviews*) 稱為*內容檢視階層*。 場景所連接的話題物件，代表檢視控制器之間的轉換。 這通常是藉由建立初始的檢視，物件與連接的檢視之間 segue 達成。 在下圖說明在設計介面上的關聯性：

 [![](images/storyboardsview.png "在此映像中說明的設計介面上的關聯性")](images/storyboardsview.png#lightbox)

如所示，分鏡腳本會配置每個您場景具有已經呈現的內容，並說明它們之間的連線。  值得此時，我們在 iPhone 上討論場景時安全地假設一個*場景*腳本上等於是*螢幕*在裝置上的內容。 不過，與 iPad 很可能有多個場景，會出現一次 – 例如，使用 Popover 檢視控制器。

有許多優點，以建立您的應用程式 UI，尤其是使用 Xamarin 的分鏡腳本。 首先，它是以視覺化的 ui 中，做為表示 – 包括的所有物件[自訂控制項](~/ios/user-interface/designer/ios-designable-controls-overview.md)– 會在設計階段進行轉譯。 這表示，再建置或部署其外觀和流程，您可以視覺化您的應用程式。 上圖中，為例。 我們可以確定從快速查看有多少場景介面是設計、 每個檢視的配置和所有項目關聯的方式。 這是讓分鏡腳本非常強大。

特別是當使用 iOS 設計工具時，事件是以分鏡腳本，更容易管理。 大部分的 UI 控制項將屬性輸入板中有一份可能的事件。 事件處理常式可以在此處加入和檢視控制器類別中的部分方法中完成...

分鏡腳本的內容會儲存為 XML 檔案。 在建置時，任何`.storyboard`檔案會編譯成二進位檔案，稱為 nibs。 在執行階段，這些 nibs 會初始化並具現化，以建立新檢視。

## <a name="segues"></a>Segues

A *Segue*，或*話題物件*，iOS 開發中用來代表場景之間的轉換。 若要建立 segue，按住**Ctrl**索引鍵和按一下拖曳從一個場景到另一個。 當我們拖曳我們滑鼠時，會出現藍色的連接器，指出其中 segue 會導致在下列影像所示：

 [![](images/createsegue.png "藍色的連接器會出現，指出其中 segue 會導致此映像中所示")](images/createsegue.png#lightbox)

上滑鼠向上功能表會讓我們選擇我們 segue 的動作。 看起來類似下面的影像： 

**前 iOS 8 和大小類別**:

[![](images/segue1.png "動作話題下拉式清單中沒有大小類別")](images/segue1.png#lightbox)

**當使用大小類別和自動調整 Segues**:

[![](images/16new.png "動作話題下拉式清單的大小類別")](images/16new.png#lightbox)

> [!IMPORTANT]
> **注意：** ctrl 如果您使用 VMWare 的 Windows 虛擬機器，對應為_以滑鼠右鍵按一下_預設滑鼠按鈕。 若要建立 Segue，編輯您的鍵盤喜好設定，透過**喜好設定** > **鍵盤及滑鼠** > **滑鼠捷徑**再重新對應程式**次要按鈕**如下所示：
> 
> [![](images/image22.png "鍵盤和滑鼠的喜好設定")](images/image22.png#lightbox)
> 
> 您現在應該能夠新增 segue 像平常一樣檢視控制站之間。

有不同類型的轉換、 新的檢視控制器呈現給使用者的方式，以及它如何與其他檢視中的控制站的分鏡腳本互動每個提供控制權。 下面將說明這些。 此外，也可以子類別以實作自訂的轉換 segue 物件：

-  **顯示 / 推入**– 推入話題檢視控制器加入巡覽堆疊。 它會假設來自推入檢視控制器是相同的瀏覽控制站新增至堆疊的檢視控制站的一部分。 這會執行相同動作`pushViewController`，和通常會在螢幕上的資料之間某種關聯性時使用。 使用推入話題可讓您的導覽列標題加入到在堆疊上，瀏覽檢視階層中向下的鑽研可讓每個檢視的上一頁按鈕與 luxury。
-  **強制回應**– 強制回應 segue 建立您的專案，請使用正在顯示動畫轉換選項中任何兩個檢視控制站之間的關聯性。 子檢視控制器將會完全混淆帶入檢視時，才會進行父檢視控制器。 不同於推入話題，這樣會為我們; 將上一頁按鈕當使用強制回應話題`DismissViewController`必須使用，以回到先前檢視控制器。
-  **自訂**– 任何自訂 segue 可建立的子類別為` UIStoryboardSegue`。
-  **回溯**-回溯的話題可用來向後巡覽發送或強制回應話題 – 例如，由解除強制呈現檢視控制站。 此外，您可以透過不是只有一個回溯，但一系列的推播和獨佔式 segues 並返回瀏覽以單一階層中的多個步驟回溯動作。 若要了解如何使用回溯話題在 iOS 中，讀取[建立回溯 Segues](https://developer.xamarin.com/recipes/ios/general/storyboard/unwind_segue/)配方。
-  **Sourceless** : sourceless segue 表示包含的初始檢視控制站的場景，並且因此之檢視的使用者會看到第一次。 它會以 segue 如下所示：  

    [![](images/sourcelesssegue.png "Sourceless segue")](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>自動調整話題類型

 iOS 8 引進[大小類別](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes)允許使用所有可用的螢幕大小，讓開發人員建立所有 iOS 裝置的單一 UI iOS 分鏡腳本檔案。 根據預設，所有新的 Xamarin.iOS 應用程式會使用大小類別。 若要使用大小類別，從較舊的專案，請參閱[簡介統一分鏡腳本](~/ios/user-interface/storyboards/unified-storyboards.md)指南。 
 
使用大小類別的任何應用程式也會使用新[*適應性 Segues*](~/ios/user-interface/storyboards/unified-storyboards.md)。 當使用大小類別，請記住，我們不直接指定是否使用 iPhone 或 iPad。 換句話說，我們會建立將永遠相同的外觀，不論它必須使用多少不動產的單一 UI。 可在環境中，並決定如何呈現內容的彈性 Segues 工作。 自動調整 Segues 如下所示： 

[![](images/adaptivesegue.png "自動調整 Segues 下拉式清單中")](images/adaptivesegue.png#lightbox)

<table>
    <thead>
        <tr>
            <th>話題</th>
            <th>描述</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>顯示</td>
            <td>這是非常類似於話題推入，但是螢幕的內容列入考量。 </td>
        </tr>
        <tr>
            <td>顯示詳細資料</td>
            <td>如果應用程式會顯示主要和詳細的檢視 （例如，在 iPAd 上的分割檢視控制站），內容會取代詳細資料檢視。 如果應用程式會顯示只有主要<strong>或</strong>詳細資料，內容將會取代檢視控制器堆疊的頂端。</td>
        </tr>
        <tr>
            <td>Presentation</td>
            <td>這是強制回應 segue，類似，而且可讓簡報和轉換樣式的選取範圍。</td>
        </tr>
        <tr>
            <td>Popover 簡報</td>
            <td>這會將內容呈現成 popover</td>
        </tr>
    </tbody>
</table>

### <a name="transferring-data-with-segues"></a>傳輸資料與 Segues

Segue 優點不結束與轉換。 它們也可以用來管理檢視控制站之間的資料傳輸。 這藉由覆寫`PrepareForSegue`和自己處理資料的初始檢視控制站上的方法。 觸發 segue – 時，例如按下按鈕 – 與應用程式會呼叫此方法，提供準備新的檢視控制站的機會*之前*任何瀏覽，就會發生。 下列程式碼從[Phoneword](https://developer.xamarin.com/samples/monotouch/Hello_iOS/)範例，示範這項處理： 


```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, 
NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    var callHistoryContoller = segue.DestinationViewController 
                                  as CallHistoryController;

    if (callHistoryContoller != null) {
        callHistoryContoller.PhoneNumbers = PhoneNumbers;
    }
}
```

在此範例中， `PrepareForSegue` segue 觸發使用者時，就會呼叫方法。 我們需要先建立的接收' 的檢視控制器執行個體，並將此設為 segue 的目的地檢視控制器。 下列程式碼行達成此目的：

```csharp
var callHistoryContoller = segue.DestinationViewController as CallHistoryController;
```

此方法現在能夠設定屬性`DestinationViewController`。 在此範例中我們已利用這藉由傳遞清單稱為`PhoneNumbers`至`CallHistoryController`，並且將它指派了相同名稱的物件：

```csharp
if (callHistoryContoller != null) {
        callHistoryContoller.PhoneNumbers = PhoneNumbers;
    }
```

在轉換完成時，使用者會看到`CallHistoryController`與填入的清單。

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>非分鏡腳本專案中加入分鏡腳本

在某些情況下，您可能需要加入至先前非腳本檔案的分鏡腳本。 可簡化一次在 Visual Studio 中執行此動作適用於 Mac 遵循下列步驟：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 建立新的分鏡腳本檔案瀏覽至**檔案 > 新的檔案 > iOS > 分鏡腳本**，如下所示： 
    
    [![](images/new-storyboard-xs.png "新的 [檔案] 對話方塊")](images/new-storyboard-xs.png#lightbox)

2. 加入您的分鏡腳本名稱**主要介面**區段**Info.plist**，如下所示：
    
    [![](images/infoplist.png "Info.plist 編輯器")](images/infoplist.png#lightbox)
    
    這樣會具現化中的初始檢視控制站的對應項`FinishedLaunching`內應用程式委派方法。 設定這個選項，應用程式具現化的視窗 （請參閱下文）、 載入主要腳本，並將指定的分鏡腳本的初始檢視控制器 （即 sourceless Segue 除外） 執行個體`RootViewController`屬性 視窗，然後讓在螢幕上看見的視窗。

3. 在`AppDelegate`，覆寫預設`Window`方法，並包含下列程式碼來實作的視窗屬性：
        
        public override UIWindow Window {
            get;
            set;
            }
            
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 以滑鼠右鍵按一下專案，以建立新的分鏡腳本檔案**新增 > 新的檔案 > iOS > 空白分鏡腳本**，如下所示： 
    
    [![](images/new-storyboard-vs.png "新的項目 對話方塊")](images/new-storyboard-vs.png#lightbox)

2. 加入您的分鏡腳本名稱**主要介面**> 一節的 ios 應用程式，如下所示：
    
    [![](images/ios-app.png "Info.plist 編輯器")](images/ios-app.png#lightbox)
    
    這樣會具現化中的初始檢視控制站的對應項`FinishedLaunching`內應用程式委派方法。 設定這個選項，應用程式具現化的視窗 （請參閱下文）、 載入主要腳本，並將指定的分鏡腳本的初始檢視控制器 （即 sourceless Segue 除外） 執行個體`RootViewController`屬性 視窗，然後讓在螢幕上看見的視窗。

3. 在`AppDelegate`，覆寫預設`Window`方法，並包含下列程式碼來實作的視窗屬性：

        public override UIWindow Window {
            get;
            set;
            }
            
-----

## <a name="creating-a-storyboard-with-the-ios-designer"></a>使用 iOS 設計工具中建立分鏡腳本

可以使用適用於 iOS 已整合順暢地與 Visual Studio 用於 Mac 和 Visual Studio 的 Xamarin 設計工具建立分鏡腳本。

若要開始使用 iOS 設計工具建立分鏡腳本，請遵循[Hello，iOS 多重畫面](~/ios/get-started/hello-ios-multiscreen/index.md)指南。 在本逐步解說中，您將探索瀏覽檢視控制站使用 Segues 及如何處理您的控制項上的事件之間。

## <a name="instantiate-storyboards-manually"></a>以手動方式將分鏡腳本具現化

分鏡腳本完全取代您的專案中的個別 XIB 檔案，不過個別的檢視分鏡腳本中的控制站可以仍然使用具現化`Storyboard.InstantiateViewController`。

有時候應用程式必須與設計工具提供的內建的分鏡腳本轉換無法處理的特殊需求。 例如，如果我們要建立的應用程式會啟動不同的畫面，從相同的按鈕，視應用程式的目前狀態而定，我們可能想要具現化檢視控制器以手動方式，以及自己程式轉換。

下列螢幕擷取畫面顯示兩者之間的兩個檢視控制器我們設計介面上的沒有話題。 下一節將逐步引導如何該轉換可以設定程式碼中。

 [![](images/viewcontrollerspink.png "這個螢幕擷取畫面會顯示兩個檢視控制器設計介面上的沒有話題兩者之間")](images/viewcontrollerspink.png#lightbox)

1. 新增_清空 iPhone 分鏡腳本_加入現有專案的專案：
    
    [![](images/add-storyboard1.png "加入分鏡腳本")](images/add-storyboard1.png#lightbox)

2. 按兩下以開啟它，新建立的分鏡腳本，並加入新**導覽控制站**至設計介面。 因為導覽控制站是無 UI，根據預設，它會隨附在根檢視控制站，如下所示：

    [![](images/uinavigationcontroller.png "檢視與控制器 Segues")](images/uinavigationcontroller.png#lightbox)

3. 選取_檢視控制器_按一下底部的 [黑色] 列上。 在設計工具中**屬性板**下**識別**我們可以檢視控制站指定為自訂類別，以及唯一識別碼。 設定**類別名稱**和**分鏡腳本識別碼**至`MainViewController`。

    [![](images/identitypanelnew.png "指定自訂類別")](images/identitypanelnew.png#lightbox)

4. 更新版本中，我們必須將具現化從分鏡腳本，我們檢視控制站，會分鏡腳本識別碼，在我們的程式碼中參考它們。 設定要符合分鏡腳本識別碼的還原作業識別碼，可確保如果需要還原狀態檢視控制器取得正確重新建立。

5. 我們目前只能有一個檢視控制站。 將另一個檢視控制器拖曳至設計介面。 在**屬性板**，以身分識別，可設定的類別和分鏡腳本識別碼`PinkViewController`，如下所示：

    [![](images/pinkvcnew.png "屬性填補")](images/pinkvcnew.png#lightbox)
    
    IDE 會建立這些自訂的類別檢視控制站。 這些檢視可以使用**方案板**，如以下螢幕擷取畫面所示：
    
    [![](images/solution-pad.png "方案填補")](images/solution-pad.png#lightbox)

6. 在`PinkViewController`，朝控制器的框架的中央按一下選取的檢視。 在屬性板，檢視下變更**背景**洋紅色：
    
    [![](images/pinkcontroller.png "設定背景色彩")](images/pinkcontroller.png#lightbox)

7. 最後，將從按鈕拖曳**工具箱**到`MainViewController`。 在屬性板，將其命名`PinkButton`和標題 GoToPink，如下所示：

    [![](images/pinkbutton.png "設定按鈕名稱")](images/pinkbutton.png#lightbox)

分鏡腳本已完成，但如果我們現在部署專案時，就會得到空白畫面。 這是因為我們仍需要指示 IDE 使用我們的分鏡腳本，並設定在根檢視控制站做為第一個檢視。 通常這可以透過我們的專案選項，如上所示。 不過在此範例中我們將會達成相同的結果，在程式碼中加入下列命令以**AppDelegate**:

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

這是大量程式碼，但不熟悉只要少數幾行。 首先，我們會註冊與我們分鏡腳本**AppDelegate**分鏡腳本名稱傳入**MainStoryboard**。 接下來，我們會告訴應用程式來具現化的初始檢視控制站，從 分鏡腳本呼叫`InstantiateInitialViewController`上我們分鏡腳本，並將該檢視控制器為我們的應用程式根目錄檢視控制站。 這個方法會判斷使用者所見，第一個畫面，並建立該檢視控制站的新執行個體。

請注意，在 [方案] 窗格建立 IDE`MainViewcontroller.cs`類別，並將其`corresponding designer.cs`當我們類別名稱加入至在步驟 4 中屬性填補。 我們可以看到此建立包含基底類別的特殊建構函式的類別：

```csharp
public MainViewController (IntPtr handle) : base (handle) 
{
}
```


建立使用設計工具的分鏡腳本，IDE 會自動加入[[註冊]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/)頂端屬性`designer.cs`類別，並傳入字串識別項，等同於中指定的分鏡腳本識別碼上一個步驟。 這將會連結 C# 至相關的場景中的分鏡腳本。

在某個時間點您可能想要加入現有的類別，這是**不**設計師中所建立。 在此情況下，您會註冊這個類別，像平常一樣：

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

如需註冊的類別和方法的詳細資訊，請參閱[類型註冊機構](http://docs.xamarin.com/guides/ios/advanced_topics/registrar/)文件。

此類別中的最後一個步驟是連接 按鈕，並轉換到粉紅色檢視控制站。 我們會具現化`PinkViewController`來自分鏡腳本; 然後，我們將程式推入與話題`PushViewController`，如下列範例程式碼所示：

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

執行應用程式會產生 2 螢幕應用程式：

![](images/finishedstoryboard.png "範例應用程式執行畫面")

## <a name="conditional-segues"></a>條件式 Segues

通常，從一個檢視控制站移至下一步是取決於特定條件。 比方說，如果我們已進行簡單 登入畫面我們只想移至下一個畫面*如果*已驗證的使用者名稱和密碼。

下一個範例中，我們會將密碼欄位新增至上述範例。 使用者只能存取*PinkViewController*如果使用者輸入正確的密碼，否則會顯示錯誤。

開始之前，請遵循步驟 1 – 8 上述。 我們在這些步驟中建立我們分鏡腳本，開始建立我們的 UI，並告訴我們的應用程式委派来使用哪一個檢視控制站，所以 RootViewController。

1. 現在，讓我們來建置我們的 UI，並加入所列的其他檢視`MainViewController`讓它看起來像這樣，在下面的螢幕擷取畫面：

    - UITextField
        - 名稱： PasswordTextField
        - 輸入密碼的密碼 ' 預留位置：
    - UILabel
        - 文字: ' 錯誤： 錯誤的密碼。 您不應該傳遞 ！ '
        - 色彩： 紅色
        - 對齊方式： 中心
        - 線條： 2
        - 'Hidden' checkbox 已核取 
        
    [![](images/passwordvc.png "Center 行")](images/passwordvc.png#lightbox)
    
2. 建立移至粉紅色按鈕與檢視控制器之間從 Ctrl 拖曳 Segue *PinkButton*至*PinkViewController*，並選取**推播**上滑鼠向上. 

3. 按一下 Segue 並為它提供*識別碼* `SegueToPink`:

    [![](images/namesegue.png "按一下 Segue 並給予其識別碼 SegueToPink")](images/namesegue.png#lightbox)  
    

4. 最後，將下列 ShouldPerformSegue 方法加入`MainViewController`類別：

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

這段程式碼中我們已經比對到 segueIdentifier 我們`SegueToPink`話題，所以我們可以再測試條件; 在此情況下有效的密碼。 如果我們條件傳回`true`，Segue 會執行，並提供`PinkViewController`。 如果`false`，不會提供新的檢視控制站。

我們可以套用至任何 Segue 此檢視控制站上的這種方法，藉由檢查 ShouldPerformSegue 方法的 segueIdentifier 引數。 在此情況下我們只能有一個 Segue 識別碼 – `SegueToPink`。

Storyboards.Conditional 方案，請參閱[手動分鏡腳本範例](https://developer.xamarin.com/samples/monotouch/ManualStoryboard/)的操作範例。

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>使用分鏡腳本參考

分鏡腳本參考可讓您的大型、 複雜的分鏡腳本設計，並將它切為取得參考原始的較小的分鏡腳本，因此移除移除複雜性及使所產生的個別 分鏡腳本更容易設計和維護。

此外，可提供分鏡腳本參考_錨點_至相同的分鏡腳本或不同的特定場景中的另一個場景。

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>參考外部的分鏡腳本

若要加入外部的分鏡腳本的參考，執行下列作業：

1. 在**方案總管 中**，以滑鼠右鍵按一下專案名稱，然後選取**新增** > **新的檔案...**  >  **iOS** > **分鏡腳本**。 輸入**名稱**新分鏡腳本並按一下**新增**按鈕：
    
    [![](images/ref01.png "新的 [檔案] 對話方塊")](images/ref01.png#lightbox)
    
2. 設計新分鏡腳本場景的版面配置，以及您通常會儲存您的變更： 
    
    [![](images/ref02.png "新的場景的版面配置")](images/ref02.png#lightbox)
    
3. 開啟您要在 iOS 設計工具中的參考加入分鏡腳本。

4. 拖曳**分鏡腳本參考**從**工具箱**拖曳至設計介面： 
    
    [![](images/ref03.png "分鏡腳本參考")](images/ref03.png#lightbox)
    
5. 在**Widget**  索引標籤**屬性總管**，選取名稱**分鏡腳本**先前建立的： 

    [![](images/ref04.png "[小工具] 索引標籤")](images/ref04.png#lightbox)
    
6. 控制項按一下 UI 上的小工具 （例如按鈕） 現有的場景，並建立以新 Segue**分鏡腳本參考**您剛建立： 

    [![](images/ref05.png "建立 segue")](images/ref05.png#lightbox) 
    
7. 從快顯功能表選取**顯示**完成 Segue: 

    [![](images/ref06.png "選取顯示完成 Segue")](images/ref06.png#lightbox) 
    
8. 將您的變更儲存到分鏡腳本。

當執行應用程式，且使用者按一下 UI 項目上，從初始檢視控制器從外部分鏡腳本參考中所指定的分鏡腳本建立 Segue 隨即出現。

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>參考外部的分鏡腳本中的特定場景

將參考加入至特定的場景外部的分鏡腳本 （並不初始檢視控制器），執行下列作業：

1. 在**方案總管 中**，連按兩下以開啟檔案進行編輯外部分鏡腳本。

2. 加入新的場景，並設計其版面配置，像平常一樣： 

    [![](images/ref07.png "新的場景版面配置")](images/ref07.png#lightbox)
    
3. 在**Widget**  索引標籤**屬性總管**，輸入**分鏡腳本識別碼**新場景檢視控制站： 

    [![](images/ref08.png "輸入新的場景檢視控制器分鏡腳本的識別碼")](images/ref08.png#lightbox)
    
3. 開啟您要在 iOS 設計工具中的參考加入分鏡腳本。

4. 拖曳**分鏡腳本參考**從**工具箱**拖曳至設計介面： 

    [![](images/ref03.png "分鏡腳本參考")](images/ref03.png#lightbox)
    
5. 在**Widget**  索引標籤**屬性總管**，選取名稱**分鏡腳本**和**參考識別碼**(分鏡腳本 ID) 的先前建立的場景： 

    [![](images/ref09.png "[小工具] 索引標籤 ")](images/ref09.png#lightbox)
    
6. 控制項按一下 UI 上的小工具 （例如按鈕） 現有的場景，並建立以新 Segue**分鏡腳本參考**您剛建立： 

    [![](images/ref10.png "建立 segue")](images/ref10.png#lightbox) 
    
7. 從快顯功能表選取**顯示**完成 Segue: 

    [![](images/ref06.png "選取顯示完成 Segue")](images/ref06.png#lightbox) 
    
8. 將您的變更儲存到分鏡腳本。

當應用程式執行且使用者按一下您從與場景建立 Segue 此 UI 項目指定**分鏡腳本識別碼**將顯示來自外部的分鏡腳本分鏡腳本參考中所指定。

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>參考在相同的分鏡腳本的特定場景

若要新增特定場景相同分鏡腳本的參考，執行下列作業：

1. 在**方案總管 中**，連按兩下以開啟檔案進行編輯分鏡腳本。

2. 加入新的場景，並設計其版面配置，像平常一樣： 

    [![](images/ref11.png "新的場景版面配置")](images/ref11.png#lightbox)

3. 在**Widget**  索引標籤**屬性總管**，輸入**分鏡腳本識別碼**新場景檢視控制站： 

    [![](images/ref12.png "[小工具] 索引標籤")](images/ref12.png#lightbox)
    
3. 拖曳**分鏡腳本參考**從**工具箱**拖曳至設計介面： 

    [![](images/ref03.png "分鏡腳本參考")](images/ref03.png#lightbox)
    
5. 在**Widget**  索引標籤**屬性總管**，選取**參考識別碼**（分鏡腳本識別碼） 先前建立的場景： 

    [![](images/ref13.png "[小工具] 索引標籤")](images/ref13.png#lightbox)
    
6. 控制項按一下 UI 上的小工具 （例如按鈕） 現有的場景，並建立以新 Segue**分鏡腳本參考**您剛建立： 

    [![](images/ref14.png "建立 segue")](images/ref14.png#lightbox) 
    
7. 從快顯功能表選取**顯示**完成 Segue: 

    [![](images/ref06.png "選取顯示完成 Segue")](images/ref06.png#lightbox) 
    
8. 將您的變更儲存到分鏡腳本。

當應用程式執行且使用者按一下您從與場景建立 Segue 此 UI 項目指定**分鏡腳本識別碼**將顯示在相同的分鏡腳本分鏡腳本參考中所指定。

## <a name="summary"></a>總結

本文介紹的概念分鏡腳本和如何，都可能會將他們的 iOS 應用程式開發很有用。 它討論場景，檢視控制器、 檢視和檢視階層以及如何搭配不同類型的 Segues 連結場景。  它也會探討具現化檢視控制器以手動方式從分鏡腳本，並建立條件式 Segues。



## <a name="related-links"></a>相關連結

- [手動分鏡腳本 （範例）](https://developer.xamarin.com/samples/ManualStoryboard/)
- [IOS 設計工具簡介](~/ios/user-interface/designer/introduction.md)
- [轉換到分鏡腳本](http://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [UIStoryboard 類別參考](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
