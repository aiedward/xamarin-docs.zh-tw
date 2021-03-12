---
title: 在 Xamarin 中 HomeKit
description: HomeKit 是 Apple 用來控制家用自動化裝置的架構。 本文介紹 HomeKit，並說明如何在 HomeKit 配件模擬器中設定測試附屬應用程式，並撰寫簡單的 Xamarin iOS 應用程式來與這些配件互動。
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 54b3b361110d3ad63effe0c2afc77a6398ab8034
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602992"
---
# <a name="homekit-in-xamarinios"></a>在 Xamarin 中 HomeKit

_HomeKit 是 Apple 用來控制家用自動化裝置的架構。本文介紹 HomeKit，並說明如何在 HomeKit 配件模擬器中設定測試附屬應用程式，並撰寫簡單的 Xamarin iOS 應用程式來與這些配件互動。_

[![HomeKit 啟用的應用程式範例](homekit-images/accessory01.png)](homekit-images/accessory01.png#lightbox)

Apple 在 iOS 8 中引進了 HomeKit，可將多個家庭自動化裝置從不同的廠商緊密整合到單一、一致的單位。 藉由提升探索、設定及控制家庭自動化裝置的常見通訊協定，HomeKit 可讓非相關廠商的裝置一起運作，而不需要個別的廠商協調工作。

透過 HomeKit，您可以建立可控制任何已啟用 HomeKit 之裝置的 Xamarin iOS 應用程式，而不需使用廠商提供的 Api 或應用程式。 使用 HomeKit，您可以執行下列動作：

- 探索已啟用新 HomeKit 的家用自動化裝置，並將其新增至將在所有使用者的 iOS 裝置上保存的資料庫。
- 設定、設定、顯示及控制 HomeKit _Home Configuration 資料庫_ 中的任何裝置。
- 與任何預先設定的 HomeKit 裝置通訊，然後命令以執行個別動作或工作，例如開啟廚房中的所有燈光。

除了將 Home 設定資料庫中的裝置提供給 HomeKit 啟用的應用程式之外，HomeKit 還提供 Siri voice 命令的存取權。 有了適當設定的 HomeKit 設定，使用者就可以發出語音命令，例如「Siri，開啟客廳中的燈光」。

<a name="Home-Configuration-Database"></a>

## <a name="the-home-configuration-database"></a>Home Configuration 資料庫

HomeKit 會將指定位置中的所有自動化裝置組織成一個首頁集合。 此集合提供一種方式，讓使用者將其家庭自動化裝置群組為具有意義、人們可讀取標籤的邏輯排列單位。

首頁集合儲存在 Home 設定資料庫中，它會自動在所有使用者的 iOS 裝置上進行備份和同步處理。 HomeKit 提供下列類別來使用 Home 設定資料庫：

- `HMHome` -這是最上層容器，其中保存所有家用自動化裝置在單一實體位置的所有資訊和設定 (例如 ) 的單一家庭。 使用者可能會有一個以上的居住，例如主要家庭和休假房子。 或者，在相同的屬性上可能會有不同的「房子」，例如主要房子和車庫上的來賓房子。 無論何種方式，都 `HMHome` _必須_ 先設定和儲存至少一個物件，才能輸入任何其他 HomeKit 資訊。
- `HMRoom` -雖然是選擇性的，但 `HMRoom` 可讓使用者定義家用 (的特定房間 `HMHome`) 例如：廚房、衛生間、舊貨或客廳。 使用者可以將其內部特定位置中的所有家庭自動化裝置群組至 `HMRoom` ，並作為一個單位來處理。 例如，要求 Siri 關閉車庫燈。
- `HMAccessory` -這代表已安裝在使用者居住 (（例如智慧型控溫器) ）中的個別實體 HomeKit 啟用自動化裝置。 每個 `HMAccessory` 都會指派給 `HMRoom` 。 如果使用者尚未設定任何房間，HomeKit 會將配件指派給特殊的預設房間。
- `HMService` -表示指定的服務 `HMAccessory` ，例如燈光的開啟/關閉狀態或其色彩 (是否支援色彩變更) 。 每個 `HMAccessory` 服務都可以有一個以上的服務，例如也包含燈光的車庫門開啟工具。 此外，指定的 `HMAccessory` 也可能是使用者控制項以外的服務，例如，固件更新。
- `HMZone` -可讓使用者將物件的集合群組為 `HMRoom` 邏輯區域，例如樓上、樓下或下出。 雖然是選擇性的，但這允許進行互動，例如要求 Siri 關閉所有淺色樓下。

<a name="Provisioning-a-HomeKit-App"></a>

## <a name="provisioning-a-homekit-app"></a>布建 HomeKit 應用程式

由於 HomeKit 所加諸的安全性需求，因此必須在 Apple 開發人員入口網站和 Xamarin. iOS 專案檔中正確設定使用 HomeKit 架構的 Xamarin iOS 應用程式。

執行下列動作：

1. 登入 [Apple Developer 入口網站](https://developer.apple.com)。
2. 按一下 [ **憑證]、[識別碼] & 設定檔**。
3. 如果您尚未這麼做，請按一下 [ **識別碼** ] 並建立應用程式的識別碼 (例如 `com.company.appname`) ，否則請編輯現有的識別碼。
4. 確定已檢查 **HomeKit** 服務是否有指定的識別碼： 

    [![為指定的識別碼啟用 HomeKit 服務](homekit-images/provision01.png)](homekit-images/provision01.png#lightbox)
5. 儲存您的變更。
6. 按一下 [布建 **設定檔**  >  **開發**]，並為您的應用程式建立新的開發布建設定檔： 

    [![為應用程式建立新的開發提供設定檔](homekit-images/provision02.png)](homekit-images/provision02.png#lightbox)
7. 請下載並安裝新的布建設定檔，或使用 Xcode 下載並安裝設定檔。
8. 編輯您的 Xamarin iOS 專案選項，並確定您使用的是您剛才建立的布建設定檔： 

    [![選取剛剛建立的布建設定檔](homekit-images/provision03.png)](homekit-images/provision03.png#lightbox)
9. 接下來，請編輯您的 **plist** 檔案，並確定您使用的是用來建立布建設定檔的應用程式識別碼： 

    [![設定應用程式識別碼 ](homekit-images/provision04.png)](homekit-images/provision04.png#lightbox)
10. 最後，編輯 **plist** 檔案，並確定已選取 **HomeKit** 權利： 

    [![啟用 HomeKit 權利](homekit-images/provision05.png)](homekit-images/provision05.png#lightbox)
11. 將變更儲存至所有檔案。

設定好這些設定之後，應用程式就可以開始存取 HomeKit Framework Api。 如需布建的詳細資訊，請參閱我們的 [裝置](~/ios/get-started/installation/device-provisioning/index.md) 布建和布建 [應用程式](~/ios/get-started/installation/device-provisioning/index.md) 指南。

> [!IMPORTANT]
> 測試啟用 HomeKit 的應用程式需要已正確布建以進行開發的真實 iOS 裝置。 HomeKit 無法從 iOS 模擬器進行測試。

## <a name="the-homekit-accessory-simulator"></a>HomeKit 配件模擬器

為了提供測試所有可能家用自動化裝置和服務的方法，而不需要有實體裝置，Apple 建立了 _HomeKit 的配件_ 模擬器。 您可以使用此模擬器來設定和設定虛擬 HomeKit 裝置。

### <a name="installing-the-simulator"></a>安裝模擬器

Apple 提供 HomeKit 配件模擬器作為 Xcode 的個別下載，因此您必須先安裝它，然後再繼續進行。

執行下列動作：

1. 在網頁瀏覽器中，流覽 [Apple 開發人員的下載](https://developer.apple.com/download/more/?name=for%20Xcode)
2. 下載 **Xcode (xxx 的其他工具** ，其中 xxx 是您已) 安裝的 Xcode 版本： 

    [![下載 Xcode 的其他工具](homekit-images/simulator01.png)](homekit-images/simulator01.png#lightbox)
3. 開啟磁片映射，並在您的 **應用程式** 目錄中安裝工具。

安裝 HomeKit 配件模擬器之後，就可以建立虛擬附屬應用程式來進行測試。

### <a name="creating-virtual-accessories"></a>建立虛擬附屬配件

若要啟動 HomeKit 配件模擬器並建立幾個虛擬附屬配件，請執行下列動作：

1. 從應用程式資料夾啟動 HomeKit 配件模擬器： 

    [![HomeKit 配件模擬器](homekit-images/simulator02.png)](homekit-images/simulator02.png#lightbox)
2. 按一下該 **+** 按鈕，然後選取 [ **新增配件**]： 

    [![新增配件](homekit-images/simulator03.png)](homekit-images/simulator03.png#lightbox)
3. 填寫有關新附件的資訊，然後按一下 [ **完成]** 按鈕： 

    [![填寫新配件的相關資訊](homekit-images/simulator04.png)](homekit-images/simulator04.png#lightbox)
4. 按一下 [**新增服務**]。 按鈕，然後從下拉式清單中選取服務類型： 

    [![從下拉式清單中選取服務類型](homekit-images/simulator05.png)](homekit-images/simulator05.png#lightbox)
5. 提供服務的 **名稱** ，然後按一下 [ **完成]** 按鈕： 

    [![輸入服務的名稱](homekit-images/simulator06.png)](homekit-images/simulator06.png#lightbox)
6. 您可以按一下 [ **加入特性** ] 按鈕並設定必要的設定，以提供服務的選用特性： 

    [![設定必要設定](homekit-images/simulator07.png)](homekit-images/simulator07.png#lightbox)
7. 重複上述步驟，以建立 HomeKit 支援的每一種虛擬家用自動化裝置。

建立並設定一些範例虛擬 HomeKit 附屬配件之後，您現在可以從您的 Xamarin iOS 應用程式取用及控制這些裝置。

## <a name="configuring-the-infoplist-file"></a>設定 plist 檔案

新的 iOS 10 (及更高的) ，開發人員必須將 `NSHomeKitUsageDescription` 金鑰新增至應用程式的檔案， `Info.plist` 並提供字串來宣告應用程式想要存取使用者 HomeKit 資料庫的原因。 第一次執行應用程式時，將會向使用者顯示這個字串：

[![HomeKit 許可權對話方塊](homekit-images/info01.png)](homekit-images/info01.png#lightbox)

若要設定此機碼，請執行下列動作：

1. `Info.plist`在 [**方案瀏覽器**] 中按兩下該檔案以開啟它進行編輯。
2. 在畫面底部，切換至 [ **來源** ] view。
3. 將新 **專案** 新增至清單。
4. 從下拉式清單中，選取 [ **隱私權-HomeKit 使用方式描述**： 

    [![選取隱私權-HomeKit 使用方式描述](homekit-images/info02.png)](homekit-images/info02.png#lightbox)
5. 輸入應用程式想要存取使用者 HomeKit 資料庫的原因說明： 

    [![輸入描述](homekit-images/info03.png)](homekit-images/info03.png#lightbox)
6. 將變更儲存至檔案。

> [!IMPORTANT]
> 若無法在檔案 `NSHomeKitUsageDescription` 中設定金鑰， `Info.plist` 將會導致應用程式以 _無_ 訊息方式失敗 (在執行時間關閉系統，) 在 iOS 10 (或更高的) 中執行時沒有錯誤。

## <a name="connecting-to-homekit"></a>連接至 HomeKit

若要與 HomeKit 通訊，您的 Xamarin iOS 應用程式必須先具現化類別的實例 `HMHomeManager` 。 Home Manager 是 HomeKit 的核心進入點，負責提供可用的家裡清單、更新和維護該清單，以及傳回使用者的 _主要首頁_。

此 `HMHome` 物件包含提供首頁的所有資訊，包括任何可能包含的房間、群組或區域，以及任何已安裝的家用自動化附屬應用程式。 在 HomeKit 中可執行任何作業之前，必須先建立至少一個， `HMHome` 並將其指派為主要首頁。

您的應用程式會負責檢查主要首頁是否存在，如果不存在，則會建立並指派一個。

### <a name="adding-a-home-manager"></a>新增首頁管理員

若要將 HomeKit 感知新增至 Xamarin iOS 應用程式，請編輯 **AppDelegate.cs** 檔案進行編輯，使其看起來如下所示：

```csharp
using HomeKit;
...

public HMHomeManager HomeManager { get; set; }
...

public override void FinishedLaunching (UIApplication application)
{
    // Attach to the Home Manager
    HomeManager = new HMHomeManager ();
    Console.WriteLine ("{0} Home(s) defined in the Home Manager", HomeManager.Homes.Count());

    // Wire-up Home Manager Events
    HomeManager.DidAddHome += (sender, e) => {
        Console.WriteLine("Manager Added Home: {0}",e.Home);
    };

    HomeManager.DidRemoveHome += (sender, e) => {
        Console.WriteLine("Manager Removed Home: {0}",e.Home);
    };
    HomeManager.DidUpdateHomes += (sender, e) => {
        Console.WriteLine("Manager Updated Homes");
    };
    HomeManager.DidUpdatePrimaryHome += (sender, e) => {
        Console.WriteLine("Manager Updated Primary Home");
    };
}
```

當應用程式第一次執行時，系統會詢問使用者是否要允許它存取其 HomeKit 資訊：

[![系統會詢問使用者是否要允許其存取其 HomeKit 資訊](homekit-images/home01.png)](homekit-images/home01.png#lightbox)

如果使用者回答 **[確定]**，則應用程式將能使用其 HomeKit 附屬應用程式，否則不會有任何 HomeKit 的呼叫會失敗，並出現錯誤。

使用 Home Manager 之後，應用程式的下一個應用程式將需要查看主要家庭是否已設定，如果不是，請提供一種方法讓使用者建立及指派一個。

### <a name="accessing-the-primary-home"></a>存取主要首頁

如上所述，必須先建立並設定主要家庭，才能使用 HomeKit，而且應用程式必須負責提供方法，讓使用者建立及指派主要家庭（如果尚未存在的話）。

當您的應用程式第一次啟動或從背景返回時，它需要監視 `DidUpdateHomes` 類別的事件， `HMHomeManager` 以檢查主要首頁是否存在。 如果不存在，則應該提供介面讓使用者建立一個介面。

您可以將下列程式碼新增至 view controller，以檢查主要首頁：

```csharp
using HomeKit;
...

public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
...

// Wireup events
ThisApp.HomeManager.DidUpdateHomes += (sender, e) => {

    // Was a primary home found?
    if (ThisApp.HomeManager.PrimaryHome == null) {
        // Ask user to add a home
        PerformSegue("AddHomeSegue",this);
    }
};
```

當「首頁管理員」連接到 HomeKit 時， `DidUpdateHomes` 就會引發此事件，任何現有的家用都會載入到管理員的家裡集合，而且會載入主要首頁（如果有的話）。

### <a name="adding-a-primary-home"></a>新增主要首頁

如果的 `PrimaryHome` 屬性是在 `HMHomeManager` `null` `DidUpdateHomes` 事件之後，您必須提供方法讓使用者在繼續之前，先建立並指派主要首頁。

應用程式通常會顯示一個表單，讓使用者命名新的首頁，然後將其傳遞給 Home Manager，以設定為主要首頁。 針對 **HomeKitIntro** 範例應用程式，會在 Xcode Interface Builder 中建立模式視圖，並由 `AddHomeSegue` segue 從應用程式的主要介面呼叫。

它會提供文字欄位，讓使用者輸入新首頁的名稱，以及用來新增首頁的按鈕。 當使用者按下 [ **新增首頁** ] 按鈕時，下列程式碼會呼叫「首頁管理員」來新增首頁：

```csharp
// Add new home to HomeKit
ThisApp.HomeManager.AddHome(HomeName.Text,(home,error) =>{
    // Did an error occur
    if (error!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Add Home Error",string.Format("Error adding {0}: {1}",HomeName.Text,error.LocalizedDescription),this);
        return;
    }

    // Make the primary house
    ThisApp.HomeManager.UpdatePrimaryHome(home,(err) => {
        // Error?
        if (err!=null) {
            // Inform user of error
            AlertView.PresentOKAlert("Add Home Error",string.Format("Unable to make this the primary home: {0}",err.LocalizedDescription),this);
            return ;
        }
    });

    // Close the window when the home is created
    DismissViewController(true,null);
});
```

`AddHome`方法會嘗試建立新的首頁，並將其傳回給指定的回呼常式。 如果 `error` 屬性不是，就會 `null` 發生錯誤，而且應該向使用者顯示。 最常見的錯誤是由非唯一的 home 名稱或 Home Manager 無法與 HomeKit 通訊所造成。

如果已成功建立主版，您必須呼叫方法， `UpdatePrimaryHome` 將新的首頁設定為主要首頁。 同樣地，如果 `error` 屬性不是 `null` ，就會發生錯誤，並向使用者顯示。

您也應該監視主管理員 `DidAddHome` 和 `DidRemoveHome` 事件，並視需要更新應用程式的使用者介面。

> [!IMPORTANT]
> `AlertView.PresentOKAlert`上述範例程式碼中使用的方法，是 HomeKitIntro 應用程式中的協助程式類別，可讓您更輕鬆地處理 IOS 警示。

## <a name="finding-new-accessories"></a>尋找新的配件

從「首頁管理員」定義或載入主要首頁之後，您的 Xamarin iOS 應用程式就可以呼叫 `HMAccessoryBrowser` 來尋找任何新的家用自動化附屬應用程式，並將其新增至首頁。

呼叫 `StartSearchingForNewAccessories` 方法以開始在完成時尋找新的配件和 `StopSearchingForNewAccessories` 方法。

> [!IMPORTANT]
> `StartSearchingForNewAccessories` 不應長時間執行，因為它會對 iOS 裝置的電池壽命和效能造成負面影響。 Apple 建議在 `StopSearchingForNewAccessories` 一分鐘後呼叫，或只在尋找使用者的尋找配件 UI 時進行搜尋。

探索到新的配件時，系統會 `DidFindNewAccessory` 呼叫此事件，並將其新增至 `DiscoveredAccessories` 配件瀏覽器中的清單。

此 `DiscoveredAccessories` 清單將包含物件的集合 `HMAccessory` ，這些物件會定義 HomeKit 啟用的家用自動化裝置及其可用的服務，例如燈光或舊貨門控制項。

一旦找到新的配件之後，應該會向使用者呈現，讓他們可以選取並將它新增至首頁。 範例：

[![尋找新的配件](homekit-images/accessory01.png)](homekit-images/accessory01.png#lightbox)

呼叫 `AddAccessory` 方法，將選取的配件加入至首頁的集合中。 例如：

```csharp
// Add the requested accessory to the home
ThisApp.HomeManager.PrimaryHome.AddAccessory (_controller.AccessoryBrowser.DiscoveredAccessories [indexPath.Row], (err) => {
    // Did an error occur
    if (err !=null) {
        // Inform user of error
        AlertView.PresentOKAlert("Add Accessory Error",err.LocalizedDescription,_controller);
    }
});
```

如果 `err` 屬性不是，就會 `null` 發生錯誤，而且應該向使用者顯示。 否則，系統會要求使用者輸入要新增之裝置的安裝程式碼：

[![輸入要新增之裝置的安裝程式碼](homekit-images/accessory02.png)](homekit-images/accessory02.png#lightbox)

在 HomeKit 配件模擬器中，您可以在 [ **安裝程式碼** ] 欄位下找到這個數位：

[![HomeKit 配件模擬器中的 [安裝程式碼] 欄位](homekit-images/accessory03.png)](homekit-images/accessory03.png#lightbox)

針對真實的 HomeKit 配件，安裝程式程式碼會列印在裝置本身的標籤上、產品方塊或配件的使用者手冊中。

您應該監視附件瀏覽器的 `DidRemoveNewAccessory` 事件，並更新使用者介面，以便在使用者將其新增至其首頁集合之後，從可用清單中移除該配件。

## <a name="working-with-accessories"></a>使用配件

一旦建立主要首頁並新增配件之後，您就可以顯示一份配件清單 (以及使用者可選擇的房間) 供使用者使用。

`HMRoom`物件包含有關指定的房間和任何屬於該房間之附屬應用程式的所有資訊。 您可以選擇性地將房間組織成一或多個區域。 `HMZone`包含有關給定區域和所有所屬房間的所有資訊。

基於此範例的目的，我們將保持簡單，並直接使用家庭的配件，而不是將它們組織成房間或區域。

`HMHome`物件包含指派的配件清單，可以在其屬性中呈現給使用者 `Accessories` 。 例如：

[![範例配件](homekit-images/accessory04.png)](homekit-images/accessory04.png#lightbox)

在這裡的表單中，使用者可以選取指定的配件，並使用它所提供的服務。

## <a name="working-with-services"></a>使用服務

當使用者與已啟用 HomeKit 的已啟用家用自動化裝置互動時，通常會透過它所提供的服務。 `Services`類別的屬性 `HMAccessory` 包含物件的集合 `HMService` ，這些物件會定義裝置所提供的服務。

服務就像燈光、恒溫器、、舊貨門車庫遙控器、交換器或鎖定等。 某些裝置 (像是車庫門開啟工具) 將會提供多項服務，例如燈光以及開啟或關閉門的能力。

除了指定的配件所提供的特定服務之外，每個配件都包含一個 `Information Service` 來定義屬性，例如名稱、製造商、型號和序號。

### <a name="accessory-service-types"></a>配件服務類型

下列服務類型可透過列舉來取得 `HMServiceType` ：

- **AccessoryInformation** -提供指定的家用自動化裝置 (附屬) 的相關資訊。
- **AirQualitySensor** -定義空氣品質感應器。
- **電池** -定義配件電池的狀態。
- **CarbonDioxideSensor** -定義碳二氧化碳感應器。
- **CarbonMonoxideSensor** -定義碳一氧化碳感應器。
- **ContactSensor** -定義連絡人感應器 (例如開啟或關閉的視窗) 。
- **門** -定義門狀態感應器 (例如開啟或關閉) 。
- **風扇** -定義遠端控制風扇。
- **GarageDoorOpener** -定義車庫大門開啟工具。
- **HumiditySensor** -定義濕度感應器。
- **LeakSensor** -定義流失感應器 (例如熱水加熱器或洗手機) 。
- **燈泡** -定義獨立光源或屬於另一個配件 (的光線，例如車庫門開啟工具) 。
- **LightSensor** -定義燈光感應器。
- **LockManagement** -定義管理自動化門鎖定的服務。
- **LockMechanism** -定義遠端控制的鎖定 (例如門鎖) 。
- **MotionSensor** -定義移動感應器。
- **OccupancySensor** -定義佔用量感應器。
- [**輸出**]-定義遠端控制的牆上電源。
- **SecuritySystem** -定義家用安全性系統。
- **StatefulProgrammableSwitch** -定義一個可程式化的參數，該參數會在觸發之後維持「提供」狀態 (例如翻轉切換) 。
- **StatelessProgrammableSwitch** -定義一個可程式化參數，其會在觸發 (（例如按下按鈕) ）後，返回其初始狀態。
- **SmokeSensor** -定義冒煙感應器。
- **Switch** -定義 on/off 參數，例如標準的牆交換器。
- **溫度感應器** -定義溫度感應器。
- **控溫器** -定義用來控制 HVAC 系統的智慧型控溫器。
- **視窗** -定義棒棒糖遠端開啟或關閉的自動視窗。
- **WindowCovering** -定義從遠端控制的視窗，例如可以開啟或關閉的遮蔽。

### <a name="displaying-service-information"></a>顯示服務資訊

載入之後， `HMAccessory` 您可以查詢它所提供的個別 `HNService` 物件，並向使用者顯示該資訊：

[![顯示服務資訊](homekit-images/accessory05.png)](homekit-images/accessory05.png#lightbox)

您應該一律先檢查的 `Reachable` 屬性， `HMAccessory` 然後再嘗試使用它。 無法連上使用者不在裝置範圍內或已將其插上。

一旦選取服務之後，使用者就可以查看或修改該服務的一或多個特性，以監視或控制指定的家用自動化裝置。

<a name="Working-with-Characteristics"></a>

## <a name="working-with-characteristics"></a>使用特性

每個 `HMService` 物件都可以包含物件的集合 `HMCharacteristic` ，這些物件可以提供服務狀態的相關資訊 (例如開啟或關閉的門) 或允許使用者調整狀態 (例如設定燈光) 的色彩。

`HMCharacteristic` 不僅提供特性及其狀態的相關資訊，還提供透過 _特性中繼資料_ () 處理狀態的方法 `HMCharacteristisMetadata` 。 此中繼資料可提供屬性 (例如最基本和最大值範圍，) 在向使用者顯示資訊或允許他們修改狀態時很有用。

`HMCharacteristicType`列舉會提供一組特性中繼資料值，可依下列方式定義或修改：

- AdminOnlyAccess
- AirParticulateDensity
- AirParticulateSize
- AirQuality
- AudioFeedback
- BatteryLevel
- 亮度
- CarbonDioxideDetected
- CarbonDioxideLevel
- CarbonDioxidePeakLevel
- CarbonMonoxideDetected
- CarbonMonoxideLevel
- CarbonMonoxidePeakLevel
- ChargingState
- ContactState
- CoolingThreshold
- CurrentDoorState
- CurrentHeatingCooling
- CurrentHorizontalTilt
- CurrentLightLevel
- CurrentLockMechanismState
- CurrentPosition
- CurrentRelativeHumidity
- CurrentSecuritySystemState
- CurrentTemperature
- CurrentVerticalTilt
- FirmwareVersion
- HardwareVersion
- HeatingCoolingStatus
- HeatingThreshold
- HoldPosition
- 色調
- 識別
- InputEvent
- LeakDetected
- LockManagementAutoSecureTimeout
- LockManagementControlPoint
- LockMechanismLastKnownAction
- 記錄
- 製造商
- 模型
- MotionDetected
- Name
- ObstructionDetected
- OccupancyDetected
- OutletInUse
- OutputState
- PositionState
- PowerState
- RotationDirection
- RotationSpeed
- 飽和度
- SerialNumber
- SmokeDetected
- SoftwareVersion
- StatusActive
- StatusFault
- StatusJammed
- StatusLowBattery
- StatusTampered
- TargetDoorState
- TargetHeatingCooling
- TargetHorizontalTilt
- TargetLockMechanismState
- TargetPosition
- TargetRelativeHumidity
- TargetSecuritySystemState
- TargetTemperature
- TargetVerticalTilt
- TemperatureUnits
- 版本

### <a name="working-with-a-characteristics-value"></a>使用特性的值

為確保您的應用程式具有指定特性的最新狀態，請呼叫 `ReadValue` 類別的方法 `HMCharacteristic` 。 如果 `err` 屬性不是 `null` ，就會發生錯誤，而且不會向使用者顯示。

特性的 `Value` 屬性包含給定特性的目前狀態為 `NSObject` ，因此無法直接使用 c # 來處理。

若要讀取此值，下列 helper 類別已新增至 **HomeKitIntro** 範例應用程式：

```csharp
using System;
using Foundation;
using System.Globalization;
using CoreGraphics;

namespace HomeKitIntro
{
    /// <summary>
    /// NS object converter is a helper class that helps to convert NSObjects into
    /// C# objects
    /// </summary>
    public static class NSObjectConverter
    {
        #region Static Methods
        /// <summary>
        /// Converts to an object.
        /// </summary>
        /// <returns>The object.</returns>
        /// <param name="nsO">Ns o.</param>
        /// <param name="targetType">Target type.</param>
        public static Object ToObject (NSObject nsO, Type targetType)
        {
            if (nsO is NSString) {
                return nsO.ToString ();
            }

            if (nsO is NSDate) {
                var nsDate = (NSDate)nsO;
                return DateTime.SpecifyKind ((DateTime)nsDate, DateTimeKind.Unspecified);
            }

            if (nsO is NSDecimalNumber) {
                return decimal.Parse (nsO.ToString (), CultureInfo.InvariantCulture);
            }

            if (nsO is NSNumber) {
                var x = (NSNumber)nsO;

                switch (Type.GetTypeCode (targetType)) {
                case TypeCode.Boolean:
                    return x.BoolValue;
                case TypeCode.Char:
                    return Convert.ToChar (x.ByteValue);
                case TypeCode.SByte:
                    return x.SByteValue;
                case TypeCode.Byte:
                    return x.ByteValue;
                case TypeCode.Int16:
                    return x.Int16Value;
                case TypeCode.UInt16:
                    return x.UInt16Value;
                case TypeCode.Int32:
                    return x.Int32Value;
                case TypeCode.UInt32:
                    return x.UInt32Value;
                case TypeCode.Int64:
                    return x.Int64Value;
                case TypeCode.UInt64:
                    return x.UInt64Value;
                case TypeCode.Single:
                    return x.FloatValue;
                case TypeCode.Double:
                    return x.DoubleValue;
                }
            }

            if (nsO is NSValue) {
                var v = (NSValue)nsO;

                if (targetType == typeof(IntPtr)) {
                    return v.PointerValue;
                }

                if (targetType == typeof(CGSize)) {
                    return v.SizeFValue;
                }

                if (targetType == typeof(CGRect)) {
                    return v.RectangleFValue;
                }

                if (targetType == typeof(CGPoint)) {
                    return v.PointFValue;
                }
            }

            return nsO;
        }

        /// <summary>
        /// Convert to string
        /// </summary>
        /// <returns>The string.</returns>
        /// <param name="nsO">Ns o.</param>
        public static string ToString(NSObject nsO) {
            return (string)ToObject (nsO, typeof(string));
        }

        /// <summary>
        /// Convert to date time
        /// </summary>
        /// <returns>The date time.</returns>
        /// <param name="nsO">Ns o.</param>
        public static DateTime ToDateTime(NSObject nsO){
            return (DateTime)ToObject (nsO, typeof(DateTime));
        }

        /// <summary>
        /// Convert to decimal number
        /// </summary>
        /// <returns>The decimal.</returns>
        /// <param name="nsO">Ns o.</param>
        public static decimal ToDecimal(NSObject nsO){
            return (decimal)ToObject (nsO, typeof(decimal));
        }

        /// <summary>
        /// Convert to boolean
        /// </summary>
        /// <returns><c>true</c>, if bool was toed, <c>false</c> otherwise.</returns>
        /// <param name="nsO">Ns o.</param>
        public static bool ToBool(NSObject nsO){
            return (bool)ToObject (nsO, typeof(bool));
        }

        /// <summary>
        /// Convert to character
        /// </summary>
        /// <returns>The char.</returns>
        /// <param name="nsO">Ns o.</param>
        public static char ToChar(NSObject nsO){
            return (char)ToObject (nsO, typeof(char));
        }

        /// <summary>
        /// Convert to integer
        /// </summary>
        /// <returns>The int.</returns>
        /// <param name="nsO">Ns o.</param>
        public static int ToInt(NSObject nsO){
            return (int)ToObject (nsO, typeof(int));
        }

        /// <summary>
        /// Convert to float
        /// </summary>
        /// <returns>The float.</returns>
        /// <param name="nsO">Ns o.</param>
        public static float ToFloat(NSObject nsO){
            return (float)ToObject (nsO, typeof(float));
        }

        /// <summary>
        /// Converts to double
        /// </summary>
        /// <returns>The double.</returns>
        /// <param name="nsO">Ns o.</param>
        public static double ToDouble(NSObject nsO){
            return (double)ToObject (nsO, typeof(double));
        }
        #endregion
    }
}
```

`NSObjectConverter`當應用程式需要讀取特性的目前狀態時，就會使用。 例如：

```csharp
var value = NSObjectConverter.ToFloat (characteristic.Value);
```

上述程式碼會將值轉換為 `float` ，以便在 Xamarin c # 程式碼中使用。

若要修改 `HMCharacteristic` ，請呼叫其 `WriteValue` 方法，並將新值包裝在 `NSObject.FromObject` 呼叫中。 例如：

```csharp
Characteristic.WriteValue(NSObject.FromObject(value),(err) =>{
    // Was there an error?
    if (err!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Update Error",err.LocalizedDescription,Controller);
    }
});
```

如果 `err` 屬性不是，就會 `null` 發生錯誤，而且應該向使用者顯示。

### <a name="testing-characteristic-value-changes"></a>測試特性值變更

使用 `HMCharacteristics` 和模擬的附屬應用程式時， `Value` 可以在 HomeKit 配件模擬器內監視對屬性的修改。

在實際的 iOS 裝置硬體上執行 **HomeKitIntro** 應用程式時，在 HomeKit 配件模擬器中幾乎可以立即看到特性值的變更。 例如，變更 iOS 應用程式中的燈光狀態：

[![變更 iOS 應用程式中的燈光狀態](homekit-images/test01.png)](homekit-images/test01.png#lightbox)

應該在 HomeKit 配件模擬器中變更光線的狀態。 如果值不會變更，請在寫入新的特性值時檢查錯誤訊息的狀態，並確定該配件仍可連線。

## <a name="advanced-homekit-features"></a>Advanced HomeKit 功能

本文涵蓋了在 Xamarin iOS 應用程式中使用 HomeKit 附屬應用程式所需的基本功能。 不過，本簡介中未涵蓋 HomeKit 的數個先進功能：

- HomeKit **啟用的配件** 可以選擇性地組織到使用者的房間。 如此一來，HomeKit 就能以方便使用者瞭解和使用的方式呈現配件。 如需有關建立和維護房間的詳細資訊，請參閱 Apple 的 [HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom) 檔。
- **區域** -房間可以選擇性地依使用者組織至區域。 區域指的是使用者可能視為單一單位的房間集合。 例如：樓上、樓下或下出。 同樣地，這讓 HomeKit 能以對終端使用者有意義的方式來呈現和使用配件。 如需有關建立和維護區域的詳細資訊，請參閱 Apple 的 [HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone) 檔。
- **動作和動作集** -動作會修改配件服務特性，並且可以分組到集合中。 動作集的作用如同腳本，可控制一組附屬應用程式並協調其動作。 例如，「觀賞電視」腳本可能會關閉遮蔽、使燈光變暗，以及開啟電視及其音效系統。 如需建立和維護動作和動作集的詳細資訊，請參閱 Apple 的 [HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction) 和 [HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet) 檔。
- **觸發** 程式-觸發程式可以在符合一組特定條件時啟用一或多個動作集。 例如，開啟 portch 燈光，並在外部的外部大門變暗時，鎖定所有外部門。 如需有關建立和維護觸發程式的詳細資訊，請參閱 Apple 的 [HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger) 檔。

由於這些功能使用上述相同的技術，因此您應該遵循 Apple 的 [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)、 [HomeKit 使用者介面指導方針](https://developer.apple.com/homekit/ui-guidelines/) 和 [HomeKit 架構參考](https://developer.apple.com/library/ios/home_kit_framework_ref)，輕鬆地執行這些功能。

## <a name="homekit-app-review-guidelines"></a>HomeKit 應用程式審查指導方針

在 iTunes App Store 中將已啟用 HomeKit 的 Xamarin iOS 應用程式提交至 iTunes Connect 以進行發行之前，請確定您遵循 Apple 的 HomeKit 啟用應用程式指導方針：

- 如果使用 HomeKit 架構，則應用程式的主要用途 _必須_ 是 home automation。
- 應用程式的行銷文字必須通知使用者正在使用 HomeKit，而且必須提供隱私權原則。
- 嚴格禁止收集使用者資訊或使用 HomeKit 進行廣告。

如需完整的審查指導方針，請參閱 Apple 的 [App Store 審核指南](https://developer.apple.com/app-store/review/guidelines/)。

## <a name="whats-new-in-ios-9"></a>IOS 9 的新功能

Apple 已針對 iOS 9 的 HomeKit 進行下列變更和新增：

- **維護現有的物件** -如果已修改現有的附件，[首頁管理員 (`HMHomeManager`) 會通知您已修改的特定專案。
- **持續性識別碼** ：所有相關的 HomeKit 類別現在都包含 `UniqueIdentifier` 屬性，可在已啟用 HomeKit 的應用程式 (或相同應用程式) 的實例之間，唯一地識別指定的專案。
- **使用者管理** -新增了內建的查看控制器，可讓使用者透過可存取主要使用者首頁中 HomeKit 裝置的使用者進行管理。
- **使用者功能** -HomeKit 使用者現在有一組許可權，可控制他們可以在 HomeKit 和 HomeKit 啟用的配件中使用哪些功能。 您的應用程式應該只顯示與目前使用者相關的功能。 例如，只有系統管理員才能維護其他使用者。
- **預先** 定義的場景預先定義的場景，已針對平均 HomeKit 使用者所發生的四個常見事件建立：開始使用、離開、返回、前往床。 這些預先定義的場景無法從家裡刪除。
- **場景和 Siri** -Siri 在 iOS 9 中有更深入的場景支援，而且可以辨識 HomeKit 中定義之任何場景的名稱。 使用者只要說出其名稱就可以執行場景 Siri。
- **配件分類** -一組預先定義的類別已新增至所有配件，有助於識別要新增至家裡或從您的應用程式中處理的配件類型。 這些新類別可在配件設定期間使用。
- **Apple Watch 支援** -HomeKit 現在可供 watchOS 使用，且 Apple watch 將能夠控制 HomeKit 啟用的裝置，而不需要 iPhone 的監看式。 HomeKit for watchOS 支援下列功能：觀賞房屋、控制附屬應用程式，以及執行場景。
- **新的事件觸發程式類型** -除了 ios 8 中支援的計時器類型觸發程式之外，ios 9 現在也支援以配件狀態 (（例如感應器資料) 或地理位置）為基礎的事件觸發程式。 事件觸發 `NSPredicates` 程式用來設定其執行的條件。
- **遠端存取** -透過「遠端存取」，使用者現在可以在遠端位置離開辦公室時，控制其已啟用 HomeKit 的家用自動化配件。 在 iOS 8 中，只有在使用者在家中有第三代 Apple 電視時，才支援此功能。 在 iOS 9 中，會提高這項限制，並透過 iCloud 和 HomeKit 附屬通訊協定 (HAP) 支援遠端存取。
- **新的藍牙低能量 (BLE) 能力** -HomeKit 現在支援更多可透過藍牙低能源 (BLE) 通訊協定進行通訊的配件類型。 使用 HAP 安全通道時，HomeKit 配件可以公開另一個 Bluetooth 配件，Wi-Fi (如果它不在藍牙範圍) 。 在 iOS 9 中，BLE 配件具有通知和中繼資料的完整支援。
- **新的配件類別** -Apple 在 iOS 9 中新增了下列新的配件類別： Window Coverings、電動大門和 Windows、鬧鐘系統、感應器和可程式化參數。

如需 iOS 9 中 HomeKit 新功能的詳細資訊，請參閱 Apple 的 [HomeKit 索引](https://developer.apple.com/homekit/) 和 [HomeKit 影片中的新](https://developer.apple.com/videos/wwdc/2015/?id=210) 功能。

## <a name="summary"></a>摘要

本文引進 Apple 的 HomeKit home automation framework。 它會示範如何使用 HomeKit 附屬模擬器來設定和設定測試裝置，以及如何建立簡單的 Xamarin iOS 應用程式，以使用 HomeKit 來探索、通訊及控制家庭自動化裝置。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [適用于開發人員的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [IOS 9.0 的新功能](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [HomeKit 使用者介面指導方針](https://developer.apple.com/homekit/ui-guidelines/)
- [HomeKit 架構參考](https://developer.apple.com/library/ios/home_kit_framework_ref)