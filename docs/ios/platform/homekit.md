---
title: 在 Xamarin.iOS HomeKit
description: HomeKit 是 Apple 的架構控制擸裝置。 本文介紹 HomeKit，並涵蓋 HomeKit 附屬應用程式模擬器和撰寫簡單的 Xamarin.iOS 應用程式，這些附屬應用程式與互動中設定測試附屬應用程式。
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 0dfc6e9ba5098df66a72292d6c8b89ea1bbd1f97
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787457"
---
# <a name="homekit-in-xamarinios"></a>在 Xamarin.iOS HomeKit

_HomeKit 是 Apple 的架構控制擸裝置。本文介紹 HomeKit，並涵蓋 HomeKit 附屬應用程式模擬器和撰寫簡單的 Xamarin.iOS 應用程式，這些附屬應用程式與互動中設定測試附屬應用程式。_

[![](homekit-images/accessory01.png "範例 HomeKit 啟用應用程式")](homekit-images/accessory01.png#lightbox)

Apple iOS 8 中引進 HomeKit，來順暢地將來自各種不同廠商的多個擸裝置整合到單一且一致的單位。 升級，將用來探索一般通訊協定、 設定及控制擸裝置 HomeKit 允許裝置從非相關的廠商，以搭配使用，而不需個別供應商需要協調工作。

您可以使用 HomeKit，建立利用廠商提供的應用程式開發介面或應用程式不會控制任何 HomeKit 啟用裝置的 Xamarin.iOS 應用程式。 使用 HomeKit，您可以執行下列動作：

- 探索新 HomeKit 啟用擸裝置，並將其加入至資料庫會保存在所有使用者的 iOS 裝置。
- 設定、 設定、 顯示及控制任何裝置在 HomeKit_首頁設定資料庫_。
- 與任何預先設定的 HomeKit 裝置進行通訊，以及命令執行個別的動作，或是正常運作，例如開啟的所有位在廚房光線。

除了首頁設定資料庫中啟用 HomeKit 應用程式提供的裝置，HomeKit 存取 Siri 語音命令。 指定適當設定的 HomeKit 安裝程式，使用者可以發出語音命令例如"Siri，開啟的燈號在客廳。 」

<a name="Home-Configuration-Database" />

## <a name="the-home-configuration-database"></a>主要的組態資料庫

HomeKit 會組織成首頁集合某一特定位置中的所有自動化裝置。 此集合可讓使用者在其擸裝置分組具有有意義、 人類看得懂的標籤排列邏輯單位。

首頁集合會儲存在首頁設定資料庫將會自動備份和同步處理在所有使用者的 iOS 裝置。 HomeKit 首頁設定資料庫使用提供的以下類別：

- `HMHome` -這是在單一實體位置 （例如保存所有資訊和所有擸裝置設定的最上層容器 單一家族的居住地)。 使用者可能有一個以上的居住地，其主要首頁等假期房子。 或者，可能會有不同 「 裝載 」 上相同的屬性，例如主要的房屋和來賓房屋機庫透過。 無論如何，至少一個`HMHome`物件_必須_設定，而且您可以輸入任何其他 HomeKit 資訊之前，儲存。
- `HMRoom` -一些選擇性的`HMRoom`可讓使用者定義特定房間內家用 (`HMHome`) 例如： 廚房、 洗手間、 機庫或客廳。 使用者可以群組所有擸中的裝置到他們家中的特定位置`HMRoom`而且作它們做為一個單位。 例如，要求 Siri 關閉機庫燈號。
- `HMAccessory` -這表示個別、 實體 HomeKit 啟用自動化的裝置已安裝在使用者的 （例如智慧型的 thermostat)。 每個`HMAccessory`指派給`HMRoom`。 如果使用者尚未設定任何房間，HomeKit 會指派至特殊的預設聊天室的附屬應用程式。
- `HMService` -代表所提供的服務指定`HMAccessory`，例如光線或其色彩 （如果支援色彩變更） 的開啟/關閉狀態。 每個`HMAccessory`可以有一個以上的服務，例如機庫機門開啟工具同時也包含光線。 此外，給定`HMAccessory`可能服務，例如韌體更新外部使用者控制項。
- `HMZone` -可讓使用者群組的集合`HMRoom`成邏輯的區域，例如樓上、 Downstairs 或地下室物件。 雖然是選擇性的這可以讓像是詢問 Siri 互動開啟所有燈號 downstairs 關閉。

