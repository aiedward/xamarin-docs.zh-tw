---
title: 在 Xamarin.iOS 中 HomeKit
description: HomeKit 是 Apple 的架構，用於控制家用自動化裝置。 這篇文章介紹 HomeKit，並說明如何與這些附屬應用程式互動的 HomeKit 配件模擬器和撰寫簡單的 Xamarin.iOS 應用程式中設定測試附屬應用程式。
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 6793190fa3278455a00d7ea08ab52a643c369a35
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61371431"
---
# <a name="homekit-in-xamarinios"></a>在 Xamarin.iOS 中 HomeKit

_HomeKit 是 Apple 的架構，用於控制家用自動化裝置。這篇文章介紹 HomeKit，並說明如何與這些附屬應用程式互動的 HomeKit 配件模擬器和撰寫簡單的 Xamarin.iOS 應用程式中設定測試附屬應用程式。_

[![](homekit-images/accessory01.png "範例 HomeKit 啟用的應用程式")](homekit-images/accessory01.png#lightbox)

Apple iOS 8 中導入 HomeKit，來順暢地將來自各種不同廠商的多個居家自動化裝置整合到單一、 一致的單位。 藉由提升常見的通訊協定，用於探索、 設定和控制家用自動化裝置 HomeKit 讓裝置從非相關的廠商，以搭配使用，完全不需要協調工作，個別供應商。

HomeKit，您可以建立沒有利用廠商提供的 Api 或應用程式會控制任何 HomeKit 啟用裝置的 Xamarin.iOS 應用程式。 使用 HomeKit，您可以執行下列作業：

- 探索新的啟用 HomeKit 家用自動化裝置，並將其新增至會保存到所有使用者的 iOS 裝置的資料庫。
- 設定、 組態、 顯示和控制各種裝置 HomeKit_首頁設定資料庫_。
- 與任何預先設定的 HomeKit 裝置進行通訊，以及命令執行個別的動作，或是正常運作，例如開啟的所有位在廚房光線。

除了首頁設定資料庫中啟用 HomeKit 應用程式，提供裝置 HomeKit 會提供存取 Siri 語音命令。 指定適當設定的 HomeKit 安裝程式，使用者可以發出語音命令這類 「 Siri，開燈在客廳中。 」

<a name="Home-Configuration-Database" />

## <a name="the-home-configuration-database"></a>主要的組態資料庫

HomeKit 會組織成首頁集合的指定位置中的所有自動化裝置。 此集合可以讓使用者在其家用自動化裝置分組成有意義、 人類看得懂的標籤以邏輯方式排列的單位。

常用的集合會儲存在常用組態資料庫，會自動備份和同步處理所有使用者的 iOS 裝置。 HomeKit 使用首頁設定資料庫提供下列類別：

- `HMHome` -這是在單一實體位置 （例如保留所有資訊和組態的所有家用自動化裝置的最上層容器。 一系列的居住地)。 使用者可能會有一個以上的居住地，例如其主要的家用和休假房屋。 或者，它們可能會有不同 「 樹木上相同的屬性，例如主要的房屋和透過車庫客體房屋。 無論如何，至少一個`HMHome`物件_必須_設定和儲存之前，您可以輸入任何其他 HomeKit 資訊。
- `HMRoom` -While 選擇性的`HMRoom`可讓使用者定義特定房間內的首頁 (`HMHome`) 這類：廚房、 臥、 車庫或起居室的絨布。 使用者可以將群組的所有家用自動化裝置到他們家中的特定位置中`HMRoom`和做為一個單位處理它們。 例如，要求 Siri 車庫關燈。
- `HMAccessory` -這表示個別、 實體 HomeKit 啟用自動化的裝置已安裝的使用者居住地 （例如智慧控溫器）。 每個`HMAccessory`指派給`HMRoom`。 如果使用者尚未設定任何房間，HomeKit 會將附屬應用程式指派至特殊的預設聊天室。
- `HMService` -代表所提供的服務指定`HMAccessory`，例如光線或其色彩 （如果有色彩變更為支援） 的開啟/關閉狀態。 每個`HMAccessory`可以有一個以上的服務，例如車庫門開啟器也包含光線。 此外，指定`HMAccessory`可能服務，例如韌體更新、 外部使用者控制項。
- `HMZone` -可讓使用者群組的集合`HMRoom`成邏輯區域，例如樓上、 Downstairs 或地下室的物件。 雖然是選擇性的這可讓互動，例如要求使用 Siri 開啟所有燈號 downstairs 關閉。

