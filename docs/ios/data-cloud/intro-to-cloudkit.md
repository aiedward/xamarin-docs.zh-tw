---
title: 在 Xamarin 中 CloudKit
description: 本檔說明如何在 Xamarin 中使用 CloudKit。 其中提供 CloudKit 的總覽，並討論如何啟用它、CloudKit 便利 API、擴充性、使用者帳戶，以及開發和生產環境。
ms.prod: xamarin
ms.assetid: 66B207F2-FAA0-4551-B43B-3DB9F620C397
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/11/2016
ms.openlocfilehash: 3ff868ed10d59a7c1026a222f314a1166a8de45e
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86930191"
---
# <a name="cloudkit-in-xamarinios"></a>在 Xamarin 中 CloudKit

CloudKit 架構可簡化存取 iCloud 之應用程式的開發。 這包括抓取應用程式資料和資產許可權，以及能夠安全地儲存應用程式資訊。 此套件可讓使用者透過其 iCloud 識別碼存取應用程式，而不需要共用個人資訊，以提供匿名層。

開發人員可以專注于其用戶端應用程式，讓 iCloud 不必撰寫伺服器端應用程式邏輯。 CloudKit 提供驗證、私用和公用資料庫，以及結構化資料和資產儲存體服務。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)協助開發人員適當地處理歐盟一般資料保護規定 (GDPR)。

## <a name="requirements"></a>需求

需要下列專案，才能完成本文中顯示的步驟：

- **Xcode 和 IOS SDK** – Apple 的 Xcode 和 Ios 8 api 必須在開發人員的電腦上安裝及設定。
- **Visual Studio for Mac** –應該在使用者裝置上安裝和設定最新版本的 Visual Studio for Mac。
- **ios 8 裝置**–執行最新版本 ios 8 進行測試的 ios 裝置。

## <a name="what-is-cloudkit"></a>什麼是 CloudKit？

CloudKit 是將 iCloud 伺服器的存取權授與開發人員的方式。 它提供 iCloud 磁片磁碟機和 iCloud 相片 Library 的基礎。 MacOS 和 iOS 裝置都支援 CloudKit。