<a name="Provisioning-a-HomeKit-App" />

## <a name="provisioning-a-homekit-app"></a>佈建 HomeKit 應用程式

因為 HomeKit 所加諸的安全性需求，使用 HomeKit framework Xamarin.iOS 應用程式必須正確設定在這兩個 Apple 開發人員入口網站和 Xamarin.iOS 專案檔中。

請執行下列動作：

1. 登入[Apple 開發人員入口網站](http://developer.apple.com)。
2. 按一下**憑證、 識別項與設定檔**。
3. 如果您尚未這樣做，請按一下 上**識別碼**並建立您的應用程式的識別碼 (例如`com.company.appname`)，否則請編輯您現有的識別碼。
4. 請確認**HomeKit**已核取指定之識別碼的服務： 

    [![](homekit-images/provision01.png "啟用指定之識別碼的 HomeKit 服務")](homekit-images/provision01.png#lightbox)
5. 儲存您的變更。
4. 按一下**佈建的設定檔** > **開發**並建立新的開發，佈建您的應用程式設定檔： 

    [![](homekit-images/provision02.png "建立新的開發佈建設定檔的應用程式")](homekit-images/provision02.png#lightbox)
5. 請下載並安裝新的佈建設定檔或使用 Xcode 下載及安裝設定檔。
6. 編輯您 Xamarin.iOS 專案選項，並確保您使用您剛才建立的佈建設定檔： 

    [![](homekit-images/provision03.png "選取剛才建立的佈建設定檔")](homekit-images/provision03.png#lightbox)
7. 接著，編輯您**Info.plist**檔案，並確定您使用的用來建立佈建設定檔的應用程式識別碼： 

    [![](homekit-images/provision04.png "設定應用程式識別碼 ")](homekit-images/provision04.png#lightbox)
8. 最後，編輯您**Entitlements.plist**檔案，並確定**HomeKit**已選取權限： 

    [![](homekit-images/provision05.png "啟用 HomeKit 權利")](homekit-images/provision05.png#lightbox)
9. 儲存所有檔案的變更。

這些設定，應用程式現在已準備好存取 HomeKit 架構應用程式開發介面。 如需佈建的詳細資訊，請參閱我們[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)和[佈建您的應用程式](~/ios/get-started/installation/device-provisioning/index.md)輔助線。

> [!IMPORTANT]
> 測試 HomeKit 啟用應用程式開發需要正確佈建真正的 iOS 裝置。 無法從 iOS 模擬器中測試 HomeKit。

## <a name="the-homekit-accessory-simulator"></a>HomeKit 附屬應用程式模擬器

若要提供一個方式來測試所有可能的擸裝置和服務，而不需要實體裝置，建立 Apple _HomeKit 附屬應用程式模擬器_。 您可以使用此模擬器，來安裝和設定虛擬 HomeKit 裝置項目。

### <a name="installing-the-simulator"></a>安裝模擬器

Apple HomeKit 附屬應用程式模擬器會隨著提供個別下載 Xcode，因此您必須安裝才能繼續。

請執行下列動作：

1. 在網頁瀏覽器中瀏覽[Apple 開發人員下載](https://developer.apple.com/download/more/?name=for%20Xcode)
2. 下載**Xcode xxx 的其他工具**（xxx 是您已安裝 Xcode 的版本）： 

    [![](homekit-images/simulator01.png "下載 Xcode 的其他工具")](homekit-images/simulator01.png#lightbox)
3. 開啟磁碟映像和安裝的工具，在您**應用程式**目錄。

與安裝 HomeKit 附屬應用程式的模擬器，可以建立虛擬附屬應用程式進行測試。

### <a name="creating-virtual-accessories"></a>建立虛擬附屬應用程式

若要啟動 HomeKit 附屬應用程式模擬器，並建立一些虛擬附屬應用程式，執行下列作業：

1. 從應用程式 資料夾中，啟動 HomeKit 附屬應用程式模擬器： 

    [![](homekit-images/simulator02.png "HomeKit 附屬應用程式模擬器")](homekit-images/simulator02.png#lightbox)
2. 按一下**+** 按鈕，然後選取**新附屬應用程式...**: 

    [![](homekit-images/simulator03.png "加入新的附屬應用程式")](homekit-images/simulator03.png#lightbox)
3. 新的附屬應用程式的相關資訊，然後按一下**完成**按鈕： 

    [![](homekit-images/simulator04.png "填寫新附屬應用程式的相關資訊")](homekit-images/simulator04.png#lightbox)
4. 按一下**新增服務...** 按鈕，然後從下拉式清單中選取服務類型： 

    [![](homekit-images/simulator05.png "從下拉式清單中選取服務類型")](homekit-images/simulator05.png#lightbox)
5. 提供**名稱**服務並按一下**完成**按鈕： 

    [![](homekit-images/simulator06.png "輸入服務的名稱")](homekit-images/simulator06.png#lightbox)
6. 您也可以按一下 服務提供選擇性的特性**新增特性**按鈕和設定必要的設定： 

    [![](homekit-images/simulator07.png "設定必要的設定")](homekit-images/simulator07.png#lightbox)
7. 重複上述步驟，以建立每個虛擬擸裝置 HomeKit 支援類型的其中一個。

使用一些範例虛擬 HomeKit 附屬應用程式建立及設定，現在可以使用，並從 Xamarin.iOS 應用程式中控制這些裝置。

## <a name="configuring-the-infoplist-file"></a>設定 Info.plist 檔案

IOS 10 的新功能 （和更高），開發人員必須加入`NSHomeKitUsageDescription`應用程式的金鑰`Info.plist`檔案，並提供字串，宣告應用程式想要存取使用者的 HomeKit 資料庫的原因。 這個字串會顯示在使用者第一次執行應用程式：

[![](homekit-images/info01.png "HomeKit 權限對話方塊")](homekit-images/info01.png#lightbox)

若要設定此機碼，執行下列作業：

1. 按兩下`Info.plist`檔案**方案總管 中**開啟進行編輯。
2. 在畫面底部，切換至**來源**檢視。
3. 加入新**項目**至清單。
4. 從下拉式清單中，選取**隱私權-HomeKit 使用方式描述**: 

    [![](homekit-images/info02.png "選取隱私權-HomeKit 使用方式描述")](homekit-images/info02.png#lightbox)
5. 輸入應用程式想要存取使用者的 HomeKit 資料庫的原因的描述： 

    [![](homekit-images/info03.png "輸入的描述")](homekit-images/info03.png#lightbox)
6. 將變更儲存到檔案。

> [!IMPORTANT]
> 無法設定`NSHomeKitUsageDescription`中的索引鍵`Info.plist`檔案將會導致應用程式_以無訊息模式失敗_（正在關閉由系統在執行階段） 不會發生錯誤時 iOS （大於或等於 10） 中執行。

## <a name="connecting-to-homekit"></a>連接到 HomeKit

若要與 HomeKit 通訊，Xamarin.iOS 應用程式必須先具現化的執行個體`HMHomeManager`類別。 首頁管理員是 HomeKit 中央的進入點，並負責提供一份可用的家庭更新和維護該清單，並傳回使用者的_主要首頁_。

`HMHome`物件包含的所有授與家用包括任何房間、 群組或區域，它可能包含，以及任何擸附屬應用程式已安裝的相關資訊。 您可以按照 HomeKit，至少一個執行任何作業之前`HMHome`必須建立並指派為主要首頁。

您的應用程式負責檢查主要首頁是否存在以及建立及指派其中一個，如果不存在。

### <a name="adding-a-home-manager"></a>加入主管理員

若要新增 HomeKit 感知 Xamarin.iOS 應用程式，請編輯**d**檔案進行編輯，並使其看起來如下：

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

第一次執行應用程式時，使用者將會要求您是否要允許使用它來存取其 HomeKit 資訊：

[![](homekit-images/home01.png "如果要允許使用它來存取其 HomeKit 資訊將會要求使用者")](homekit-images/home01.png#lightbox)

如果使用者回答**確定**，則應用程式將能夠使用其 HomeKit 附屬應用程式否則不會和 HomeKit 的任何呼叫將會失敗，發生錯誤。

首頁中的管理員的地方，接著應用程式將必須如果已設定主要首頁，請參閱並如果不是，為提供的方式來建立和指派一個使用者。

### <a name="accessing-the-primary-home"></a>存取主要首頁

如前所述，就必須建立和設定後再 HomeKit 可用，而且它是提供一種方式來建立和指派主要家用如果一個使用者的應用程式的責任不存在主要首頁。

當您的應用程式第一次啟動或從背景傳回時，它需要監視`DidUpdateHomes`事件`HMHomeManager`類別來檢查是否存在主要首頁。 如果不存在的話，它應該提供建立一個使用者介面。

下列程式碼可以加入要檢查主要首頁檢視控制器：

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

如果首頁管理員連線到 HomeKit，`DidUpdateHomes`會引發事件，任何現有的家庭將載入至家庭主管的集合，也會被載入主要首頁，如果有的話。

### <a name="adding-a-primary-home"></a>加入主要首頁

如果`PrimaryHome`屬性`HMHomeManager`是`null`之後`DidUpdateHomes`事件，您必須提供方法，讓使用者建立和指派主要首頁，然後再繼續。

通常應用程式，會提供使用者名稱新首頁，然後取得傳遞給要主要為主的安裝程式的主要管理員。 如**HomeKitIntro** IOS 設計工具中建立範例應用程式，強制回應檢視並呼叫`AddHomeSegue`話題從應用程式的主要介面。

它提供文字欄位，讓使用者輸入新的首頁和加入首頁按鈕的名稱。 當使用者點選**加入首頁** 按鈕，下列程式碼會呼叫加入首頁家用管理員：

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

`AddHome`方法會嘗試建立新的首頁，並將其傳回至指定的回呼常式。 如果`error`屬性不是`null`、 發生錯誤，以及應該呈現給使用者。 最常見的錯誤被因非唯一的 home 名稱或主要的管理員無法與 HomeKit 通訊。

如果已成功建立首頁，您需要呼叫`UpdatePrimaryHome`方法，以做為主要首頁設定新的首頁。 同樣地，如果`error`屬性不是`null`、 發生錯誤，以及應該呈現給使用者。

您還應該監視首頁主管`DidAddHome`和`DidRemoveHome`事件和更新所需的應用程式的使用者介面。

> [!IMPORTANT]
> `AlertView.PresentOKAlert`上述範例程式碼中使用的方法是讓使用 iOS 警示更容易在 HomeKitIntro 應用程式中的協助程式類別。


## <a name="finding-new-accessories"></a>尋找新的附屬應用程式

一旦主要首頁已定義，或是從主要管理員載入，Xamarin.iOS 應用程式可以呼叫`HMAccessoryBrowser`尋找任何新擸附屬應用程式，並將其加入至首頁。

呼叫`StartSearchingForNewAccessories`開始搜尋新附屬應用程式的方法和`StopSearchingForNewAccessories`方法完成時。

> [!IMPORTANT]
> `StartSearchingForNewAccessories` 不能保留長時間執行，因為它會產生負面影響電池壽命和 iOS 裝置的效能。 Apple 建議呼叫`StopSearchingForNewAccessories`之後的分鐘或唯一的搜尋時找到附屬應用程式 UI 呈現給使用者。

`DidFindNewAccessory`會呼叫事件，當發現新的附屬應用程式，並會加入至`DiscoveredAccessories`附屬應用程式瀏覽器清單中的。

`DiscoveredAccessories`清單會包含一系列`HMAccessory`擸裝置和可用的服務，例如號誌或機庫門控制項，啟用定義 HomeKit 授與的物件。

一旦找到新的附屬應用程式，應該呈現給使用者，因此它們選取它，將它加入至首頁。 範例：

[![](homekit-images/accessory01.png "尋找新的附屬應用程式")](homekit-images/accessory01.png#lightbox)

呼叫`AddAccessory`方法，將選取的附屬應用程式加入至家中的集合。 例如: 

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

如果`err`屬性不是`null`、 發生錯誤，以及應該呈現給使用者。 否則，將需要使用者輸入來新增裝置的安裝程式碼：

[![](homekit-images/accessory02.png "輸入要新增裝置的安裝程式碼")](homekit-images/accessory02.png#lightbox)

HomeKit 附屬應用程式模擬器中可以找到這個數字下**設定程式碼**欄位：

[![](homekit-images/accessory03.png "安裝程式碼中的欄位 HomeKit 附屬應用程式模擬器")](homekit-images/accessory03.png#lightbox)

實際 HomeKit 附屬應用程式，安裝程式碼可能是裝置本身，在 [產品] 方塊或附屬應用程式的使用者手冊中的標籤上列印。

您應該監視配件的瀏覽器`DidRemoveNewAccessory`事件以及更新使用者介面，從可用的清單移除附屬應用程式，一旦使用者已將它加入至其常用的集合。

## <a name="working-with-accessories"></a>使用 附屬應用程式

一次主要家用和建立附屬應用程式已新增至它，您可以使用使用者呈現的附屬應用程式 （及選擇性地室） 清單。

`HMRoom`物件包含有關指定聊天室的所有資訊和任何屬於該附屬應用程式。 聊天室可以選擇性地組織成一個或多個區域。 A`HMZone`包含指定的區域的所有資訊和所有屬於該房間。

為了此範例中，我們將會保留項目簡單和工作與首頁的 附屬應用程式直接管理，而不是將它們組成房間或區域。

`HMHome`物件包含一份指派任一附屬應用程式中的使用者可以看到其`Accessories`屬性。 例如: 

[![](homekit-images/accessory04.png "範例附屬應用程式")](homekit-images/accessory04.png#lightbox)

在此表單中，使用者可以選取指定的附屬應用程式，並使用它所提供的服務。

## <a name="working-with-services"></a>使用服務

當使用者互動具有指定 HomeKit 啟用的擸裝置時，通常是透過它所提供的服務。 `Services`屬性`HMAccessory`類別包含的集合`HMService`物件，以定義服務的裝置提供。

服務是等號誌、 thermostats、 機庫門 openers、 交換器或鎖定。 某些裝置 （例如機庫機門開啟工具） 會提供一個以上的服務，例如光線，並且能夠開啟或關閉媒體櫃門。

除了指定的附屬應用程式提供的特定服務，每個附屬應用程式包含`Information Service`，定義屬性，例如其名稱、 製造商、 型號和序號。

### <a name="accessory-service-types"></a>配件的服務類型

下列服務類型是可透過使用`HMServiceType`列舉：

- **AccessoryInformation** -提供給定的擸裝置 （附屬應用程式） 的相關資訊。
- **AirQualitySensor** -定義空中品質感應器。
- **電池**-定義附屬應用程式的電池的狀態。
- **CarbonDioxideSensor** -定義碳來感應器。
- **CarbonMonoxideSensor** -定義一氧化碳感應器。
- **ContactSensor** -定義連絡人的感應器 （例如開啟或關閉視窗）。
- **門**-定義門狀態感應器 （例如開啟或關閉）。
- **風扇**-定義遠端控制的風扇。
- **GarageDoorOpener** -定義機庫機門開啟工具。
- **HumiditySensor** -定義溼度感應器。
- **LeakSensor** -定義遺漏感應器 （類似熱水器或洗好機器）。
- **LightBulb** -定義的獨立燈號或屬於另一個附屬應用程式 （例如機庫機門開啟工具） 的燈號。
- **LightSensor** -定義光感應器。
- **LockManagement** -定義管理自動化的媒體櫃門鎖定的服務。
- **LockMechanism** -定義 （例如媒體櫃門鎖定） 的遠端控制的鎖定。
- **MotionSensor** -定義動作感應器。
- **OccupancySensor** -定義佔有感應器。
- **插座**-定義遠端控制牆上插座。
- **SecuritySystem** -定義家中的安全性系統。
- **StatefulProgrammableSwitch** -定義維持在給定狀態 （例如翻轉的交換器） 一次觸發的可程式化交換器。
- **StatelessProgrammableSwitch** -定義其初始狀態之後觸發 （例如按鈕） 傳回的可程式化交換器。
- **SmokeSensor** -定義煙霧感應器。
- **切換**-定義像牆上標準交換器 on/off 開關。
- **溫度感應器**-定義溫度感應器。
- **Thermostat** -定義用來控制 HVAC 系統智慧 thermostat。
- **視窗**-定義要從遠端開啟或關閉細長自動化的視窗。
- **WindowCovering** -定義涵蓋，例如隱蔽可以開啟或關閉遠端控制的視窗。

### <a name="displaying-service-information"></a>顯示服務資訊

載入之後`HMAccessory`您可以查詢個別`HNService`物件，它提供，並向使用者顯示該資訊：

[![](homekit-images/accessory05.png "顯示服務資訊")](homekit-images/accessory05.png#lightbox)

您應該永遠應該檢查`Reachable`屬性`HMAccessory`再嘗試使用它。 無法連線到使用者不在範圍內，或如果已經拔除，可以是裝置的附屬應用程式。

一旦選取服務後，使用者就可以檢視或修改的監視或控制給定的擸裝置該服務的一個或多個特性。

<a name="Working-with-Characteristics" />

## <a name="working-with-characteristics"></a>使用特性

每個`HMService`物件可以包含的集合`HMCharacteristic`物件，可提供有關 （例如開啟或關閉媒體櫃門） 服務的狀態資訊或讓使用者能夠調整的狀態 （例如設定光線色彩）。

`HMCharacteristic` 不只提供資訊特性以及其狀態，但也會提供方法，以處理透過狀態_特性中繼資料_(`HMCharacteristisMetadata`)。 此中繼資料可以提供資訊顯示給使用者或讓它們能夠修改狀態時很有用的屬性 （例如最小和最大值的範圍）。

`HMCharacteristicType`列舉提供一組特性中繼資料值可定義或修改，如下所示：

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

若要確保您應用程式具有指定特性的最新狀態，請呼叫`ReadValue`方法`HMCharacteristic`類別。 如果`err`屬性不是`null`、 發生錯誤，可能或可能不會提供給使用者。

特點`Value`屬性包含目前狀態的給定特性做為`NSObject`，因此無法操作與直接在 C# 和。

若要讀取的值，下列 helper 類別已加入至**HomeKitIntro**範例應用程式：

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

`NSObjectConverter`應用程式需要讀取特性的目前狀態時，都會使用。 例如：

```csharp
var value = NSObjectConverter.ToFloat (characteristic.Value);
```

上述列將轉換成值`float`，然後用 Xamarin C# 程式碼。

若要修改`HMCharacteristic`，呼叫其`WriteValue`方法，並將包裝中的新值`NSObject.FromObject`呼叫。 例如：

```csharp
Characteristic.WriteValue(NSObject.FromObject(value),(err) =>{
    // Was there an error?
    if (err!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Update Error",err.LocalizedDescription,Controller);
    }
});
```

如果`err`屬性不是`null`，發生錯誤，並應該呈現給使用者。

### <a name="testing-characteristic-value-changes"></a>測試特性值的變更

當使用`HMCharacteristics`模擬附屬應用程式、 修改和`Value`屬性可同時監視 HomeKit 附屬應用程式的模擬器內。

與**HomeKitIntro**真實的 iOS 裝置的硬體特性值的變更上執行的應用程式應該幾乎立即看到 HomeKit 附屬應用程式模擬器中。 例如，變更 iOS 應用程式中的燈號的狀態：

[![](homekit-images/test01.png "IOS 應用程式中的燈號狀態變更")](homekit-images/test01.png#lightbox)

應該變更的淺 HomeKit 附屬應用程式模擬器中的狀態。 如果值不會變更，撰寫新的特性值時，請檢查錯誤訊息的狀態，並確定 附屬應用程式時仍可連線。

## <a name="advanced-homekit-features"></a>進階的 HomeKit 功能

這篇文章已涵蓋使用 HomeKit Xamarin.iOS 應用程式中的附屬應用程式所需的基本功能。 不過，有幾項進階的功能的 HomeKit 未涵蓋在本簡介：

- **聊天室**-啟用 HomeKit 附屬應用程式可以選擇性地組織到聊天室終端使用者。 這可讓 HomeKit 存在附屬應用程式，讓使用者了解及使用簡單的方式。 如需有關建立和維護聊天室的詳細資訊，請參閱 Apple [HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom)文件。
- **區域**-聊天室可以選擇性地組織成區域終端使用者。 區域是指使用者可能會視為單一單位的聊天室的集合。 例如： 樓上、 Downstairs 或地下室。 同樣地，這可讓 HomeKit 呈現，並使用 附屬應用程式中對終端使用者有意義的方式。 如需有關建立和維護區域的詳細資訊，請參閱 Apple [HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone)文件。
- **動作和動作設定**-動作修改配件服務特性，並可分組為設定。 動作集做為控制一群附屬應用程式和協調其動作的指令碼。 比方說，「 觀賞電視 」 指令碼可能會關閉隱蔽、 dim 號誌燈和電視和其音效系統上。 如需有關建立及維護動作和動作設定的詳細資訊，請參閱 Apple [HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction)和[HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet)文件。
- **觸發程序**-觸發程序可以啟動其中一個或多個動作時，設定一組指定的條件都符合。 比方說，開啟 portch 淺並鎖定所有外部門，當它取得深色之外。 如需有關建立和維護觸發程序的詳細資訊，請參閱 Apple [HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger)文件。

由於這些功能使用以上所顯示的相同技巧，它們應該容易實作下列 apple [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)， [HomeKit 使用者介面指導方針](https://developer.apple.com/homekit/ui-guidelines/)和[HomeKit Framework 參考](https://developer.apple.com/library/ios/home_kit_framework_ref)。

## <a name="homekit-app-review-guidelines"></a>HomeKit 應用程式檢閱指導方針

然後再提交 HomeKit iTunes Connect iTunes App Store 中發行的啟用的 Xamarin.iOS 應用程式會確保您遵循 HomeKit 啟用應用程式的 Apple 的指導方針：

 - 應用程式的主要目的_必須_擸，才使用 HomeKit 架構。
 - 應用程式的行銷文字必須通知使用者正在使用 HomeKit 他們必須提供隱私權原則。
 - 收集使用者資訊或廣告使用 HomeKit 嚴格禁止。

針對完整檢閱指導方針，請參閱 Apple[應用程式存放區檢閱指引](https://developer.apple.com/app-store/review/guidelines/)。

## <a name="whats-new-in-ios-9"></a>功能在 iOS 9 中的新功能

Apple 已進行下列變更和新增項目 HomeKit ios 9︰

- **維護現有的物件**-如果現有的附屬應用程式已經過修改，首頁管理員 (`HMHomeManager`) 會通知您已修改的特定項目。
- **持續性識別項**-所有相關的 HomeKit 類別現在包含`UniqueIdentifier`HomeKit 唯一識別指定項目的屬性會啟用應用程式 （或相同的應用程式的執行個體）。
- **使用者管理**-加入以存取 HomeKit 裝置之主要使用者在家中的使用者提供使用者管理的內建檢視控制站。
- **使用者功能**-和 HomeKit 使用者現在有一組權限，以控制哪些函式，就可以用於 HomeKit HomeKit 啟用附屬應用程式。 您的應用程式應僅會顯示目前的使用者相關的功能。 例如，只有系統管理員應該能夠維護其他使用者。
- **預先定義的場景**-四個常見的事件發生的平均 HomeKit 使用者已建立預先定義的運作原理： 開始、 保留、 傳回，請移至平台。 無法從家裡刪除這些預先定義的場景。
- **場景和 Siri** -Siri 有更深入的支援，如場景中 iOS 9 和可以辨認 HomeKit 中定義任何場景的名稱。 使用者可以執行場景只需說 Siri 其名稱。
- **附屬類別**-已加入至所有的附屬應用程式和可協助識別的附屬應用程式新增至家用類型或將其應用程式內有效執行的從一組預先定義的類別目錄。 這些新的類別目錄可在附屬應用程式安裝期間。
- **Apple Watch 支援**-HomeKit 現已可供 watchOS 和 Apple Watch 將能夠的控制 HomeKit 不 iPhone 正在監看式附近的情況下啟用裝置。 WatchOS 的 HomeKit 支援下列功能： 檢視家庭、 控制附屬應用程式和執行場景。
- **新增事件觸發程序類型**-除了支援 iOS 8、 iOS 9 現在支援事件觸發程序根據附屬應用程式狀態 （例如感應器資料） 或地理位置中的計時器型別觸發程序。 事件觸發程序使用`NSPredicates`設定其執行的條件。
- **遠端存取**-與遠端存取，使用者現在就可以控制其 HomeKit 啟用首頁自動化附屬應用程式，當它們不在遠端位置的房子。 在 iOS 8 這已才支援使用者具有一個 Apple TV 在家中的第 3 個層代。 在 iOS 9，則會消除這項限制，並透過 icloud 的功能和 HomeKit 附屬應用程式通訊協定 (HAP) 支援遠端存取。
- **新的藍芽低能源 (B) 能力**-HomeKit 現在支援更多的配件類型可透過藍牙低能源 (B) 通訊協定進行通訊。 使用安全通道 HAP，HomeKit 附屬應用程式可以公開其他藍芽附屬應用程式透過 Wi-fi （如果它是超出藍芽範圍）。 在 iOS 9，b 附屬應用程式會有完整支援通知與中繼資料。
- **新的附屬應用程式類別目錄**-Apple iOS 9 中新增下列新的附屬應用程式類別目錄： 窗簾、 電動門和 Windows、 警報系統、 感應器和可程式化的參數。

在 iOS 9 HomeKit 的新功能的相關詳細資訊，請參閱 Apple [HomeKit 索引](https://developer.apple.com/homekit/)和[What's New in HomeKit](https://developer.apple.com/videos/wwdc/2015/?id=210)視訊。

## <a name="summary"></a>總結

這篇文章已導入 Apple HomeKit 擸架構。 說明如何安裝及設定使用 HomeKit 附屬應用程式模擬器測試裝置以及如何建立簡單的 Xamarin.iOS 應用程式來探索、 溝通及控制使用 HomeKit 擸裝置。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [新功能 iOS 9.0 以上版本](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [HomeKit 使用者介面指導方針](https://developer.apple.com/homekit/ui-guidelines/)
- [HomeKit Framework 參考](https://developer.apple.com/library/ios/home_kit_framework_ref)