<a name="Provisioning-a-HomeKit-App" />

## <a name="provisioning-a-homekit-app"></a>佈建 HomeKit 應用程式

HomeKit 所加諸的安全性需求，因為使用 HomeKit 架構的 Xamarin.iOS 應用程式必須正確設定這兩個 Apple 開發人員入口網站中，與 Xamarin.iOS 專案檔中。

請執行下列動作：

1. 登入[Apple 開發人員入口網站](https://developer.apple.com)。
2. 按一下 **憑證、 識別碼與設定檔**。
3. 如果您尚未這麼做，按一下**識別碼**並建立您的應用程式的識別碼 (例如`com.company.appname`)，否則請編輯您現有的識別碼。
4. 請確認**HomeKit**已核取指定之識別碼的服務： 

    [![](homekit-images/provision01.png "啟用指定之識別碼的 「 HomeKit 」 服務")](homekit-images/provision01.png#lightbox)
5. 儲存您的變更。
4. 按一下 **佈建設定檔** > **開發**並建立新的開發，佈建您的應用程式設定檔： 

    [![](homekit-images/provision02.png "建立新的開發，佈建應用程式設定檔")](homekit-images/provision02.png#lightbox)
5. 下載並安裝新的佈建設定檔，或使用 Xcode 來下載並安裝設定檔。
6. 編輯您的 Xamarin.iOS 專案選項，並確定您使用您剛才建立的佈建設定檔： 

    [![](homekit-images/provision03.png "選取剛才建立的佈建設定檔")](homekit-images/provision03.png#lightbox)
7. 接著，編輯您**Info.plist**檔案，並確定您使用的用來建立佈建設定檔的應用程式識別碼： 

    [![](homekit-images/provision04.png "設定應用程式識別碼 ")](homekit-images/provision04.png#lightbox)
8. 最後，編輯您**Entitlements.plist**檔案，並確保**HomeKit**已選取權利： 

    [![](homekit-images/provision05.png "啟用 HomeKit 權利")](homekit-images/provision05.png#lightbox)
9. 儲存所有檔案的變更。

使用這些設定之後，應用程式現在已準備好存取 HomeKit 架構 Api。 如需佈建的詳細資訊，請參閱我們[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)並[佈建您的應用程式](~/ios/get-started/installation/device-provisioning/index.md)輔助線。

> [!IMPORTANT]
> 測試已啟用 HomeKit 應用程式開發需要正確佈建的真實的 iOS 裝置。 無法從 iOS 模擬器中測試 HomeKit。

## <a name="the-homekit-accessory-simulator"></a>HomeKit 配件模擬器

若要提供方法來測試所有可能的家用自動化裝置和服務，而不需要有實體裝置，建立 Apple _HomeKit 配件模擬器_。 您可以使用此模擬器，來安裝和設定虛擬 HomeKit 裝置項目。

### <a name="installing-the-simulator"></a>安裝模擬器

Apple 提供 HomeKit 配件模擬器作為個別下載從 Xcode，因此您必須安裝才能繼續。

請執行下列動作：

1. 在網頁瀏覽器中瀏覽[Apple 開發人員下載](https://developer.apple.com/download/more/?name=for%20Xcode)
2. 下載**Xcode xxx 的其他工具**（其中 xxx 是您已安裝的 Xcode 版本）： 

    [![](homekit-images/simulator01.png "Xcode 中下載的其他工具")](homekit-images/simulator01.png#lightbox)
3. 開啟磁碟映像並安裝的工具，在您**應用程式**目錄。

與 HomeKit 配件模擬器安裝，您可以針對測試，建立虛擬附屬應用程式。

### <a name="creating-virtual-accessories"></a>建立虛擬附屬應用程式

若要啟動 HomeKit 配件模擬器，並建立幾個虛擬的附屬應用程式，執行下列作業：

1. 從應用程式 資料夾中，啟動 HomeKit 配件模擬器： 

    [![](homekit-images/simulator02.png "HomeKit 配件模擬器")](homekit-images/simulator02.png#lightbox)
2. 按一下  **+** 按鈕，然後選取**新附屬應用程式...**: 

    [![](homekit-images/simulator03.png "加入新的附屬應用程式")](homekit-images/simulator03.png#lightbox)
3. 填寫 新的附屬應用程式的相關資訊，然後按一下**完成**按鈕： 

    [![](homekit-images/simulator04.png "填寫 新的附屬應用程式的相關資訊")](homekit-images/simulator04.png#lightbox)
4. 按一下 **新增服務...** 按鈕，然後從下拉式清單中選取的服務類型： 

    [![](homekit-images/simulator05.png "從下拉式清單中選取的服務類型")](homekit-images/simulator05.png#lightbox)
5. 提供**名稱**服務，然後按一下**完成**按鈕： 

    [![](homekit-images/simulator06.png "輸入服務名稱")](homekit-images/simulator06.png#lightbox)
6. 您也可以按一下 [服務提供選擇性的特性**新增特性**] 按鈕，並設定必要的設定： 

    [![](homekit-images/simulator07.png "設定必要的設定")](homekit-images/simulator07.png#lightbox)
7. 重複上述步驟，以建立每一種虛擬的家用自動化裝置 HomeKit 支援的其中一個。

與某些範例虛擬 HomeKit 配件建立及設定，您現在可以取用，並控制這些裝置，從您的 Xamarin.iOS 應用程式。

## <a name="configuring-the-infoplist-file"></a>設定 Info.plist 檔案

IOS 10 的新功能 （和更新版本），開發人員必須將`NSHomeKitUsageDescription`重要應用程式的`Info.plist`檔案，並提供字串，宣告應用程式想要存取使用者的 HomeKit 資料庫的原因。 若要在使用者第一次執行應用程式，將會看到此字串：

[![](homekit-images/info01.png "HomeKit 權限 對話方塊")](homekit-images/info01.png#lightbox)

若要設定此機碼，執行下列作業：

1. 按兩下`Info.plist`檔案中**方案總管 中**以開啟它進行編輯。
2. 在畫面底部，切換至**來源**檢視。
3. 加入新**項目**至清單。
4. 從下拉式清單中，選取**隱私權-HomeKit 使用方式描述**: 

    [![](homekit-images/info02.png "選取隱私權-HomeKit 使用方式描述")](homekit-images/info02.png#lightbox)
5. 輸入應用程式想要存取使用者的 HomeKit 資料庫的原因的描述： 

    [![](homekit-images/info03.png "輸入的描述")](homekit-images/info03.png#lightbox)
6. 將變更儲存到檔案。

> [!IMPORTANT]
> 若未設定`NSHomeKitUsageDescription`中的索引鍵`Info.plist`檔案會導致應用程式_以無訊息方式失敗_（正在關閉系統在執行階段） 而不需要在 iOS 10 （或更新版本） 中執行時的錯誤。

## <a name="connecting-to-homekit"></a>連接到 HomeKit

若要與 HomeKit 通訊，您的 Xamarin.iOS 應用程式必須先具現化的執行個體`HMHomeManager`類別。 首頁 Manager 是 HomeKit 中央進入點，而且會負責提供一份可用的住家，更新和維護該清單，並傳回使用者的_主要首頁_。

`HMHome`物件包含提供首頁，包括任何房間、 群組或區域，它可能包含，以及任何已安裝的家庭自動化附屬應用程式的所有資訊。 在 HomeKit，至少一個會執行任何作業之前`HMHome`必須建立並指派為主要首頁。

您的應用程式會負責檢查主要首頁是否存在，以及建立和指派其中一個，如果沒有項目。

### <a name="adding-a-home-manager"></a>加入主 Manager

若要將 HomeKit 感知功能新增至 Xamarin.iOS 應用程式中，編輯**AppDelegate.cs**檔案加以編輯，並使其看起來如下：

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

第一次執行應用程式時，會要求使用者是否要允許它存取他們 HomeKit 的資訊：

[![](homekit-images/home01.png "如果他們想要允許它以存取其 HomeKit 資訊就會要求使用者")](homekit-images/home01.png#lightbox)

如果使用者回答**確定**，則應用程式將能夠使用其 HomeKit 配件否則不會與 HomeKit 的任何呼叫將會失敗並發生錯誤。

使用首頁中的管理員的地方，接下來應用程式必須查看是否已設定的主要首頁，和如果沒有，可讓使用者建立並指派使用者帳戶。

### <a name="accessing-the-primary-home"></a>存取主要首頁

如上所述，就必須建立和設定之前 HomeKit 出現，而且它是提供一個方法來建立及指派一個主要家，如果有一個使用者的應用程式的責任不存在主要首頁。

當您的應用程式第一次啟動，或從背景傳回時，它必須監視`DidUpdateHomes`事件的`HMHomeManager`類別，以檢查是否存在的主要首頁。 如果不存在的話，它應該提供建立一個使用者介面。

若要檢查主要首頁檢視控制器可以加入下列程式碼：

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

「 首頁 」 管理員 HomeKit，來進行連線時`DidUpdateHomes`會引發事件，任何現有的家庭皆要載入的家庭的管理員 的集合，也會被載入主要首頁，如果有的話。

### <a name="adding-a-primary-home"></a>新增主要首頁

如果`PrimaryHome`的屬性`HMHomeManager`是`null`之後`DidUpdateHomes`事件，您必須提供方法讓使用者建立和指派主要首頁，再繼續進行。

通常應用程式將會顯示用來命名新的位置，然後取得傳遞至主管理員來為主要首頁的安裝程式使用者的表單。 針對**HomeKitIntro**範例應用程式，強制回應檢視已在 IOS 設計工具中建立，而且由呼叫`AddHomeSegue`segue 從應用程式的主要介面。

它會提供使用者輸入的名稱的新家和按鈕以新增首頁的文字欄位。 當使用者點選**新增首頁**按鈕時，下列程式碼呼叫家用的管理員，以新增首頁：

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

`AddHome`方法會嘗試建立新的位置，並將它傳回至指定的回呼常式。 如果`error`屬性不是`null`、 發生錯誤，以及應該呈現給使用者。 最常見的錯誤是由非唯一的 home 名稱或家用的管理員無法與 HomeKit 通訊而造成的。

如果已成功建立首頁，您需要呼叫`UpdatePrimaryHome`方法，以將新的位置設定為主要首頁。 同樣地，如果`error`屬性不是`null`、 發生錯誤，以及應該呈現給使用者。

您還應該監視首頁經理`DidAddHome`和`DidRemoveHome`事件和更新所需的應用程式的使用者介面。

> [!IMPORTANT]
> `AlertView.PresentOKAlert`在上述範例程式碼中使用的方法是在處理 iOS 警示更容易 HomeKitIntro 應用程式中的協助程式類別。


## <a name="finding-new-accessories"></a>尋找新的附屬應用程式

一旦主要首頁已定義，或是從主要管理員載入，您的 Xamarin.iOS 應用程式可以呼叫`HMAccessoryBrowser`尋找任何新的家庭自動化附屬應用程式，並將它們新增至首頁。

呼叫`StartSearchingForNewAccessories`方法，以開始尋找新的附屬應用程式和`StopSearchingForNewAccessories`方法完成時。

> [!IMPORTANT]
> `StartSearchingForNewAccessories` 不能保留執行長的時間，因為它會造成負面影響電池壽命和 iOS 裝置的效能。 Apple 建議呼叫`StopSearchingForNewAccessories`之後的分鐘或唯一時，搜尋 [尋找] 附屬應用程式 UI 呈現給使用者。

`DidFindNewAccessory`事件時探索到新的附屬應用程式，並會將它們加入呼叫`DiscoveredAccessories`配件瀏覽器中的清單。

`DiscoveredAccessories`清單會包含集合`HMAccessory`物件會定義為 HomeKit 啟用家用自動化裝置和其可用的服務，例如號誌或車庫門控制項。

一旦已找到新的附屬應用程式，它應該呈現給使用者，因此它們可以在 選取它，並將它新增至首頁。 範例：

[![](homekit-images/accessory01.png "尋找新的附屬應用程式")](homekit-images/accessory01.png#lightbox)

呼叫`AddAccessory`方法，以將選取的附屬應用程式新增至家中的集合。 例如: 

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

如果`err`屬性不是`null`、 發生錯誤，以及應該呈現給使用者。 否則，會要求使用者輸入要新增的裝置的安裝程式碼：

[![](homekit-images/accessory02.png "輸入要新增的裝置的安裝程式碼")](homekit-images/accessory02.png#lightbox)

HomeKit 配件模擬器中可以找到這個數字之下**安裝程式碼**欄位：

[![](homekit-images/accessory03.png "HomeKit 配件模擬器中的 [設定程式碼] 欄位")](homekit-images/accessory03.png#lightbox)

針對實際 HomeKit 配件，設定程式碼是在裝置本身，在產品中或在 附屬應用程式的使用者手動在標籤上列印。

您應該監視配件的瀏覽器`DidRemoveNewAccessory`事件和更新使用者介面，可以從可用清單中移除附屬應用程式之後使用者已將它新增至他們常用的集合。

## <a name="working-with-accessories"></a>使用 附屬應用程式

一次主要家用和建立附屬應用程式已新增至它，您可以呈現一份 附屬應用程式 （並選擇性地室） 讓使用者使用。

`HMRoom`物件包含指定聊天室的所有資訊和它的任何附屬應用程式。 聊天室選擇性地分成一個或多個區域。 A`HMZone`包含指定的區域的所有資訊及其所有屬於該聊天室。

基於此範例中，我們將會保持簡化頗簡單和家中的 附屬應用程式直接處理而不是將它們組織到聊天室或區域。

`HMHome`物件包含一份指派附屬應用程式，可以呈現給使用者，以其`Accessories`屬性。 例如: 

[![](homekit-images/accessory04.png "範例附屬應用程式")](homekit-images/accessory04.png#lightbox)

在此表單中，使用者可以選取指定的附屬應用程式，並使用它所提供的服務。

## <a name="working-with-services"></a>使用服務

當使用者與指定的 HomeKit 啟用家用自動化裝置互動時，通常是透過它所提供的服務。 `Services`的屬性`HMAccessory`類別包含的集合`HMService`物件會定義服務的裝置提供。

服務是等號誌、 控溫器、 車庫門 openers、 交換器或鎖定。 某些裝置 （例如車庫門開啟器） 會提供一個以上的服務，例如光線，並且能夠開啟或關閉媒體櫃門。

除了指定的附屬應用程式提供的特定服務，每個附屬應用程式包含`Information Service`定義屬性，例如其名稱、 製造商、 型號及序號。

### <a name="accessory-service-types"></a>配件的服務類型

以下服務類型是可透過`HMServiceType`列舉：

- **AccessoryInformation** -提供指定的家用自動化裝置 （附屬應用程式） 的相關資訊。
- **AirQualitySensor** -定義空氣品質感應器。
- **電池**-定義附屬應用程式的電池的狀態。
- **CarbonDioxideSensor** -定義碳來感應器。
- **CarbonMonoxideSensor** -定義一氧化碳感應器。
- **ContactSensor** -定義連絡人的感應器 （例如正在開啟或關閉的視窗）。
- **門**-定義門狀態感應器 （例如開啟或關閉）。
- **風扇**-定義遠端的受控制的風扇。
- **GarageDoorOpener** -定義車庫門開啟工具。
- **HumiditySensor** -定義溼度感應器。
- **LeakSensor** -定義遺漏感應器 （如熱水器或要洗機器）。
- **燈泡**-定義的獨立燈號或屬於另一個的附屬應用程式 （例如車庫門開啟器） 的燈號。
- **LightSensor** -定義光感應器。
- **LockManagement** -定義服務，以管理自動化的媒體櫃門鎖定。
- **LockMechanism** -定義 （例如門鎖定） 的遠端控制的鎖定。
- **MotionSensor** -定義動作感應器。
- **OccupancySensor** -定義佔用量感應器。
- **輸出**-定義遠端控制的牆上電源插座。
- **SecuritySystem** -定義家中的安全性系統。
- **StatefulProgrammableSwitch** -定義維持在提供的狀態 （例如翻頁動畫的交換器） 一次觸發的可程式化交換器。
- **StatelessProgrammableSwitch** -定義的可程式化的交換器，之後觸發 （例如按鈕），傳回至其初始狀態。
- **SmokeSensor** -定義煙霧感應器。
- **切換**-定義 on/off 開關，像是牆上標準交換器。
- **溫度感應器**-定義溫度感應器。
- **控溫器**-定義智慧控溫器用來控制的 HVAC 系統。
- **視窗**-定義自動化的視窗棒棒糖，從遠端開啟或關閉。
- **WindowCovering** -定義遠端控制的視窗，其中涵蓋，例如隱蔽可以開啟或關閉。

### <a name="displaying-service-information"></a>顯示服務資訊

載入之後`HMAccessory`您可以查詢個別`HNService`物件，它提供，並向使用者顯示該資訊：

[![](homekit-images/accessory05.png "顯示服務資訊")](homekit-images/accessory05.png#lightbox)

您應該一律應檢查`Reachable`屬性`HMAccessory`然後再嘗試使用它。 無法連線到使用者不在範圍內，或如果它已拔除，可以是裝置的附屬應用程式。

一旦選取一項服務，使用者就可以檢視或修改一或多個要監視或控制給定的家用自動化裝置的該服務的特性。

<a name="Working-with-Characteristics" />

## <a name="working-with-characteristics"></a>使用特性

每個`HMService`物件可包含的集合`HMCharacteristic`可以提供的服務 （例如機門開啟或關閉） 狀態的相關資訊，或允許使用者調整 （例如設定燈光的色彩） 狀態的物件。

`HMCharacteristic` 不只提供資訊的特性和它的狀態，但也提供方法來處理透過狀態_特性的中繼資料_(`HMCharacteristisMetadata`)。 此中繼資料可以提供資訊顯示給使用者或讓他們修改狀態時很有用的屬性 （例如最小和最大值的範圍）。

`HMCharacteristicType`列舉提供一組特性的中繼資料值可定義或修改，如下所示：

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
 - 記錄檔
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

### <a name="working-with-a-characteristics-value"></a>使用的特性值

若要確保您應用程式具有指定特性的最新狀態，請呼叫`ReadValue`方法的`HMCharacteristic`類別。 如果`err`屬性不是`null`、 發生錯誤，可能會或可能不會向使用者顯示。

特性`Value`屬性包含與指定的特性的目前狀態`NSObject`，並因此無法處理與直接在C#。

若要讀取的值，下列協助程式類別已新增至**HomeKitIntro**範例應用程式：

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

`NSObjectConverter`每當應用程式需要讀取特性的目前狀態就會使用。 例如：

```csharp
var value = NSObjectConverter.ToFloat (characteristic.Value);
```

上述程式碼會將值轉換成`float`，然後在 Xamarin 中使用C#程式碼。

若要修改`HMCharacteristic`，呼叫其`WriteValue`方法，並將包裝在新的值`NSObject.FromObject`呼叫。 例如：

```csharp
Characteristic.WriteValue(NSObject.FromObject(value),(err) =>{
    // Was there an error?
    if (err!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Update Error",err.LocalizedDescription,Controller);
    }
});
```

如果`err`屬性不是`null`，已發生錯誤，而應該呈現給使用者。

### <a name="testing-characteristic-value-changes"></a>測試特性的值變更

使用時`HMCharacteristics`模擬的附屬應用程式，以及修改和`Value`HomeKit 配件模擬器內，您可以監視屬性。

具有**HomeKitIntro**真實的 iOS 裝置的硬體特性值的變更上執行的應用程式應該幾乎是立即看到 HomeKit 配件模擬器中。 例如，變更光線的 iOS 應用程式中的狀態：

[![](homekit-images/test01.png "變更光線的 iOS 應用程式中的狀態")](homekit-images/test01.png#lightbox)

應該變更燈光 HomeKit 配件模擬器中的狀態。 如果值不會變更，撰寫新的特性值時，請檢查錯誤訊息的狀態，並確定 附屬應用程式時仍然可以到達。

## <a name="advanced-homekit-features"></a>進階的 HomeKit 功能

這篇文章已涵蓋與 HomeKit 配件的 Xamarin.iOS 應用程式中使用所需的基本功能。 不過，有幾項進階的功能的 HomeKit 未涵蓋在本簡介：

- **聊天室**-啟用 HomeKit 配件可以選擇性地分成終端使用者的聊天室。 這可讓 HomeKit 存在的附屬應用程式，讓使用者了解及使用簡單的方式。 如需有關建立和維護聊天室的詳細資訊，請參閱 Apple [HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom)文件。
- **區域**-聊天室可以選擇性地組織成區域為終端使用者。 區域是指使用者可能會視為單一單位的房間內的集合。 例如: 樓上，Downstairs 或地下室。 同樣地，這可讓 HomeKit 呈現，並使用 附屬應用程式對使用者有意義的方式。 如需有關建立和維護區域的詳細資訊，請參閱 Apple [HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone)文件。
- **動作和動作設定**-動作修改配件服務特性，並可分組為設定。 動作集做為控制一群附屬應用程式，並協調其動作的指令碼。 比方說，「 監看電視 」 指令碼可能會關閉隱蔽、 變暗，並開啟電視和其音效的系統。 如需有關建立和維護動作和動作設定的詳細資訊，請參閱 Apple [HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction)並[HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet)文件。
- **觸發程序**-觸發程序可以啟動其中一個或多個動作時，設定一組指定的條件已符合。 比方說，開啟 portch 光線，並收到深色之外時，鎖定所有外部的門。 如需有關建立和維護觸發程序的詳細資訊，請參閱 Apple [HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger)文件。

由於這些功能會使用上述的相同技術，它們應該能輕鬆實作下列 apple [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)， [HomeKit 使用者介面指導方針](https://developer.apple.com/homekit/ui-guidelines/)和[HomeKit 架構參考](https://developer.apple.com/library/ios/home_kit_framework_ref)。

## <a name="homekit-app-review-guidelines"></a>HomeKit 應用程式審查指導方針

提交 HomeKit 之前啟用的 Xamarin.iOS 應用程式，itunes Connect 在 iTunes App Store 中的版本請確定您遵循 HomeKit 啟用應用程式的 Apple 的指導方針：

 - 應用程式的主要用途_必須_居家自動化，才使用 HomeKit 架構。
 - 應用程式的行銷文字必須通知使用者正在使用 HomeKit，而且必須提供隱私權原則。
 - 嚴格禁止收集使用者資訊，或使用 HomeKit 的廣告。

針對完整檢閱指導方針，請參閱 Apple [App Store 檢閱方針](https://developer.apple.com/app-store/review/guidelines/)。

## <a name="whats-new-in-ios-9"></a>新 iOS 9 功能

Apple 已進行下列變更和新功能 HomeKit 適用於 iOS 9:

- **維護現有的物件**-如果現有附屬應用程式已經過修改，首頁經理 (`HMHomeManager`) 會通知您已修改的特定項目。
- **持續性識別項**-所有相關的 HomeKit 類別現在包含`UniqueIdentifier`屬性來唯一識別指定的項目，跨 HomeKit 啟用應用程式 （或相同的應用程式的執行個體）。
- **使用者管理**-加入內建的檢視控制器中的主要使用者的首頁有 HomeKit 裝置的存取權的使用者提供使用者管理。
- **使用者功能**-HomeKit 使用者現在可以控制他們就能夠使用 HomeKit 中哪些函式的權限的一組與 HomeKit 啟用 附屬應用程式。 您的應用程式應該只會顯示目前的使用者相關功能。 比方說，只有系統管理員應該能夠維護其他使用者。
- **預先定義的場景**-四個常見的事件發生的平均 HomeKit 使用者已建立預先定義的場景：啟動、 保留、 傳回，請移至平台。 無法從首頁中刪除這些預先定義的場景。
- **場景和 Siri** -Siri 有更深入的支援，如場景中 iOS 9 和可以辨認 HomeKit 中定義任何場景的名稱。 使用者可以執行場景，只要談到 Siri 其名稱。
- **配件類別**-已新增至所有的附屬應用程式和可協助您識別要新增至首頁的附屬應用程式的類型或處理從應用程式內一組預先定義的類別。 這些新的類別可在附屬應用程式安裝期間。
- **Apple Watch 支援**-HomeKit 現已供 watchOS 和 Apple Watch 都能夠 HomeKit 正在監看式附近 iPhone 不啟用裝置的控制項。 為 watchOS HomeKit 支援下列功能：檢視、 住家控制 附屬應用程式，以及執行場景。
- **新增事件觸發程序類型**-除了支援 iOS 8，iOS 9 現在支援事件觸發程序根據附屬應用程式狀態 （例如感應器資料） 或地理位置中的計時器類型觸發程序。 事件觸發程序使用`NSPredicates`設定為其執行的條件。
- **遠端存取**-與遠端存取，使用者現在就可以控制其 HomeKit 遠離房子的遠端位置時，請啟用家用自動化附屬應用程式。 在 iOS 8 中已當才支援此使用者具有一個 Apple TV，在家中的第 3 層代。 在 iOS 9 中，則會消除這項限制，並透過 iCloud 與 HomeKit 配件通訊協定 (HAP) 支援遠端存取。
- **新的藍牙低功耗 (BLE) 能力**-HomeKit 現在支援更多配件的類型可透過藍牙低功耗 (BLE) 通訊協定進行通訊。 使用安全通道 HAP，HomeKit 配件可以公開其他藍牙配件透過 Wi-fi （如果它是超出藍芽範圍）。 在 iOS 9、 BLE 附屬應用程式會有完整支援通知和中繼資料。
- **新的附屬應用程式類別目錄**-Apple 在 iOS 9 中新增下列新的附屬應用程式類別目錄：窗簾、 機械化的門和 Windows、 警報系統、 感應器和可程式化的參數。

如 HomeKit iOS 9 中的新功能的相關詳細資訊，請參閱 Apple [HomeKit 索引](https://developer.apple.com/homekit/)並[What's New in HomeKit](https://developer.apple.com/videos/wwdc/2015/?id=210)視訊。

## <a name="summary"></a>總結

這篇文章介紹了 Apple 的 HomeKit 居家自動化架構。 它示範了如何安裝和設定使用 HomeKit 配件模擬器測試裝置，以及如何建立簡單的 Xamarin.iOS 應用程式，來探索、 通訊及控制使用 HomeKit 的家用自動化裝置。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [新功能 iOS 9.0 以上版本](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [HomeKit 使用者介面指導方針](https://developer.apple.com/homekit/ui-guidelines/)
- [HomeKit 架構參考](https://developer.apple.com/library/ios/home_kit_framework_ref)
