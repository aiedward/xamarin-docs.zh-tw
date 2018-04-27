---
title: Hello, iOS：深度剖析
description: 這份含有兩部分的指南將說明如何使用 Visual Studio for Mac 或 Visual Studio 來建置基本的 Xamarin.iOS 應用程式，以及了解使用 Xamarin 進行 iOS 應用程式開發的基本知識。 其中將介紹建置和部署 Xamarin.iOS 應用程式所需的工具、概念和步驟。
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 61ba3a7e-fe11-4439-8bc8-9809512b8eff
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 71bfccdcab73b651f458dd8d9c5396bffd55004b
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
---
# <a name="hello-ios-deep-dive"></a>Hello, iOS 深度剖析

＜快速入門＞逐步解說，介紹如何建置和執行基本的 Xamarin.iOS 應用程式。 現在是時候更深入了解 iOS 應用程式的運作方式，讓您能夠建置更複雜的程式。 本指南會檢閱在 Hello, iOS 逐步解說中，能夠用來了解 iOS 應用程式開發基本概念的步驟。

此文章中會探索下列主題：


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

- **Visual Studio for Mac 簡介**：介紹 Visual Studio for Mac，並建立新的應用程式。
- **Xamarin.iOS 應用程式的結構**：導覽 Xamarin.iOS 應用程式的基本組件。
- **架構和應用程式基本概念**：檢閱 iOS 應用程式的組件以及組件之間的關聯性。
- **使用者介面 (UI)**：使用 iOS 設計工具來建立使用者介面。
- **檢視控制器和檢視生命週期**：介紹檢視生命週期，以及如何使用檢視控制器來管理內容檢視階層。
- **測試、部署及最後的修飾**：透過測試、部署、產生作品等等的建議來完成應用程式。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

- **Visual Studio 簡介**：介紹 Visual Studio，以及如何建立新的應用程式。
- **Xamarin.iOS 應用程式的結構**：導覽 Xamarin.iOS 應用程式的基本組件。
- **架構和應用程式基本概念**：檢閱 iOS 應用程式的組件以及組件之間的關聯性。
- **使用者介面 (UI)**：使用 iOS 設計工具來建立使用者介面。
- **檢視控制器和檢視生命週期**：介紹檢視生命週期，以及如何使用檢視控制器來管理內容檢視階層。
- **測試、部署及最後的修飾**：透過測試、部署、產生作品等等的建議來完成應用程式。

-----

本指南將協助您發展建置單一畫面之 iOS 應用程式所需的技能與知識。 透過它運作之後，您應該能夠了解 Xamarin.iOS 應用程式的不同組件，以及如何將它們結合在一起。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="introduction-to-visual-studio-for-mac"></a>Visual Studio for Mac 簡介

Visual Studio for Mac 是一個免費的開放原始碼 IDE，結合了來自 Visual Studio 和 XCode 的功能。 其中包含完全整合的視覺化設計工具、具有重構工具的文字編輯器、組件瀏覽器、原始程式碼整合等等。 本指南介紹一些基本的 Visual Studio for Mac 功能，但如果您還不熟悉 Visual Studio for Mac，請參閱 [Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/) 文件。

Visual Studio for Mac 遵循 Visual Studio 的做法，將程式碼組織成「方案」和「專案」。 方案是可以容納一或多個專案的容器。 專案可以是應用程式 (例如 iOS 或 Android)、支援程式庫、測試應用程式等等。 在 Phoneword 應用程式中，使用了**單一檢視應用程式**範本來新增新的 iPhone 專案。 最初的方案看起來如下：

![](hello-ios-deepdive-images/image30.png "最初方案的螢幕擷取畫面")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="introduction-to-visual-studio"></a>Visual Studio 簡介

Visual Studio 是 Microsoft 功能強大的 IDE。 其中包含完全整合的視覺化設計工具、具有重構工具的文字編輯器、組件瀏覽器、原始程式碼整合等等。 本指南介紹一些可搭配 Xamarin 外掛程式使用的基本 Visual Studio 功能。

