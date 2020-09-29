---
title: 在 Xamarin 中 CloudKit
description: 本檔說明如何在 Xamarin 中使用 CloudKit。 它提供 CloudKit 的總覽，並討論如何啟用它、CloudKit 便利性 API、擴充性、使用者帳戶，以及開發和生產環境。
ms.prod: xamarin
ms.assetid: 66B207F2-FAA0-4551-B43B-3DB9F620C397
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/11/2016
ms.openlocfilehash: a01f0075ec7f47d6653313ea50047b7f89529bc6
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431392"
---
# <a name="cloudkit-in-xamarinios"></a>在 Xamarin 中 CloudKit

CloudKit 架構可簡化存取 iCloud 的應用程式開發。 這包括抓取應用程式資料和資產許可權，以及能夠安全地儲存應用程式資訊。 此套件可讓使用者以其 iCloud 識別碼存取應用程式，而不需共用個人資訊，藉此為使用者提供一層匿名。

開發人員可以專注于其用戶端應用程式，並讓 iCloud 免除撰寫伺服器端應用程式邏輯的需求。 CloudKit 提供驗證、私用和公用資料庫，以及結構化資料和資產儲存體服務。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)協助開發人員適當地處理歐盟一般資料保護規定 (GDPR)。

## <a name="requirements"></a>需求

若要完成本文中所述的步驟，必須具備下列專案：

- **Xcode 和 IOS SDK** –必須在開發人員的電腦上安裝和設定 Apple 的 Xcode 和 Ios 8 api。
- **Visual Studio for Mac** –應該在使用者裝置上安裝並設定最新版本的 Visual Studio for Mac。
- **ios 8 裝置** –執行最新版本 ios 8 以進行測試的 ios 裝置。

## <a name="what-is-cloudkit"></a>什麼是 CloudKit？

CloudKit 是為開發人員提供 iCloud 伺服器存取權的方式。 它同時為 iCloud 磁片磁碟機和 iCloud 相片圖庫提供基礎。 MacOS 和 iOS 裝置都支援 CloudKit。

