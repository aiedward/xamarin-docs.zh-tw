---
title: 在 Xamarin 中 HomeKit
description: HomeKit 是用來控制家庭自動化裝置的 Apple 架構。 本文介紹 HomeKit，並說明如何在 HomeKit 的配件模擬器中設定測試配件，以及撰寫簡單的 Xamarin iOS 應用程式來與這些配件互動。
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: b9b8d1dbaf7872ab629392e15d906540ae491db4
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84572594"
---
# <a name="homekit-in-xamarinios"></a>在 Xamarin 中 HomeKit

_HomeKit 是用來控制家庭自動化裝置的 Apple 架構。本文介紹 HomeKit，並說明如何在 HomeKit 的配件模擬器中設定測試配件，以及撰寫簡單的 Xamarin iOS 應用程式來與這些配件互動。_

[![](homekit-images/accessory01.png "An example HomeKit enabled App")](homekit-images/accessory01.png#lightbox)

Apple 在 iOS 8 中引進了 HomeKit，可將多個家庭自動化裝置從不同的廠商緊密整合成單一、一致的單位。 藉由推廣常用的通訊協定來探索、設定和控制家庭自動化裝置，HomeKit 可讓非相關廠商的裝置一起工作，而不需要個別廠商協調工作。

透過 HomeKit，您可以建立一個 Xamarin iOS 應用程式，以控制任何已啟用 HomeKit 的裝置，而不需使用廠商提供的 Api 或應用程式。 使用 HomeKit，您可以執行下列動作：

- 探索新的 HomeKit 啟用的家用自動化裝置，並將其新增至將在所有使用者的 iOS 裝置上保存的資料庫。
- 設定、設定、顯示及控制 HomeKit _Home 設定資料庫_中的任何裝置。
- 與任何預先設定的 HomeKit 裝置通訊，並命令以執行個別動作或共同作業，例如開啟廚房中的所有光源。

除了在 Home 設定資料庫中提供裝置以 HomeKit 啟用的應用程式之外，HomeKit 還提供 Siri 語音命令的存取權。 假設有適當設定的 HomeKit 設定，使用者可以發出語音命令，例如「Siri、開啟聊天室中的燈。」

<a name="Home-Configuration-Database"></a>

## <a name="the-home-configuration-database"></a>首頁設定資料庫

HomeKit 會將指定位置中的所有自動化裝置組織成一個主集合。 此集合提供一種方式，讓使用者將其家用自動化裝置分組成邏輯排列單位，使其具有有意義、人類可讀的標籤。

首頁集合儲存在家庭設定資料庫中，會自動在所有使用者的 iOS 裝置上進行備份及同步處理。 HomeKit 提供下列用來處理 Home 設定資料庫的類別：

- `HMHome`-這是最上層的容器，可保留單一實體位置中所有家用自動化裝置的所有資訊和設定（例如 單一家族的居民）。 使用者可能會有一個以上的居住，例如其主要家庭和假房屋。 或者，在相同的屬性上可能會有不同的「房屋」，例如主要房子和車庫上的來賓房子。 不論是哪一種方式，都 `HMHome` _必須_先設定並儲存一個物件，然後才能輸入任何其他 HomeKit 資訊。
- `HMRoom`-雖然是選擇性的，但 `HMRoom` 可讓使用者定義家中的特定會議室（ `HMHome` ），例如：廚房、衛生間、車庫或聊天室。 使用者可以將其所在的特定位置中的所有家庭自動化裝置組成群組 `HMRoom` ，並將其當作一個單位來處理。 例如，要求 Siri 關閉車庫燈。
- `HMAccessory`-這代表已安裝在使用者居住中的個別實體 HomeKit 啟用自動化裝置（例如智慧型控溫器）。 每個 `HMAccessory` 都會指派給 `HMRoom` 。 如果使用者尚未設定任何會議室，HomeKit 會將附件指派給特殊的預設房間。
- `HMService`-表示指定的所提供的服務 `HMAccessory` ，例如淺色或其色彩的開啟/關閉狀態（如果支援色彩變更）。 每個 `HMAccessory` 可以有一個以上的服務，例如也包含光線的車庫門開啟工具。 此外，指定的 `HMAccessory` 可能會有使用者控制項以外的服務（例如，「固件更新」）。
- `HMZone`-可讓使用者將物件集合分組 `HMRoom` 到邏輯區域中，例如樓上、樓下或「中」。 雖然是選擇性的，但這可以讓互動，例如要求 Siri 將所有輕量樓下關閉。

<a name="Provisioning-a-HomeKit-App"></a>

## <a name="provisioning-a-homekit-app"></a>布建 HomeKit 應用程式

基於 HomeKit 所加諸的安全性需求，使用 HomeKit 架構的 Xamarin iOS 應用程式必須在 Apple 開發人員入口網站和 Xamarin 專案檔中正確設定。

執行下列動作：

1. 登入[Apple Developer 入口網站](https://developer.apple.com)。
2. 按一下 [**憑證]、[識別碼] & 設定檔**。
3. 如果您尚未這麼做，請按一下 [**識別碼**]，然後為您的應用程式建立識別碼（例如 `com.company.appname` ），或者編輯您現有的識別碼。
4. 確定已檢查指定識別碼的**HomeKit**服務： 

    [![](homekit-images/provision01.png "Enable the HomeKit service for the given ID")](homekit-images/provision01.png#lightbox)
5. 儲存您的變更。
6. 按一下 [布建**設定檔**  >  **開發**]，然後為您的應用程式建立新的開發布建設定檔： 

    [![](homekit-images/provision02.png "Create a new development provisioning profile for the app")](homekit-images/provision02.png#lightbox)
7. 請下載並安裝新的布建設定檔，或使用 Xcode 來下載並安裝設定檔。
8. 編輯您的 Xamarin iOS 專案選項，並確定您使用的是您剛才建立的布建設定檔： 

    [![](homekit-images/provision03.png "Select provisioning profile just created")](homekit-images/provision03.png#lightbox)
9. 接下來，編輯您的**plist**檔案，並確定您使用的是用來建立布建設定檔的應用程式識別碼： 

    [![](homekit-images/provision04.png "Set the App ID ")](homekit-images/provision04.png#lightbox)
10. 最後，編輯您的**plist**檔案，並確定已選取**HomeKit**權利： 

    [![](homekit-images/provision05.png "Enable the HomeKit entitlement")](homekit-images/provision05.png#lightbox)
11. 將變更儲存至所有檔案。

這些設定都備妥之後，應用程式就可以開始存取 HomeKit Framework Api。 如需布建的詳細資訊，請參閱我們的[裝置](~/ios/get-started/installation/device-provisioning/index.md)布建和布建[您的應用程式](~/ios/get-started/installation/device-provisioning/index.md)指南。

> [!IMPORTANT]
> 測試啟用 HomeKit 的應用程式需要已正確布建以進行開發的實際 iOS 裝置。 無法從 iOS 模擬器測試 HomeKit。

## <a name="the-homekit-accessory-simulator"></a>HomeKit 的配件模擬器

為了提供一種方法來測試所有可能的家用自動化裝置和服務，而不需要擁有實體裝置，Apple 建立了_HomeKit 的配件_模擬器。 您可以使用此模擬器來設定和設定虛擬 HomeKit 裝置。

### <a name="installing-the-simulator"></a>安裝模擬器

Apple 提供 HomeKit 配件模擬器作為 Xcode 的個別下載，因此您必須先安裝它，才能繼續進行。

執行下列動作：

1. 在網頁瀏覽器中，造訪[Apple 開發人員的下載](https://developer.apple.com/download/more/?name=for%20Xcode)
2. 下載**Xcode xxx 的其他工具**（其中 xxx 是您已安裝的 Xcode 版本）： 

    [![](homekit-images/simulator01.png "Download the Additional Tools for Xcode")](homekit-images/simulator01.png#lightbox)
3. 開啟磁片映射，並在您的**應用程式**目錄中安裝工具。

安裝 HomeKit 配件模擬器之後，就可以建立虛擬附屬應用程式來進行測試。

### <a name="creating-virtual-accessories"></a>建立虛擬附屬應用程式

若要啟動 HomeKit 配件模擬器並建立幾個虛擬附屬應用程式，請執行下列動作：

1. 從 [應用程式] 資料夾，啟動 HomeKit 附屬模擬器： 

    [![](homekit-images/simulator02.png "The HomeKit Accessory Simulator")](homekit-images/simulator02.png#lightbox)
2. 按一下 **+** 按鈕，然後選取 [**新增附件**...]： 

    [![](homekit-images/simulator03.png "Add a new accessory")](homekit-images/simulator03.png#lightbox)
3. 填寫新附件的相關資訊，然後按一下 [**完成]** 按鈕： 

    [![](homekit-images/simulator04.png "Fill out the information about the new accessory")](homekit-images/simulator04.png#lightbox)
4. 按一下 [**新增服務**]。 按鈕，然後從下拉式清單中選取一個服務類型： 

    [![](homekit-images/simulator05.png "Select a service type from the dropdown")](homekit-images/simulator05.png#lightbox)
5. 提供服務的**名稱**，然後按一下 [**完成]** 按鈕： 

    [![](homekit-images/simulator06.png "Enter a Name for the service")](homekit-images/simulator06.png#lightbox)
6. 您可以藉由按一下 [**新增特性**] 按鈕，並進行必要的設定，來提供服務的選用特性： 

    [![](homekit-images/simulator07.png "Configuring the required settings")](homekit-images/simulator07.png#lightbox)
7. 重複上述步驟，以建立 HomeKit 支援的每一種虛擬家用自動化裝置。

建立和設定一些範例虛擬 HomeKit 配件之後，您現在可以從您的 Xamarin iOS 應用程式取用及控制這些裝置。

## <a name="configuring-the-infoplist-file"></a>設定 plist 檔案

IOS 10 （和更新版本）的新工作，開發人員必須將 `NSHomeKitUsageDescription` 金鑰新增至應用程式的檔案 `Info.plist` ，並提供字串來宣告應用程式為何想要存取使用者的 HomeKit 資料庫。 第一次執行應用程式時，會向使用者顯示這個字串：

[![](homekit-images/info01.png "The HomeKit permission dialog")](homekit-images/info01.png#lightbox)

若要設定此機碼，請執行下列動作：

1. 按兩下 `Info.plist` [**方案總管**中的檔案以開啟它進行編輯。
2. 在畫面底部，切換至 [**來源**] 視圖。
3. 將新**專案**新增至清單。
4. 從下拉式清單中選取 [**隱私權-HomeKit 使用方式描述**]： 

    [![](homekit-images/info02.png "Select Privacy - HomeKit Usage Description")](homekit-images/info02.png#lightbox)
5. 輸入應用程式想要存取使用者 HomeKit 資料庫的原因描述： 

    [![](homekit-images/info03.png "Enter a description")](homekit-images/info03.png#lightbox)
6. 將變更儲存到檔案。

> [!IMPORTANT]
> 如果無法在檔案 `NSHomeKitUsageDescription` 中設定金鑰， `Info.plist` 會導致應用程式以_無_訊息方式失敗（在執行時間由系統關閉），而在 iOS 10 （或更新版本）中執行時，不會發生錯誤。

## <a name="connecting-to-homekit"></a>連接到 HomeKit

若要與 HomeKit 通訊，您的 Xamarin iOS 應用程式必須先具現化類別的實例 `HMHomeManager` 。 Home Manager 是 HomeKit 的中心進入點，負責提供可用家庭的清單、更新和維護該清單，以及傳回使用者的_主要家庭_。

`HMHome`物件包含「提供」的所有相關資訊，包括任何可能包含的房間、群組或區域，以及任何已安裝的「家庭自動化」附屬應用程式。 在 HomeKit 中執行任何作業之前，必須先建立至少一個， `HMHome` 並將其指派為主要首頁。

您的應用程式會負責檢查主要首頁是否存在，並建立並指派一個（如果沒有的話）。

### <a name="adding-a-home-manager"></a>新增主管理員

若要將 HomeKit 認知新增至 Xamarin iOS 應用程式，請編輯**AppDelegate.cs**檔案進行編輯，使其看起來如下所示：

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

第一次執行應用程式時，系統會詢問使用者是否要允許它存取其 HomeKit 資訊：

[![](homekit-images/home01.png "The user will be asked if they want to allow it to access their HomeKit information")](homekit-images/home01.png#lightbox)

如果使用者回答 **[確定]**，則應用程式將能夠使用其 HomeKit 的配件，否則將不會發生錯誤，而且任何對 HomeKit 的呼叫都會失敗並產生錯誤。

有了 Home Manager 之後，應用程式就必須查看是否已設定主要家庭，如果沒有，則提供一種方法讓使用者建立和指派一個。

### <a name="accessing-the-primary-home"></a>存取主要首頁

如上所述，必須先建立並設定主要家庭，才能使用 HomeKit，而應用程式會負責提供一種方式，讓使用者建立和指派主要家庭（如果尚未存在的話）。

當您的應用程式第一次啟動或從背景傳回時，它需要監視 `DidUpdateHomes` 類別的事件， `HMHomeManager` 以檢查主要首頁是否存在。 如果不存在，則應該提供介面讓使用者建立一個。

您可以將下列程式碼新增至視圖控制器，以檢查主要首頁：

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

當「主要管理員」建立 HomeKit 的連線時， `DidUpdateHomes` 將會引發事件，任何現有的家庭將會載入至經理的家庭集合，而主要家庭則會載入（如果有的話）。

### <a name="adding-a-primary-home"></a>新增主要首頁

如果的 `PrimaryHome` 屬性是在 `HMHomeManager` `null` `DidUpdateHomes` 事件之後，您必須提供方法讓使用者建立和指派主要家庭，再繼續進行。

應用程式通常會顯示一個表單，讓使用者為新的家裡命名，然後將它傳遞給 Home Manager，以作為主要家所設定。 針對**HomeKitIntro**範例應用程式，會在 IOS 設計工具中建立模式視圖，並由 `AddHomeSegue` segue 從應用程式的主要介面呼叫。

它會提供文字欄位，讓使用者輸入新首頁的名稱，並使用按鈕來新增首頁。 當使用者按下 [**新增首頁**] 按鈕時，下列程式碼會呼叫 [首頁管理員] 來新增 home：

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

`AddHome`方法會嘗試建立新的 home，並將它傳回給指定的回呼常式。 如果 `error` 屬性不是，就會 `null` 發生錯誤，而且應該會向使用者顯示。 最常見的錯誤是由非唯一的家庭名稱或主管理員無法與 HomeKit 通訊所造成。

如果已成功建立家庭，您必須呼叫方法，將 `UpdatePrimaryHome` 新家裡設定為主要家庭。 同樣地，如果 `error` 屬性不是 `null` ，就會發生錯誤，而且應該向使用者呈現。

您也應該監視主管理員 `DidAddHome` 和 `DidRemoveHome` 事件，並視需要更新應用程式的使用者介面。

> [!IMPORTANT]
> `AlertView.PresentOKAlert`上述範例程式碼中所使用的方法，是 HomeKitIntro 應用程式中可讓您更輕鬆地使用 IOS 警示的 helper 類別。

## <a name="finding-new-accessories"></a>尋找新的配件

從 Home Manager 定義或載入主要家裡之後，您的 Xamarin iOS 應用程式就可以呼叫 `HMAccessoryBrowser` 來尋找任何新的家庭自動化附屬專案，並將其新增至首頁。

呼叫 `StartSearchingForNewAccessories` 方法，以在完成時開始尋找新的附屬應用程式和 `StopSearchingForNewAccessories` 方法。

> [!IMPORTANT]
> `StartSearchingForNewAccessories`不應長時間執行，因為它會對 iOS 裝置的電池壽命和效能造成負面影響。 Apple 建議在 `StopSearchingForNewAccessories` 一分鐘後呼叫，或只在尋找附件 UI 呈現給使用者時進行搜尋。

`DidFindNewAccessory`系統會在探索到新的配件時呼叫此事件，並將它們新增至 `DiscoveredAccessories` [附屬瀏覽器] 中的清單。

此 `DiscoveredAccessories` 清單會包含物件的集合 `HMAccessory` ，這些物件定義提供已啟用 HomeKit 的家用自動化裝置和其可用的服務，例如光源或車庫門控制項。

一旦找到新的附件之後，應該會向使用者顯示，因此他們可以選取它並將它新增至首頁。 範例：

[![](homekit-images/accessory01.png "Finding a new accessory")](homekit-images/accessory01.png#lightbox)

呼叫 `AddAccessory` 方法，將選取的附件加入至首頁的集合。 例如：

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

如果 `err` 屬性不是，就會 `null` 發生錯誤，而且應該會向使用者顯示。 否則，系統會要求使用者輸入裝置的安裝程式碼以新增：

[![](homekit-images/accessory02.png "Enter the setup code for the device to add")](homekit-images/accessory02.png#lightbox)

在 HomeKit 配件模擬器中，您可以在 [**設定程式碼**] 欄位底下找到此數位：

[![](homekit-images/accessory03.png "The Setup Code field in the HomeKit Accessory Simulator")](homekit-images/accessory03.png#lightbox)

對於實際 HomeKit 的配件，安裝程式碼會列印在裝置本身的標籤、產品方塊或附件的使用者手冊中。

當使用者將附件新增至其主集合之後，您應該監視此附件瀏覽器的 `DidRemoveNewAccessory` 事件，並更新使用者介面，以從可用清單中移除該附件。

## <a name="working-with-accessories"></a>使用配件

一旦主要家庭和已建立，且已將配件加入其中，您就可以顯示要供使用者使用的附屬配件清單（以及會議室）。

`HMRoom`物件包含指定房間的所有相關資訊，以及屬於該會議室的任何附屬應用程式。 會議室可以選擇性地組織成一或多個區域。 `HMZone`包含指定區域及其所屬房間的所有相關資訊。

就此範例而言，我們會將專案保持簡單，並直接使用家庭的配件，而不是將它們組織成房間或區域。

`HMHome`物件包含指派的附件清單，可在其屬性中向使用者顯示 `Accessories` 。 例如：

[![](homekit-images/accessory04.png "An example accessory")](homekit-images/accessory04.png#lightbox)

在此表單中，使用者可以選取指定的附件，並使用它所提供的服務。

## <a name="working-with-services"></a>使用服務

當使用者與指定的 HomeKit 啟用的家用自動化裝置互動時，通常會透過它所提供的服務。 `Services`類別的屬性 `HMAccessory` 包含物件的集合 `HMService` ，這些物件會定義裝置所提供的服務。

服務包括光源、恒溫器、、車庫門 openers、交換器或鎖定等。 有些裝置（例如車庫門開啟工具）將會提供一項以上的服務，例如光線和開啟或關閉門的能力。

除了給定的附件所提供的特定服務以外，每個附件都會包含 `Information Service` 定義屬性的，例如其名稱、製造商、型號和序號。

### <a name="accessory-service-types"></a>裝飾服務類型

以下是透過列舉提供的服務類型 `HMServiceType` ：

- **AccessoryInformation** -提供指定的家用自動化裝置（配件）的相關資訊。
- **AirQualitySensor** -定義空氣品質感應器。
- **電池**-定義配件電池的狀態。
- **CarbonDioxideSensor** -定義碳二氧化碳感應器。
- **CarbonMonoxideSensor** -定義碳一氧化碳感應器。
- **ContactSensor** -定義連絡人感應器（例如開啟或關閉的視窗）。
- **門**-定義門狀態感應器（例如 [已開啟] 或 [已關閉]）。
- **風扇**-定義遠端控制的風扇。
- **GarageDoorOpener** -定義車庫門開啟工具。
- **HumiditySensor** -定義濕度感應器。
- **LeakSensor** -定義流失感應器（例如熱水 heater 或洗手機器）。
- **燈泡**-定義一個獨立光源或屬於另一個配件的光線（例如車庫門開啟工具）。
- **LightSensor** -定義光線感應器。
- **LockManagement** -定義管理自動化門鎖的服務。
- **LockMechanism** -定義遠端控制的鎖定（例如門鎖定）。
- **MotionSensor** -定義動作感應器。
- **OccupancySensor** -定義佔用感應器。
- **插座**-定義遠端控制的牆輸出口。
- **SecuritySystem** -定義家庭安全性系統。
- **StatefulProgrammableSwitch** -定義會在觸發後維持狀態的可程式化參數（例如翻轉參數）。
- **StatelessProgrammableSwitch** -定義可程式化的參數，在觸發之後回到其初始狀態（例如 [推送] 按鈕）。
- **SmokeSensor** -定義冒煙感應器。
- **Switch** -定義開啟/關閉交換器，例如標準牆交換器。
- **溫度感應器**-定義溫度感應器。
- **控溫器**-定義用來控制 HVAC 系統的智慧型控溫器。
- **視窗**-定義棒棒糖要遠端開啟或關閉的自動化視窗。
- **WindowCovering** -定義一個涵蓋的遠端控制視窗，例如可以開啟或關閉的遮蔽。

### <a name="displaying-service-information"></a>顯示服務資訊

載入之後， `HMAccessory` 您可以查詢它所提供的個別 `HNService` 物件，並向使用者顯示該資訊：

[![](homekit-images/accessory05.png "Displaying Service Information")](homekit-images/accessory05.png#lightbox)

您應該一律先檢查的 `Reachable` 屬性， `HMAccessory` 然後再嘗試使用它。 無法連線到某個附件，但該使用者不在裝置的範圍內，或已被拔掉。

一旦選取服務，使用者就可以查看或修改該服務的一或多個特性，以監視或控制指定的家用自動化裝置。

<a name="Working-with-Characteristics"></a>

## <a name="working-with-characteristics"></a>使用特性

每個 `HMService` 物件都可以包含物件的集合 `HMCharacteristic` ，這些物件可以提供服務狀態的相關資訊（例如，要開啟或關閉的門），或允許使用者調整狀態（例如設定光源的色彩）。

`HMCharacteristic`不僅提供特性和其狀態的相關資訊，還提供透過_特性中繼資料_（）處理狀態的方法 `HMCharacteristisMetadata` 。 此中繼資料可以提供向使用者顯示資訊或允許他們修改狀態時很有用的屬性（例如最小和最大值範圍）。

`HMCharacteristicType`列舉會提供一組特性中繼資料值，您可以如下所示加以定義或修改：

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
- 型號
- MotionDetected
- 名稱
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

為確保您的應用程式具有給定特性的最新狀態，請呼叫 `ReadValue` 類別的方法 `HMCharacteristic` 。 如果 `err` 屬性不是 `null` ，就會發生錯誤，而且不一定會向使用者呈現。

特性的 `Value` 屬性包含給定特性的目前狀態做為 `NSObject` ，因此無法直接在 c # 中使用。

若要讀取值，下列 helper 類別已新增至**HomeKitIntro**範例應用程式：

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

`NSObjectConverter`每當應用程式需要讀取特性的目前狀態時，就會使用。 例如：

```csharp
var value = NSObjectConverter.ToFloat (characteristic.Value);
```

上述行會將值轉換為 `float` ，然後在 Xamarin c # 程式碼中使用。

若要修改 `HMCharacteristic` ，請呼叫其 `WriteValue` 方法，並在呼叫中包裝新的值 `NSObject.FromObject` 。 例如：

```csharp
Characteristic.WriteValue(NSObject.FromObject(value),(err) =>{
    // Was there an error?
    if (err!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Update Error",err.LocalizedDescription,Controller);
    }
});
```

如果 `err` 屬性不是，就會 `null` 發生錯誤，應該向使用者呈現。

### <a name="testing-characteristic-value-changes"></a>測試特性值變更

使用 `HMCharacteristics` 和模擬的配件時， `Value` 可以在 HomeKit 的配件模擬器內監視對屬性的修改。

在實際的 iOS 裝置硬體上執行**HomeKitIntro**應用程式時，您應該幾乎可以立即在 HomeKit 的配件模擬器中看到特性值的變更。 例如，變更 iOS 應用程式中光線的狀態：

[![](homekit-images/test01.png "Changing the state of a light in an iOS app")](homekit-images/test01.png#lightbox)

應變更 HomeKit 配件模擬器中的光線狀態。 如果值不會變更，請在寫入新的特性值時檢查錯誤訊息的狀態，並確定該附件仍然可供連線。

## <a name="advanced-homekit-features"></a>Advanced HomeKit 功能

本文涵蓋在 Xamarin iOS 應用程式中使用 HomeKit 配件所需的基本功能。 不過，此簡介中未涵蓋 HomeKit 的數個先進功能：

- **會議室**-HomeKit 啟用的配件可以選擇性地組織到房間內供使用者使用。 這可讓 HomeKit 以方便使用者瞭解和使用的方式呈現配件。 如需建立和維護會議室的詳細資訊，請參閱 Apple 的[HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom)檔。
- **區域**-使用者可以選擇性地將會議室組織成區域。 「區域」是指使用者可能視為單一單位的會議室集合。 例如：樓上、樓下或「中」。 同樣地，這讓 HomeKit 能夠以對終端使用者有意義的方式來呈現和處理配件。 如需建立和維護區域的詳細資訊，請參閱 Apple 的[HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone)檔。
- **動作和動作集**-動作會修改附件服務特性，而且可以分組到集合中。 動作集作為腳本來控制配件群組並協調其動作。 例如，「觀賞電視」腳本可能會關閉遮蔽、讓光線變暗，並開啟電視和其音效系統。 如需建立和維護動作和動作集的詳細資訊，請參閱 Apple 的[HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction)和[HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet)檔。
- **觸發**程式-當符合一組指定的條件時，觸發程式可以啟動一或多個動作集。 例如，開啟 portch 光線，並在其深超出時鎖定所有外部門。 如需建立和維護觸發程式的詳細資訊，請參閱 Apple 的[HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger)檔。

由於這些功能使用上述的相同技巧，因此您可以遵循 Apple 的[HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)、 [HomeKit 使用者介面指導方針](https://developer.apple.com/homekit/ui-guidelines/)和[HomeKit 架構參考](https://developer.apple.com/library/ios/home_kit_framework_ref)，輕鬆地執行。

## <a name="homekit-app-review-guidelines"></a>HomeKit 應用程式審查指導方針

在 iTunes App Store 中將已啟用 HomeKit 的 Xamarin iOS 應用程式提交至 iTunes Connect 以進行發行之前，請確定您遵循 Apple 的 HomeKit 啟用應用程式指導方針：

- 如果使用 HomeKit 架構，則應用程式的主要目的_必須_是「家庭自動化」。
- 應用程式的行銷文字必須通知使用者正在使用 HomeKit，而他們必須提供隱私權原則。
- 嚴格禁止收集使用者資訊或使用 HomeKit 進行廣告。

如需完整的審查指導方針，請參閱 Apple 的[App Store 審查指導方針](https://developer.apple.com/app-store/review/guidelines/)。

## <a name="whats-new-in-ios-9"></a>IOS 9 的新功能

Apple 已對 iOS 9 的 HomeKit 進行下列變更和新增：

- **維護現有的物件**-如果已修改現有的附件，則主管理員（ `HMHomeManager` ）會通知您已修改的特定專案。
- **持續性識別碼**-所有相關的 HomeKit 類別現在都包含一個 `UniqueIdentifier` 屬性，可在已啟用 HomeKit 的應用程式（或相同應用程式的實例）上唯一識別指定的專案。
- **使用者管理**-新增內建視圖控制器，以提供使用者管理功能，讓使用者可以存取主要使用者首頁中的 HomeKit 裝置。
- **使用者功能**-HomeKit 使用者現在有一組許可權，可控制他們可以在 HomeKit 和 HomeKit 啟用的配件中使用哪些功能。 您的應用程式應該只會顯示與目前使用者相關的功能。 例如，只有系統管理員才能維護其他使用者。
- **預先定義的場景**-預先定義的場景已針對平均 HomeKit 使用者所發生的四個常見事件而建立： [取得]、[離開]、[返回]、[移至平臺]。 這些預先定義的場景無法從家中刪除。
- **場景和 Siri** -Siri 對 iOS 9 中的場景有更深入的支援，而且可以辨識 HomeKit 中定義的任何場景名稱。 使用者只要說出其名稱就可以執行場景，以 Siri。
- **裝飾類別**-已將一組預先定義的類別新增至所有的配件，並協助識別要新增至家裡或從您的應用程式中處理的附件類型。 這些新的類別可在配件設定期間使用。
- **Apple Watch 支援**-HomeKit 現在可供 watchOS，而 Apple Watch 將能夠控制 HomeKit 已啟用的裝置，而不會有 iPhone 靠近監看式。 HomeKit for watchOS 支援下列功能：觀看家裡、控制配件和執行場景。
- **新事件觸發程式類型**-除了 ios 8 中支援的計時器類型觸發程式以外，ios 9 現在支援以輔助狀態（例如感應器資料）或地理位置為基礎的事件觸發程式。 事件觸發程式會使用 `NSPredicates` 來設定其執行的條件。
- **遠端存取**-透過遠端存取，使用者現在可以控制其 HomeKit 已啟用的家庭自動化附屬元件，而不受遠端位置的房子支援。 在 iOS 8 中，只有在使用者在家中有第三代的 Apple 電視時，才支援這種情況。 在 iOS 9 中，這項限制已獲得提升，並透過 iCloud 和 HomeKit 附屬通訊協定（HAP）支援遠端存取。
- **新的藍牙低功耗（BLE）功能**-HomeKit 現在支援更多可透過藍牙低功耗（BLE）通訊協定來溝通的配件類型。 使用 HAP 安全通道，HomeKit 的附件可以透過 Wi-fi 公開另一個藍牙附件（如果它超出藍牙範圍）。 在 iOS 9 中，BLE 配件具有通知和中繼資料的完整支援。
- **新的附件類別**-Apple 在 iOS 9 中新增了下列新的附件類別：視窗 Coverings、電動門和 Windows、鬧鐘系統、感應器和可程式化交換器。

如需 iOS 9 中 HomeKit 之新功能的詳細資訊，請參閱 Apple 的[HomeKit Index](https://developer.apple.com/homekit/)和[HomeKit](https://developer.apple.com/videos/wwdc/2015/?id=210) video 的新功能。

## <a name="summary"></a>總結

本文引進了 Apple 的 HomeKit home automation 架構。 它示範如何使用 HomeKit 的附屬模擬器來設定和設定測試裝置，以及如何建立簡單的 Xamarin iOS 應用程式來探索、與使用 HomeKit 的家用自動化裝置通訊並加以控制。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 開發人員](https://developer.apple.com/ios/pre-release/)
- [IOS 9.0 中的新功能](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [HomeKit 使用者介面指導方針](https://developer.apple.com/homekit/ui-guidelines/)
- [HomeKit 架構參考](https://developer.apple.com/library/ios/home_kit_framework_ref)