Visual Studio 會將程式碼組織成「方案」和「專案」。 方案是可以容納一或多個專案的容器。 專案可以是應用程式 (例如 iOS 或 Android)、支援程式庫、測試應用程式等等。 在 Phoneword 應用程式中，使用了**單一檢視應用程式**範本來新增新的 iPhone 專案。 最初的方案看起來如下：

![](hello-ios-deepdive-images/vs-image30.png "最初方案的螢幕擷取畫面")

-----

<a name="anatomy" />

## <a name="anatomy-of-a-xamarinios-application"></a>Xamarin.iOS 應用程式的結構

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

左邊為 *Solution Pad*，其中包含目錄結構以及與方案相關聯的所有檔案：

![](hello-ios-deepdive-images/image31.png "Solution Pad，其中包含目錄結構以及與方案相關聯的所有檔案")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

右邊為「方案窗格」，其中包含目錄結構以及與方案相關聯的所有檔案：

![](hello-ios-deepdive-images/vs-image31.png "方案窗格，其中包含目錄結構以及與方案相關聯的所有檔案")

-----

在 [Hello, iOS](~/ios/get-started/hello-ios/hello-ios-quickstart.md) 逐步解說中，您已建立名為 **Phoneword** 的方案，並在其中放置了一個 iOS 專案 (**Phoneword_iOS**)。 專案內的項目包括：

