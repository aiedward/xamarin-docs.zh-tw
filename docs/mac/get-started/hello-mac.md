---
title: Hello, Mac – 逐步解說
description: 本文件會示範如何建立 Xamarin.Mac 應用程式，並介紹 Visual Studio for Mac、Xcode 和介面產生器。 並討論透過輸出和動作將 UI 控制項公開到程式碼，以及示範如何建置、執行及測試 Xamarin.Mac 應用程式。
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 37D0E9E6-979B-7069-B3BE-C5F0AF99BA72
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 09/02/2018
ms.openlocfilehash: f79a509e3122cbccbe6bf10040f8143432c9d66c
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2018
ms.locfileid: "43780502"
---
# <a name="hello-mac--walkthrough"></a>Hello, Mac – 逐步解說

Xamarin.Mac 允許在 C# 與 .NET 中使用與在 *Objective-C* 或 *Swift*. 中開發時使用的相同 macOS API 來開發完整的原生 Mac 應用程式。 由於 Xamarin.Mac 直接與 Xcode 整合，開發人員可以使用 Xcode 的 _Interface Builder_ 來建立應用程式的使用者介面 (或選擇直接在 C# 程式碼中建立)。

此外，由於 Xamarin.Mac 應用程式是以 C# 和 .NET 撰寫的，因此可以和 Xamarin.iOS 與 Xamarin.Android 行動應用程式共用，並能同時在各平台上提供原生體驗。

本文將逐步解說如何建置一個簡單的 **Hello, Mac** 應用程式，以計算按鈕被按的次數，同時也藉由這個程序來介紹使用 Xamarin.Mac、Visual Studio for Mac 和 Xcode 的 Interface Builder 建立 Mac 應用程式所需的重要概念：

