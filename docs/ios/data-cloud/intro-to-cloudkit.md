---
title: CloudKit
description: "iCloud Api 可讓 iOS 8 的應用程式將資料儲存在 icloud 的功能，以支援透過使用者的帳戶自動同步處理。 使用 CloudKit 可提供使用者一致、 順暢的體驗跨 icloud 的功能已啟用的裝置。 本文件涵蓋使用便利性 API iOS 8 應用程式中啟用 CloudKit。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 66B207F2-FAA0-4551-B43B-3DB9F620C397
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/11/2016
ms.openlocfilehash: c4ee5c0457dd1faea74cbbc30dd2d0f42087a8d0
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2018
---
# <a name="cloudkit"></a>CloudKit

_iCloud Api 可讓 iOS 8 的應用程式將資料儲存在 icloud 的功能，以支援透過使用者的帳戶自動同步處理。使用 CloudKit 可提供使用者一致、 順暢的體驗跨 icloud 的功能已啟用的裝置。本文件涵蓋使用便利性 API iOS 8 應用程式中啟用 CloudKit。_

CloudKit 架構來簡化應用程式的開發該存取 icloud 的功能。 這包括應用程式資料和資產的權限，以及能夠以安全地儲存應用程式資訊的擷取。 此套件可讓使用者的匿名層藉由允許存取其 iCloud Id 的應用程式，而不需要共用個人資訊。

開發人員可以專注於其用戶端應用程式，並讓 icloud 的功能，即使不需要撰寫伺服器端應用程式邏輯。 CloudKit 提供驗證、 私人和公用資料庫和結構化的資料和資產的儲存體服務。

## <a name="requirements"></a>需求

需要下列項目才能完成本文章中呈現的步驟：

-  **Xcode 和 iOS SDK** – Apple 安裝 Xcode 和 iOS 8 應用程式開發介面需要安裝和設定開發人員的電腦上。
-  **Visual Studio for Mac** – 應安裝及設定使用者裝置上最新版本的 Visual Studio for Mac。
-  **iOS 8 裝置**– 執行來測試 iOS 8 的最新版本的 iOS 裝置。

## <a name="what-is-cloudkit"></a>什麼是 CloudKit？

