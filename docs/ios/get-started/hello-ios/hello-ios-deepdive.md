---
title: Hello, iOS – 深度剖析
description: 本文件會深入探討 Hello, iOS 範例應用程式，考量其架構、使用者介面、內容檢視階層、測試、部署等等。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 61ba3a7e-fe11-4439-8bc8-9809512b8eff
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: d0d879f9a4eadc7541736ab78aee54ac7b4e4cf0
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602374"
---
# <a name="hello-ios--deep-dive"></a>Hello, iOS – 深度剖析

＜快速入門＞逐步解說，介紹如何建置和執行基本的 Xamarin.iOS 應用程式。 現在是時候更深入了解 iOS 應用程式的運作方式，讓您能夠建置更複雜的程式。 本指南會檢閱在 Hello, iOS 逐步解說中，能夠用來了解 iOS 應用程式開發基本概念的步驟。

本指南將協助您發展建置單一畫面之 iOS 應用程式所需的技能與知識。 透過它運作之後，您應該能夠了解 Xamarin.iOS 應用程式的不同組件，以及如何將它們結合在一起。

::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Visual Studio for Mac 簡介

Visual Studio for Mac 是一個免費的開放原始碼 IDE，結合了來自 Visual Studio 和 XCode 的功能。 其中包含完全整合的視覺化設計工具、具有重構工具的文字編輯器、組件瀏覽器、原始程式碼整合等。 本指南介紹一些基本的 Visual Studio for Mac 功能，但如果您還不熟悉 Visual Studio for Mac，請參閱 [Visual Studio for Mac](/visualstudio/mac/) 文件。

Visual Studio for Mac 遵循 Visual Studio 的做法，將程式碼組織成 *方案* 和 *專案*。 方案是可以容納一或多個專案的容器。 專案可以是應用程式 (例如 iOS 或 Android)、支援程式庫、測試應用程式等等。 在 Phoneword 應用程式中，使用了 **單一檢視應用程式** 範本來新增 iPhone 專案。 最初的方案看起來如下：

![初始方案的螢幕擷取畫面](hello-ios-deepdive-images/image30.png)

::: zone-end
::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Visual Studio 簡介

Visual Studio 是 Microsoft 所提供之功能強大的 IDE。 其中包含完全整合的視覺化設計工具、具有重構工具的文字編輯器、組件瀏覽器、原始程式碼整合等。 本指南介紹一些基本 Visual Studio 功能，可搭配適用於 Visual Studio 的 Xamarin 工具使用。

Visual Studio 會將程式碼組織成方案和專案。 方案是可以容納一或多個專案的容器。 專案可以是應用程式 (例如 iOS 或 Android)、支援程式庫、測試應用程式等等。 在 Phoneword 應用程式中，使用了 **單一檢視應用程式** 範本來新增 iPhone 專案。 最初的方案看起來如下：

![初始方案的螢幕擷取畫面](hello-ios-deepdive-images/vs-image30.png)

::: zone-end

## <a name="anatomy-of-a-xamarinios-application"></a>Xamarin.iOS 應用程式的結構

::: zone pivot="macos"

左邊是 **Solution Pad**，其中包含目錄結構以及與方案相關聯的所有檔案：

![Solution Pad，包含目錄結構以及與方案建立關聯的所有檔案](hello-ios-deepdive-images/image31.png)

::: zone-end
::: zone pivot="windows"

右邊為 **方案窗格**，其中包含目錄結構以及與方案建立關聯的所有檔案：

![方案窗格，包含目錄結構以及與方案建立關聯的所有檔案](hello-ios-deepdive-images/vs-image31.png)

::: zone-end

在 [Hello, iOS](~/ios/get-started/hello-ios/hello-ios-quickstart.md) 逐步解說中，您已建立稱為 **Phoneword** 的方案，並在其中放置了一個 iOS 專案 (**Phoneword_iOS**)。 專案內的項目包括：