-  **參考**：包含建置及執行應用程式所需的組件。 展開目錄以查看對 .NET 組件的參考，例如，[System](http://msdn.microsoft.com/library/system%28v=vs.110%29.aspx) \(機器翻譯\)、System.Core 和 [System.Xml](http://msdn.microsoft.com/library/system.xml%28v=vs.110%29.aspx) \(機器翻譯\)，以及對 Xamarin 之 Xamarin.iOS 組件的參考。
-  **套件**：套件目錄包含現成的 NuGet 套件。
-  **資源**：資源資料夾會儲存其他媒體。
-  **Main.cs**：這當中包含應用程式的主要進入點。 為了啟動應用程式，會傳入主應用程式類別 (`AppDelegate`) 的名稱。
-  **AppDelegate.cs**：這個檔案包含主應用程式類別，並負責建立視窗、建置使用者介面，以及接聽來自作業系統的事件。
-  **Main.storyboard**：分鏡腳本包含應用程式使用者介面的視覺化設計。 分鏡腳本檔案會在名為 iOS 設計工具的圖形化編輯器中開啟。
-  **ViewController.cs**：檢視控制器會提供使用者所看見和觸控的畫面 (檢視)。 檢視控制器會負責處理使用者和檢視之間的互動。
-  **ViewController.designer.cs**：`designer.cs` 是一個自動產生的檔案，可在檢視中的控制項與它們在檢視控制器中的程式碼表示法之間產生黏附作用。 由於這是內部連接檔案，因此，IDE 將覆寫任何手動變更，而且在大部分情況下均可忽略此檔案。 如需視覺化設計工具與支援程式碼之間關聯性的詳細資訊，請參閱 [iOS 設計工具簡介](~/ios/user-interface/designer/introduction.md)指南。
-  **Info.plist**：`Info.plist` 為設定應用程式屬性 (例如應用程式名稱、圖示、啟動影像等) 的地方。 這是一個功能強大的檔案，您可以在[運用屬性清單](~/ios/app-fundamentals/property-lists.md)指南中找到它的完整介紹。
-  **Entitlements.plist**：權利屬性清單可讓我們指定應用程式「功能」(也稱為 App Store 技術)，例如 iCloud、PassKit 等等。 更多有關 `Entitlements.plist` 的資訊可在[運用屬性清單](~/ios/app-fundamentals/property-lists.md)指南中找到。 如需權利的一般簡介，請參閱[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)指南。

## <a name="architecture-and-app-fundamentals"></a>架構和應用程式基本概念

有兩件事必須先準備就緒，iOS 應用程式才能載入使用者介面。 首先，應用程式必須定義「進入點」，此為將應用程式的處理序載入記憶體時所執行的第一個程式碼。 其次，它需要定義類別來處理整個應用程式的事件，並與作業系統互動。

本節將探究下圖中所述的關聯性：

[![](hello-ios-deepdive-images/image32.png "此圖說明了架構和應用程式基本概念的關聯性")](hello-ios-deepdive-images/image32.png#lightbox)

讓我們從頭開始了解應用程式啟動時會發生什麼事。

### <a name="main"></a>主要

iOS 應用程式的主要進入點是 `Main.cs` 檔案。 `Main.cs` 包含靜態的 Main 方法來建立新的 Xamarin.iOS 應用程式執行個體，並傳遞將處理 OS 事件之「應用程式委派」類別的名稱。 以下顯示靜態 `Main` 方法的範本程式碼：

```csharp
using System;
using UIKit;

namespace Phoneword_iOS
{
    public class Application
    {
        static void Main (string[] args)
        {
            UIApplication.Main (args, null, "AppDelegate");
        }
    }
}
```

### <a name="application-delegate"></a>應用程式委派

在 iOS 中，「應用程式委派」類別會處理系統事件；此類別存留於 `AppDelegate.cs` 內。 `AppDelegate` 類別會管理應用程式「視窗」。 視窗是 `UIWindow` 類別的單一執行個體，可作為使用者介面的容器。 根據預設，應用程式只會取得一個要載入其內容的視窗，並將此視窗連結至「畫面」(單一 `UIScreen` 執行個體)，以提供符合實體裝置畫面維度的週框。

*AppDelegate* 也會負責訂閱有關重要應用程式事件的系統更新，例如，當應用程式完成啟動時，或當記憶體不足時。

以下顯示 AppDelegate 的範本程式碼：

```csharp
using System;
using Foundation;
using UIKit;

namespace Phoneword_iOS
{

    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        public override UIWindow Window {
            get;
            set;
        }

        ...
    }
}
```

當應用程式定義其視窗之後，就可開始載入使用者介面。 下一節將探索 UI 建立。

## <a name="user-interface"></a>使用者介面

iOS 應用程式的使用者介面就像一個店面：應用程式通常會取得一個視窗，但可在該視窗內填入其所需的物件數目，並根據應用程式想要顯示的內容來變更物件和排列方式。 此案例中的物件 (使用者所看見的內容) 稱為「檢視」。 為了在應用程式中建置單一畫面，檢視會在「內容檢視階層」中彼此堆疊，並由單一檢視控制器管理該階層。 具有多個畫面的應用程式會有多個內容檢視階層，每個都有它自己的檢視控制器，而應用程式會將檢視放置於視窗中，以根據使用者所在畫面建立不同的內容檢視階層。

本節將藉由說明檢視、內容檢視階層及 iOS 設計工具來探討使用者介面。

### <a name="ios-designer-and-storyboards"></a>iOS 設計工具和分鏡腳本

iOS 設計工具是用來在 Xamarin 中建置使用者介面的視覺化工具。 按兩下任一分鏡腳本 (.storyboard) 檔案，即可啟動設計工具，其將會開啟到類似下列螢幕擷取畫面的檢視：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](hello-ios-deepdive-images/image33.png "iOS 設計工具介面")

「分鏡腳本」是一個檔案，其中包含我們應用程式畫面的視覺化設計，以及畫面之間的轉換和關聯性。 分鏡腳本中應用程式畫面的表示法稱為「場景」。 每個場景均代表一個檢視控制器，以及其所管理之檢視的堆疊 (內容檢視階層)。 從範本建立新的**單一檢視應用程式**專案時，Visual Studio for Mac 會自動產生名為 `Main.storyboard` 的分鏡腳本檔案並填入單一場景，如下列螢幕擷取畫面所示：

![](hello-ios-deepdive-images/image34.png "Visual Studio for Mac 會自動產生名為 Main.storyboard 的分鏡腳本檔案並填入單一場景")

您可以選取分鏡腳本畫面底部的黑色列來選擇適用於場景的檢視控制器。 檢視控制器是 `UIViewController` 類別的執行個體，其中包含支援內容檢視階層的程式碼。 此檢視控制器上的屬性可在 **Properties Pad** 內進行檢視和設定，如下列螢幕擷取畫面所示：

![](hello-ios-deepdive-images/image35.png "[屬性] 窗格")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](hello-ios-deepdive-images/vs-image33.png "iOS 設計工具介面")