CloudKit 是一種方法讓開發人員存取至 icloud 的功能的伺服器。 它提供的基礎 iCloud 磁碟機和 iCloud 相片程式庫。 Mac OS X 和 Apple iOS 裝置支援 CloudKit。

 [![](intro-to-cloudkit-images/image1.png "如何在 Mac OS X 和 Apple iOS 裝置上支援 CloudKit")](intro-to-cloudkit-images/image1.png#lightbox)

CloudKit 使用 iCloud 帳戶基礎結構。 如果沒有使用者登入的 iCloud 帳戶在裝置上，CloudKit 會使用其識別碼來識別使用者。 如果沒有帳戶可供使用，系統會提供有限的唯讀存取。

CloudKit 支援公用和私用資料庫的概念。 公用資料庫提供 「 soup"的使用者具有存取權的所有資料。 私用的資料庫是用來儲存繫結至特定使用者的私用資料。

CloudKit 支援結構化和大量資料。 它是能夠順暢地處理大型檔案傳輸。 CloudKit 會負責有效率地傳送大型檔案從 iCloud 伺服器在背景中，釋放開發人員專注於其他工作。

> [!NOTE]
> 請務必注意 CloudKit_傳輸技術_。 它並不提供任何持續性;它只會讓應用程式傳送和有效率地從伺服器接收資訊。

撰寫本文時，Apple 一開始提供 CloudKit 免費的高頻寬和儲存體容量限制。 較大的專案或應用程式具有大型使用者基底，Apple 已有所提示將會提供合理的定價小數位數。


## <a name="enabling-cloudkit-in-a-xamarin-application"></a>Xamarin 應用程式中啟用 CloudKit

Xamarin 應用程式可以利用 CloudKit Framework 之前，應用程式必須正確佈建中所詳述[功能使用](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)和[使用權利](~/ios/deploy-test/provisioning/entitlements.md)輔助線

1.  Mac 或 Visual Studio 中開啟 Visual Studio 中的專案。
2.  在**方案總管 中**，開啟**Info.plist**檔案，並確定**配套識別碼**符合已定義的一個**應用程式識別碼**建立佈建的部分設定如下：
 
    [![](intro-to-cloudkit-images/image26a.png "輸入的套件組合識別碼")](intro-to-cloudkit-images/image26a-orig.png#lightbox "Info.plist file displaying Bundle Identifier")

3.  捲動到底部**Info.plist**檔案，然後選取**啟用背景模式**，**位置更新**和**遠端通知**:

    [![](intro-to-cloudkit-images/image27a.png "選取已啟用的背景模式、 位置更新和遠端的通知")](intro-to-cloudkit-images/image27a-orig.png#lightbox "Info.plist file displaying background modes")
4.  以滑鼠右鍵按一下方案、 選取中的 iOS 專案**選項**。
5.  選取**iOS 套件組合簽署**，選取**開發人員識別**和**佈建設定檔**上面所建立。
6.  請確定**Entitlements.plist**包含**啟用 iCloud** ，**機碼值的儲存體**和**CloudKit** 。
7.  請確定**普遍性而益發嚴重容器**存在應用程式 （如上面所建立）。 範例：`iCloud.com.your-company.CloudKitAtlas`
8.  將變更儲存到檔案。


這些設定，應用程式現在已準備好存取 CloudKit 架構應用程式開發介面。

## <a name="cloudkit-api-overview"></a>CloudKit API 概觀

在實作之前 CloudKit Xamarin iOS 應用程式中，這篇文章會涵蓋 CloudKit 架構，其中包含下列主題的基本概念：

1.  **容器**– 隔離定址接收器的 iCloud 通訊。
2.  **資料庫**– 公用和私用可用應用程式。
3.  **記錄**– 結構化的資料中移入及從 CloudKit 的機制。
4.  **記錄區域**– 是群組的記錄。
5.  **記錄識別碼**-完全正規化，並表示特定記錄的位置。
6.  **參考**– 提供在給定資料庫內的相關記錄之間的關聯性的父-子系。
7.  **資產**– 允許的大型、 非結構化資料上傳到 icloud 的功能，並指定記錄相關聯的檔案。


### <a name="containers"></a>容器

一律執行指定的 iOS 裝置上執行的應用程式以及其他應用程式和服務，該裝置上的側邊。 在用戶端裝置上，應用程式將要筒倉式或以某種方式沙箱化。 在某些情況下，這是常值的沙箱，而且在其他應用程式是只在它執行自己的記憶體空間。

取得用戶端應用程式，然後執行其他用戶端從分隔的概念是非常強大，並提供下列優點：

1.  **安全性**– 一個應用程式不會干擾其他用戶端應用程式或作業系統本身。
1.  **穩定性**– 如果用戶端應用程式當機，它無法取出作業系統的其他應用程式。
1.  **隱私權**– 每個用戶端應用程式具有有限的儲存裝置中的個人資訊的存取權。


CloudKit 是設計用來提供相同的優點，如上述所列，並將它們套用至使用雲端架構的資訊：

 [![](intro-to-cloudkit-images/image31.png "使用容器 CloudKit 應用程式進行通訊")](intro-to-cloudkit-images/image31.png#lightbox)

如同應用程式正在的一對多裝置上執行，所以使用 iCloud 的一對多應用程式的通訊。 每個這些不同的通訊定址接收器稱為容器。

容器會公開在 CloudKit 架構透過`CKContainer`類別。 根據預設，一個應用程式與一個容器和此容器隔離，並為該應用程式資料。 這表示相同的 iCloud 帳戶的資訊可以儲存數個應用程式，但絕不會混合這項資訊。

ICloud 資料得以也可讓 CloudKit 封裝使用者資訊。 如此一來，應用程式將會有某些有限的存取權的 iCloud 帳戶和使用者資訊儲存在同時仍受到保護的隱私權和使用者的安全性。

容器會完全管理透過 WWDR 入口網站應用程式的開發人員。 容器的命名空間是跨所有 Apple 開發人員共用，因此容器不才必須是唯一指定開發人員應用程式，但所有 Apple 開發人員和應用程式。

Apple 建議為應用程式容器中建立命名空間時，請使用反向 DNS 標記法。 範例：

```csharp
iCloud.com.company-name.application-name
```

雖然容器，根據預設，繫結至指定的應用程式的一對一，它們可以跨應用程式共用。 讓多個應用程式可以在單一容器上協調。 單一應用程式也可以向多個容器。

### <a name="databases"></a>資料庫

其中一個 CloudKit 的主要功能是讓應用程式的資料模型和複寫該模型到 iCloud 伺服器。 某些資訊適用於建立它的使用者、 其他資訊是公用的資料，無法由使用者提供公用用途 （例如 餐廳評論），或可能是開發人員已發行應用程式的資訊。 在任一情況下，對象不只是單一使用者，但是一個社群的人員。

 [![](intro-to-cloudkit-images/image32.png "CloudKit 容器圖表")](intro-to-cloudkit-images/image32.png#lightbox)

在容器內第一次和最重要是公用的資料庫。 這是所有公用資訊都位於而共置 mingles。 此外，還有數個個別的私用資料庫應用程式的每位使用者。

IOS 裝置上執行時，應用程式，將只有 iCloud 目前登入的使用者存取的資訊。 讓應用程式的容器的檢視會顯示如下所示：

 [![](intro-to-cloudkit-images/image33.png "容器的 [應用程式] 檢視")](intro-to-cloudkit-images/image33.png#lightbox)

它只可以看到公用資料庫和私用與目前登入 iCloud 使用者相關聯的資料庫。

資料庫會公開在 CloudKit 架構透過`CKDatabase`類別。 每個應用程式具有兩個資料庫權限： 公用資料庫和私密金鑰。

容器是 CloudKit 初始的進入點。 下列程式碼可以用來從應用程式的預設容器存取公用和私用的資料庫：

```csharp
using CloudKit;
...

public CKDatabase PublicDatabase { get; set; }
public CKDatabase PrivateDatabase { get; set; }
...

// Get the default public and private databases for
// the application
PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;
```

以下是資料庫類型之間的差異：

||公用資料庫|私用的資料庫|
|---|--- |--- |
|**資料類型**|共用的資料|目前使用者的資料|
|**配額**|開發人員的配額中說明|使用者的配額中說明|
|**預設權限**|可讀取的世界|使用者可讀取|
|**編輯權限**|資料錄類別層級透過 iCloud 儀表板角色|N/A|

### <a name="records"></a>資料錄

容器保存的資料庫，並在資料庫內不會有記錄。 記錄是結構化的資料中移入及從 CloudKit 的機制：

 [![](intro-to-cloudkit-images/image34.png "容器保存的資料庫，並在資料庫內不會有記錄")](intro-to-cloudkit-images/image34.png#lightbox)

記錄會公開在 CloudKit 架構透過`CKRecord`類別，包裝了多索引鍵-值組。 應用程式中的物件執行個體相當於`CKRecord`CloudKit 中。 此外，每個`CKRecord`擁有記錄類型，相當於物件的類別。

讓資料描述為 CloudKit 之前發出處理記錄都有在 just-in-time 結構描述。 從該點，CloudKit 將資訊解譯並處理儲存和擷取記錄的邏輯。

`CKRecord`類別也支援各種不同的中繼資料。 例如，記錄會包含建立時，建立它的使用者相關資訊。 記錄也會包含上次修改並修改它的使用者相關資訊。

記錄包含變更標記的概念。 這是修訂的舊版的特定記錄。 變更標記用做為輕量的方式判斷是否會在用戶端和伺服器具有相同版本的特定記錄。

如上所述，`CKRecords`包裝金鑰-值配對，因此，下列類型的資料可以儲存在記錄：

1.   `NSString`
1.   `NSNumber`
1.   `NSData`
1.   `NSDate`
1.   `CLLocation`
1.   `CKReferences`
1.   `CKAssets`


單一數值類型，除了記錄可以包含任何上述列出的類型的同質性陣列。

下列程式碼可用來建立新的記錄，並將它儲存在資料庫中：

```csharp
using CloudKit;
...

private const string ReferenceItemRecordName = "ReferenceItems";
...

var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;
await CloudManager.SaveAsync (newRecord);
```

### <a name="record-zones"></a>記錄的區域

記錄不存在自己本身全部放在給定資料庫內 – 記錄群組在一起存在記錄區域內。 記錄區域可以視為資料表傳統關聯式資料庫中：

 [![](intro-to-cloudkit-images/image35.png "記錄群組在一起存在記錄區域內")](intro-to-cloudkit-images/image35.png#lightbox)

可以在給定的記錄區域中的多筆記錄，在給定資料庫內的多個記錄區域。 每個資料庫包含預設記錄區域：

 [![](intro-to-cloudkit-images/image36.png "每個資料庫包含的預設記錄區域和自訂區域")](intro-to-cloudkit-images/image36.png#lightbox)

這是預設記錄的儲存位置。 此外，您可以建立自訂記錄區域。 記錄是不可部分完成的認可和變更追蹤的基底資料粒度的區域代表。

## <a name="record-identifiers"></a>記錄識別碼

記錄的識別項會表示當做元組、 包含這兩個用戶端提供的記錄名稱和區域有記錄存在。 記錄識別碼具有下列特性：

-  由用戶端應用程式所建立。
-  它們完全正規化，而且代表記錄的特定位置。
-  藉由指派成記錄名稱的外部資料庫中記錄的唯一識別碼，它們可以用於橋接 CloudKit 內未儲存的本機資料庫。


當開發人員建立新的記錄時，他們可以選擇將記錄識別項。 如果未指定記錄識別碼，UUID 會自動建立並指派給記錄。

當開發人員建立新的記錄識別碼時，他們可以選擇指定每一筆記錄均屬於記錄區域。 如果未指定，則將使用預設記錄區域。

記錄識別碼會公開在 CloudKit 架構透過`CKRecordID`類別。 下列程式碼可以用來建立新的記錄識別碼：

```csharp
var recordID =  new CKRecordID("My Record");
```

### <a name="references"></a>參考

參考會提供在給定資料庫內的相關記錄之間的關聯性：

 [![](intro-to-cloudkit-images/image37.png "參考會提供在給定資料庫內的相關記錄之間的關聯性")](intro-to-cloudkit-images/image37.png#lightbox)

在上述範例中，父項擁有子系，所以子系是父資料錄的子記錄。 關聯性會從子記錄移至父記錄和指*回參考*。

參考會公開在 CloudKit 架構透過`CKReference`類別。 它們是一種讓 iCloud 伺服器了解記錄之間的關聯性。

參考提供串聯刪除背後的機制。 如果從資料庫刪除父記錄，從資料庫也將自動刪除 （如指定關聯性中） 的任何子記錄。

> [!NOTE]
> CloudKit 時，則懸空指標有可能。 例如，應用程式已擷取的記錄指標的清單，選取 一筆記錄，而且然後要求記錄的時間，記錄可能不存在資料庫中。 應用程式必須予以編碼，正常處理這種情況。

雖然並非必要，傳回參考是慣用使用 CloudKit 架構時。 Apple 已微調系統以最有效率的參考類型。

當建立參考，開發人員可以提供已在記憶體中的記錄或建立記錄識別碼的參考。 如果使用記錄識別碼和指定的參考不存在資料庫中，就會建立懸空的指標。

建立參考，以針對已知的記錄的範例如下：

```csharp
var reference = new CKReference(newRecord, new CKReferenceAction());
```

### <a name="assets"></a>資產

針對大型、 非結構化資料上傳到 icloud 的功能，與指定的記錄相關聯的檔案，允許資產：

 [![](intro-to-cloudkit-images/image38.png "允許檔案的大型、 非結構化資料上傳到 icloud 的功能，與指定的記錄相關聯的資產")](intro-to-cloudkit-images/image38.png#lightbox)

在用戶端，`CKRecord`建立描述要上傳到 iCloud 伺服器上的檔案。 A`CKAsset`是用來包含檔案，並且連結到其描述的記錄。

伺服器上傳檔案時，記錄放在資料庫中，並將檔案複製到特殊的大量儲存體資料庫。 建立的記錄指標和上傳的檔案之間的連結。

資產會公開在 CloudKit 架構透過`CKAsset`類別，並可用來儲存大型、 非結構化資料。 因為開發人員不想要在記憶體中有大型、 非結構化資料，資產是使用磁碟上的檔案來實作。

允許從 iCloud 的指標使用記錄中擷取資產的記錄所擁有的資產。 這種方式在伺服器可以在記憶體回收收集資產時擁有資產記錄會被刪除。

因為`CKAssets`是處理大型資料檔案，Apple 是設計成能夠有效率地將上傳和下載的資產 CloudKit。

下列程式碼可用來建立資產，並將它與記錄產生關聯：

```csharp
var fileUrl = new NSUrl("LargeFile.mov");
var asset = new CKAsset(fileUrl);
newRecord ["name"] = asset;
```

現在我們已涵蓋所有 CloudKit 內的基本物件。 容器應用程式相關聯，而且包含資料庫。 資料庫包含分組為記錄的區域並指向記錄識別碼的記錄。 使用參考資料錄之間定義父子式關聯性。 最後，大型檔案可以上傳，並使用資產記錄相關聯。

## <a name="cloudkit-convenience-api"></a>CloudKit 便利 API

Apple 提供兩個不同 CloudKit 所使用的 API 集：

-  **Operational API** – 提供 CloudKit 的每一個單一的功能。 對於更複雜的應用程式，此 API 會提供 CloudKit 更細微的控制。
-  **便利 API** – 提供常見且預先設定的 CloudKit 功能子集。 它提供方便且簡單的存取方案 CloudKit 功能包括在 iOS 應用程式。


便利 API 通常是大部分的 iOS 應用程式的最佳選擇，Apple 建議與它啟動。 此章節的其餘部分將討論下列便利 API 主題：

-  儲存記錄。
-  擷取資料錄。
-  更新一筆記錄。


### <a name="common-setup-code"></a>安裝程式的通用程式碼

開始使用 CloudKit 便利 API，使用之前沒有所需的一些標準安裝程式碼。 藉由修改應用程式的啟動`AppDelegate.cs`檔案，並讓它看起來如下所示：

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
        #region Computed Properties
        public override UIWindow Window { get; set;}
        public CKDatabase PublicDatabase { get; set; }
        public CKDatabase PrivateDatabase { get; set; }
        #endregion

        #region Override Methods
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
        #endregion
    }
}
```

上述程式碼公開的公用和私用 CloudKit 資料庫以使其更容易使用的應用程式的其餘部分的捷徑。

接下來，加入下列程式碼的任何檢視或檢視的容器將會使用 CloudKit:

```csharp
using CloudKit;
...

#region Computed Properties
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

這會將捷徑移至`AppDelegate`及存取上面所建立的公用和私用的資料庫快速鍵。

這個位置中的程式碼，讓我們來看看 Xamarin iOS 8 應用程式中實作 CloudKit 便利 API。

### <a name="saving-a-record"></a>儲存記錄

使用以上討論記錄時所顯示的模式，下列程式碼會建立新的記錄，並將它儲存到公用資料庫使用便利性 API:

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

請注意上述程式碼相關的三件事：

1.  藉由呼叫`SaveRecord`方法`PublicDatabase`，開發人員不需要指定資料傳送的方式，正在寫入至等哪些區域。便利 API 會處理所有的這些詳細資料本身。
1.  的呼叫是非同步，而且在呼叫完成，請為成功或失敗時提供回呼常式。 如果呼叫失敗，系統會提供錯誤訊息。
1.  CloudKit 不提供本機儲存體/持續性;它是傳送媒體。 因此若要儲存記錄提出要求時立即傳送至 iCloud 伺服器。


> [!NOTE]
> 因為行動網路通訊，其中連接不斷正在卸除或中斷，開發人員必須先使用 CloudKit 錯誤處理時的第一個考量的其中一個 「 遺失 」 的本質。

### <a name="fetching-a-record"></a>擷取資料錄

建立並已成功儲存 iCloud 伺服器上的記錄，以使用下列程式碼來擷取的記錄：

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

如同儲存記錄，上述程式碼會是非同步的簡單，而且需要很好的錯誤處理。

### <a name="updating-a-record"></a>更新資料錄

從 iCloud 伺服器擷取一筆記錄之後，下列程式碼可用來修改記錄，並將變更儲存回資料庫：

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

`FetchRecord`方法`PublicDatabase`傳回`CKRecord`如果呼叫成功。 接著，應用程式修改的記錄和呼叫`SaveRecord`再次將變更寫回資料庫。

本節顯示應用程式處理 CloudKit 便利 API 時，會使用一般循環。 應用程式會將記錄儲存到 icloud 的功能、 擷取這些記錄從 icloud 的功能、 修改的記錄並備份到 iCloud 儲存這些變更。

## <a name="designing-for-scalability"></a>設計延展性

到目前為止此發行項已經考慮過儲存和擷取從 iCloud 伺服器、 應用程式的整個物件模型，每次它即將使用過。 雖然這種方法適用於少量的資料和基底非常小的使用者，它並未妥善延展時的資訊和/或使用者數量基底會增加。

### <a name="big-data-tiny-device"></a>巨量資料，很小的裝置

更常用的應用程式會變成，資料庫中的詳細資料，它是具有該裝置上的整個資料的快取就越不可行。 下列技術可以用來解決此問題：

-  **大型的資料保留在雲端**– CloudKit 設計用來有效率地處理大型資料。
-  **用戶端應該只能檢視該資料的配量**– 將給定的時間處理任何工作所需資料的最低限度關機。
-  **可以變更用戶端檢視**– 因為每個使用者擁有不同的喜好設定、 顯示的資料配量可以變更使用者和使用者的個別檢視任何指定的配量可能會不同。
-  **用戶端會使用查詢專注觀點來看**– 查詢可讓使用者檢視雲端內的一小部分的較大的資料集存在。


### <a name="queries"></a>查詢

如前所述，查詢可讓開發人員選擇較大的資料集，存在於雲端中的小型子集。 查詢會公開在 CloudKit 架構透過`CKQuery`類別。

查詢結合三個不同的項目： 記錄類型 ( `RecordType`)，述詞 ( `NSPredicate`) 和 （選擇性） 排序描述元 ( `NSSortDescriptors`)。 CloudKit 支援大部分`NSPredicate`。

#### <a name="supported-predicates"></a>支援的述詞

CloudKit 支援下列類型的`NSPredicates`處理查詢時：


1. 比對名稱等於儲存在變數值的記錄：
    
    ```
    NSPredicate.FromFormat(string.Format("name = '{0}'", recordName))
    ```
   
2. 可讓如此索引鍵不需要知道在編譯時期動態索引鍵值是根據比對：
    
    ```
    NSPredicate.FromFormat(string.Format("{0} = '{1}'", key, value))
    ```
    
3. 比對記錄的值大於指定值的記錄：
   
    ```
    NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date))
    ```

4. 比對其中記錄的位置是 100 公尺內的指定位置的記錄：
    
    ```
    var location = new CLLocation(37.783,-122.404);
    var predicate = NSPredicate.FromFormat(string.Format("distanceToLocation:fromLocation(Location,{0}) < 100", location));
    ```

5. CloudKit 支援語彙基元化的搜尋。 此呼叫將會建立兩個語彙基元，一個用於`after`，另一個用於`session`。 它會傳回包含這些兩個語彙基元的記錄：
    
    ```
    NSPredicate.FromFormat(string.Format("ALL tokenize({0}, 'Cdl') IN allTokens", "after session"))
    ```
    
 6. CloudKit 支援複合聯結會使用述詞`AND`運算子。
    
    ```
    NSPredicate.FromFormat(string.Format("start > {0} AND name = '{1}'", (NSDate)date, recordName))
    ```
    


#### <a name="creating-queries"></a>建立查詢

下列程式碼可以用來建立`CKQuery`Xamarin iOS 8 應用程式中：

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = '{0}'", recordName));
var query = new CKQuery("CloudRecords", predicate);
```

首先，它會建立一個述詞，選取符合指定名稱的記錄。 然後它會建立查詢，將會選擇符合述詞指定的記錄類型的記錄。

#### <a name="performing-a-query"></a>執行查詢

一旦建立查詢之後，請使用下列程式碼來執行查詢和處理傳回的記錄：

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

上述程式碼會建立上述的查詢，並針對公用資料庫執行。 因為未記錄指定區域，則會搜尋所有區域。 如果沒有發生錯誤，陣列`CKRecords`將會傳回符合查詢的參數。

它們會輪詢，而非常適合用在透過大型資料集配量思考如何查詢的方式。 查詢，不過，不是很適合大型、 大部分靜態的資料集的原因如下：

-  它們是不正確的裝置電池壽命。
-  它們是不正確的網路流量。
-  它們是不正確的使用者經驗，因為他們會看到的資訊會受到頻率的應用程式正在輪詢資料庫。 內容變更時，使用者現在應該推播通知。


### <a name="subscriptions"></a>訂閱

當處理大型、 大部分靜態的資料集時，不應在用戶端裝置上執行查詢時，它應該在代表用戶端上的伺服器上執行。 查詢應該在背景中執行，並儲存每一個單一的記錄，無論是目前的裝置或觸控相同資料庫的另一個裝置之後應該執行。

最後，應該推播通知傳送至執行伺服器端查詢時連接至資料庫的每個裝置。

訂閱會公開在 CloudKit 架構透過`CKSubscription`類別。 結合記錄類型 ( `RecordType`)，述詞 ( `NSPredicate`) 和 Apple 推播通知 ( `Push`)。

> [!NOTE]
> 因為它們包含裝載包含 CloudKit 專屬的資訊，例如造成推入到，就可能發生的原因，會稍微增強 CloudKit 推播通知。

#### <a name="how-subscriptions-work"></a>訂用帳戶的運作方式

在實作之前訂用帳戶在 C# 程式碼，讓我們看訂用帳戶的運作方式的快速概觀：

 [![](intro-to-cloudkit-images/image39.png "一個訂用帳戶的運作方式的概觀")](intro-to-cloudkit-images/image39.png#lightbox)

上面的圖形會顯示一般訂閱程序，如下所示：

1.  用戶端裝置會建立新的訂閱包含的一組將會觸發訂閱，觸發程序時，將會傳送推播通知的條件。
2.  訂用帳戶會傳送到資料庫並新增至現有的訂用帳戶的集合。
3.  第二個裝置建立一個新的記錄，並將該記錄儲存至資料庫。
4.  資料庫會搜尋其新的記錄是否符合其條件的訂用帳戶清單。
5.  如果找到相符項目，推播通知會傳送到裝置的資訊記錄，導致觸發註冊訂用帳戶。


與就地這項知識，讓我們看看建立訂用帳戶中 Xamarin iOS 8 的應用程式。

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

首先，它會建立提供要觸發訂閱的條件的述詞。 接下來，它會建立針對特定的記錄類型的訂用帳戶，並設定觸發程序會進行測試的選項。 最後，它定義訂用帳戶，就會觸發，並將其附加訂用帳戶時，會發生的通知的類型。

### <a name="saving-subscriptions"></a>正在儲存訂用帳戶

建立訂用帳戶，下列程式碼會將其儲存到資料庫：

```csharp
// Save the subscription to the database
ThisApp.PublicDatabase.SaveSubscription(subscription, (s, err) => {
    // Was there an error?
    if (err != null) {

    }
});
```

使用方便 API 時，呼叫是非同步的簡單，並提供簡單的錯誤處理。

#### <a name="handling-push-notifications"></a>處理推播通知

如果開發人員先前已使用 Apple 推播通知 (AP)，產生 CloudKit 通知處理程序應該熟悉。

在`AppDelegate.cs`，覆寫`ReceivedRemoteNotification`類別，如下所示：

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

上述程式碼會要求 CloudKit 剖析成 CloudKit 通知的使用者資訊。 接下來，會與警示相關的擷取資訊。 最後，會測試通知的類型，並據以處理通知。

本節顯示如何回答巨量資料、 查詢和訂用帳戶使用以上所顯示的小裝置問題。 應用程式會將其大型的資料保留在雲端中，並使用這些技術提供插入此資料集的檢視。

## <a name="cloudkit-user-accounts"></a>CloudKit 使用者帳戶

如同本文開頭所述，CloudKit 是建置在現有的 iCloud 基礎結構之上。 下一節將討論，在詳細資料，使用 CloudKit API 的開發人員如何公開帳戶。

### <a name="authentication"></a>驗證

當處理使用者帳戶，第一個考量是驗證。 CloudKit 在裝置上支援透過 iCloud 目前登入的使用者進行驗證。 驗證會在幕後進行，而且由 iOS。 如此一來，開發人員不需要實作驗證的詳細資訊。 它們只測試以查看使用者是否登入。

### <a name="user-account-information"></a>使用者帳戶資訊

CloudKit 開發人員提供下列的使用者資訊：

-  **識別**– 用來唯一識別使用者的方式。
-  **中繼資料**– 儲存和擷取使用者的相關資訊的能力。
-  **隱私權**– 隱私權做出明智的 manor 中處理所有的資訊。 除非使用者同意，公開執行任何動作。
-  **探索**– 可讓使用者以探索要使用相同的應用程式的朋友。


接下來，我們將探討這些主題的詳細資料。

#### <a name="identity"></a>身分識別

如前所述，CloudKit 會提供方法來唯一識別指定之使用者的應用程式：

 [![](intro-to-cloudkit-images/image40.png "唯一您識別指定的使用者")](intro-to-cloudkit-images/image40.png#lightbox)

沒有使用者的裝置和所有 CloudKit 容器內的特定使用者的私用資料庫上執行的用戶端應用程式。 用戶端應用程式要連結至這些特定使用者的其中一個。 這根據使用者登入裝置本機上 iCloud。

這來自 icloud 的功能，因為沒有 rich 備份存放區的使用者資訊。 和 iCloud 實際上主控容器，因為它可以相互關聯的使用者。 在上述的圖中，使用者的 iCloud 帳戶`user@icloud.com`連結至目前的用戶端。

針對容器的容器，唯一的、 隨機產生的使用者識別碼建立並在使用者的 iCloud 帳戶 （電子郵件地址） 相關聯。 此使用者 ID 傳回至應用程式，並可用於任何開發人員得以透過適當的方式。

> [!NOTE]
> 相同的 iCloud 使用者相同的裝置上執行的不同應用程式將會有不同的使用者識別碼，因為連線到不同的 CloudKit 容器。

下列程式碼會取得 CloudKit 使用者識別碼的目前登入 iCloud 使用者在裝置上：

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

上述程式碼正在詢問 CloudKit 容器，以提供目前登入使用者的識別碼。 由於這項資訊來自伺服器 icloud 的功能，呼叫就是非同步，而且就需要錯誤處理。

#### <a name="metadata"></a>中繼資料

CloudKit 中的每個使用者擁有特定描述它們的中繼資料。 此中繼資料被以 CloudKit 記錄：

 [![](intro-to-cloudkit-images/image41.png "CloudKit 中的每個使用者擁有特定描述它們的中繼資料")](intro-to-cloudkit-images/image41.png#lightbox)

尋找資料庫內部的私用容器有的特定使用者會定義該使用者的一筆記錄。 有許多容器的每個使用者一張在公用資料庫內的使用者記錄。 下列其中一種會有一個記錄識別碼相符目前登入使用者的記錄識別碼。

公用資料庫中的使用者記錄是可讀取的世界。 它們會被視為，針對大部分情況下，為一般的記錄和型別為`CKRecordTypeUserRecord`。 這些記錄由系統保留，並不是可供查詢。

您可以使用下列程式碼來存取使用者記錄：

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

上述程式碼正在詢問要傳回的使用者，是我們存取上述識別碼之使用者記錄之公用資料庫。 由於這項資訊來自伺服器 icloud 的功能，呼叫就是非同步，而且就需要錯誤處理。

#### <a name="privacy"></a>隱私權

CloudKit 所設計，根據預設，來保護目前登入使用者的隱私權。 沒有使用者的個人識別資訊會公開預設。 有某些情況下，應用程式需要使用者的有限的資訊。

在這些情況下，應用程式可以要求使用者揭露此項資訊。 對話方塊將會向使用者要求他們加入以公開其帳戶資訊。

#### <a name="discovery"></a>探索

假設您所選擇以允許應用程式的使用者限制其使用者帳戶資訊的存取權，它們是可搜尋應用程式的其他使用者：

 [![](intro-to-cloudkit-images/image42.png "使用者可以是可探索與其他使用者的應用程式")](intro-to-cloudkit-images/image42.png#lightbox)

一種容器，用戶端應用程式進行通訊，容器正與其溝通 iCloud 存取使用者資訊。 使用者可以提供電子郵件地址，並探索可用來取得回使用者的相關資訊。 或者，使用者識別碼也可用來探索使用者的相關資訊。

CloudKit 也提供方法來探索資訊的任何使用者都可能是 friend 目前登入 iCloud 使用者藉由查詢整個通訊錄。 CloudKit 程序將使用者的連絡人活頁簿中提取，並使用電子郵件地址請參閱是否可以找到其他使用者的應用程式符合這些位址。

這可讓應用程式以利用使用者的連絡人活頁簿而不需提供其存取權，或要求使用者核准的存取權之連絡人。 在任何時間的連絡資訊開放給應用程式中，只有 CloudKit 程序具有存取權。

若要複習，有三種不同的輸入適用於使用者探索：

-  **使用者記錄識別碼**– 探索，才可以針對使用者識別碼的目前登入 CloudKit 使用者。
-  **使用者電子郵件地址**– 使用者可提供電子郵件地址，並用於探索。
-  **請連絡書籍**– 在使用者通訊錄可以用來探索使用者的應用程式具有相同電子郵件地址，如下所示的連絡人。


使用者探索將會傳回下列資訊：

-  **使用者記錄識別碼**-公用資料庫中使用者的唯一識別碼。
-  **第一個和最後一個名稱**-公用資料庫中儲存。


這項資訊將只會傳回使用者的選擇為探索。

下列程式碼會探索目前登入至 icloud 的功能在裝置上的相關資訊：

```csharp
public CKDiscoveredUserInfo UserInfo { get; set; }
...

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

您可以使用下列程式碼來查詢連絡人活頁簿中的所有使用者：

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

本節中，我們已涵蓋 CloudKit 可以提供給應用程式的使用者帳戶的存取權的四個主要區域。 取得使用者的身分識別和中繼資料，隱私權原則的內建 CloudKit，以及最終的探索應用程式的其他使用者的能力。

## <a name="the-development-and-production-environments"></a>開發和生產環境

CloudKit 提供個別的開發和生產環境應用程式的記錄類型和資料。 在開發環境是更具彈性的環境，僅適用於開發小組的成員。 當應用程式會將新欄位加入至記錄，並將該記錄儲存在開發環境中時，伺服器會自動更新結構描述資訊。

開發人員可以使用這項功能，來進行結構描述中的變更，在開發期間，以節省時間。 必須注意的是，已加入至記錄的欄位之後，該欄位相關聯的資料類型無法變更以程式設計的方式。 若要變更欄位的類型，開發人員必須先刪除中的欄位[CloudKit 儀表板](https://icloud.developer.apple.com/dashboard/)並將其再次加入與新的類型。

之前部署應用程式，開發人員可以將移轉其結構描述和資料至生產環境使用**CloudKit 儀表板**。 當執行針對實際執行環境時，伺服器會防止應用程式以程式設計方式變更結構描述。 開發人員仍然可以變更**CloudKit 儀表板**，但嘗試將欄位加入至生產環境中的結果錯誤中的記錄。

> [!NOTE]
> IOS 模擬器只適用於**開發環境**。 準備進行測試中的應用程式開發人員時**實際執行環境**，無須實體 iOS 裝置。


## <a name="shipping-a-cloudkit-enabled-app"></a>傳送 CloudKit 啟用應用程式

使用 CloudKit 的應用程式之前，它必須設定目標**生產 CloudKit 環境**或應用程式將會拒絕的 Apple。

請執行下列動作：

1. 在 Visual Studio 中的 Ma，編譯的應用程式**發行** > **iOS 裝置**: 

    [![](intro-to-cloudkit-images/shipping01.png "編譯應用程式版本")](intro-to-cloudkit-images/shipping01.png#lightbox)

2. 從**建置**功能表上，選取**封存**: 

    [![](intro-to-cloudkit-images/shipping02.png "選取 封存")](intro-to-cloudkit-images/shipping02.png#lightbox)

3. **封存**，會建立並顯示在 Visual Studio for Mac: 

    [![](intro-to-cloudkit-images/shipping03.png "會建立並顯示封存")](intro-to-cloudkit-images/shipping03.png#lightbox)

4. 啟動 **Xcode**。
5. 從**視窗**功能表上，選取**召集人**: 

    [![](intro-to-cloudkit-images/shipping04.png "選取 組合管理")](intro-to-cloudkit-images/shipping04.png#lightbox)

6. 選取應用程式封存檔，然後按一下**匯出...**按鈕： 

    [![](intro-to-cloudkit-images/shipping05.png "應用程式封存檔")](intro-to-cloudkit-images/shipping05.png#lightbox)
    
7. 選取匯出的方法，然後按一下**下一步**按鈕： 

    [![](intro-to-cloudkit-images/shipping06.png "選取匯出的方法")](intro-to-cloudkit-images/shipping06.png#lightbox)

8. 選取**開發團隊**從下拉式清單按一下**選擇**按鈕： 

    [![](intro-to-cloudkit-images/shipping07.png "從下拉式清單中選取的開發小組")](intro-to-cloudkit-images/shipping07.png#lightbox)

9. 選取**生產**從下拉式清單按一下**下一步**按鈕： 

    [![](intro-to-cloudkit-images/shipping08.png "從下拉式清單選取生產環境")](intro-to-cloudkit-images/shipping08.png#lightbox)

10. 檢閱設定，並按一下**匯出**按鈕： 

    [![](intro-to-cloudkit-images/shipping09.png "檢閱的設定")](intro-to-cloudkit-images/shipping09.png#lightbox)

11. 選擇要產生結果的應用程式的位置`.ipa`檔案。

此處理序送出應用程式直接 iTunes Connect 類似，只要按一下**送出...**而不是匯出 組合管理 視窗中選取封存之後，... 按鈕。

## <a name="when-to-use-cloudkit"></a>何時使用 CloudKit

如我們所見本文中，CloudKit 會提供一個簡單的方法來儲存和擷取資訊從 iCloud 伺服器應用程式。 也就是說，CloudKit 不過時或已被取代的任何現有的工具或架構。

### <a name="use-cases"></a>使用案例

下列使用案例應該可以協助開發人員決定何時使用特定 iCloud framework 或技術：

-  **iCloud 鍵值存放區**-以非同步方式保留少量資料的最新且最適合用於使用應用程式喜好設定。 不過，它被限制為非常少量的資訊。
-  **磁碟機 iCloud** – 之上現有 icloud 的功能文件應用程式開發介面，並提供一個簡單的 API，將從檔案系統的非結構化的資料同步。 它提供在 Mac OS X 的完整離線快取，並且適合用於文件中心的應用程式。
-  **iCloud 的核心資料**– 可讓所有使用者的裝置之間都複寫資料。 單一使用者，並保留私用、 結構化資料同步處理對資料。
-  **CloudKit** – 提供這兩種結構的公用資料和大量和能夠處理大型資料集和大型的非結構化的檔案。 其繫結到使用者的 iCloud 帳戶，並提供用戶端導向的資料傳輸。


保留這些使用案例，請注意，開發人員就應該挑選正確 iCloud 技術來提供這兩個目前必要的應用程式的功能，並提供良好的可調適性，供未來成長。

## <a name="summary"></a>總結

這篇文章已涵蓋 CloudKit API 的快速簡介。 它示範了如何佈建及設定使用 CloudKit Xamarin iOS 應用程式。 它已涵蓋 CloudKit 方便應用程式開發介面的功能。 它有顯示如何設計 CloudKit 啟用應用程式使用查詢和訂用帳戶的延展性。 而且它最後顯示 CloudKit 所公開的應用程式的使用者帳戶資訊。

## <a name="related-links"></a>相關連結

- [CloudKitAtlas （範例）](https://developer.xamarin.com/samples/monotouch/ios8/CloudKitAtlas/)
- [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)
- [建立佈建設定檔](~/ios/get-started/installation/device-provisioning/index.md)