- **參考** -包含組建和執行應用程式所需的元件。 展開目錄以查看對 .NET 組件的參考 (例如 [System](/dotnet/api/system)、System.Core 和 [System.Xml](/dotnet/api/system.xml))，以及對 Xamarin.iOS 組件的參考。
- **套件**：套件目錄包含現成的 NuGet 套件。
- **資源** ：資源資料夾會儲存其他媒體。
- **Main.cs**：這當中包含應用程式的主要進入點。 為了啟動應用程式，會傳入主應用程式類別 (`AppDelegate`) 的名稱。
- **AppDelegate.cs**：這個檔案包含主應用程式類別，並負責建立視窗、建置使用者介面，以及接聽來自作業系統的事件。
- **Main.storyboard**：分鏡腳本包含應用程式使用者介面的視覺化設計。 分鏡腳本檔案會在名為 iOS 設計工具的圖形化編輯器中開啟。
- **ViewController.cs** –視圖控制器可讓螢幕 (查看使用者看到和觸控的) 。 檢視控制器會負責處理使用者和檢視之間的互動。
- **ViewController.designer.cs** – `designer.cs` 是自動產生的檔案，可作為視圖中控制項與視圖控制器中的程式碼表示之間的粘附。 由於這是內部連接檔案，因此，IDE 將覆寫任何手動變更，而且在大部分情況下均可忽略此檔案。 
- **Info.plist**：**Info.plist** 為設定應用程式屬性 (例如應用程式名稱、圖示、啟動影像等) 的地方。 這是一個功能強大的檔案，您可以在[運用屬性清單](~/ios/app-fundamentals/property-lists.md)指南中找到它的完整介紹。
- **Entitlements.plist**：權利屬性清單可讓我們指定應用程式「功能」(也稱為 App Store 技術)，例如 iCloud、PassKit 等等。 有關 **Entitlements.plist** 的詳細資訊可在 [運用屬性清單](~/ios/app-fundamentals/property-lists.md)指南中找到。 如需權利的一般簡介，請參閱[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)指南。

## <a name="architecture-and-app-fundamentals"></a>架構和應用程式基本概念

有兩件事必須先準備就緒，iOS 應用程式才能載入使用者介面。 首先，應用程式必須定義「進入點」，此為將應用程式的處理序載入記憶體時所執行的第一個程式碼。 其次，它需要定義類別來處理整個應用程式的事件，並與作業系統互動。

本節將探究下圖中所述的關聯性：