[![MacOS 和 iOS 裝置上支援 CloudKit 的方式](intro-to-cloudkit-images/image1.png)](intro-to-cloudkit-images/image1.png#lightbox)

CloudKit 會使用 iCloud 帳戶基礎結構。 如果有使用者登入裝置上的 iCloud 帳戶，CloudKit 將會使用其識別碼來識別使用者。 如果沒有可用的帳戶，則會提供有限的唯讀存取權。

CloudKit 同時支援公用和私用資料庫的概念。 公用資料庫提供使用者可存取之所有資料的「< a0」。 私用資料庫的目的是要儲存系結至特定使用者的私用資料。

CloudKit 同時支援結構化和大量資料。 它能夠順暢地處理大型檔案傳輸。 CloudKit 會在背景處理在 iCloud 伺服器之間有效率地傳輸大型檔案，讓開發人員能夠專注于其他工作。

> [!NOTE]
> 請務必注意，CloudKit 是一種 _傳輸技術_。 它不會提供任何持續性;它只會讓應用程式有效率地從伺服器傳送和接收資訊。

在撰寫本文時，Apple 最初會以高限制的頻寬和儲存體容量，提供免費的 CloudKit。 針對較大型的專案或具有大型使用者群的應用程式，Apple 已提示將提供經濟實惠的定價規模。

## <a name="enabling-cloudkit-in-a-xamarin-application"></a>在 Xamarin 應用程式中啟用 CloudKit

在 Xamarin 應用程式可以使用 CloudKit 架構之前，必須正確地布建應用程式，如 [使用功能](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) 和 [使用權利](~/ios/deploy-test/provisioning/entitlements.md) 指南中所述。

若要存取 CloudKit， **plist** 檔案必須包含 **啟用 iCloud**、機 **碼值儲存體**和 **CloudKit** 許可權。

### <a name="sample-app"></a>範例應用程式

[CloudKitAtlas 範例](/samples/xamarin/ios-samples/ios8-cloudkitatlas)示範如何搭配 Xamarin 使用 CloudKit。 下列步驟顯示如何設定範例–除了需要 CloudKit 之外，還需要額外的設定：

1. 在 Visual Studio for Mac 中開啟專案或 Visual Studio。
2. 在**方案總管**中，開啟**plist**檔案，並確定套件組合識別碼符合在布建設定過程中所建立的**應用程式識別碼**中所定義的套件組合**識別碼**。
3. 向下 **plist** 檔案的底部，並選取 [ **啟用背景模式**]、[ **位置更新**] 和 [ **遠端通知**]。
4. 以滑鼠右鍵按一下方案中的 iOS 專案，然後選取 [ **選項**]。
5. 選取 [IOS 套件組合 **簽署**]，選取先前建立的 **開發人員身分識別** 和布建 **設定檔** 。
6. 確認 **plist** 包含 **啟用 iCloud**、機 **碼值儲存體**和 **CloudKit**。
7. 請確定應用程式已有 **無不入容器** 。 範例：`iCloud.com.your-company.CloudKitAtlas`
8. 將變更儲存至檔案。

設定好這些設定後，範例應用程式就可以開始存取 CloudKit Framework Api 以及背景、位置和通知服務。

## <a name="cloudkit-api-overview"></a>CloudKit API 總覽

在 Xamarin iOS 應用程式中執行 CloudKit 之前，本文將討論 CloudKit 架構的基本概念，其中包含下列主題：

1. **容器** -iCloud 通訊的隔離存放庫。
2. **資料庫** –公用和私用可供應用程式使用。
3. **記錄** ：結構化資料在 CloudKit 之間移動的機制。
4. **記錄區域** -是一組記錄。
5. **記錄識別碼** –完全正規化，並代表記錄的特定位置。
6. **參考** –提供指定資料庫中相關記錄之間的父子式關聯性。
7. **資產** –允許將大型、非結構化資料的檔案上傳至 iCloud，並與指定的記錄相關聯。

### <a name="containers"></a>容器

在 iOS 裝置上執行的特定應用程式一律會在該裝置上與其他應用程式和服務一起執行。 在用戶端裝置上，應用程式會以某種方式孤立或沙箱化。 在某些情況下，這是常值沙箱，而在其他情況下，應用程式只是在它自己的記憶體空間中執行。

讓用戶端應用程式與其他用戶端分開執行的概念非常強大，而且提供下列優點：

1. **安全性** –一個應用程式無法干擾其他用戶端應用程式或作業系統本身。
1. **穩定性** -如果用戶端應用程式損毀，則無法將作業系統的其他應用程式取出。
1. **隱私權** -每個用戶端應用程式對儲存在裝置內的個人資訊具有有限的存取權。

CloudKit 的設計是為了提供與上述所列相同的優點，並將其套用至使用雲端式資訊：

 [![CloudKit apps 使用容器進行通訊](intro-to-cloudkit-images/image31.png)](intro-to-cloudkit-images/image31.png#lightbox)

就像應用程式是在裝置上執行的應用程式一樣，因此應用程式與 iCloud 一對多的通訊。 這些不同的通訊接收器都稱為容器。

容器會透過類別在 CloudKit 架構中公開 `CKContainer` 。 根據預設，一個應用程式會與一個容器交談，而此容器會隔離該應用程式的資料。 這表示多個應用程式可以將資訊儲存至相同的 iCloud 帳戶，但這項資訊永遠不會混用。

ICloud 資料的容器化也可讓 CloudKit 封裝使用者資訊。 如此一來，應用程式將會對 iCloud 帳戶和儲存在其中的使用者資訊進行有限的存取，同時仍會保護使用者的隱私權和安全性。

容器完全由應用程式開發人員透過 WWDR 入口網站來管理。 容器的命名空間在所有 Apple 開發人員中都是全域的，因此容器不僅必須對特定開發人員的應用程式而言是唯一的，而是對所有 Apple 開發人員和應用程式都是唯一的。

當建立應用程式容器的命名空間時，Apple 建議使用反向 DNS 標記法。 範例：`iCloud.com.company-name.application-name`

雖然容器預設會系結至指定的應用程式，但它們可以在應用程式間共用。 因此，多個應用程式可以在單一容器上協調。 單一應用程式也可以與多個容器交談。

### <a name="databases"></a>資料庫

CloudKit 的其中一個主要功能是取得應用程式的資料模型，並將模型複製到 iCloud 伺服器。 某些資訊適用于建立它的使用者，其他資訊是可由使用者建立以供公開使用的公用資料 (例如餐廳評論) ，或是開發人員為應用程式發佈的資訊。 無論是哪一種情況，觀眾都不只是單一使用者，而是一群人。

 [![CloudKit 容器圖表](intro-to-cloudkit-images/image32.png)](intro-to-cloudkit-images/image32.png#lightbox)

在容器內，首先是最重要的是公用資料庫。 這就是所有公開資訊的存留和共同 mingles。 此外，應用程式的每個使用者都有數個個別私用資料庫。

在 iOS 裝置上執行時，應用程式只能存取目前登入的 iCloud 使用者的資訊。 因此，應用程式的容器視圖會如下所示：

 [![容器的應用程式視圖](intro-to-cloudkit-images/image33.png)](intro-to-cloudkit-images/image33.png#lightbox)

它只能看到與目前登入的 iCloud 使用者相關聯的公用資料庫和私用資料庫。

資料庫會透過類別在 CloudKit 架構中公開 `CKDatabase` 。 每一個應用程式都可以存取兩個資料庫：公用資料庫和私用資料庫。

容器是 CloudKit 的初始進入點。 您可以使用下列程式碼，從應用程式的預設容器存取公用和私用資料庫：

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
|**配額**|以開發人員的配額為考慮|在使用者的配額中列入考慮|
|**預設權限**|全球可讀取|使用者可讀取|
|**編輯許可權**|透過記錄類別層級的 iCloud 儀表板角色|N/A|

### <a name="records"></a>記錄

容器會保存資料庫，而資料庫內會有記錄。 記錄是結構化資料在 CloudKit 之間移動的機制：

 [![容器包含資料庫，而資料庫內有記錄](intro-to-cloudkit-images/image34.png)](intro-to-cloudkit-images/image34.png#lightbox)

記錄會透過類別在 CloudKit 架構中公開 `CKRecord` ，這會包裝成對的索引鍵/值。 應用程式中物件的實例相當於 `CKRecord` CloudKit 中的。 此外，每個都 `CKRecord` 擁有一種記錄類型，這相當於物件的類別。

記錄有及時的架構，因此會先將資料描述為 CloudKit，然後再進行處理。 從該時間點開始，CloudKit 將會解讀資訊，並處理儲存和抓取記錄的物流。

`CKRecord`類別也支援廣泛的中繼資料。 例如，記錄包含建立時間和建立它的使用者的相關資訊。 記錄也包含上次修改時間以及修改它的使用者的相關資訊。

記錄包含變更標記的概念。 這是指定記錄的舊版修訂版本。 變更標記是用來判斷用戶端和伺服器是否具有相同的指定記錄版本的輕量方式。

如上所述， `CKRecords` 將索引鍵/值組換行，如此一來，您可以將下列類型的資料儲存在記錄中：

1. `NSString`
1. `NSNumber`
1. `NSData`
1. `NSDate`
1. `CLLocation`
1. `CKReferences`
1. `CKAssets`

除了單一值型別之外，記錄還可以包含上述任何一種列出類型的同質陣列。

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

記錄本身不存在於指定的資料庫中-記錄區域內的記錄群組會一起存在。 您可以將記錄區域視為傳統關係資料庫中的資料表：

 [![記錄區域內同時存在一組記錄](intro-to-cloudkit-images/image35.png)](intro-to-cloudkit-images/image35.png#lightbox)

給定的記錄區域內可以有多筆記錄，以及指定資料庫內的多個記錄區域。 每個資料庫都包含預設的記錄區域：

 [![每個資料庫都包含預設的記錄區域和自訂區域](intro-to-cloudkit-images/image36.png)](intro-to-cloudkit-images/image36.png#lightbox)

這是預設儲存記錄的位置。 此外，也可以建立自訂記錄區域。 記錄區域代表執行不可部分完成認可和變更追蹤的基本細微性。

## <a name="record-identifiers"></a>記錄識別碼

記錄識別碼會表示為元組，其中包含用戶端提供的記錄名稱，以及記錄所在的區域。 記錄識別碼具有下列特性：

- 它們是由用戶端應用程式所建立。
- 它們會完全正規化，並代表記錄的特定位置。
- 藉由在外部資料庫中將記錄的唯一識別碼指派給記錄名稱，可用來橋接未儲存在 CloudKit 中的本機資料庫。

當開發人員建立新的記錄時，他們可以選擇傳入記錄識別碼。 如果未指定記錄識別碼，則會自動建立並指派 UUID 給記錄。

當開發人員建立新的記錄識別碼時，可以選擇指定每個記錄所屬的記錄區域。 如果沒有指定，則會使用預設的記錄區域。

記錄識別碼會透過類別在 CloudKit 架構中公開 `CKRecordID` 。 下列程式碼可以用來建立新的記錄識別碼：

```csharp
var recordID =  new CKRecordID("My Record");
```

### <a name="references"></a>參考資料

參考提供指定資料庫中相關記錄之間的關聯性：

 [![參考提供指定資料庫中相關記錄之間的關聯性](intro-to-cloudkit-images/image37.png)](intro-to-cloudkit-images/image37.png#lightbox)

在上述範例中，父系擁有子系，讓子系成為父記錄的子記錄。 關聯性會從子記錄移至父記錄，並稱為 *反向參考*。

參考會透過類別在 CloudKit 架構中公開 `CKReference` 。 它們是讓 iCloud 伺服器瞭解記錄之間關聯性的方法。

參考提供階層式刪除背後的機制。 如果從資料庫中刪除父記錄，則會自動從資料庫中刪除與關聯性) 中所指定 (的任何子記錄。

> [!NOTE]
> 使用 CloudKit 時，有一個可能的無關聯指標。 例如，應用程式在提取記錄指標清單之後，選取一筆記錄，然後再要求記錄，該記錄可能不會再存在於資料庫中。 應用程式必須撰寫程式碼，才能正常地處理這種情況。

雖然並非必要，但在使用 CloudKit Framework 時，建議使用反向參考。 Apple 已微調系統，使其成為最有效率的參考類型。

建立參考時，開發人員可以提供已在記憶體中的記錄，或建立記錄識別碼的參考。 如果使用記錄識別碼，而指定的參考不存在於資料庫中，則會建立一個非關聯指標。

以下是針對已知記錄建立參考的範例：

```csharp
var reference = new CKReference(newRecord, new CKReferenceAction());
```

### <a name="assets"></a>Assets

資產允許將大型、非結構化資料的檔案上傳至 iCloud，並與指定的記錄相關聯：

 [![資產允許將大型、非結構化資料的檔案上傳至 iCloud，並與指定的記錄相關聯](intro-to-cloudkit-images/image38.png)](intro-to-cloudkit-images/image38.png#lightbox)

在用戶端上， `CKRecord` 會建立，以描述即將上傳到 iCloud 伺服器的檔案。 `CKAsset`建立以包含檔案，並連結至描述它的記錄。

當檔案上傳到伺服器時，會將記錄放在資料庫中，並將檔案複製到特殊的大量儲存資料庫中。 記錄指標和上傳的檔案之間會建立連結。

資產會透過類別公開在 CloudKit 架構中 `CKAsset` ，並用來儲存大型的非結構化資料。 由於開發人員永遠不會想要在記憶體中有大量的非結構化資料，因此會使用磁片上的檔案來執行資產。

資產是由記錄所擁有，可讓您使用記錄作為指標，從 iCloud 取出資產。 如此一來，當擁有資產的記錄被刪除時，伺服器就可以對資產進行垃圾收集。

因為 `CKAssets` 是為了處理大型資料檔案，所以 Apple 設計 CloudKit 以有效率地上傳及下載資產。

您可以使用下列程式碼來建立資產，並將其與記錄建立關聯：

```csharp
var fileUrl = new NSUrl("LargeFile.mov");
var asset = new CKAsset(fileUrl);
newRecord ["name"] = asset;
```

我們現在已涵蓋 CloudKit 中的所有基本物件。 容器與應用程式相關聯，並包含資料庫。 資料庫包含分組至記錄區域並由記錄識別碼指向的記錄。 父子式關聯性是使用參考在記錄間定義。 最後，您可以使用資產將大型檔案上傳並與記錄產生關聯。

## <a name="cloudkit-convenience-api"></a>CloudKit 便利性 API

Apple 提供兩個不同的 API 集來使用 CloudKit：

- 作業**API** -提供 CloudKit 的每一項功能。 針對更複雜的應用程式，此 API 可讓您更精細地控制 CloudKit。
- **便利性 API** -提供常用、預先設定的 CloudKit 功能子集。 它提供便利、簡單的存取解決方案，可在 iOS 應用程式中包含 CloudKit 功能。

便利性 API 通常是大多數 iOS 應用程式的最佳選擇，且 Apple 建議從其開始。 本節的其餘部分將涵蓋下列便利 API 主題：

- 儲存記錄。
- 提取記錄。
- 更新記錄。

### <a name="common-setup-code"></a>一般安裝程式碼

開始使用 CloudKit 便利性 API 之前，需要一些標準的設定程式碼。 先修改應用程式的檔案 `AppDelegate.cs` ，使其看起來如下所示：

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

上述程式碼會將公用和私用 CloudKit 資料庫公開為快捷方式，讓您更輕鬆地在應用程式的其餘部分使用。

接下來，將下列程式碼新增至將使用 CloudKit 的任何視圖或 view 容器：

```csharp
using CloudKit;
//...

public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
```

這會加入快捷方式，以 `AppDelegate` 存取並存取上方建立的公用和私用資料庫快捷方式。

有了此程式碼之後，讓我們看看如何在 Xamarin iOS 8 應用程式中執行 CloudKit 的便利性 API。

### <a name="saving-a-record"></a>儲存記錄

使用上述的模式討論記錄時，下列程式碼會建立新的記錄，並使用便利性 API 將其儲存至公用資料庫：

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

關於上述程式碼的三件事：

1. 藉由呼叫的  `SaveRecord` 方法  `PublicDatabase` ，開發人員不需要指定傳送資料的方式、寫入的區域等等。便利性 API 會負責處理這些細節本身的所有細節。
1. 呼叫是非同步，而且會在呼叫完成時提供回呼常式（不論成功或失敗）。 如果呼叫失敗，則會提供一則錯誤訊息。
1. CloudKit 不提供本機儲存/持續性;它只是傳送媒體。 因此，當提出要求來儲存記錄時，它會立即傳送到 iCloud 伺服器。

> [!NOTE]
> 由於行動網路通訊的「失真」本質，也就是在中斷連線或中斷連線的情況下，開發人員在使用 CloudKit 時必須做的第一項考慮是錯誤處理。

### <a name="fetching-a-record"></a>提取記錄

若已建立記錄並成功儲存在 iCloud 伺服器上，請使用下列程式碼來取出記錄：

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

如同儲存記錄一樣，上述程式碼是非同步，而且需要很大的錯誤處理。

### <a name="updating-a-record"></a>更新記錄

從 iCloud 伺服器提取記錄之後，您可以使用下列程式碼來修改記錄，並將變更存回資料庫：

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

`FetchRecord` `PublicDatabase` 如果呼叫成功，的方法會傳回 `CKRecord` 。 然後，應用程式會修改記錄，然後再呼叫 `SaveRecord` 一次，將變更寫回資料庫。

本節顯示使用 CloudKit 便利性 API 時，應用程式將會使用的一般週期。 應用程式會將記錄儲存到 iCloud、從 iCloud 取出這些記錄、修改記錄，並將這些變更儲存回 iCloud。

## <a name="designing-for-scalability"></a>擴充性設計

到目前為止，本文將探討如何在每次使用時，從 iCloud 伺服器儲存和取出應用程式的整個物件模型。 雖然此方法適用于少量的資料和非常小的使用者群，但在資訊量和/或使用者群增加時，不會適當地進行調整。

### <a name="big-data-tiny-device"></a>大型資料，小型裝置

應用程式越普及，資料庫中的資料越多，在裝置上擁有整個資料的快取就越不可行。 下列技巧可以用來解決此問題：

- 將**大型資料保留在雲端中**-CloudKit 是設計來有效率地處理大型資料。
- **用戶端應該只會查看該資料的配量，** 並關閉在指定時間處理任何工作所需的最少資料。
- **用戶端視圖可能會變更** –因為每個使用者都有不同的喜好設定，顯示的資料配量可以從使用者變更為使用者，而使用者的任何特定配量的個別觀點可能會不同。
- **用戶端會使用查詢來專注于觀點** –查詢可讓使用者查看存在於雲端中較大資料集的一小部分。

### <a name="queries"></a>查詢

如上所述，查詢可讓開發人員選取存在於雲端中較大資料集的一小部分。 查詢會透過類別在 CloudKit 架構中公開 `CKQuery` 。

查詢結合三種不同的專案：記錄類型 ( `RecordType`) 、述詞 ( `NSPredicate`) ，以及選擇性的排序描述項 ( `NSSortDescriptors`) 。 CloudKit 支援大部分的 `NSPredicate` 。

#### <a name="supported-predicates"></a>支援的述詞

使用查詢時，CloudKit 支援下列類型 `NSPredicates` ：

1. 比對名稱等於儲存在變數中之值的記錄：

    ```csharp
    NSPredicate.FromFormat(string.Format("name = '{0}'", recordName))
    ```

2. 允許依據動態索引鍵值進行比對，如此一來，就不需要在編譯時間知道金鑰：

    ```csharp
    NSPredicate.FromFormat(string.Format("{0} = '{1}'", key, value))
    ```

3. 符合記錄值大於指定值的記錄：

    ```csharp
    NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date))
    ```

4. 比對記錄的位置在指定位置100計量內的相符記錄：

    ```csharp
    var location = new CLLocation(37.783,-122.404);
    var predicate = NSPredicate.FromFormat(string.Format("distanceToLocation:fromLocation(Location,{0}) < 100", location));
    ```

5. CloudKit 支援 token 化搜尋。 此呼叫會建立兩個權杖，一個用於，另一個用於 `after` `session` 。 它會傳回包含這兩個權杖的記錄：

    ```csharp
    NSPredicate.FromFormat(string.Format("ALL tokenize({0}, 'Cdl') IN allTokens", "after session"))
    ```

6. CloudKit 支援使用運算子聯結的複合述詞 `AND` 。

    ```csharp
    NSPredicate.FromFormat(string.Format("start > {0} AND name = '{1}'", (NSDate)date, recordName))
    ```

#### <a name="creating-queries"></a>建立查詢

下列程式碼可以用來 `CKQuery` 在 Xamarin iOS 8 應用程式中建立：

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = '{0}'", recordName));
var query = new CKQuery("CloudRecords", predicate);
```

首先，它會建立述詞，只選取符合指定名稱的記錄。 然後，它會建立查詢，以選取符合述詞之指定記錄類型的記錄。

#### <a name="performing-a-query"></a>執行查詢

建立查詢之後，請使用下列程式碼來執行查詢並處理傳回的記錄：

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

上述程式碼會採用上面所建立的查詢，並針對公用資料庫執行查詢。 由於未指定任何記錄區域，因此會搜尋所有區域。 如果未發生任何錯誤，將會 `CKRecords` 傳回符合查詢參數的陣列。

思考查詢的方式是它們會進行輪詢，很適合透過大型資料集進行切割。 但是，由於下列原因，查詢並不太適合大型、大部分的靜態資料集：

- 它們對裝置電池壽命而言是不正確的。
- 它們對網路流量而言是不正確的。
- 因為他們看到的資訊受限於應用程式輪詢資料庫的頻率，所以使用者體驗的原因很差。 現今的使用者在發生變更時，會預期推播通知。

### <a name="subscriptions"></a>訂用帳戶

處理大型、大部分的靜態資料集時，不應在用戶端裝置上執行查詢，它應該代表用戶端在伺服器上執行。 此查詢應該在背景中執行，而且應在每次儲存記錄之後執行（不論是由目前的裝置還是觸及相同資料庫的其他裝置）。

最後，在執行伺服器端查詢時，應該將推播通知傳送到連接至資料庫的每個裝置。

訂用帳戶會透過類別在 CloudKit 架構中公開 `CKSubscription` 。 它們結合了記錄類型 ( `RecordType`) 、述詞 ( `NSPredicate`) 和 Apple 推播通知 ( `Push`) 。

> [!NOTE]
> CloudKit 推播會稍微擴大，因為它們包含的承載包含 CloudKit 特定的資訊，例如造成推送發生的原因。

#### <a name="how-subscriptions-work"></a>訂閱的運作方式

在 c # 程式碼中執行訂用帳戶之前，讓我們先快速流覽一下訂用帳戶的運作方式：

 [![訂用帳戶如何運作的總覽](intro-to-cloudkit-images/image39.png)](intro-to-cloudkit-images/image39.png#lightbox)

上圖顯示一般的訂用帳戶處理常式，如下所示：

1. 用戶端裝置會建立新的訂用帳戶，其中包含將觸發訂用帳戶的一組條件，以及觸發程式發生時將傳送的推播通知。
2. 訂用帳戶會傳送至資料庫，並將它加入至現有訂閱的集合。
3. 第二個裝置會建立新的記錄，並將該記錄儲存至資料庫。
4. 資料庫會在其訂用帳戶清單中搜尋，以查看新的記錄是否符合其任何條件。
5. 如果找到相符的結果，則會將推播通知傳送到註冊訂用帳戶的裝置，並提供造成其觸發之記錄的相關資訊。

瞭解這一點之後，讓我們來看看如何在 Xamarin iOS 8 應用程式中建立訂用帳戶。

#### <a name="creating-subscriptions"></a>建立訂閱

下列程式碼可以用來建立訂用帳戶：

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

首先，它會建立一個述詞，以提供觸發訂閱的條件。 接下來，它會針對特定的記錄類型建立訂用帳戶，並設定測試觸發程式的選項。 最後，它會定義觸發訂閱並將其附加至訂用帳戶時，將會發生的通知類型。

### <a name="saving-subscriptions"></a>正在儲存訂閱

建立訂用帳戶之後，下列程式碼會將它儲存至資料庫：

```csharp
// Save the subscription to the database
ThisApp.PublicDatabase.SaveSubscription(subscription, (s, err) => {
    // Was there an error?
    if (err != null) {

    }
});
```

使用便利性 API 時，呼叫是非同步且簡單的，可提供簡單的錯誤處理。

#### <a name="handling-push-notifications"></a>處理推播通知

如果開發人員先前已使用 Apple 推播通知 (AP) ，則處理 CloudKit 所產生之通知的程式應該很熟悉。

在中 `AppDelegate.cs` ，覆寫類別，如下所示 `ReceivedRemoteNotification` ：

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

上述程式碼會要求 CloudKit 將使用者剖析為 CloudKit 通知。 接下來，會將警示的相關資訊解壓縮。 最後，會測試通知的類型，並據以處理通知。

本節顯示如何使用查詢和訂用帳戶來回答 Big Data、上述的微小裝置問題。 應用程式會將它的大型資料保留在雲端中，並使用這些技術來提供此資料集的觀點。

## <a name="cloudkit-user-accounts"></a>CloudKit 使用者帳戶

如同本文開頭所述，CloudKit 是建置於現有的 iCloud 基礎結構之上。 下一節將詳細說明如何使用 CloudKit API 將帳戶公開給開發人員。

### <a name="authentication"></a>驗證

處理使用者帳戶時，第一個考慮是驗證。 CloudKit 支援透過裝置上目前登入的 iCloud 使用者進行驗證。 驗證會在幕後進行，由 iOS 處理。 如此一來，開發人員就不需要擔心執行驗證的詳細資料。 他們只會進行測試，以查看使用者是否已登入。

### <a name="user-account-information"></a>使用者帳戶資訊

CloudKit 會提供下列使用者資訊給開發人員：

- 身分**識別–可**唯一識別使用者的方式。
- **中繼資料** –儲存和取得使用者相關資訊的能力。
- **隱私權** -所有資訊都是在隱私權重視 manor 中處理。 除非使用者已同意，否則不會公開任何內容。
- **探索** -讓使用者能夠探索使用相同應用程式的朋友。

接下來，我們將詳細探討這些主題。

#### <a name="identity"></a>身分識別

如上所述，CloudKit 提供了一個方法，讓應用程式能夠唯一識別指定的使用者：

 [![唯一找指定的使用者](intro-to-cloudkit-images/image40.png)](intro-to-cloudkit-images/image40.png#lightbox)

在使用者的裝置上執行的用戶端應用程式，以及 CloudKit 容器內的所有特定使用者私用資料庫。 用戶端應用程式將會連結到其中一個特定使用者。 這是以在裝置本機登入 iCloud 的使用者為基礎。

因為這是來自 iCloud，所以有豐富的使用者資訊備份存放區。 因為 iCloud 實際上是裝載容器，所以它可以讓使用者相互關聯。 在上圖中，其 iCloud 帳戶  `user@icloud.com` 會連結到目前用戶端的使用者。

根據容器，會建立唯一、隨機產生的使用者識別碼，並將其與使用者的 iCloud 帳戶相關聯， (電子郵件地址) 。 此使用者識別碼會傳回給應用程式，並可在開發人員認為適合的任何方式使用。

> [!NOTE]
> 相同的 iCloud 使用者在相同裝置上執行的不同應用程式將會有不同的使用者識別碼，因為這些應用程式會連線到不同的 CloudKit 容器。

下列程式碼會取得裝置上目前登入的 iCloud 使用者的 CloudKit 使用者識別碼：

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

上述程式碼會要求 CloudKit 容器提供目前登入使用者的識別碼。 由於這項資訊來自 iCloud 伺服器，因此呼叫是非同步，而且需要錯誤處理。

#### <a name="metadata"></a>中繼資料

CloudKit 中的每個使用者都有描述這些使用者的特定中繼資料。 此中繼資料會表示為 CloudKit 記錄：

 [![CloudKit 中的每個使用者都有特定的中繼資料來描述它們](intro-to-cloudkit-images/image41.png)](intro-to-cloudkit-images/image41.png#lightbox)

在私用資料庫中查看容器的特定使用者，有一筆記錄定義該使用者。 公用資料庫中有許多使用者記錄，容器的每個使用者都有一個記錄。 其中一個記錄識別碼會符合目前登入使用者的記錄識別碼。

公開資料庫中的使用者記錄是可讀取的。 在大部分的情況下，系統會將它們視為一般記錄並具有的型別 `CKRecordTypeUserRecord` 。 這些記錄是由系統所保留，並不適用于查詢。

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

上述程式碼會要求公用資料庫針對我們在上方存取的識別碼，傳回使用者記錄。 由於這項資訊來自 iCloud 伺服器，因此呼叫是非同步，而且需要錯誤處理。

#### <a name="privacy"></a>隱私權

根據預設，CloudKit 是設計來保護目前登入之使用者的隱私權。 預設不會公開使用者的個人識別資訊。 在某些情況下，應用程式會要求使用者提供有限的資訊。

在這些情況下，應用程式可以要求使用者公開這項資訊。 系統會向使用者顯示對話方塊，要求他們加入宣告來公開其帳戶資訊。

#### <a name="discovery"></a>探索

假設使用者已選擇允許應用程式存取其使用者帳戶資訊的有限存取權，則可以對應用程式的其他使用者進行探索：

 [![應用程式的其他使用者可以探索使用者](intro-to-cloudkit-images/image42.png)](intro-to-cloudkit-images/image42.png#lightbox)

用戶端應用程式正在與容器進行通訊，而容器正在討論 iCloud 以存取使用者資訊。 使用者可以提供電子郵件地址，而且可以使用探索來取得使用者的相關資訊。 （選擇性）使用者識別碼也可以用來探索使用者的相關資訊。

CloudKit 也提供一種方法來探索任何使用者的相關資訊，這些使用者可能是目前登入 iCloud 使用者的朋友，方法是查詢整個通訊錄。 CloudKit 程式將會提取使用者的連絡人書籍，並使用電子郵件地址來查看是否可以找到符合這些位址的其他使用者應用程式。

這可讓應用程式利用使用者的連絡人書籍，而不需要提供其存取權或要求使用者核准連絡人的存取權。 在此情況下，應用程式提供的連絡人資訊沒有任何時間，只有 CloudKit 程式才有存取權。

要回顧，有三種不同類型的輸入可供使用者探索使用：

- **使用者記錄識別碼** –可針對目前登入之 CloudKit 使用者的使用者識別碼來進行探索。
- **使用者電子郵件地址** –使用者可以提供電子郵件地址，並可用於探索。
- **Contact book** –使用者的通訊錄可以用來探索應用程式的使用者，該應用程式的電子郵件地址會列在其連絡人中。

使用者探索將會傳回下列資訊：

- **使用者記錄識別碼** -公用資料庫中使用者的唯一識別碼。
- **First 和 Last Name** -儲存在公用資料庫中。

此資訊只會針對已加入探索的使用者傳回。

下列程式碼將探索裝置上目前登入 iCloud 的使用者相關資訊：

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

使用下列程式碼查詢 Contact Book 中的所有使用者：

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

在本節中，我們涵蓋了 CloudKit 可提供給應用程式之使用者帳戶的四個主要區域存取權。 從取得使用者的身分識別和中繼資料，到 CloudKit 內建的隱私權原則，最後是探索應用程式其他使用者的能力。

## <a name="the-development-and-production-environments"></a>開發和生產環境

CloudKit 為應用程式的記錄類型和資料提供個別的開發和生產環境。 開發環境是更有彈性的環境，只適用于開發小組的成員。 當應用程式將新欄位加入至記錄，並將該記錄儲存在開發環境中時，伺服器會自動更新架構資訊。

開發人員可以使用這項功能，在開發期間對架構進行變更，這樣可節省時間。 有一點要注意的是，在將欄位加入至記錄之後，與該欄位相關聯的資料類型就無法以程式設計方式變更。 若要變更欄位的類型，開發人員必須刪除 [CloudKit 儀表板](https://icloud.developer.apple.com/dashboard/) 中的欄位，然後使用新的類型重新加入該欄位。

在部署應用程式之前，開發人員可以使用 **CloudKit 儀表板**，將其架構和資料移轉到生產環境。 針對生產環境執行時，伺服器會防止應用程式以程式設計方式變更架構。 開發人員仍可使用 **CloudKit 儀表板** 進行變更，但嘗試在生產環境中將欄位新增至記錄會導致錯誤。

> [!NOTE]
> IOS 模擬器只適用于 **開發環境**。 當開發人員準備好在 **生產環境**中測試應用程式時，需要實體 iOS 裝置。

## <a name="shipping-a-cloudkit-enabled-app"></a>傳送啟用 CloudKit 的應用程式

在出貨使用 CloudKit 的應用程式之前，必須先將其設定為以 **生產 CloudKit 環境** 為目標，否則 Apple 會拒絕應用程式。

執行下列動作：

1. 在 Ma 的 Visual Studio 中，為**Release**  >  **iOS 裝置**編譯應用程式：

    [![編譯應用程式以供發行](intro-to-cloudkit-images/shipping01.png)](intro-to-cloudkit-images/shipping01.png#lightbox)

2. 從 [ **組建** ] 功能表中 **，選取 [** 封存：

    [![選取封存](intro-to-cloudkit-images/shipping02.png)](intro-to-cloudkit-images/shipping02.png#lightbox)

3. 封存 **將會在 Visual Studio for Mac** 中建立並顯示：

    [![將會建立並顯示封存](intro-to-cloudkit-images/shipping03.png)](intro-to-cloudkit-images/shipping03.png#lightbox)

4. 開始 **Xcode**。
5. 從 [ **視窗]** 功能表中選取 [ **召集人**]：

    [![選取召集人](intro-to-cloudkit-images/shipping04.png)](intro-to-cloudkit-images/shipping04.png#lightbox)

6. 選取應用程式的封存，然後按一下 [ **匯出 ...** ] 按鈕：

    [![應用程式的封存](intro-to-cloudkit-images/shipping05.png)](intro-to-cloudkit-images/shipping05.png#lightbox)

7. 選取匯出的方法，然後按 [ **下一步]** 按鈕：

    [![選取匯出的方法](intro-to-cloudkit-images/shipping06.png)](intro-to-cloudkit-images/shipping06.png#lightbox)

8. 從下拉式清單中選取 **開發小組** ，然後按一下 [ **選擇** ] 按鈕：

    [![從下拉式清單中選取開發小組](intro-to-cloudkit-images/shipping07.png)](intro-to-cloudkit-images/shipping07.png#lightbox)

9. 從下拉式清單中選取 [ **生產** ]，然後按 [ **下一步]** 按鈕：

    [![從下拉式清單中選取 [生產]](intro-to-cloudkit-images/shipping08.png)](intro-to-cloudkit-images/shipping08.png#lightbox)

10. 檢查設定，然後按一下 [ **匯出** ] 按鈕：

    [![檢查設定](intro-to-cloudkit-images/shipping09.png)](intro-to-cloudkit-images/shipping09.png#lightbox)

11. 選擇要產生應用程式檔的位置 `.ipa` 。

此程式類似于直接將應用程式提交至 iTunes Connect，只需按一下 [ **提交** ] 按鈕，而不是 [匯出 ...]在 [召集人] 視窗中選取封存之後。

## <a name="when-to-use-cloudkit"></a>使用 CloudKit 的時機

如本文所示，CloudKit 提供一個簡單的方法，讓應用程式儲存及取出 iCloud 伺服器的資訊。 話雖如此，CloudKit 並不會淘汰或取代任何現有的工具或架構。

### <a name="use-cases"></a>使用案例

下列使用案例應可協助開發人員決定何時要使用特定的 iCloud 架構或技術：

- **iCloud 索引鍵/值存放區** –以非同步方式將少量的資料保持在最新狀態，而且很適合用來處理應用程式喜好設定。 不過，它受限於非常少量的資訊。
- **ICloud 磁片磁碟機** –建置於現有的 ICloud 檔 api 之上，並提供簡單的 API 來同步處理檔案系統中的非結構化資料。 它提供 Mac OS X 的完整離線快取，適用于以檔為主的應用程式。
- **ICloud 核心資料** –允許在所有使用者的裝置之間複寫資料。 資料是單一使用者，而且很適合用來保持私人、結構化資料的同步。
- **CloudKit** –提供結構和大量的公用資料，而且能夠處理大型資料集和大型非結構化檔案。 它系結至使用者的 iCloud 帳戶，並提供用戶端導向的資料傳輸。

請記住這些使用案例，開發人員應該挑選正確的 iCloud 技術，以提供目前必要的應用程式功能，並為未來的成長提供良好的擴充性。

## <a name="summary"></a>摘要

本文涵蓋了 CloudKit API 的快速簡介。 它已示範如何布建及設定 Xamarin iOS 應用程式以使用 CloudKit。 其中涵蓋了 CloudKit 便利性 API 的功能。 它說明如何使用查詢和訂閱來設計可調整規模的 CloudKit 應用程式。 最後它已顯示 CloudKit 公開給應用程式的使用者帳戶資訊。

## <a name="related-links"></a>相關連結

- [CloudKit (Apple) ](https://developer.apple.com/icloud/cloudkit/)
- [CloudKitAtlas (範例) ](/samples/xamarin/ios-samples/ios8-cloudkitatlas)
- [建立布建設定檔](~/ios/get-started/installation/device-provisioning/index.md)