「分鏡腳本」是一個檔案，其中包含我們應用程式畫面的視覺化設計，以及畫面之間的轉換和關聯性。 分鏡腳本中應用程式畫面的表示法稱為「場景」。 每個場景均代表一個檢視控制器，以及其所管理之檢視的堆疊 (內容檢視階層)。 從範本建立新的**單一檢視應用程式**專案時，Visual Studio 會自動產生名為 `Main.storyboard` 的分鏡腳本檔案並填入單一場景，如下列螢幕擷取畫面所示：

![](hello-ios-deepdive-images/vs-image34.png "Visual Studio 會自動產生名為 Main.storyboard 的分鏡腳本檔案並填入單一場景")

您可以選取分鏡腳本畫面底部的列來選擇適用於場景的檢視控制器。 檢視控制器是 `UIViewController` 類別的執行個體，其中包含支援內容檢視階層的程式碼。 此檢視控制器上的屬性可在**屬性窗格**內進行檢視和設定，如下列螢幕擷取畫面所示：

![](hello-ios-deepdive-images/vs-image35.png "[屬性] 窗格")

-----

「檢視」可藉由在場景的白色部分內按一下來選取。 檢視是 `UIView` 類別的執行個體，會定義畫面的區域，並提供介面來運用該區域中的內容。 預設檢視是單一「根檢視」，可填滿整個裝置畫面。

場景左邊是具有旗標圖示的灰色箭號，如下列螢幕擷取畫面所示：

 [![](hello-ios-deepdive-images/image37.png "具有旗標圖示的灰色箭號")](hello-ios-deepdive-images/image37.png#lightbox)

灰色箭號代表名為 *Segue* (唸成 "seg way") 的分鏡腳本轉換。 由於此 Segue 不具任何來源，因此稱為「無來源的 Segue」。 無來源的 Segue 會指向第一個場景，其檢視會在應用程式啟動時載入至應用程式的視窗。 場景及其內部的檢視將是應用程式載入時，使用者看到的第一件事。

建置使用者介面時，可以將額外的檢視從**工具箱**拖曳至設計介面上的主要檢視，如下列螢幕擷取畫面所示：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](hello-ios-deepdive-images/image38.png "可以將額外的檢視從工具箱拖曳至設計介面上的主要檢視")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](hello-ios-deepdive-images/vs-image38.png "可以將額外的檢視從工具箱拖曳至設計介面上的主要檢視")

-----

這些額外的檢視稱為「子檢視」。 根檢視和子檢視同時都屬於 `ViewController` 所管理之「內容檢視階層」的一部分。 場景中所有元素之大綱的檢視方式是在 [文件大綱] 面板上進行檢查：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](hello-ios-deepdive-images/image39.png "[文件大綱] 面板")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](hello-ios-deepdive-images/vs-image39.png "[文件大綱] 面板")

-----

下圖中會將子檢視反白顯示：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](hello-ios-deepdive-images/image40.png "圖中會將子檢視反白顯示")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](hello-ios-deepdive-images/vs-image40.png "圖中會將子檢視反白顯示")

-----

下一節將分解此場景所代表的內容檢視階層。

## <a name="content-view-hierarchy"></a>內容檢視階層