[![MacOS 和 iOS 裝置上的 CloudKit 支援方式](intro-to-cloudkit-images/image1.png)](intro-to-cloudkit-images/image1.png#lightbox)

CloudKit 會使用 iCloud 帳戶基礎結構。 如果有使用者登入裝置上的 iCloud 帳戶，CloudKit 將會使用其識別碼來識別使用者。 如果沒有可用的帳戶，則會提供有限的唯讀存取權。

CloudKit 支援公用和私用資料庫的概念。 公用資料庫提供使用者可存取之所有資料的「湯品」。 私用資料庫的目的是要儲存系結至特定使用者的私用資料。

CloudKit 同時支援結構化和大量資料。 它可以順暢地處理大型檔案傳輸。 CloudKit 會負責在背景中有效率地將大型檔案傳輸到 iCloud 伺服器，並讓開發人員專注于其他工作。

> [!NOTE]
> 請務必注意，CloudKit 是一種_傳輸技術_。 它不會提供任何持續性;它只會讓應用程式有效率地從伺服器傳送和接收資訊。

在撰寫本文時，Apple 一開始會提供免費的 CloudKit，並具有高限制的頻寬和儲存容量。 對於具有大型使用者群的較大型專案或應用程式，Apple 已提示您將提供價格實惠的定價比例。

## <a name="enabling-cloudkit-in-a-xamarin-application"></a>在 Xamarin 應用程式中啟用 CloudKit

在 Xamarin 應用程式可以利用 CloudKit 架構之前，必須正確地布建應用程式，如[使用功能](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)和[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南中所述。

若要存取 CloudKit， **plist**檔案必須包含 [**啟用 iCloud**]、[索引**鍵-值] 儲存體**和 [ **CloudKit** ] 許可權。

### <a name="sample-app"></a>範例應用程式

[CloudKitAtlas 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-cloudkitatlas)會示範如何搭配使用 CloudKit 與 Xamarin。 下列步驟顯示如何設定範例–除了 CloudKit 以外的需求之外，還需要額外的設定：

1. 在 Visual Studio for Mac 或 Visual Studio 中開啟專案。
2. 在**方案總管**中，開啟**plist**檔案，並確定套件組合**識別碼**符合在布建設定中建立的 [**應用程式**識別碼] 中所定義的套件組合。
3. 向下**plist**檔案的底部，然後選取 [**啟用背景模式**]、[**位置更新**] 和 [**遠端通知**]。
4. 以滑鼠右鍵按一下方案中的 iOS 專案，然後選取 [**選項**]。
5. 選取 [IOS 套件組合**簽署**]，然後選取先前建立的**開發人員身分識別**和布建**設定檔**。
6. 請確認**權利。 plist**包含**Enable iCloud**、 **Key-value storage**和**CloudKit**。
7. 請確定應用程式的**無所不在容器**存在。 範例： `iCloud.com.your-company.CloudKitAtlas`
8. 將變更儲存至檔案。

備妥這些設定後，範例應用程式現在已可存取 CloudKit Framework Api，以及背景、位置和通知服務。

## <a name="cloudkit-api-overview"></a>CloudKit API 總覽

在 Xamarin iOS 應用程式中執行 CloudKit 之前，本文將討論 CloudKit 架構的基本概念，其中包含下列主題：

1. **容器**-iCloud 通訊的隔離定址接收器。
2. **資料庫**-公用和私用可供應用程式使用。
3. **記錄**–結構化資料在 CloudKit 之間移動的機制。
4. **記錄區域**–是一組記錄。
5. **記錄識別碼**–完全正規化，並代表記錄的特定位置。
6. **參考**–提供給定資料庫內相關記錄間的父子關聯性。
7. **資產**–允許將大型非結構化資料的檔案上傳至 iCloud，並與指定的記錄相關聯。

### <a name="containers"></a>容器

在 iOS 裝置上執行的特定應用程式，一律會在該裝置上的其他應用程式和服務上執行。 在用戶端裝置上，應用程式會以某種方式進行孤立或沙箱處理。 在某些情況下，這是常值沙箱，而在其他情況中，應用程式只會在它自己的記憶體空間中執行。

建立用戶端應用程式並與其他用戶端分開執行的概念非常強大，而且提供下列優點：

1. **安全性**–一個應用程式不能幹擾其他用戶端應用程式或作業系統本身。
1. **穩定性**-如果用戶端應用程式當機，則無法取出 OS 的其他應用程式。
1. **隱私權**–每個用戶端應用程式對於儲存在裝置內的個人資訊具有有限的存取權。

CloudKit 的設計目的是要提供與上述所列相同的優點，並將其套用至使用雲端式資訊：

 [![CloudKit apps 使用容器進行通訊](intro-to-cloudkit-images/image31.png)](intro-to-cloudkit-images/image31.png#lightbox)

就像是在裝置上執行的應用程式一樣，應用程式是與 iCloud 一對多的通訊。 每個不同的通訊定址接收器稱為容器。

容器會透過類別在 CloudKit 架構中公開 `CKContainer` 。 根據預設，一個應用程式會與一個容器交談，而此容器會都會隔離該應用程式的資料。 這表示有數個應用程式可以將資訊儲存到相同的 iCloud 帳戶，但這項資訊永遠不會混用。

ICloud 資料的容器化也可讓 CloudKit 封裝使用者資訊。 如此一來，應用程式將對 iCloud 帳戶和儲存在內的使用者資訊有一些有限的存取權，同時仍然保護使用者的隱私權與安全性。

容器是由應用程式的開發人員透過 WWDR 入口網站完全管理。 容器的命名空間在所有的 Apple 開發人員中都是全域的，因此，容器不得只有特定開發人員的應用程式才能使用，而是對所有 Apple 開發人員和應用程式都是唯一的。

Apple 建議在建立應用程式容器的命名空間時，使用反向 DNS 標記法。 範例： `iCloud.com.company-name.application-name`

雖然容器預設會系結至指定的應用程式，但它們可以在應用程式之間共用。 因此，多個應用程式可以在單一容器上協調。 單一應用程式也可以與多個容器交談。

### <a name="databases"></a>資料庫

CloudKit 的主要功能之一，就是採用應用程式的資料模型和複寫，以模型到 iCloud 伺服器。 某些資訊適用于建立該檔案的使用者、其他資訊是可由使用者建立以供公開使用的公用資料（例如餐廳評論），或可能是開發人員為應用程式發行的資訊。 不論是哪一種情況，物件都不只是單一使用者，而是一群人。

 [![CloudKit 容器圖表](intro-to-cloudkit-images/image32.png)](intro-to-cloudkit-images/image32.png#lightbox)

在容器內，首先和最重要的是公用資料庫。 這就是所有公用資訊的存在和共同 mingles 的地方。 此外，應用程式的每個使用者都有數個個別的私用資料庫。

在 iOS 裝置上執行時，應用程式只能存取目前已登入的 iCloud 使用者的資訊。 因此，應用程式的容器視圖會如下所示：

 [![容器的應用程式視圖](intro-to-cloudkit-images/image33.png)](intro-to-cloudkit-images/image33.png#lightbox)

它只能看到公用資料庫和私人資料庫與目前登入的 iCloud 使用者相關聯。

資料庫會透過類別在 CloudKit 架構中公開 `CKDatabase` 。 每個應用程式都有兩個資料庫的存取權：公用資料庫和私用資料庫。

容器是 CloudKit 的初始進入點。 下列程式碼可以用來從應用程式的預設容器存取公用和私用資料庫：

```csharp
using CloudKit;
//...

public CKDatabase PublicDatabase { get; set; }
public CKDatabase PrivateDatabase { get; set; }
//...

// Get the default public and private databases for
// the application
PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;
```

以下是資料庫類型之間的差異：

||公用資料庫|私用資料庫|
|---|--- |--- |
|**資料類型**|共用資料|目前使用者的資料|
|**配額**|在開發人員的配額中列入考慮|在使用者的配額中列入考慮|
|**預設權限**|全球可讀取|使用者可讀取|
|**編輯許可權**|透過記錄類別層級的 iCloud 儀表板角色|不適用|

### <a name="records"></a>記錄

容器會保存資料庫，而在資料庫內則是記錄。 記錄是將結構化資料移入和移出 CloudKit 的機制：

 [![容器會保存資料庫，而在資料庫內則是記錄](intro-to-cloudkit-images/image34.png)](intro-to-cloudkit-images/image34.png#lightbox)

記錄會透過類別在 CloudKit 架構中公開 `CKRecord` ，這會包裝索引鍵/值組。 應用程式中物件的實例相當於 `CKRecord` CloudKit 中的。 此外，每個都 `CKRecord` 擁有一種記錄類型，相當於物件的類別。

記錄具有及時的架構，因此在將資料交給處理之前，會先將其描述為 CloudKit。 CloudKit 會從該時間點解讀資訊，並處理儲存和抓取記錄的物流。

`CKRecord`類別也支援範圍廣泛的中繼資料。 例如，記錄包含建立時間和建立它的使用者相關資訊。 記錄也包含上次修改時間和修改它的使用者相關資訊。

記錄包含變更標記的概念。 這是指定記錄的舊版修訂版本。 變更標記是用來判斷用戶端和伺服器是否具有相同的指定記錄版本的輕量方式。

如上所述，將索引 `CKRecords` 鍵/值組換行，因此可以將下列類型的資料儲存在記錄中：

1. `NSString`
1. `NSNumber`
1. `NSData`
1. `NSDate`
1. `CLLocation`
1. `CKReferences`
1. `CKAssets`

除了單一數值型別以外，記錄可以包含上述任何一種類型的同質陣列。

下列程式碼可以用來建立新的記錄，並將它儲存在資料庫中：

```csharp
using CloudKit;
//...

private const string ReferenceItemRecordName = "ReferenceItems";
//...

var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;
await CloudManager.SaveAsync (newRecord);
```

### <a name="record-zones"></a>記錄區域

記錄本身不會存在於指定的資料庫中-記錄群組會同時存在於記錄區域中。 您可以將記錄區域視為傳統關係資料庫中的資料表：

 [![記錄區域中有一組記錄存在於一起](intro-to-cloudkit-images/image35.png)](intro-to-cloudkit-images/image35.png#lightbox)

給定的記錄區域和指定資料庫內的多個記錄區域中可以有多個記錄。 每個資料庫都包含預設的記錄區域：

 [![每個資料庫都包含預設的記錄區域和自訂區域](intro-to-cloudkit-images/image36.png)](intro-to-cloudkit-images/image36.png#lightbox)

這是預設儲存記錄的位置。 此外，您可以建立自訂記錄區域。 記錄區域代表進行不可部分完成認可和變更追蹤的基本資料細微性。

## <a name="record-identifiers"></a>記錄識別碼

記錄識別碼會表示為元組，其中包含用戶端提供的記錄名稱和記錄所在的區域。 記錄識別碼具有下列特性：

- 它們是由用戶端應用程式所建立。
- 它們會完全正規化，並代表記錄的特定位置。
- 藉由將外部資料庫中記錄的唯一識別碼指派給記錄名稱，它們可以用來橋接未儲存在 CloudKit 中的本機資料庫。

當開發人員建立新記錄時，他們可以選擇傳入記錄識別碼。 如果未指定記錄識別碼，則會自動建立 UUID，並將其指派給記錄。

當開發人員建立新的記錄識別碼時，他們可以選擇指定每一筆記錄所屬的記錄區域。 如果未指定，則會使用預設的記錄區域。

記錄識別碼會透過類別在 CloudKit 架構中公開 `CKRecordID` 。 下列程式碼可以用來建立新的記錄識別碼：

```csharp
var recordID =  new CKRecordID("My Record");
```

### <a name="references"></a>參考資料

參考提供給定資料庫中相關記錄間的關聯性：

 [![參考提供給定資料庫中相關記錄間的關聯性](intro-to-cloudkit-images/image37.png)](intro-to-cloudkit-images/image37.png#lightbox)

在上述範例中，父系擁有子系，因此子系是父記錄的子記錄。 關聯性會從子記錄移至父記錄，並稱為*反向參考*。

參考會透過類別在 CloudKit 架構中公開 `CKReference` 。 這種方式可讓 iCloud 伺服器瞭解記錄之間的關聯性。

參考會提供串聯式刪除背後的機制。 如果父記錄是從資料庫中刪除，則也會自動從資料庫中刪除任何子記錄（如關聯性中所指定）。

> [!NOTE]
> 使用 CloudKit 時，可能有無關聯指標。 例如，當應用程式提取記錄指標清單、選取一筆記錄，然後要求記錄時，該記錄可能已不存在於資料庫中。 應用程式必須經過編碼，才能正常處理這種情況。

雖然不是必要的，但在使用 CloudKit 架構時，會慣用反向參考。 Apple 已微調系統，讓這是最有效率的參考型別。

建立參考時，開發人員可以提供已在記憶體中的記錄，或建立記錄識別碼的參考。 如果使用記錄識別碼，而且指定的參考不存在於資料庫中，則會建立一個無符號指標。

以下是針對已知記錄建立參考的範例：

```csharp
var reference = new CKReference(newRecord, new CKReferenceAction());
```

### <a name="assets"></a>Assets

資產允許將大量的非結構化資料上傳到 iCloud，並與指定的記錄相關聯：

 [![資產允許將大型的非結構化資料上傳到 iCloud 並與指定的記錄相關聯的檔案](intro-to-cloudkit-images/image38.png)](intro-to-cloudkit-images/image38.png#lightbox)

在用戶端上 `CKRecord` 會建立，以描述即將上傳到 iCloud 伺服器的檔案。 `CKAsset`會建立以包含檔案，並連結至描述該檔案的記錄。

當檔案上傳到伺服器時，記錄會放在資料庫中，並將檔案複製到特殊的大量儲存資料庫中。 記錄指標和上傳的檔案之間會建立連結。

資產會透過類別在 CloudKit 架構中公開 `CKAsset` ，並用來儲存大量的非結構化資料。 由於開發人員不想要在記憶體中有大型的非結構化資料，因此會使用磁片上的檔案來執行資產。

資產是由記錄所擁有，可讓您使用記錄做為指標從 iCloud 中取出資產。 如此一來，當擁有資產的記錄遭到刪除時，伺服器就可以垃圾收集資產。

因為 `CKAssets` 的用途是處理大型資料檔案，所以 Apple 設計 CloudKit 以有效率地上傳和下載資產。

下列程式碼可用來建立資產，並將其與記錄建立關聯：

```csharp
var fileUrl = new NSUrl("LargeFile.mov");
var asset = new CKAsset(fileUrl);
newRecord ["name"] = asset;
```

我們現在已涵蓋 CloudKit 內的所有基本物件。 容器會與應用程式建立關聯，並包含資料庫。 資料庫包含分組到記錄區域中，並由記錄識別碼指向的記錄。 父子式關聯性是使用參考在記錄之間定義的。 最後，您可以使用資產上傳大型檔案，並將其與記錄相關聯。

## <a name="cloudkit-convenience-api"></a>CloudKit 便利性 API

Apple 提供兩個不同的 API 集合來使用 CloudKit：

- **操作 API** -提供 CloudKit 的每一項功能。 針對更複雜的應用程式，此 API 可提供對 CloudKit 的精細控制。
- **便利性 API** -提供通用、預先設定的 CloudKit 功能子集。 它提供便利、輕鬆的存取解決方案，在 iOS 應用程式中包含 CloudKit 功能。

方便使用的 API 通常是大部分 iOS 應用程式和 Apple 建議的最佳選擇。 本節的其餘部分將涵蓋下列便利 API 主題：

- 儲存記錄。
- 正在提取記錄。
- 正在更新記錄。

### <a name="common-setup-code"></a>一般設定程式碼

開始使用 CloudKit 便利性 API 之前，需要一些標準設定程式碼。 一開始先修改應用程式的檔案 `AppDelegate.cs` ，讓它看起來如下所示：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using UIKit;
using CloudKit;

namespace CloudKitAtlas
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        public override UIWindow Window { get; set;}
        public CKDatabase PublicDatabase { get; set; }
        public CKDatabase PrivateDatabase { get; set; }

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            application.RegisterForRemoteNotifications ();

            // Get the default public and private databases for
            // the application
            PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
            PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;

            return true;
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            Console.WriteLine ("Registered for Push notifications with token: {0}", deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications (UIApplication application, NSError error)
        {
            Console.WriteLine ("Push subscription failed");
        }

        public override void ReceivedRemoteNotification (UIApplication application, NSDictionary userInfo)
        {
            Console.WriteLine ("Push received");
        }
    }
}
```

上述程式碼會將公用和私用 CloudKit 資料庫公開為快捷方式，讓它們在應用程式的其餘部分中更容易使用。

接下來，將下列程式碼新增至將使用 CloudKit 的任何 view 或 view 容器：

```csharp
using CloudKit;
//...

public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
```

這會加入快捷方式，以取得 `AppDelegate` 並存取上述建立的公用和私用資料庫快捷方式。

在此程式碼準備就緒之後，讓我們看看如何在 Xamarin iOS 8 應用程式中執行 CloudKit 便利性 API。

### <a name="saving-a-record"></a>儲存記錄

當討論記錄時，使用上述的模式，下列程式碼會建立新的記錄，並使用便利性 API 將它儲存到公用資料庫：

```csharp
private const string ReferenceItemRecordName = "ReferenceItems";
...

// Create a new record
var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;

// Save it to the database
ThisApp.PublicDatabase.SaveRecord(newRecord, (record, err) => {
    // Was there an error?
    if (err != null) {
        ...
    }
});
```

關於上述程式碼，需要注意的三件事：

1. 藉由呼叫的 `SaveRecord` 方法 `PublicDatabase` ，開發人員不需要指定資料的傳送方式、其寫入的區域等。便利性 API 會負責所有這些詳細資料本身。
1. 呼叫是非同步，而且會在呼叫完成時（不論成功或失敗）提供回呼常式。 如果呼叫失敗，則會提供錯誤訊息。
1. CloudKit 不提供本機儲存/持續性;這只是一個傳輸媒體。 因此當提出要求來儲存記錄時，它會立即傳送到 iCloud 伺服器。

> [!NOTE]
> 因為行動網路通訊的「損及」本質，其中的連線經常遭到捨棄或中斷，所以開發人員在使用 CloudKit 時必須進行的第一個考慮就是錯誤處理。

### <a name="fetching-a-record"></a>提取記錄

建立記錄並成功儲存在 iCloud 伺服器上時，請使用下列程式碼來抓取記錄：

```csharp
// Create a record ID and fetch the record from the database
var recordID = new CKRecordID("MyRecordName");
ThisApp.PublicDatabase.FetchRecord(recordID, (record, err) => {
    // Was there an error?
    if (err != null) {
        ...
    }
});
```

就像儲存記錄一樣，上述程式碼是非同步、簡單的，而且需要很大的錯誤處理。

### <a name="updating-a-record"></a>更新記錄

從 iCloud 伺服器提取記錄之後，您可以使用下列程式碼來修改記錄，並將變更儲存回資料庫：

```csharp
// Create a record ID and fetch the record from the database
var recordID = new CKRecordID("MyRecordName");
ThisApp.PublicDatabase.FetchRecord(recordID, (record, err) => {
    // Was there an error?
    if (err != null) {

    } else {
        // Modify the record
        record["name"] = (NSString)"New Name";

        // Save changes to database
        ThisApp.PublicDatabase.SaveRecord(record, (r, e) => {
            // Was there an error?
            if (e != null) {
                 ...
            }
        });
    }
});
```

`FetchRecord` `PublicDatabase` 如果呼叫成功，的方法會傳回 `CKRecord` 。 然後，應用程式會修改記錄，然後 `SaveRecord` 再次呼叫，將變更寫回資料庫。

本節已顯示應用程式在使用 CloudKit 便利性 API 時所將使用的一般週期。 應用程式會將記錄儲存至 iCloud、從 iCloud 抓取這些記錄、修改記錄，並將這些變更儲存回 iCloud。

## <a name="designing-for-scalability"></a>擴充性設計

到目前為止，本文探討了如何從 iCloud 伺服器儲存和抓取應用程式的整個物件模型，每次都能使用它。 雖然這種方法適用于少量的資料和非常小型的使用者群，但當資訊和/或使用者群的數量增加時，不會適當地進行調整。

### <a name="big-data-tiny-device"></a>海量資料，小型裝置

應用程式會變得越來越熱門，資料庫中的資料越多，在裝置上擁有該完整資料的快取就愈不可行。 下列技巧可以用來解決此問題：

- 將**大型資料保留在雲端中**– CloudKit 的設計是要有效率地處理大型資料。
- **用戶端應該只會查看該資料**的配量–減少在指定時間處理任何工作所需的最少資料。
- **用戶端視圖可以變更**–因為每個使用者都有不同的喜好設定，顯示的資料配量可以從使用者變更為使用者，而使用者的任何指定配量的個別觀點也可能不同。
- **用戶端會使用查詢來專注于觀點**–查詢可讓使用者查看存在於雲端中較大資料集的小型子集。

### <a name="queries"></a>查詢

如上所述，查詢可讓開發人員選取存在於雲端中較大資料集的一小部分。 查詢會透過類別在 CloudKit 架構中公開 `CKQuery` 。

查詢結合了三個不同的專案：記錄類型（ `RecordType` ）、述詞（ `NSPredicate` ）和（選擇性）排序描述元（ `NSSortDescriptors` ）。 CloudKit 支援大部分的 `NSPredicate` 。

#### <a name="supported-predicates"></a>支援的述詞

使用查詢時，CloudKit 支援下列類型的 `NSPredicates` ：

1. 符合名稱等於儲存在變數中之值的記錄：

    ```csharp
    NSPredicate.FromFormat(string.Format("name = '{0}'", recordName))
    ```

2. 允許依據動態索引鍵值進行比對，讓索引鍵不一定要在編譯時期知道：

    ```csharp
    NSPredicate.FromFormat(string.Format("{0} = '{1}'", key, value))
    ```

3. 記錄值大於指定值的相符記錄：

    ```csharp
    NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date))
    ```

4. 記錄位置在指定位置的100計量內的相符記錄：

    ```csharp
    var location = new CLLocation(37.783,-122.404);
    var predicate = NSPredicate.FromFormat(string.Format("distanceToLocation:fromLocation(Location,{0}) < 100", location));
    ```

5. CloudKit 支援標記化的搜尋。 此呼叫會建立兩個權杖，一個用於， `after` 另一個用於 `session` 。 它會傳回包含這兩個權杖的記錄：

    ```csharp
    NSPredicate.FromFormat(string.Format("ALL tokenize({0}, 'Cdl') IN allTokens", "after session"))
    ```

6. CloudKit 支援使用運算子聯結的複合述詞 `AND` 。

    ```csharp
    NSPredicate.FromFormat(string.Format("start > {0} AND name = '{1}'", (NSDate)date, recordName))
    ```

#### <a name="creating-queries"></a>建立查詢

下列程式碼可 `CKQuery` 在 Xamarin iOS 8 應用程式中用來建立：

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = '{0}'", recordName));
var query = new CKQuery("CloudRecords", predicate);
```

首先，它會建立述詞，只選取符合指定名稱的記錄。 然後，它會建立一個查詢，以選取符合述詞之給定記錄類型的記錄。

#### <a name="performing-a-query"></a>執行查詢

建立查詢之後，請使用下列程式碼來執行查詢，並處理傳回的記錄：

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = {0}", recordName));
var query = new CKQuery("CloudRecords", predicate);

ThisApp.PublicDatabase.PerformQuery(query, CKRecordZone.DefaultRecordZone().ZoneId, (NSArray results, NSError err) => {
    // Was there an error?
    if (err != null) {
       ...
    } else {
        // Process the returned records
        for(nint i = 0; i < results.Count; ++i) {
            var record = (CKRecord)results[i];
        }
    }
});
```

上述程式碼會採用上面建立的查詢，並針對公用資料庫執行。 由於未指定任何記錄區域，因此會搜尋所有區域。 如果未發生任何錯誤，將會 `CKRecords` 傳回符合查詢參數的陣列。

思考查詢的方式是它們會進行輪詢，而且很適合透過大型資料集來切割。 不過，查詢並不適合大型、大部分的靜態資料集，因為下列原因：

- 這些裝置的電池壽命不佳。
- 它們對網路流量而言是壞的。
- 因為他們所看到的資訊受限於應用程式輪詢資料庫的頻率，所以不是使用者經驗的錯誤。 當使用者變更時，現在會預期推播通知。

### <a name="subscriptions"></a>訂用帳戶

處理大型且大部分的靜態資料集時，不應在用戶端裝置上執行查詢，它應該代表用戶端在伺服器上執行。 查詢應該在背景中執行，而且應該在每一筆記錄儲存之後執行，不論是由目前裝置或其他裝置觸及相同的資料庫。

最後，當伺服器端查詢執行時，應該將推播通知傳送到連接至資料庫的每個裝置。

訂閱會透過類別在 CloudKit 架構中公開 `CKSubscription` 。 它們結合了記錄類型（ `RecordType` ）、述詞（ `NSPredicate` ）和 Apple 推播通知（ `Push` ）。

> [!NOTE]
> CloudKit 推送會稍微擴大，因為它們包含包含 CloudKit 特定資訊的裝載，例如造成推送發生的原因。

#### <a name="how-subscriptions-work"></a>訂閱的工作方式

在 c # 程式碼中執行訂用帳戶之前，讓我們快速流覽訂閱的工作方式：

 [![概述訂閱的工作方式](intro-to-cloudkit-images/image39.png)](intro-to-cloudkit-images/image39.png#lightbox)

上圖顯示典型的訂用帳戶程式，如下所示：

1. 用戶端裝置會建立新的訂用帳戶，其中包含將會觸發訂用帳戶的條件集，以及觸發程式發生時將傳送的推播通知。
2. 此訂用帳戶會傳送至資料庫，並將其加入現有訂閱的集合中。
3. 第二個裝置會建立新的記錄，並將該記錄儲存至資料庫。
4. 資料庫會搜尋其訂閱清單，以查看新記錄是否符合其任何條件。
5. 如果找到相符的結果，推播通知會傳送至註冊訂用帳戶的裝置，並包含導致其觸發之記錄的相關資訊。

有了這項知識之後，讓我們來看一下如何在 Xamarin iOS 8 應用程式中建立訂閱。

#### <a name="creating-subscriptions"></a>建立訂閱

下列程式碼可用來建立訂用帳戶：

```csharp
// Create a new subscription
DateTime date;
var predicate = NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date));
var subscription = new CKSubscription("RecordType", predicate, CKSubscriptionOptions.FiresOnRecordCreation);

// Describe the type of notification
var notificationInfo = new CKNotificationInfo();
notificationInfo.AlertLocalizationKey = "LOCAL_NOTIFICATION_KEY";
notificationInfo.SoundName = "ping.aiff";
notificationInfo.ShouldBadge = true;

// Attach the notification info to the subscription
subscription.NotificationInfo = notificationInfo;
```

首先，它會建立可提供觸發訂閱之條件的述詞。 接下來，它會針對特定記錄類型建立訂閱，並設定測試觸發程式時的選項。 最後，它會定義觸發訂閱時所發生的通知類型，並將它附加到訂用帳戶。

### <a name="saving-subscriptions"></a>正在儲存訂閱

建立訂用帳戶之後，下列程式碼會將它儲存到資料庫：

```csharp
// Save the subscription to the database
ThisApp.PublicDatabase.SaveSubscription(subscription, (s, err) => {
    // Was there an error?
    if (err != null) {

    }
});
```

使用便利性 API，呼叫是非同步、簡單的，並且提供簡單的錯誤處理。

#### <a name="handling-push-notifications"></a>處理推播通知

如果開發人員先前已使用 Apple 推播通知（AP），則應該熟悉 CloudKit 所產生之通知的處理常式。

在中 `AppDelegate.cs` ，覆寫 `ReceivedRemoteNotification` 類別，如下所示：

```csharp
public override void ReceivedRemoteNotification (UIApplication application, NSDictionary userInfo)
{
    // Parse the notification into a CloudKit Notification
    var notification = CKNotification.FromRemoteNotificationDictionary (userInfo);

    // Get the body of the message
    var alertBody = notification.AlertBody;

    // Was this a query?
    if (notification.NotificationType == CKNotificationType.Query) {
        // Yes, convert to a query notification and get the record ID
        var query = notification as CKQueryNotification;
        var recordID = query.RecordId;
    }
}
```

上述程式碼會要求 CloudKit 將使用者資訊剖析為 CloudKit 通知。 接下來，會將警示的相關資訊解壓縮。 最後，會測試通知的類型，並據此處理通知。

本節說明如何使用查詢和訂用帳戶，來回答上面所呈現的 Big Data、微小的裝置問題。 應用程式會將其大型資料留在雲端，並使用這些技術來提供資料集的視圖。

## <a name="cloudkit-user-accounts"></a>CloudKit 使用者帳戶

如同本文開頭所述，CloudKit 是建立在現有的 iCloud 基礎結構之上。 下一節將詳細說明如何使用 CloudKit API 將帳戶公開給開發人員。

### <a name="authentication"></a>驗證

處理使用者帳戶時，第一個考慮是驗證。 CloudKit 支援透過裝置上目前登入的 iCloud 使用者進行驗證。 驗證會在幕後進行，並由 iOS 處理。 如此一來，開發人員就不需要擔心執行驗證的細節。 他們只會測試使用者是否已登入。

### <a name="user-account-information"></a>使用者帳戶資訊

CloudKit 提供下列使用者資訊給開發人員：

- 身分**識別–可**唯一識別使用者的方式。
- **Metadata** –儲存和抓取使用者相關資訊的能力。
- **隱私權**–所有資訊都是以隱私權 manor 的方式處理。 除非使用者同意，否則不會公開任何內容。
- **探索**–讓使用者能夠探索使用相同應用程式的朋友。

接下來，我們將詳細探討這些主題。

#### <a name="identity"></a>身分識別

如上所述，CloudKit 提供了一種方法，讓應用程式可以唯一識別指定的使用者：

 [![唯一根本指定的使用者](intro-to-cloudkit-images/image40.png)](intro-to-cloudkit-images/image40.png#lightbox)

在使用者裝置上執行的用戶端應用程式，以及 CloudKit 容器內的所有特定使用者私用資料庫。 用戶端應用程式將會連結到這些特定使用者的其中一個。 這是以裝置本機上登入 iCloud 的使用者為基礎。

因為這是來自 iCloud，所以有豐富的使用者資訊備份存放區。 由於 iCloud 實際上是裝載容器，因此它可以讓使用者相互關聯。 在上圖中，其 iCloud 帳戶 `user@icloud.com` 連結至目前用戶端的使用者。

以容器為基礎的容器上，會建立唯一且隨機產生的使用者識別碼，並與使用者的 iCloud 帳戶（電子郵件地址）相關聯。 此使用者識別碼會傳回給應用程式，並可透過任何開發人員所看到的適當方式來使用。

> [!NOTE]
> 相同 iCloud 使用者在相同裝置上執行的不同應用程式將會有不同的使用者識別碼，因為它們會連線到不同的 CloudKit 容器。

下列程式碼會針對裝置上目前登入的 iCloud 使用者取得 CloudKit 使用者識別碼：

```csharp
public CKRecordID UserID { get; set; }
...

// Get the CloudKit User ID
CKContainer.DefaultContainer.FetchUserRecordId ((recordID, err) => {
    // Was there an error?
    if (err!=null) {
        Console.WriteLine("Error: {0}", err.LocalizedDescription);
    } else {
        // Save user ID
        UserID = recordID;
    }
});
```

上述程式碼會要求 CloudKit 容器提供目前登入之使用者的識別碼。 因為這是來自 iCloud 伺服器的資訊，所以呼叫是非同步，而且需要錯誤處理。

#### <a name="metadata"></a>中繼資料

CloudKit 中的每個使用者都有特定的中繼資料來描述它們。 此中繼資料會表示為 CloudKit 記錄：

 [![CloudKit 中的每個使用者都有特定的中繼資料來描述它們](intro-to-cloudkit-images/image41.png)](intro-to-cloudkit-images/image41.png#lightbox)

在私人資料庫中查看容器的特定使用者，有一筆記錄會定義該使用者。 公用資料庫內有許多使用者記錄，容器的每個使用者都有一個。 其中一個記錄識別碼會符合目前登入之使用者的記錄識別碼。

公用資料庫中的使用者記錄可供全球讀取。 在大部分的情況下，會將它們視為一般記錄，而且具有類型 `CKRecordTypeUserRecord` 。 這些記錄是由系統所保留，無法供查詢使用。

使用下列程式碼來存取使用者記錄：

```csharp
public CKRecord UserRecord { get; set; }
...

// Get the user's record
PublicDatabase.FetchRecord(UserID, (record ,er) => {
    //was there an error?
    if (er != null) {
        Console.WriteLine("Error: {0}", er.LocalizedDescription);
    } else {
        // Save the user record
        UserRecord = record;
    }
});
```

上述程式碼會要求公用資料庫針對我們在上面存取的識別碼，傳回使用者記錄。 因為這是來自 iCloud 伺服器的資訊，所以呼叫是非同步，而且需要錯誤處理。

#### <a name="privacy"></a>隱私權

CloudKit 是預設的設計，用來保護目前登入之使用者的隱私權。 預設不會公開使用者的任何個人識別資訊。 在某些情況下，應用程式會要求使用者提供有限的資訊。

在這些情況下，應用程式可以要求使用者公開這項資訊。 使用者會看到一個對話方塊，要求他們加入宣告來公開其帳戶資訊。

#### <a name="discovery"></a>探索

假設使用者加入宣告以允許應用程式限制存取其使用者帳戶資訊，則應用程式的其他使用者就可以找到它們：

 [![應用程式的其他使用者可以探索到使用者](intro-to-cloudkit-images/image42.png)](intro-to-cloudkit-images/image42.png#lightbox)

用戶端應用程式正在與容器交談，且容器正在與 iCloud 交談以存取使用者資訊。 使用者可以提供電子郵件地址，而且可以使用探索來取得使用者的相關資訊。 （選擇性）使用者識別碼也可以用來探索使用者的相關資訊。

CloudKit 也提供一種方式，可讓您藉由查詢整個通訊錄，探索任何可能是目前登入 iCloud 使用者之朋友的使用者相關資訊。 CloudKit 程式將會提取使用者的連絡人書籍，並使用電子郵件地址來查看是否可以找到與這些位址相符的其他使用者的應用程式。

這可讓應用程式在不提供存取權的情況下，利用使用者的連絡人書籍，或要求使用者核准連絡人的存取權。 目前，只有 CloudKit 程式可以存取的連絡人資訊才可供應用程式使用。

回顧一下，有三種不同的輸入可供使用者探索使用：

- **使用者記錄識別碼**–可以針對目前登入 CloudKit 使用者的使用者識別碼來進行探索。
- **使用者電子郵件地址**–使用者可以提供電子郵件地址，並可用於探索。
- **Contact book** –使用者的通訊錄可以用來探索應用程式的使用者，其電子郵件地址與連絡人中列出的相同。

使用者探索會傳回下列資訊：

- **使用者記錄識別碼**-公用資料庫中使用者的唯一識別碼。
- **名字和姓氏**-儲存在公用資料庫中。

只有加入宣告探索的使用者才會傳回這則資訊。

下列程式碼會探索目前已登入裝置上 iCloud 的使用者相關資訊：

```csharp
public CKDiscoveredUserInfo UserInfo { get; set; }
//...

// Get the user's metadata
CKContainer.DefaultContainer.DiscoverUserInfo(UserID, (info, e) => {
    // Was there an error?
    if (e != null) {
        Console.WriteLine("Error: {0}", e.LocalizedDescription);
    } else {
        // Save the user info
        UserInfo = info;
    }
});
```

使用下列程式碼來查詢連絡人書籍中的所有使用者：

```csharp
// Ask CloudKit for all of the user's friends information
CKContainer.DefaultContainer.DiscoverAllContactUserInfos((info, er) => {
    // Was there an error
    if (er != null) {
        Console.WriteLine("Error: {0}", er.LocalizedDescription);
    } else {
        // Process all returned records
        for(int i = 0; i < info.Count(); ++i) {
            // Grab a user
            var userInfo = info[i];
        }
    }
});
```

在本節中，我們涵蓋了存取 CloudKit 可提供給應用程式之使用者帳戶的四個主要區域。 從取得使用者的身分識別和中繼資料，到內建于 CloudKit 的隱私權原則，最後是探索應用程式其他使用者的能力。

## <a name="the-development-and-production-environments"></a>開發和生產環境

CloudKit 為應用程式的記錄類型和資料提供個別的開發和生產環境。 開發環境是更有彈性的環境，僅適用于開發小組的成員。 當應用程式將新欄位加入至記錄，並將該記錄儲存在開發環境中時，伺服器會自動更新架構資訊。

開發人員可以使用這項功能，在開發期間對架構進行變更，以節省時間。 有一點要注意的是，將欄位加入至記錄之後，與該欄位相關聯的資料類型就無法以程式設計方式變更。 若要變更欄位的類型，開發人員必須刪除[CloudKit 儀表板](https://icloud.developer.apple.com/dashboard/)中的欄位，然後使用新的類型重新加入。

在部署應用程式之前，開發人員可以使用**CloudKit 儀表板**，將其架構和資料移轉至生產環境。 針對生產環境執行時，伺服器會防止應用程式以程式設計方式變更架構。 開發人員仍可使用**CloudKit 儀表板**進行變更，但嘗試將欄位新增至生產環境中的記錄會導致錯誤。

> [!NOTE]
> IOS 模擬器僅適用于**開發環境**。 當開發人員準備好在**生產環境**中測試應用程式時，就需要實體 iOS 裝置。

## <a name="shipping-a-cloudkit-enabled-app"></a>寄送已啟用 CloudKit 的應用程式

在出貨使用 CloudKit 的應用程式之前，必須將它設定為以**生產 CloudKit 環境**為目標，否則 Apple 會拒絕應用程式。

執行下列動作：

1. 在 Ma 的 Visual Studio 中，為**Release**  >  **iOS 裝置**編譯應用程式：

    [![編譯用於發行的應用程式](intro-to-cloudkit-images/shipping01.png)](intro-to-cloudkit-images/shipping01.png#lightbox)

2. 從 [**建立**] 功能表中 **，選取 [** 封存]：

    [![選取封存](intro-to-cloudkit-images/shipping02.png)](intro-to-cloudkit-images/shipping02.png#lightbox)

3. 封存**將會**建立並顯示在 Visual Studio for Mac 中：

    [![將會建立並顯示封存](intro-to-cloudkit-images/shipping03.png)](intro-to-cloudkit-images/shipping03.png#lightbox)

4. 開始**Xcode**。
5. 從 [**視窗]** 功能表中，選取 [**召集人**]：

    [![選取召集人](intro-to-cloudkit-images/shipping04.png)](intro-to-cloudkit-images/shipping04.png#lightbox)

6. 選取應用程式的封存，然後按一下 [**匯出 ...** ] 按鈕：

    [![](intro-to-cloudkit-images/shipping05.png "The application's archive")](intro-to-cloudkit-images/shipping05.png#lightbox)

7. 選取匯出的方法，然後按 [**下一步]** 按鈕：

    [![選取匯出的方法](intro-to-cloudkit-images/shipping06.png)](intro-to-cloudkit-images/shipping06.png#lightbox)

8. 從下拉式清單中選取 [**開發小組**]，然後按一下 [**選擇**] 按鈕：

    [![從下拉式清單中選取開發小組](intro-to-cloudkit-images/shipping07.png)](intro-to-cloudkit-images/shipping07.png#lightbox)

9. 從下拉式清單中選取 [**生產**]，然後按 [**下一步]** 按鈕：

    [![從下拉式清單中選取 [生產]](intro-to-cloudkit-images/shipping08.png)](intro-to-cloudkit-images/shipping08.png#lightbox)

10. 檢查設定，然後按一下 [**匯出**] 按鈕：

    [![檢查設定](intro-to-cloudkit-images/shipping09.png)](intro-to-cloudkit-images/shipping09.png#lightbox)

11. 選擇要產生應用程式檔的位置 `.ipa` 。

此程式類似于直接將應用程式提交至 iTunes Connect，只要按一下 [**提交**] 按鈕，而不是 [匯出 ...]在 [召集人] 視窗中選取封存之後。

## <a name="when-to-use-cloudkit"></a>使用 CloudKit 的時機

如我們在本文中所見，CloudKit 提供了一種簡單的方法，讓應用程式能夠從 iCloud 伺服器儲存和取出資訊。 話雖如此，CloudKit 也不會淘汰或取代任何現有的工具或架構。

### <a name="use-cases"></a>使用案例

下列使用案例應該可協助開發人員決定何時使用特定的 iCloud 架構或技術：

- **iCloud 索引鍵-值存放區**–以非同步方式將少量的資料保持在最新狀態，而且很適合用來處理應用程式喜好設定。 不過，它受限於非常少量的資訊。
- **ICloud 磁片磁碟機**–建置於現有的 ICloud 檔 api 之上，並提供簡單的 API 來同步處理檔案系統中的非結構化資料。 它會在 Mac OS X 上提供完整的離線快取，適用于以檔為主的應用程式。
- **ICloud 核心資料**-允許在所有使用者裝置之間複寫資料。 資料是單一使用者，非常適合用來保持私用的結構化資料同步。
- **CloudKit** –提供結構和大量的公用資料，而且能夠處理大型資料集和大型非結構化檔案。 其系結至使用者的 iCloud 帳戶，並提供用戶端導向的資料傳輸。

請記住這些使用案例，開發人員應挑選正確的 iCloud 技術，以提供目前所需的應用程式功能，並針對未來成長提供良好的擴充性。

## <a name="summary"></a>總結

本文涵蓋了 CloudKit API 的快速簡介。 其中已示範如何布建和設定 Xamarin iOS 應用程式來使用 CloudKit。 其中涵蓋了 CloudKit 便利性 API 的功能。 它示範如何使用查詢和訂用帳戶，設計 CloudKit 啟用的應用程式來提供擴充性。 最後，它會顯示 CloudKit 對應用程式公開的使用者帳戶資訊。

## <a name="related-links"></a>相關連結

- [CloudKit （Apple）](https://developer.apple.com/icloud/cloudkit/)
- [CloudKitAtlas （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-cloudkitatlas)
- [建立布建設定檔](~/ios/get-started/installation/device-provisioning/index.md)