[![](hello-mac-images/run02-sml.png "Hello, Mac 應用程式執行的範例")](hello-mac-images/run02.png#lightbox)

將涵蓋下列概念：

- **Visual Studio for Mac** – 介紹 Visual Studio for Mac，以及如何使用它來建立 Xamarin.Mac 應用程式。
- **Xamarin.Mac 應用程式的結構** – Xamarin.Mac 應用程式由什麼組成。
- **Xcode 的 Interface Builder** – 如何使用 Xcode 的 Interface Builder 來定義應用程式的使用者介面。
- **輸出和動作** – 如何使用「輸出」和「動作」，在使用者介面中連接控制項。
- **部署/測試** – 如何執行及測試 Xamarin.Mac 應用程式。

## <a name="requirements"></a>需求

Xamarin.Mac 應用程式開發需要：

- 執行 macOS High Sierra (10.13) 或更高版本的 Mac 電腦。
- [Xcode 9 或更高版本](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
- [Xamarin.Mac 與 Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation/) 的最新版本。

若要執行使用 Xamarin.Mac 建置的應用程式，您將需要：

- 執行 macOS 10.7 或更新版本的 Mac 電腦。

> [!WARNING]
> 即將推出的 Xamarin.Mac 4.8 版只會支援 macOS 10.9 或更高版本。
> 舊版 Xamarin.Mac 支援 macOS 10.7 或更高版本，但這些較舊的 macOS 版本缺乏足夠的 TLS 基礎結構，無法支援 TLS 1.2。 若要以 macOS 10.7 或 10.8 為目標，請使用 Xamarin.Mac 4.6 或更舊版本。

## <a name="starting-a-new-xamarinmac-app-in-visual-studio-for-mac"></a>在 Visual Studio for Mac 中啟動新的 Xamarin.Mac 應用程式

如上所述，本指南將逐步解說建立一個 Mac 應用程式 (稱為 `Hello_Mac`) 以將單一按鈕和標籤加入到主視窗的步驟。 按一下按鈕時，標籤將會顯示已按下的次數。

若要開始，請執行下列步驟：

1. 啟動 Visual Studio for Mac：

    [![](hello-mac-images/setup01-sml.png "主要 Visual Studio for Mac 介面")](hello-mac-images/setup01.png#lightbox)

2. 按一下 [新增專案] 按鈕以開啟 [新增專案] 對話方塊，並選取 [Mac] > [應用程式] > [Cocoa 應用程式]，然後按一下 [下一步] 按鈕：

    [![](hello-mac-images/setup02-sml.png "選取 Cocoa 應用程式")](hello-mac-images/setup02.png#lightbox)

3. [應用程式名稱] 請輸入 `Hello_Mac`，其他項目則保留預設值。 按一下 [下一步]：

    [![](hello-mac-images/setup03-sml.png "設定應用程式的名稱")](hello-mac-images/setup03.png#lightbox)

4. 確認新專案在您電腦上的位置：

    [![](hello-mac-images/setup04-sml.png "驗證新的解決方案詳細資料")](hello-mac-images/setup04.png#lightbox)

5. 按一下 [建立] 按鈕。

Visual Studio for Mac 會建立新的 Xamarin.Mac 應用程式，並顯示加入到應用程式方案中的預設檔案：

[![](hello-mac-images/project01-sml.png "新解決方案的預設檢視")](hello-mac-images/project01.png#lightbox)

Visual Studio for Mac 使用與 Visual Studio 2017 相同的**方案**與**專案**結構。 方案是一個容器，可保存一或多個專案；專案則可包含應用程式，並支援程式庫、測試應用程式等。[檔案] > [新增專案] 範本會自動建立方案與應用程式專案。

## <a name="anatomy-of-a-xamarinmac-application"></a>Xamarin.Mac 應用程式的結構

Xamarin.Mac 應用程式程式設計與使用 Xamarin.iOS 非常類似。 iOS 使用 CocoaTouch 架構，這是由 Mac 使用的精簡型 Cocoa。

來看看專案中的檔案：

- **Main.cs** 包含應用程式的主要進入點。 當應用程式啟動時，`Main` 類別包含執行的前幾個方法。
- **AppDelegate.cs** 包含 `AppDelegate` 類別，此類別負責接聽來自作業系統的事件。
- **Info.plist** 包含應用程式屬性，例如應用程式名稱、圖示等。
- **Entitlements.plist** 包含應用程式的註冊，並允許存取沙箱與 iCloud 支援等項目。
- **Main.storyboard** 定義應用程式的使用者介面 (視窗與功能表)，並透過 Segue 配置視窗之間的互相連線。 分鏡腳本是 XML 檔案，包含了檢視 (使用者介面元素) 的定義。 此檔案可由 Xcode 內的 Interface Builder 建立及維護。
- **ViewController.cs**  是主視窗的控制器。 控制器在另一篇文章中將有詳細說明，但現在控制器可視為任一特定檢視的主要引擎。
- **ViewController.designer.cs** 包含可協助整合主畫面使用者介面的連接程式碼。

下列各節將快速帶過其中的一部分檔案。 稍後針對它們將有更詳細的探討，但趁現在了解其基本概念是不錯的主意。

### <a name="maincs"></a>Main.cs

**Main.cs** 檔案非常簡單。 它包含靜態的 `Main` 方法，此方法會建立新的 Xamarin.Mac 應用程式執行個體，並傳遞將處理 OS 事件的類別名稱，在此案例中是 `AppDelegate` 類別：

```csharp
using System;
using System.Drawing;
using Foundation;
using AppKit;
using ObjCRuntime;

namespace Hello_Mac
{
    class MainClass
    {
        static void Main (string[] args)
        {
            NSApplication.Init ();
            NSApplication.Main (args);
        }
    }
}
```

### <a name="appdelegatecs"></a>AppDelegate.cs

`AppDelegate.cs` 檔案包含 `AppDelegate` 類別，該類別負責建立視窗和接聽 OS 事件：

```csharp
using AppKit;
using Foundation;

namespace Hello_Mac
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        public AppDelegate ()
        {
        }

        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
    }
}
```

除非開發人員之前已建置過 iOS 應用程式，否則可能並不熟悉此程式碼，但它其實相當簡單。

`DidFinishLaunching` 方法會在應用程式具現化後執行，而且實際上負責建立應用程式的視窗，並開始在其中顯示檢視的程序。

當使用者或系統具現化應用程式的關機時，將會呼叫 `WillTerminate` 方法。 開發人員應在此應用程式結束之前使用此方法將它完成，例如儲存使用者喜好設定或視窗大小和位置。

### <a name="viewcontrollercs"></a>ViewController.cs

Cocoa (和衍生產品 CocoaTouch) 使用所謂的「模型檢視控制器」(MVC) 模式。 `ViewController` 宣告表示會控制實際應用程式視窗的物件。 一般而言，針對建立的每個視窗 (以及視窗內的其他許多項目)，會有一個控制器負責視窗的生命週期，例如顯示視窗、在視窗中加入新的檢視 (控制項) 等。

`ViewController` 類別是主視窗的控制器。 控制器負責主視窗的生命週期。 現在請快速瀏覽它，稍後將會對它進行詳細檢驗：

```csharp
using System;

using AppKit;
using Foundation;

namespace Hello_Mac
{
    public partial class ViewController : NSViewController
    {
        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any additional setup after loading the view.
        }

        public override NSObject RepresentedObject {
            get {
                return base.RepresentedObject;
            }
            set {
                base.RepresentedObject = value;
                // Update the view, if already loaded.
            }
        }
    }
}
```

### <a name="viewcontrollerdesignercs"></a>ViewController.Designer.cs

主視窗類別的設計工具檔案一開始是空的，但使用 Xcode Interface Builder 建立使用者介面時，Visual Studio for Mac 會自動為其填入內容：

```csharp
// WARNING
//
// This file has been generated automatically by Visual Studio for Mac to store outlets and
// actions made in the UI designer. If it is removed, they will be lost.
// Manual changes to this file may not be handled correctly.
//
using Foundation;

namespace Hello_Mac
{
    [Register ("ViewController")]
    partial class ViewController
    {
        void ReleaseDesignerOutlets ()
        {
        }
    }
}
```

您不應該直接編輯設計工具檔案，因為它們是由 Visual Studio for Mac 自動管理，以提供必要的連接程式碼以允許存取已加入應用程式的任何視窗或檢視的控制項。

在建立 Xamarin.Mac 應用程式專案並對其元件有基本的了解後，請切換到 Xcode 以使用 Interface Builder 建立使用者介面。

### <a name="infoplist"></a>Info.plist

`Info.plist` 檔案包含 Xamarin.Mac 應用程式的相關資訊，例如其**名稱**和**套件組合識別碼**：

[![](hello-mac-images/infoplist01.png "Visual Studio for Mac Plist 編輯器")](hello-mac-images/infoplist01.png#lightbox)

它也會定義_分鏡腳本_，以用來顯示 [主要介面] 下拉式清單底下的 Xamarin.Mac 應用程式使用者介面。 在上面的範例中，下拉式清單中的 `Main` 與 [方案總管]  中專案的來源樹狀目錄中的 `Main.storyboard` 相關。 它也會定義應用程式的圖示，方法是指定包含它們的「資產目錄」(在此案例中是 **AppIcon**)。

### <a name="entitlementsplist"></a>Entitlements.plist

應用程式的 `Entitlements.plist` 檔案控制 Xamarin.Mac 應用程式擁有的權利，例如**沙箱功能**和 **iCloud**：

[![](hello-mac-images/entitlements01.png "Visual Studio for Mac 權利編輯器")](hello-mac-images/entitlements01.png#lightbox)

針對 Hello World 範例，將不需要任何權利。 下一節說明如何使用 Xcode 的 Interface Builder 來編輯 **Main.storyboard** 檔案，並定義 Xamarin.Mac 應用程式的 UI。

## <a name="introduction-to-xcode-and-interface-builder"></a>Xcode 和 Interface Builder 簡介

Interface Builder 是 Apple 建立的一種工具，屬於 Xcode 的一部分，並可讓開發人員在設計工具中以視覺化方式建立使用者介面。 Xamarin.Mac 與 Interface Builder 的流暢整合，可允許使用和 Objective-C 使用者的相同工具來建立 UI。

若要開始使用，請按兩下 [方案總管] 中的 [`Main.storyboard`] 檔案開啟它，以在 Xcode 和 Interface Builder 中編輯：

[![](hello-mac-images/xcode01.png "方案總管中的 Main.storyboard 檔案")](hello-mac-images/xcode01.png#lightbox)

這應該會啟動 Xcode，而且看起來像此螢幕擷取畫面一樣：

[![](hello-mac-images/xcode02.png "預設的 Xcode Interface Builder 檢視")](hello-mac-images/xcode02.png#lightbox)

開始設計介面之前，請先快速瀏覽 Xcode，以熟悉將使用的主要功能。

> [!NOTE]
> 開發人員不一定要使用 Xcode 和 Interface Builder 來建立 Xamarin.Mac 應用程式的使用者介面，而可以直接從 C# 程式碼建立 UI，但這不在本文的討論範圍內。 為了簡單起見，本教學課程的其餘部分將使用 Interface Builder 來建立使用者介面。

### <a name="components-of-xcode"></a>Xcode 的元件

從 Visual Studio for Mac 開啟 Xcode 中的 **.storyboard** 檔案時，[專案導覽器] 會在左側，[介面階層架構] 和 [介面編輯器] 在中間，[屬性和公用程式] 區段在右側：

[![](hello-mac-images/xcode03.png "Xcode 中不同的 Interface Builder 區段")](hello-mac-images/xcode03.png#lightbox)

下列各節探討這些 Xcode 功能各自的作用，以及如何使用它們來建立 Xamarin.Mac 應用程式的介面。

### <a name="project-navigation"></a>專案巡覽

開啟 **.storyboard** 檔案以在 Xcode 中編輯時，Visual Studio for Mac 會在背景建立「Xcode 專案檔」，以在其本身與 Xcode 之間傳達變更。 之後，當開發人員從 Xcode 切換回 Visual Studio for Mac 時，Visual Studio for Mac 會將對此專案所做的任何變更與 Xamarin.Mac 專案同步處理。

[專案巡覽] 區段可讓開發人員瀏覽組成這個「填充碼」Xcode 專案的所有檔案。 通常，他們只對此清單中的 `.storyboard` 檔案感興趣，例如 `Main.storyboard`。

### <a name="interface-hierarchy"></a>介面階層架構

[介面階層架構] 區段可讓開發人員輕鬆存取使用者介面的數個關鍵屬性，例如其 **Placeholders** 和主要 **Window** 屬性。 此區段可用來存取組成使用者介面的個別元素 (檢視)；您可以在階層內拖曳這些元素以調整內嵌的方式。

### <a name="interface-editor"></a>介面編輯器

[介面編輯器] 區段提供以圖形方式配置使用者介面的介面。從 [屬性和公用程式] 區段的 [程式庫] 區段拖曳元素以建立設計。 當使用者介面元素 (檢視) 加入到設計介面時，它們會以顯示在 [介面編輯器] 中的順序加入到 [介面階層架構] 區段。

### <a name="properties--utilities"></a>屬性和公用程式

[屬性和公用程式] 區段分成兩個主要區段：[屬性] (也稱為偵測器) 和 [程式庫]：

[![](hello-mac-images/xcode04.png "屬性偵測器")](hello-mac-images/xcode04.png#lightbox)

此區段最初幾乎是空白，但如果開發人員在 [介面編輯器] 或 [介面階層架構] 中選取元素，則 [屬性] 區段將填入和他們可調整的指定元素和屬性相關的資訊。

在 [屬性] 區段內，共有 8 個不同的「偵測器索引標籤」，如下圖所示：

[![](hello-mac-images/xcode05.png "所有偵測器的概觀")](hello-mac-images/xcode05.png#lightbox)

### <a name="properties--utility-types"></a>屬性和公用程式類型

這些索引標籤從左到右分別是：

- **檔案偵測器** – 檔案偵測器顯示檔案資訊，例如正在編輯之 Xib 檔案的檔案名稱和位置。
- **快速說明** – [快速說明] 索引標籤提供內容相關的說明，以在 Xcode 中選取的項目為基礎。
- **身分識別偵測器** – 身分識別偵測器提供關於所選控制項/檢視的相關資訊。
- **屬性偵測器** – 屬性偵測器可讓開發人員自訂所選控制項/檢視的各種屬性。
- **大小偵測器** – 大小偵測器可讓開發人員控制所選控制項/檢視的大小和調整大小行為。
- **連線偵測器** – 連線偵測器會顯示所選控制項的**輸出**和**動作**連線。 以下將詳細說明輸出和動作。
- **繫結偵測器** – 繫結偵測器可讓開發人員設定控制項，使其值自動繫結到資料模型。
- **檢視效果偵測器** – 檢視效果偵測器可讓開發人員指定控制項的效果，例如動畫。

使用 [程式庫] 區段來尋找要放入設計工具的控制項和物件，以圖形方式建立使用者介面：

[![](hello-mac-images/xcode06.png "Xcode 程式庫偵測器")](hello-mac-images/xcode06.png#lightbox)

## <a name="creating-the-interface"></a>建立介面

運用 Xcode IDE 和 Interface Builder 的基本概念，開發人員就能建立主要檢視的使用者介面。

依照這些步驟使用 Interface Builder：

1. 在 Xcode 中，從 [程式庫] 區段拖曳 [按鈕]：

    [![](hello-mac-images/xcode07.png "從 [程式庫偵測器] 中選取 [NSButton]")](hello-mac-images/xcode07.png#lightbox)

2. 將該按鈕拖曳至 [介面編輯器] 中的 [檢視] (在 [視窗控制器] 底下)：

    [![](hello-mac-images/xcode08.png "在介面設計新增按鈕")](hello-mac-images/xcode08.png#lightbox)

3. 按一下 [屬性偵測器] 中的 [標題] 屬性，將按鈕的標題變更為 **按一下我**：

    [![](hello-mac-images/xcode09.png "設定按鈕的屬性")](hello-mac-images/xcode09.png#lightbox)

4. 從 [程式庫] 區段拖曳 [標籤]：

    [![](hello-mac-images/xcode10.png "從 [程式庫偵測器] 中選取 [標籤]")](hello-mac-images/xcode10.png#lightbox)

5. 將標籤拖曳到 [介面編輯器] 中 [視窗] 上的按鈕旁：

    [![](hello-mac-images/xcode11.png "在介面設計新增標籤")](hello-mac-images/xcode11.png#lightbox)

6. 抓住標籤上的右側控點，將它拖曳到視窗邊緣附近為止：

    [![](hello-mac-images/xcode12.png "調整標籤大小")](hello-mac-images/xcode12.png#lightbox)

7. 選取剛才在 [介面編輯器] 中加入的「按鈕」，然後按一下 [條件約束編輯器] 圖示和視窗底部：

    [![](hello-mac-images/xcode13.png "新增按鈕的條件約束")](hello-mac-images/xcode13.png#lightbox)

8. 在編輯器頂端，按一下頂端及左側的**紅色 I 字形狀**。 調整視窗大小時，這會讓「按鈕」保持在畫面左上角的相同位置。

9. 接下來，核取 [高度] 和 [寬度] 方塊，並使用預設大小。 調整視窗大小時，這會讓「按鈕」保持相同大小。

10. 按一下 [加入 4 個條件約束] 按鈕以加入條件約束，並關閉編輯器。

11. 選取該標籤，然後再按一下 [條件約束編輯器] 圖示：

    [![](hello-mac-images/xcode14.png "新增標籤的條件約束")](hello-mac-images/xcode14.png#lightbox)

12. 按一下 [條件約束編輯器] 頂端、右側及左側的**紅色 I 字形狀**，會使標籤固定在其指定的 X 和 Y 位置，並隨執行中應用程式的視窗大小調整而放大和縮小。

13. 同樣地，核取 [高度] 方塊並使用預設大小，然後按一下 [加入 4 個條件約束] 按鈕以加入條件約束，並關閉編輯器。

14. 將變更儲存至使用者介面。

調整控制項大小和移動控制項時，請注意 Interface Builder 會根據 [macOS 人性化介面指導方針](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/) \(英文\) 提供有用的貼齊提示。 這些指導方針可協助開發人員建立有著 Mac 使用者熟悉之外觀與風格的高品質應用程式。

在 [介面階層架構] 區段中，查看構成使用者介面之元素的版面配置和階層架構如何顯示：

[![](hello-mac-images/xcode15.png "在 [介面階層架構] 中選取元素")](hello-mac-images/xcode15.png#lightbox)

如有需要，開發人員可從此處選取要編輯的項目，或拖曳項目以重新排列 UI 元素。 比方說，如果某個 UI 元素被另一個元素覆蓋，他們可以將它拖曳到清單底部，使其成為視窗最頂端的項目。

在建立使用者介面後，開發人員必須公開 UI 項目，Xamarin.Mac 才能在 C# 程式碼中存取它們並進行互動。 下一節的**輸出和動作**示範如何執行這項操作。

### <a name="outlets-and-actions"></a>輸出和動作

什麼是**輸出**和**動作**？ 在傳統的 .NET 使用者介面程式設計中，會在加入使用者介面的控制項時自動將其公開為屬性。 在 Mac 中的運作方式則不同，只將控制項加入至檢視是無法讓程式碼存取的。 開發人員必須明確地將 UI 元素公開到程式碼。 為了這樣做，Apple 提供兩個選項：

- **輸出** – 輸出類似屬性。 如果開發人員將控制項連接到「輸出」，它就會經由屬性公開到程式碼，也就能進行一些作業，例如附加事件處理常式、在其中呼叫方法等。
- **動作** – 動作類似 WPF 中的命令模式。 例如，在控制項上執行「動作」時，例如按一下按鈕，控制項將在程式碼中自動呼叫方法。 「動作」功能強大又方便，因為開發人員可以將許多控制項連接到相同的「動作」。

在 Xcode 中，**輸出**和**動作**是透過「拖曳控制項」直接加入程式碼中。 更具體來說，這表示若要建立**輸出**或**動作**，開發人員要選擇要加入**輸出**或**動作**的控制項元素，按住鍵盤上的 **Control** 鍵，然後將該控制項直接拖曳到程式碼。

對 Xamarin.Mac 開發人員而言，這意味著開發人員需將與 C# 檔案對應的 Objective-C Stub 檔案拖放到他們想建立**方法**或**動作**的位置。 為了使用 Interface Builder，Visual Studio for Mac 已產生填充碼 Xcode 專案，並在其中建立檔案 `ViewController.h`：

[![](hello-mac-images/xcode16-sml.png "在 Xcode 中檢視來源")](hello-mac-images/xcode16.png#lightbox)

這個 Stub `.h` 檔案會鏡像到新的 `NSWindow` 建立時自動加入到 Xamarin.Mac 專案的 `ViewController.designer.cs`。 此檔案將用於同步處理 Interface Builder 所做的變更，同時也是為了讓 UI 元素公開到 C# 程式碼而建立**輸出**和**動作**的位置。

#### <a name="adding-an-outlet"></a>加入輸出

了解**輸出**和**動作**的基本概念後，請建立**輸出**以將所建立的「標籤」公開到 C# 程式碼。

請執行下列動作：

1. 在 Xcode 中，於畫面較靠近右上角按一下 [雙圓形] 按鈕以開啟 [助理編輯器]：

    [![](hello-mac-images/outlet01.png "顯示 [助理編輯器]")](hello-mac-images/outlet01.png#lightbox)

2. Xcode 會切換成分割檢視模式，一邊是 [介面編輯器]，另一邊則是 [程式碼編輯器]。

3. 請注意，Xcode 已在 [程式碼編輯器] 中自動選取 [ViewController.m] 檔案，這並非正確的檔案。 從以上關於**輸出**和**動作**的討論，開發人員必須選取的是 [ViewController.h]。

4. 在 [程式碼編輯器] 的頂端按一下 [自動連結] 並選取 [`ViewController.h`] 檔案：

    [![](hello-mac-images/outlet02.png "選取正確的檔案")](hello-mac-images/outlet02.png#lightbox)

5. Xcode 現在應該已選取正確的檔案：

    [![](hello-mac-images/outlet03.png "檢視 ViewController.h 檔案")](hello-mac-images/outlet03.png#lightbox)

6. **最後一個步驟非常重要！** 若您未選取正確的檔案，將無法建立**輸出**與**動作**，或者它們將公開到 C# 中的錯誤類別！

7. 在 [介面編輯器] 中，按住鍵盤上的 **Control** 鍵，然後按一下先前建立的標籤並拖曳到程式碼編輯器的 `@interface ViewController : NSViewController {}` 程式碼底下：

    [![](hello-mac-images/outlet04.png "拖曳以建立輸出")](hello-mac-images/outlet04.png#lightbox)

8. 隨即顯示對話方塊。 將 [連接] 保留為**輸出**，在 [名稱] 中輸入 `ClickedLabel`：

    [![](hello-mac-images/outlet05.png "定義輸出")](hello-mac-images/outlet05.png#lightbox)

9. 按一下 [連接] 按鈕以建立**輸出**：

    [![](hello-mac-images/outlet06.png "檢視最終輸出")](hello-mac-images/outlet06.png#lightbox)

10. 將變更儲存到檔案。

#### <a name="adding-an-action"></a>加入動作

接下來，將按鈕公開到 C# 程式碼。 如同上面的「標籤」，開發人員可以將按鈕連接到**輸出**。 因為我們只想回應所按下的按鈕，因此請改用**動作**。

請執行下列動作：

1. 確認 Xcode 仍在 [助理編輯器] 畫面，而且在 [程式碼編輯器] 中可以看到 [ViewController.h] 檔案。

2. 在 [介面編輯器] 中，按住鍵盤上的 **Control** 鍵，然後按一下先前建立的按鈕並拖曳到程式碼編輯器的 `@property (assign) IBOutlet NSTextField *ClickedLabel;` 程式碼底下：

    [![](hello-mac-images/action01.png "拖曳以建立動作")](hello-mac-images/action01.png#lightbox)

3. 將 [連接] 類型變更為**動作**：

    [![](hello-mac-images/action02.png "定義動作")](hello-mac-images/action02.png#lightbox)

4. 輸入 `ClickedButton` 作為**名稱**：

    [![](hello-mac-images/action03.png "為新的動作命名")](hello-mac-images/action03.png#lightbox)

5. 按一下 [連接] 按鈕以建立**動作**：

    [![](hello-mac-images/action04.png "檢視最終動作")](hello-mac-images/action04.png#lightbox)

6. 將變更儲存到檔案。

連接使用者介面並公開到 C# 程式碼之後，請切換回 Visual Studio for Mac，讓它可以同步處理在 Xcode 和 Interface Builder 中所做的變更。

> [!NOTE]
> 雖然在為第一個應用程式建立使用者介面及**輸出**和**動作**可能花了很長的時間，而且看似頗費工夫，但其中導入許多新概念，而且許多時間是用來學習新領域。 在您練習使用 Interface Builder 一段時間後，只要一、兩分鐘就能建立這個介面及其所有**輸出**和**動作**。

### <a name="synchronizing-changes-with-xcode"></a>與 Xcode 同步處理變更

當開發人員從 Xcode 切換回 Visual Studio for Mac 時，他們在 Xcode 中所做的任何變更將自動與 Xamarin.Mac 專案同步處理。

在方案總管中選取 **ViewController.designer.cs**，以查看**輸出**和**動作**在 C# 程式碼中如何連接：

[![](hello-mac-images/sync01-sml.png "與 Xcode 同步處理變更")](hello-mac-images/sync01.png#lightbox)

注意 **ViewController.designer.cs** 檔案中的這兩個定義：

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

在 Xcode 的 `ViewController.h` 檔案中對齊這兩個定義：

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

Visual Studio for Mac 會接聽對 **.h** 檔案的變更，並在個別的 [.designer.cs] 檔案中自動同步處理那些變更，以將它們公開到應用程式。 請注意，[ViewController.designer.cs] 是部分類別，因此 Visual Studio for Mac 不需修改 [ViewController.cs]，這樣會覆寫開發人員對該類別所做的任何變更。

一般而言，開發人員永遠不需開啟 [ViewController.designer.cs]，此處提及它只是為了教學用。

> [!NOTE]
> 在大部分情況下，Visual Studio for Mac 會自動查看在 Xcode 中所做的任何變更，並將它們同步處理到 Xamarin.Mac 專案。 在關閉的情況下，同步處理不會自動發生，請切換回 Xcode，然後再次回到 Visual Studio for Mac。 這通常會啟動同步處理週期。

## <a name="writing-the-code"></a>撰寫程式碼

建立好使用者介面，並透過**輸出**和**動作**將其 UI 元素公開到程式碼之後，就可以準備撰寫程式碼讓程式實際運作。

針對此範例應用程式，每次按一下第一個按鈕時，就會更新標籤以顯示已按下按鈕的次數。 若要完成這項作業，在 [方案總管] 中按兩下 [`ViewController.cs`] 檔案以將它開啟：

[![](hello-mac-images/code01-sml.png "在 Visual Studio for Mac 中檢視 ViewController.cs 檔案")](hello-mac-images/code01.png#lightbox)

首先，在 `ViewController` 類別中建立一個類別層級變數來追蹤已按下的次數。 編輯類別定義，使它看起來如下所示：

```csharp
namespace Hello_Mac
{
    public partial class ViewController : NSViewController
    {
        private int numberOfTimesClicked = 0;
        ...
```

接下來，在同一類別 (`ViewController`) 中覆寫 `ViewDidLoad` 方法，並加入一些程式碼以設定標籤的初始訊息：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.StringValue = "Button has not been clicked yet.";
}
```

請使用 `ViewDidLoad` (而不是其他方法，例如 `Initialize`)，因為在 OS 從 **.storyboard** 檔案載入使用者介面並將其具現化「之後」，會呼叫 `ViewDidLoad`。 如果開發人員嘗試在 **.storyboard** 檔案完全載入並具現化之前存取標籤控制項，即會收到 `NullReferenceException` 錯誤，因為標籤控制項尚不存在。

接下來，加入程式碼以回應使用者按一下按鈕的動作。 將下列部分方法加入 `ViewController` 類別：

```csharp
partial void ClickedButton (Foundation.NSObject sender) {
    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

此程式碼會附加至在 Xcode 和 Interface Builder 中建立的**動作**，並於使用者按一下按鈕時呼叫。

## <a name="testing-the-application"></a>測試應用程式

現在應建置並執行應用程式，以確定它能如預期般執行。 開發人員可以在一個步驟中建置並執行，或可建置而不執行。

每當建置應用程式時，開發人員可以選擇他們想要的組建種類：

- **偵錯** – 偵錯組建會編譯到 **.app** (應用程式) 檔案，其中含有額外的中繼資料，可讓開發人員在應用程式執行時對發生的問題進行偵錯。
- **發行** – 發行組建也會建立 **.app** 檔案，但不含偵錯資訊，因此檔案較小，執行速度更快。

開發人員可以從 Visual Studio for Mac 畫面左上角的 [組態選取器] 選取組建的類型：

[![](hello-mac-images/run01-sml.png "選取偵錯組建")](hello-mac-images/run01.png#lightbox)

## <a name="building-the-application"></a>建置應用程式

在此範例的案例中，我們只需要偵錯組建，因此請確定已選取 [偵錯]。 先按下 **⌘B** 或從 [建置] 功能表選擇 [全部建置] 以建置應用程式。

如果沒有任何錯誤，Visual Studio for Mac 的狀態列會顯示 [建置成功] 訊息。 如果發生錯誤，請檢閱專案，並確定已正確依照上述步驟執行。 確認 Xcode 和 Visual Studio for Mac 兩者中的程式碼符合本教學課程中的程式碼。

## <a name="running-the-application"></a>執行應用程式

有三種方式可以執行應用程式：

- 按下 **⌘+Enter**。
- 從 [執行] 功能表中選擇 [偵錯]。
- 按一下 Visual Studio for Mac 工具列中的 [播放] 按鈕 (在 [方案總管] 上方)。

隨即建置應用程式 (如果尚未建置的話)，並以偵錯模式啟動，顯示其主要介面視窗：

[![](hello-mac-images/run02-sml.png "執行應用程式")](hello-mac-images/run02.png#lightbox)

如果多次按一下按鈕，標籤的次數應該會更新：

[![](hello-mac-images/run03-sml.png "顯示按一下按鈕的結果")](hello-mac-images/run03.png#lightbox)

## <a name="where-to-next"></a>延伸主題

具備使用第一個 Xamarin.Mac 應用程式的基本概念後，請參閱下列文件以更深入地了解：

- [分鏡腳本簡介](~/mac/platform/storyboards/index.md) - 本文提供在 Xamarin.Mac 應用程式中使用分鏡腳本的簡介。 其中涵蓋使用分鏡腳本和 Xcode 的 Interface Builder 建立與維護應用程式的 UI。
- [視窗](~/mac/user-interface/window.md) - 本文涵蓋在 Xamarin.Mac 應用程式中使用「視窗」和「面板」。 其中涵蓋在 Xcode 和 Interface Builder 中建立與維護「視窗」和「面板」、從 .xib 檔案載入「視窗」和「面板」、在 C# 程式碼中使用「視窗」和回應「視窗」。
- [對話方塊](~/mac/user-interface/dialog.md) - 本文涵蓋在 Xamarin.Mac 應用程式中使用「對話方塊」和「強制回應視窗」。 其中涵蓋在 Xcode 和 Interface Builder 中建立與維護「強制回應視窗」、使用標準對話方塊、在 C# 程式碼中顯示和回應「視窗」。
- [警示](~/mac/user-interface/alert.md) - 本文涵蓋在 Xamarin.Mac 應用程式中使用「警示」。 其中涵蓋從 C# 程式碼建立和顯示「警示」及回應「警示」。
- [功能表](~/mac/user-interface/menu.md) - 功能表運用在 Mac 應用程式使用者介面的各個部分，從畫面頂端的應用程式主功能表，到可顯示在視窗任何位置的快顯和操作功能表。 功能表是 Mac 應用程式使用者體驗不可或缺的一部分。 本文涵蓋在 Xamarin.Mac 應用程式中使用「Cocoa 功能表」。
- [工具列](~/mac/user-interface/toolbar.md) - 本文涵蓋在 Xamarin.Mac 應用程式中使用「工具列」。 它涵蓋建立及維護 Xcode 與 Interface Builder 中的「工具列」、如何使用「輸出」和「動作」將「工具列項目」公開到程式碼、啟用和停用「工具列項目」，最後在 C# 程式碼中回應「工具列項目」。
- [資料表檢視](~/mac/user-interface/table-view.md) - 本文涵蓋在 Xamarin.Mac 應用程式中使用「資料表檢視」。 其中涵蓋在 Xcode 和 Interface Builder 中建立與維護「資料表檢視」、如何使用「輸出」和「動作」將「資料表檢視項目」公開到程式碼、填入「資料表項目」，最後在 C# 程式碼中回應「資料表檢視項目」。
- [大綱檢視](~/mac/user-interface/outline-view.md) - 本文涵蓋在 Xamarin.Mac 應用程式中使用「大綱檢視」。 其中涵蓋在 Xcode 和 Interface Builder 中建立與維護「大綱檢視」、如何使用「輸出」和「動作」將「大綱檢視項目」公開到程式碼、填入「大綱項目」，最後在 C# 程式碼中回應「大綱檢視項目」。
- [來源清單](~/mac/user-interface/source-list.md) - 本文涵蓋在 Xamarin.Mac 應用程式中使用「來源清單」。 其中涵蓋在 Xcode 和 Interface Builder 中建立與維護「來源清單」、如何使用「輸出」和「動作」將「來源清單項目」公開到程式碼、填入「來源清單項目」，最後在 C# 程式碼中回應「來源清單項目」。
- [集合檢視](~/mac/user-interface/collection-view.md) - 本文涵蓋在 Xamarin.Mac 應用程式中使用「集合檢視」。 其中涵蓋在 Xcode 和 Interface Builder 中建立與維護「集合檢視」、如何使用「輸出」和「動作」將「集合檢視」元素公開到程式碼、填入「集合檢視」，最後在 C# 程式碼中回應「集合檢視」。
- [使用影像](~/mac/app-fundamentals/image.md) - 本文涵蓋在 Xamarin.Mac 應用程式中使用「影像」和「圖示」。 其中涵蓋建立與維護建立應用程式圖示所需的影像，以及在 C# 程式碼和 Xcode 的 Interface Builder 中使用影像。

[Mac 範例資源庫](https://developer.xamarin.com/samples/mac/all/)包含隨時可使用的程式碼範例，可協助您學習 Xamarin.Mac。

[SourceWriter 範例應用程式](https://developer.xamarin.com/samples/mac/SourceWriter/)是一個完整的 Xamarin.Mac 應用程式，其中包含使用者在典型的 Mac 應用程式中可以找到的許多功能。 SourceWriter 是簡單的原始程式碼編輯器，可支援程式碼完成功能和簡單的語法反白顯示。

SourceWriter 程式碼有完整註解，在適當的情況下會提供從關鍵技術或方法到 Xamarin.Mac 文件中相關資訊的連結。

## <a name="summary"></a>總結

本文涵蓋標準 Xamarin.Mac 應用程式的基本概念。 其中涵蓋在 Visual Studio for Mac 中建立新的應用程式、在 Xcode 和 Interface Builder 中設計使用者介面、使用**輸出**和**動作**將 UI 元素公開到 C# 程式碼、加入程式碼搭配 UI 元素運作，最後建置和測試 Xamarin.Mac 應用程式。

## <a name="related-links"></a>相關連結

- [Hello, Mac (範例)](https://developer.xamarin.com/samples/mac/Hello_Mac/)
- [macOS 人性化介面指導方針](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/) \(英文\)