「內容檢視階層」是指由單一檢視控制器所管理之檢視和子檢視的堆疊，如下圖所示：

 [![](hello-ios-deepdive-images/image41.png "內容檢視階層")](hello-ios-deepdive-images/image41.png#lightbox)

我們可以在 **Properties Pad** 的 [檢視] 區段中，將根檢視的背景色彩暫時變更為黃色，讓 `ViewController` 的內容檢視階層變得更容易查看，如下列螢幕擷取畫面所示：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](hello-ios-deepdive-images/image42.png "在 Properties Pad 的 [檢視] 區段中，將根檢視的背景色彩暫時變更為黃色")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](hello-ios-deepdive-images/vs-image42.png "在 Properties Pad 的 [檢視] 區段中，將根檢視的背景色彩暫時變更為黃色")

-----

下圖說明視窗、檢視、子檢視及檢視控制器之間的關聯性，以在裝置畫面中顯示使用者介面：

 [![](hello-ios-deepdive-images/image43.png "視窗、檢視、子檢視及檢視控制器之間的關聯性")](hello-ios-deepdive-images/image43.png#lightbox)

下一節將討論如何在程式碼中運用檢視，以及了解如何使用檢視控制器和檢視生命週期，針對使用者互動進行程式設計。

## <a name="view-controllers-and-the-view-lifecycle"></a>檢視控制器和檢視生命週期

每個內容檢視階層都具有相對應的檢視控制器來提供使用者互動。 檢視控制器的角色是管理內容檢視階層中的檢視。 檢視控制器不是內容檢視階層的一部分，它不是介面中的元素。 而是提供程式碼，利用畫面上的物件來提供使用者互動。

### <a name="view-controllers-and-storyboards"></a>檢視控制器和分鏡腳本

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

檢視控制器會在分鏡腳本中顯示為場景底部的列。 選取檢視控制器，會在 **Properties Pad** 中顯示其屬性：

![](hello-ios-deepdive-images/image44.png "選取檢視控制器，會在屬性窗格中顯示其屬性")

您可以在 **Properties Pad** 的 [身分識別] 區段中編輯**類別**屬性，來設定此場景所呈現之內容檢視階層的自訂檢視控制器類別。 例如，我們的 **Phoneword** 應用程式會將 `ViewController` 設定為第一個畫面的檢視控制器，如下列螢幕擷取畫面所示：

![](hello-ios-deepdive-images/image45new.png "Phoneword 應用程式會將 ViewController 設定為檢視控制器")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

檢視控制器會在分鏡腳本中顯示為場景底部的列。 選取檢視控制器，會在**屬性窗格**中顯示其屬性：

![](hello-ios-deepdive-images/vs-image44.png "選取檢視控制器，會在屬性窗格中顯示其屬性")

您可以在**屬性窗格**的 [身分識別] 區段中編輯**類別**屬性，來設定此場景所呈現之內容檢視階層的自訂檢視控制器類別。 例如，我們的 **Phoneword** 應用程式會將 `ViewController` 設定為第一個畫面的檢視控制器，如下列螢幕擷取畫面所示：

![](hello-ios-deepdive-images/vs-image45.png "Phoneword 應用程式會將 ViewController 設定為檢視控制器")

-----

這樣會將檢視控制器的分鏡腳本表示法連結至 `ViewController` C# 類別。 開啟 `ViewController.cs` 檔案，並注意檢視控制器是 `UIViewController` 的「子類別」，如下列程式碼所示：

```csharp
public partial class ViewController : UIViewController
{
    public ViewController (IntPtr handle) : base (handle)
    {

    }
}
```

`ViewController` 現在會在分鏡腳本中衍生與此檢視控制器相關聯之內容檢視階層的互動。 接下來，將藉由介紹名為「檢視生命週期」的程序，來讓您了解檢視控制器在管理檢視中所扮演的角色。

> [!NOTE]
> 針對不需使用者互動的僅供檢視畫面，**類別**屬性可以在 **Properties Pad** 中保留空白。 這會將檢視控制器的支援類別設定為 `UIViewController` 的預設實作，這適用於您不打算新增自訂程式碼時。

### <a name="view-lifecycle"></a>檢視生命週期

檢視控制器會負責從視窗載入和卸載內容檢視階層。 當內容檢視階層中的檢視發生了某個重要事件時，作業系統會透過檢視生命週期中的事件來通知檢視控制器。 藉由覆寫檢視生命週期中的方法，您就可以與畫面上的物件互動，並建立動態且可回應的使用者介面。

以下為基本的週期方法及其函式：

-  **ViewDidLoad**：會在檢視控制器第一次將其內容檢視階層載入至記憶體時呼叫「一次」。 這是執行初始安裝的好地方，因為它是程式碼中子檢視首次變成可用的時候。
-  **ViewWillAppear**：每當檢視控制器的檢視即將新增至內容檢視階層並出現在畫面上時呼叫。
-  **ViewWillDisappear**：每當檢視控制器的檢視即將從內容檢視階層移除並從畫面上消失時呼叫。 這個生命週期事件適用於清除和儲存狀態。
-  **ViewDidAppear** 和 **ViewDidDisappear**：分別在從內容檢視階層新增或移除檢視時呼叫。


將自訂程式碼新增至生命週期的任何階段時，必須「覆寫」該生命週期方法的「基底實作」。 達成此目的之方法是點選至現有的生命週期方法 (其中具有已經附加至該方法的一些程式碼)，並使用額外的程式碼加以擴充。 基底實作是從方法內部呼叫，以確定原始程式碼會在新的程式碼之前執行。 下一節將示範此範例。

如需運用檢視控制器的詳細資訊，請參閱 Apple 的[適用於 iOS 的檢視控制器程式設計指南](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/ViewLoadingandUnloading/ViewLoadingandUnloading.html) \(英文\) 和 [UIViewController 參考](https://developer.apple.com/library/ios/documentation/uikit/reference/UIViewController_Class/Reference/Reference.html) \(英文\)。

### <a name="responding-to-user-interaction"></a>回應使用者互動

檢視控制器的最重要角色就是回應使用者互動，例如按下按鈕、巡覽等等。 處理使用者互動的最簡單方式是，連接控制項來接聽使用者輸入，並連結事件處理常式來回應輸入。 例如，可連接按鈕來回應觸控事件，如 Phoneword 應用程式中所示範。

現在已對檢視和檢視控制器有更深入了解，讓我們來探索其運作方式。
在 `Phoneword_iOS` 專案中，已將名為 `TranslateButton` 的按鈕新增至內容檢視階層：

 [![](hello-ios-deepdive-images/image1.png "已將名為 TranslateButton 的按鈕新增至內容檢視階層")](hello-ios-deepdive-images/image1.png#lightbox)

將**名稱**指派給 **Properties Pad** 的**按鈕**控制項時，iOS 設計工具會自動將它對應到 **ViewController.designer.cs** 中的控制項，使 `TranslateButton` 在 `ViewController` 類別內部變成可供使用。 控制項會先在檢視生命週期的 `ViewDidLoad` 階段變成可供使用，因此可使用這個生命週期方法來回應使用者的觸控：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // wire up TranslateButton here
}
```

Phoneword 應用程式會使用名為 `TouchUpInside` 的觸控事件來接聽使用者的觸控。 `TouchUpInside` 會接聽控制項界限內的向上觸控事件 (手指離開畫面)，此事件緊接在向下觸控 (手指觸控畫面) 之後。 `TouchUpInside` 的相反是 `TouchDown` 事件，其會在使用者按下控制項時觸發。 `TouchDown` 事件會擷取大量雜訊，而且不會為使用者提供選項，使其可藉由將手指滑離控制項來取消觸控。 `TouchUpInside` 是回應**按鈕**觸控的最常見方式，並會建立使用者按下按鈕時所預期的體驗。 您可以在 Apple 的 [iOS 人性化介面指導方針](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/MobileHIG/index.html) \(英文\) 中取得關於此內容的詳細資訊。

應用程式使用 lambda 來處理 `TouchUpInside` 事件，但可能也使用了委派或具名的事件處理常式。 最後的按鈕程式碼如下：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    string translatedNumber = "";

    TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
      translatedNumber = Core.PhonewordTranslator.ToNumber(PhoneNumberText.Text);
      PhoneNumberText.ResignFirstResponder ();

      if (translatedNumber == "") {
        CallButton.SetTitle ("Call", UIControlState.Normal);
        CallButton.Enabled = false;
      } else {
        CallButton.SetTitle ("Call " + translatedNumber, UIControlState.Normal);
        CallButton.Enabled = true;
      }
  };
}
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Phoneword 中導入的其他概念

Phoneword 應用程式引入本指南未涵蓋的數個概念。 這些概念包括：

- **變更按鈕文字**：Phoneword 應用程式示範了如何呼叫**按鈕**上的 `SetTitle`，然後傳入新的文字及**按鈕**的「控制項狀態」，來變更**按鈕**的文字。 例如，下列程式碼會將 CallButton 的文字變更為 “Call”：

    ```csharp
    CallButton.SetTitle ("Call", UIControlState.Normal);
    ```
- **啟用和停用按鈕**：**按鈕**可以處於 `Enabled` 或 `Disabled` 狀態。 已停用的**按鈕**將不會回應使用者輸入。 例如，下列程式碼會停用 `CallButton`：CallButton.Enabled = false；如需有關按鈕的詳細資訊，請參閱[按鈕](~/ios/user-interface/controls/buttons.md)指南。
- **關閉鍵盤**：當使用者點選文字欄位時，iOS 會顯示鍵盤，讓使用者能夠進行輸入。 不幸的是，沒有任何內建的功能可關閉鍵盤。 您可以將下列程式碼新增至 `TranslateButton`，在使用者按下 `TranslateButton` 時關閉鍵盤：PhoneNumberText.ResignFirstResponder ()；如需關閉鍵盤的其他範例，請參閱[關閉鍵盤](https://developer.xamarin.com/recipes/ios/input/keyboards/dismiss_the_keyboard)配方。
- **使用 URL 進行通話**：在 Phoneword 應用程式中，使用 Apple URL 配置來啟動系統手機應用程式。 自訂的 URL 配置包含 “tel:” 首碼和轉譯的電話號碼，如下列程式碼所示：

    ```csharp
    var url = new NSUrl ("tel:" + translatedNumber);
    if (!UIApplication.SharedApplication.OpenUrl (url))
    {
        // show alert Controller
    }
    ```
- **顯示警示**：當使用者嘗試在不支援通話的裝置 (例如模擬器或 iPod Touch) 上撥打電話時，即會顯示警示對話方塊來通知使用者無法撥號。 下列程式碼會建立並填入警示控制器：

    ```csharp
    if (!UIApplication.SharedApplication.OpenUrl (url)) {
                    var alert = UIAlertController.Create ("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
                    alert.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                    PresentViewController (alert, true, null);
                }
    ```

如需 iOS 警示檢視的詳細資訊，請參閱[警示控制器配方](https://developer.xamarin.com/recipes/ios/standard_controls/alertcontroller/)。

## <a name="testing-deployment-and-finishing-touches"></a>測試、部署及最後的修飾

Visual Studio for Mac 和 Visual Studio 都會提供許多選項來測試和部署應用程式。 本節涵蓋偵錯選項、示範在裝置上測試應用程式，並介紹用來建立自訂應用程式圖示和啟動影像的工具。

### <a name="debugging-tools"></a>偵錯工具

應用程式程式碼中的問題有時很難診斷。 若要協助診斷複雜的程式碼問題，您可以[設定中斷點](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/set_a_breakpoint/)、[逐步執行程式碼](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/step_through_code/)或[將資訊輸出至記錄視窗](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/output_information_to_log_window/)。

### <a name="deploy-to-a-device"></a>部署到裝置

iOS 模擬器是一個快速測試應用程式的方式。 此模擬器有數個實用的最佳化可用來進行測試，包括模擬位置、[模擬移動](https://developer.xamarin.com/recipes/ios/multitasking/test_location_changes_in_simulator/)，以及更多項目。 不過，使用者將不會取用模擬器中的最終應用程式。 所有應用程式都應儘早且經常在實際裝置上進行測試。

裝置需要時間佈建，並且需要 Apple 開發人員帳戶。 [裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)指南提供準備好裝置以進行開發的完整指示。

> [!NOTE]
> 目前，由於 Apple 的要求，因此需要具有開發憑證或簽署身分識別，才能針對裝置或模擬器建置程式碼。 請依照[裝置佈建指南](~/ios/get-started/installation/device-provisioning/manual-provisioning.md)中的步驟來設定此項。

佈建裝置之後，您可插入裝置、將建置工具列中的目標變更為 iOS 裝置，然後按 [啟動] ([播放]) 以部署到裝置，如下列螢幕擷取畫面所示：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](hello-ios-deepdive-images/image46new.png "按啟動/播放")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](hello-ios-deepdive-images/vs-image46.png "按啟動/播放")

-----

應用程式將會部署到 iOS 裝置：

[![](hello-ios-deepdive-images/image1.png "應用程式將會部署到 iOS 裝置並執行")](hello-ios-deepdive-images/image1.png#lightbox)

### <a name="generate-custom-icons-and-launch-images"></a>產生自訂圖示和啟動影像

不是每個人都有設計工具可用來建立應用程式需要凸顯的自訂圖示和啟動映像。以下是數種用來產生自訂應用程式作品的替代方法：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

- [**Sketch**](https://www.sketchapp.com") \(英文\)：Sketch 是一個用來設計使用者介面、圖示等內容的 Mac 應用程式。 這是用來設計 Xamarin 應用程式圖示和啟動影像集的應用程式。 App Store 上目前提供 Sketch 3。 您也可以試用免費的 [Sketch 工具](http://bohemiancoding.com/sketch/tool/) \(英文\)。
- [**Pixelmator**](http://www.pixelmator.com/) \(英文\)：適用於 Mac 的多用途影像編輯應用程式，成本大約 $30 美元。
- [**Glyphish**](http://www.glyphish.com/) \(英文\)：高品質的預先建置圖示集，可供免費下載及購買。
- [**Fiverr**](http://www.fiverr.com/) \(英文\)：從各種不同的設計工具進行選擇來建立您適用的圖示集，從 $5 美元開始。  如果您需要動態設計的圖示，可以叫用或略過，但是個良好的資源

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

* Visual Studio：您可以使用此產品，直接在 IDE 中為您的應用程式建立簡單的圖示集。
- [**Glyphish**](http://www.glyphish.com/) \(英文\)：高品質的預先建置圖示集，可供免費下載及購買。
- [**Fiverr**](http://www.fiverr.com/) \(英文\)：從各種不同的設計工具進行選擇來建立您適用的圖示集，從 $5 美元開始。  如果您需要動態設計的圖示，可以叫用或略過，但是個良好的資源

-----

如需有關圖示和啟動影像大小及需求的詳細資訊，請參閱[運用影像指南](~/ios/app-fundamentals/images-icons/index.md)。

## <a name="summary"></a>總結

恭喜您！ 您現在已充分了解 Xamarin.iOS 應用程式的元件，以及用來建立它們的工具。
在[快速入門系列的下一個教學課程](~/ios/get-started/hello-ios-multiscreen/index.md)中，您將會擴充我們的應用程式來處理多個畫面。 過程中，您將實作巡覽控制器、了解分鏡腳本 Segue，並在您擴充應用程式來處理多個畫面時，導入模型、檢視、控制器 (MVC) 模式。


## <a name="related-links"></a>相關連結

- [Hello, iOS (範例)](https://developer.xamarin.com/samples/monotouch/Hello_iOS/) \(英文\)
- [iOS 人性化介面指導方針](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html) \(英文\)
- [iOS 佈建入口網站](https://developer.apple.com/ios/manage/overview/index.action) \(英文\)