[![此圖說明架構和應用程式基本概念的關聯性](hello-ios-deepdive-images/image32.png)](hello-ios-deepdive-images/image32.png#lightbox)

### <a name="main-method"></a>Main 方法

iOS 應用程式的主要進入點是 `Application` 類別。 `Application` 類別是在 **Main.cs** 檔案中定義，並包含靜態 `Main` 方法。 它會建立新的 Xamarin.iOS 應用程式執行個體，並傳遞將處理 OS 事件的「應用程式委派」類別名稱。 以下顯示靜態 `Main` 方法的範本程式碼：

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

在 iOS 中，「應用程式委派」類別會處理系統事件；此類別存留於 **AppDelegate.cs** 內。 `AppDelegate` 類別會管理應用程式「視窗」。 視窗是 `UIWindow` 類別的單一執行個體，可作為使用者介面的容器。 根據預設，應用程式只會取得一個要載入其內容的視窗，並將此視窗連結至「畫面」(單一 `UIScreen` 執行個體)，以提供符合實體裝置畫面維度的週框。

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

iOS 應用程式的使用者介面就像一個店面：應用程式通常會取得一個視窗，但可在該視窗內填入其所需的物件數目，並根據應用程式想要顯示的內容來變更物件和排列方式。 此案例中的物件 (使用者所看見的內容) 稱為「檢視」。 若要在應用程式中建立單一畫面，請在 *內容視圖* 階層中將視圖堆疊在彼此的上方，而階層則由單一視圖控制器來管理。 具有多個畫面的應用程式會有多個內容檢視階層，每個都有它自己的檢視控制器，而應用程式會將檢視放置於視窗中，以根據使用者所在畫面建立不同的內容檢視階層。

如需使用分鏡腳本建立使用者介面的詳細資訊，請參閱 Apple iOS 開發人員文件庫中 [Xcode 總覽](https://developer.apple.com/library/archive/documentation/ToolsLanguages/Conceptual/Xcode_Overview/DesigningwithStoryboards.html) 的「使用分鏡腳本設計」一節。

下圖說明視窗、檢視、子檢視及檢視控制器之間的關聯性，以在裝置畫面中顯示使用者介面：

[![視窗、檢視、子檢視及檢視控制器之間的關聯性](hello-ios-deepdive-images/image43.png)](hello-ios-deepdive-images/image43.png#lightbox)

下一節將討論如何在程式碼中運用檢視，以及了解如何使用檢視控制器和檢視生命週期，針對使用者互動進行程式設計。

## <a name="view-controllers-and-the-view-lifecycle"></a>檢視控制器和檢視生命週期

每個內容檢視階層都具有相對應的檢視控制器來提供使用者互動。 檢視控制器的角色是管理內容檢視階層中的檢視。 檢視控制器不是內容檢視階層的一部分，它不是介面中的項目。 而是提供程式碼，利用畫面上的物件來提供使用者互動。

### <a name="view-controllers-and-storyboards"></a>檢視控制器和分鏡腳本

::: zone pivot="macos"

檢視控制器會在分鏡腳本中顯示為場景底部的列。 選取檢視控制器，會在 **Properties Pad** 中顯示其屬性：

![選取檢視控制器即會在 [屬性] 窗格中顯示其屬性](hello-ios-deepdive-images/image44.png)

您可以在 **Properties Pad** 的 [身分識別] 區段中編輯 [類別] 屬性，來設定此場景所呈現內容檢視階層的自訂檢視控制器類別。 例如，我們的 **Phoneword** 應用程式會將 `ViewController` 設定為第一個畫面的檢視控制器，如下列螢幕擷取畫面所示：

![Phoneword 應用程式會將 ViewController 設定為檢視控制器](hello-ios-deepdive-images/image45new.png)

::: zone-end
::: zone pivot="windows"

檢視控制器會在分鏡腳本中顯示為場景底部的列。 選取視圖控制器會在 [ **屬性] 窗格** 中顯示其屬性：

![選取檢視控制器即會在 [屬性] 窗格中顯示其屬性](hello-ios-deepdive-images/vs-image44.png)

您可以在 [屬性] 窗格的 [身分識別] 區段中編輯 [類別] 屬性，來設定此場景所呈現內容檢視階層的自訂檢視控制器類別。 例如，我們的 **Phoneword** 應用程式會將 `ViewController` 設定為第一個畫面的檢視控制器，如下列螢幕擷取畫面所示：

![Phoneword 應用程式會將 ViewController 設定為檢視控制器](hello-ios-deepdive-images/vs-image45.png)

::: zone-end

這樣會將檢視控制器的分鏡腳本表示法連結至 `ViewController` C# 類別。 開啟 `ViewController.cs` 檔案，並注意檢視控制器是 `UIViewController` 的「子類別」，如下列程式碼所示：

```csharp
public partial class ViewController : UIViewController
{
    public ViewController (IntPtr handle) : base (handle)
    {

    }
}
```

`ViewController` 現在會在分鏡腳本中衍生與此檢視控制器建立關聯之內容檢視階層的互動。 接下來，將藉由介紹稱為「檢視生命週期」的程序，來讓您了解檢視控制器在管理檢視中所扮演的角色。

> [!NOTE]
> 針對不需使用者互動的僅供檢視畫面，**類別** 屬性可以在 **Properties Pad** 中保留空白。 這會將檢視控制器的支援類別設定為 `UIViewController` 的預設實作，這適用於您不打算新增自訂程式碼時。

### <a name="view-lifecycle"></a>檢視生命週期

檢視控制器會負責從視窗載入和卸載內容檢視階層。 當內容檢視階層中的檢視發生了某個重要事件時，作業系統會透過檢視生命週期中的事件來通知檢視控制器。 藉由覆寫檢視生命週期中的方法，您就可以與畫面上的物件互動，並建立動態且可回應的使用者介面。

以下為基本的週期方法及其函式：

- **ViewDidLoad** -第一次將其內容視圖階層載入至記憶體時呼叫  *一次* 。 這是執行初始安裝的好地方，因為它是程式碼中子檢視首次變成可用的時候。
- **ViewWillAppear** -每次視圖控制器的視圖即將新增至內容視圖階層並出現在畫面上時呼叫。
- **ViewWillDisappear** -每次視圖控制器的視圖即將從內容視圖階層中移除並從畫面中消失時呼叫。 這個生命週期事件適用於清除和儲存狀態。
- **ViewDidAppear** 和 **ViewDidDisappear**：分別在從內容檢視階層新增或移除檢視時呼叫。

將自訂程式碼新增至生命週期的任何階段時，必須「覆寫」該生命週期方法的「基底實作」。 達成此目的的方法是點選至現有生命週期方法 (其中具有已經附加至該方法的一些程式碼)，並使用額外的程式碼加以擴充。 基底實作是從方法內部呼叫，以確定原始程式碼會在新的程式碼之前執行。 下一節將示範此範例。

如需使用視圖控制器的詳細資訊，請參閱 Apple 的 [view controller 程式設計指南（適用于 iOS](https://developer.apple.com/library/archive/featuredarticles/ViewControllerPGforiPhoneOS/index.html#//apple_ref/doc/uid/TP40007457-CH2-SW1) ）和 [UIViewController 參考](https://developer.apple.com/documentation/uikit/uiviewcontroller?language=objc)。

### <a name="responding-to-user-interaction"></a>回應使用者互動

檢視控制器的最重要角色就是回應使用者互動，例如按下按鈕、瀏覽等等。 處理使用者互動的最簡單方式是，連接控制項來接聽使用者輸入，並連結事件處理常式來回應輸入。 例如，可連接按鈕來回應觸控事件，如 Phoneword 應用程式中所示範。

讓我們來探索其運作方式。
在 `Phoneword_iOS` 專案中，已將稱為 `TranslateButton` 的按鈕新增至內容檢視階層：

[![稱為 TranslateButton 的按鈕已新增至 [內容檢視] 階層](hello-ios-deepdive-images/image1.png)](hello-ios-deepdive-images/image1.png#lightbox)

使用 Xcode Interface Builder 建立參考的輸出時，Xcode 同步會自動將它對應至 **ViewController.designer.cs** 中的控制項，使其 `TranslateButton` 可在類別內使用 `ViewController` 。  控制項會先在檢視生命週期的 `ViewDidLoad` 階段變成可供使用，因此可使用這個生命週期方法來回應使用者的觸控：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // wire up TranslateButton here
}
```

Phoneword 應用程式會使用名為 `TouchUpInside` 的觸控事件來接聽使用者的觸控。 `TouchUpInside` 會接聽控制項界限內的向上觸控事件 (手指離開畫面)，此事件緊接在向下觸控 (手指觸控畫面) 之後。 `TouchUpInside` 的相反是 `TouchDown` 事件，其會在使用者按下控制項時觸發。 `TouchDown` 事件會擷取大量雜訊，而且不會為使用者提供選項，使其可藉由將手指滑離控制項來取消觸控。 `TouchUpInside` 是回應 **按鈕** 觸控的最常見方式，並會建立使用者按下按鈕時所預期的體驗。 您可以在 Apple 的 [iOS 人性化介面指導方針](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/MobileHIG/index.html) \(英文\) 中取得關於此內容的詳細資訊。

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

## <a name="additional-concepts-introduced-in-phoneword"></a>Phoneword 中引入的其他概念

Phoneword 應用程式引入本指南未涵蓋的數個概念。 這些概念包括：

- **變更按鈕文字**：Phoneword 應用程式示範了如何呼叫 **按鈕** 上的 `SetTitle`，然後傳入新的文字及 **按鈕** 的「控制項狀態」，來變更 **按鈕** 的文字。 例如，下列程式碼會將 CallButton 的文字變更為 “Call”：

    ```csharp
    CallButton.SetTitle ("Call", UIControlState.Normal);
    ```

- **啟用和停用按鈕**：**按鈕** 可以處於 `Enabled` 或 `Disabled` 狀態。 已停用的 **按鈕** 將不會回應使用者輸入。 例如，下列程式碼會停用 `CallButton`：

    ```csharp
    CallButton.Enabled = false;
    ```

    如需按鈕的詳細資訊，請參閱[按鈕](~/ios/user-interface/controls/buttons.md)指南。

- **關閉鍵盤**：當使用者點選文字欄位時，iOS 會顯示鍵盤，讓使用者能夠進行輸入。 不幸的是，沒有任何內建的功能可關閉鍵盤。 您可以將下列程式碼新增至 `TranslateButton`，在使用者按下 `TranslateButton` 時關閉鍵盤：

    ```csharp
    PhoneNumberText.ResignFirstResponder ();
    ```

    如需關閉鍵盤的其他範例，請參閱[關閉鍵盤](https://github.com/xamarin/recipes/tree/master/Recipes/ios/input/keyboards/dismiss_the_keyboard)配方。

- **使用 URL 進行通話**：在 Phoneword 應用程式中，使用 Apple URL 配置來啟動系統手機應用程式。 自訂的 URL 配置包含 “tel:” 首碼和轉譯的電話號碼，如下列程式碼所示：

    ```csharp
    var url = new NSUrl ("tel:" + translatedNumber);
    if (!UIApplication.SharedApplication.OpenUrl (url))
    {
        // show alert Controller
    }
    ```

- **顯示警示** –當使用者嘗試在不支援通話的裝置（例如模擬器或 iPod touch）上撥打電話時，會顯示警示對話方塊，讓使用者知道無法放置通話。 下列程式碼會建立並填入警示控制器：

    ```csharp
    if (!UIApplication.SharedApplication.OpenUrl (url)) {
                    var alert = UIAlertController.Create ("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
                    alert.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                    PresentViewController (alert, true, null);
                }
    ```

    如需 iOS 警示檢視的詳細資訊，請參閱[警示控制器配方](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)。

## <a name="testing-deployment-and-finishing-touches"></a>測試、部署及最後的修飾

Visual Studio for Mac 和 Visual Studio 都會提供許多選項來測試和部署應用程式。 本節涵蓋偵錯選項、示範在裝置上測試應用程式，並介紹用來建立自訂應用程式圖示和啟動影像的工具。

### <a name="debugging-tools"></a>偵錯工具

應用程式程式碼中的問題有時很難診斷。 若要協助診斷複雜的程式碼問題，您可以[設定中斷點](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)、[逐步執行程式碼](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code)或[將資訊輸出至記錄視窗](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window)。

### <a name="deploy-to-a-device"></a>部署到裝置

iOS 模擬器是一個快速測試應用程式的方式。 此模擬器有數個實用的最佳化可用來進行測試，包括模擬位置、[模擬移動](https://github.com/xamarin/recipes/tree/master/Recipes/ios/multitasking/test_location_changes_in_simulator)，以及更多項目。 不過，使用者將不會取用模擬器中的最終應用程式。 所有應用程式都應儘早且經常在實際裝置上進行測試。

裝置需要時間佈建，並且需要 Apple 開發人員帳戶。 [裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)指南提供準備好裝置以進行開發的完整指示。

> [!NOTE]
> 目前，由於 Apple 的要求，因此必須有開發憑證或「簽署身分識別」，才能為實體裝置或模擬器建置程式碼。 請依照[裝置佈建指南](~/ios/get-started/installation/device-provisioning/manual-provisioning.md)中的步驟來設定此項。

佈建裝置之後，您可插入裝置、將建置工具列中的目標變更為 iOS 裝置，然後按 [啟動] ([播放]) 以部署到裝置，如下列螢幕擷取畫面所示：

::: zone pivot="macos"

![按 [啟動/播放]](hello-ios-deepdive-images/image46new.png)

::: zone-end
::: zone pivot="windows"

![按 [啟動/播放]](hello-ios-deepdive-images/vs-image46.png)

::: zone-end

應用程式將會部署到 iOS 裝置：

[![應用程式將會部署到 iOS 裝置並執行](hello-ios-deepdive-images/image1.png)](hello-ios-deepdive-images/image1.png#lightbox)

### <a name="generate-custom-icons-and-launch-images"></a>產生自訂圖示和啟動影像

並非每個人都有可用來建立應用程式所需的自訂圖示和啟動影像的設計工具。以下是一些產生自訂應用程式作品的替代方法：

::: zone pivot="macos"

- [**Pixelmator**](https://www.pixelmator.com/) \(英文\)：適用於 Mac 的多用途影像編輯應用程式，成本大約 $30 美元。
- [**Fiverr**](https://www.fiverr.com/) \(英文\)：從各種不同的設計工具進行選擇來建立您適用的圖示集，從 $5 美元開始。 如果您需要動態設計的圖示，可以叫用或略過，但是個良好的資源

::: zone-end
::: zone pivot="windows"

- Visual Studio：您可以使用此產品，直接在 IDE 中為您的應用程式建立簡單的圖示集。
- [**Fiverr**](https://www.fiverr.com/) \(英文\)：從各種不同的設計工具進行選擇來建立您適用的圖示集，從 $5 美元開始。 如果您需要動態設計的圖示，可以叫用或略過，但是個良好的資源

::: zone-end

如需有關圖示和啟動影像大小及需求的詳細資訊，請參閱[運用影像指南](~/ios/app-fundamentals/images-icons/index.md)。

## <a name="summary"></a>摘要

恭喜！ 您現在已充分了解 Xamarin.iOS 應用程式的元件，以及用來建立它們的工具。
在[快速入門系列的下一個教學課程](~/ios/get-started/hello-ios-multiscreen/index.md)中，您將會擴充我們的應用程式來處理多個畫面。 過程中，您將實作瀏覽控制器、了解 Storyboard Segue，並在您擴充應用程式以處理多個畫面時，導入 Model、View、Controller (MVC) 模式。

## <a name="related-links"></a>相關連結

- [Hello, iOS (範例)](/samples/xamarin/ios-samples/hello-ios) \(英文\)
- [iOS 人性化介面指導方針](https://developer.apple.com/design/human-interface-guidelines/ios/overview/themes/)
- [iOS 佈建入口網站](https://developer.apple.com/account/#/overview)