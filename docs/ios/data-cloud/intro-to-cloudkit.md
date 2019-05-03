---
title: 在 Xamarin.iOS 中 CloudKit
description: 本文件說明如何使用 CloudKit 在 Xamarin.iOS 中。 它提供 CloudKit 的概觀，並討論如何啟用它、 CloudKit 便利 API、 延展性、 使用者帳戶，以及開發和生產環境。
ms.prod: xamarin
ms.assetid: 66B207F2-FAA0-4551-B43B-3DB9F620C397
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/11/2016
ms.openlocfilehash: daea27472ac7c0578c1cfd79ebd96428212fafb3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61165297"
---
# <a name="cloudkit-in-xamarinios"></a>在 Xamarin.iOS 中 CloudKit

此 CloudKit 架構簡化了應用程式的開發該存取 iCloud。 這包括擷取應用程式資料和資產的權限，以及能夠安全地儲存應用程式資訊。 此套件可讓使用者的匿名層藉由允許其 iCloud 識別碼的應用程式的存取，而不需要共用個人資訊。

開發人員可以專注於其用戶端應用程式，並可讓不需要撰寫伺服器端應用程式邏輯的 iCloud。 CloudKit 提供驗證、 私人和公用資料庫和結構化的資料和資產的儲存體服務。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)協助開發人員適當地處理歐盟一般資料保護規定 (GDPR)。

## <a name="requirements"></a>需求

需要下列項目來完成這篇文章所述的步驟：

-  **Xcode 和 iOS SDK** – Apple 的 Xcode 和 iOS 8 Api 需要安裝並設定開發人員電腦上。
-  **Visual Studio for Mac** – 應該安裝並設定使用者裝置上的 Visual Studio for Mac 的最新版本。
-  **iOS 8 裝置**– 執行 iOS 8 的測試的最新版本的 iOS 裝置。

## <a name="what-is-cloudkit"></a>CloudKit 是什麼？

CloudKit 是一種方法讓開發人員存取至 icloud 的功能的伺服器。 它提供的基礎 iCloud Drive 和 iCloud 照片圖庫。 CloudKit 支援在 Mac OS X 和 Apple iOS 裝置上。

 [![](intro-to-cloudkit-images/image1.png "如何在 Mac OS X 和 Apple iOS 裝置上支援 CloudKit")](intro-to-cloudkit-images/image1.png#lightbox)

CloudKit 使用 iCloud 帳戶基礎結構。 如果沒有使用者登入 iCloud 帳戶，在裝置上，CloudKit 就會使用其識別碼來識別使用者。 如果沒有帳戶功能，則會提供有限的唯讀存取。

CloudKit 支援這兩個公用和私用資料庫的概念。 公用資料庫提供使用者擁有存取權的所有資料的 「 的詳細 」。 私用的資料庫是用來儲存繫結至特定使用者的私用資料。

CloudKit 支援結構化和大量的資料。 就能夠順暢地處理大型檔案傳輸。 CloudKit 負責有效率地傳送大型檔案從 iCloud 伺服器在背景中，讓開發人員專注於其他工作。

> [!NOTE]
> 請務必請注意，CloudKit_傳輸技術_。 它並不提供任何持續性;它只會讓應用程式傳送及有效率地接收來自伺服器的資訊。

撰寫本文時，Apple 一開始提供 CloudKit 免費的高頻寬和儲存體容量限制。 較大的專案或大型使用者群的應用程式，Apple 已已經提示將會提供經濟實惠的價格小數位數。


## <a name="enabling-cloudkit-in-a-xamarin-application"></a>在 Xamarin 應用程式中啟用 CloudKit

Xamarin 應用程式可以利用 CloudKit 架構之前，應用程式必須將正確佈建中所述[使用功能](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)並[使用權利](~/ios/deploy-test/provisioning/entitlements.md)輔助線

1.  在中開啟專案 Visual Studio for Mac 或 Visual Studio。
2.  在**方案總管**，開啟**Info.plist**檔案，並確定**套件組合識別碼**比對中所定義的一個**應用程式識別碼**建立的佈建的組件設定為：
 
    [![](intro-to-cloudkit-images/image26a.png "輸入套件組合識別碼")](intro-to-cloudkit-images/image26a-orig.png#lightbox "Info.plist file displaying Bundle Identifier")

3.  捲到該頁底端**Info.plist**檔案，然後選取**啟用背景模式**，**位置更新**並**遠端通知**:

    [![](intro-to-cloudkit-images/image27a.png "選取 啟用的背景模式、 位置更新和遠端通知")](intro-to-cloudkit-images/image27a-orig.png#lightbox "Info.plist file displaying background modes")
4.  以滑鼠右鍵按一下 iOS 專案中的方案，然後選取**選項**。
5.  選取  **iOS 套件組合簽署**，選取**開發人員身分識別**並**佈建設定檔**上面所建立。
6.  請確定**Entitlements.plist**包含**啟用 iCloud** ，**機碼值儲存體**並**CloudKit** 。
7.  請確定**無所不在容器**存在應用程式 （如上述所建立）。 範例：`iCloud.com.your-company.CloudKitAtlas`
8.  將變更儲存到檔案。


使用這些設定之後，應用程式現在已準備好存取 CloudKit Framework Api。

## <a name="cloudkit-api-overview"></a>CloudKit API 概觀

實作之前 CloudKit Xamarin iOS 應用程式中，此文章將涵蓋 CloudKit 架構，其中包含下列主題的基本概念：

1.  **容器**– 隔離的 iCloud 通訊的定址接收器。
2.  **資料庫**– 公用和私用可用於應用程式。
3.  **記錄**– CloudKit 來回結構化的資料會移的機制。
4.  **記錄區域**– 是一組的記錄。
5.  **記錄識別碼**– 完全正規化，並代表記錄的特定位置。
6.  **參考**-提供在給定資料庫內的相關記錄之間的關聯性的父-子系。
7.  **資產**– 允許的大型、 非結構化資料上傳到 iCloud 並與指定的記錄相關聯的檔案。


### <a name="containers"></a>容器

指定的應用程式，在 iOS 裝置上執行不間斷以及端其他應用程式和服務，該裝置上的。 在用戶端裝置上，應用程式將會諸如或沙箱以某種方式。 在某些情況下，這是常值的沙箱，而且在其他應用程式只要在其中執行的是自己的記憶體空間。

取得用戶端應用程式，然後執行它與其他用戶端分隔概念是非常強大，並提供下列優點：

1.  **安全性**– 一個應用程式不會干擾其他用戶端應用程式或作業系統本身。
1.  **穩定性**– 如果用戶端應用程式損毀，則不能拿掉作業系統的其他應用程式。
1.  **隱私權**– 每個用戶端應用程式具有有限存取權的儲存裝置內的個人資訊。


CloudKit 用意是要提供相同的優點，如上述所列，並將它們套用至使用雲端架構的資訊：

 [![](intro-to-cloudkit-images/image31.png "使用容器 CloudKit 應用程式進行通訊")](intro-to-cloudkit-images/image31.png#lightbox)

如同應用程式正在的一對多的裝置上執行，因此是使用 iCloud 的一對多應用程式的通訊。 每個這些不同的通訊的定址接收器稱為容器。

容器會公開在透過 CloudKit Framework`CKContainer`類別。 根據預設，一個應用程式討論到一個容器，且此容器會隔離該應用程式的資料。 這表示數個應用程式可以儲存到相同的 iCloud 帳戶的資訊，但這項資訊將永遠不會互相混合。

ICloud 資料的容器化也可讓 CloudKit 封裝使用者資訊。 如此一來，應用程式會有一些有限的存取權的 iCloud 帳戶且使用者資訊儲存於內，同時仍會保護使用者的安全性與隱私權。

透過 WWDR 入口網站應用程式的開發人員完全管理容器。 容器的命名空間是跨所有 Apple 開發人員共用，因此容器必須不只是唯一指定開發人員的應用程式，但所有 Apple 開發人員和應用程式。

使用反向 DNS 標記法，適用於應用程式容器中建立的命名空間時，Apple 建議。 範例：

```csharp
iCloud.com.company-name.application-name
```

雖然容器，根據預設，繫結至指定的應用程式的一對一，它們可以在應用程式之間共用。 讓多個應用程式可以在單一容器協調。 單一應用程式也可以向多個容器。

### <a name="databases"></a>資料庫

其中一個 CloudKit 的主要功能是充分應用程式的資料模型，並複寫到 iCloud 伺服器該模型。 有些資訊適用於建立它的使用者、 其他資訊是公用的資料，可能使用者所建立的公用用途 （例如餐廳評論），或可能是開發人員已發佈應用程式的資訊。 在任一情況下，對象不只是單一的使用者，但是一個社群的人員。

 [![](intro-to-cloudkit-images/image32.png "CloudKit 容器圖表")](intro-to-cloudkit-images/image32.png#lightbox)

當容器，首先是公用的資料庫。 這是資訊的所有公開位於和共同 mingles。 此外，還有數個個別的私用資料庫，每個使用者的應用程式。

IOS 裝置上執行時，應用程式將只能存取目前登入 iCloud 使用者的資訊。 因此容器的應用程式的檢視會顯示如下所示：

 [![](intro-to-cloudkit-images/image33.png "容器的應用程式檢視")](intro-to-cloudkit-images/image33.png#lightbox)

它只可以看到公用資料庫和私用與目前登入 iCloud 使用者相關聯的資料庫。

資料庫會公開在透過 CloudKit Framework`CKDatabase`類別。 每個應用程式有兩個資料庫的存取： public 資料庫和私密金鑰。

容器是 CloudKit 初始的進入點。 下列程式碼可以用來從應用程式的預設容器中存取的公用和私用的資料庫中：

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

||Public 資料庫|私用的資料庫|
|---|--- |--- |
|**資料類型**|共用的資料|目前的使用者資料|
|**Quota**|列入開發人員的配額|納入的使用者配額|
|**預設權限**|可讀取的世界|使用者可讀取|
|**編輯權限**|透過記錄類別層級的 iCloud 儀表板角色|N/A|

### <a name="records"></a>資料錄

容器保存資料庫，並在資料庫內不會有記錄。 記錄是 CloudKit 來回結構化的資料會移的機制：

 [![](intro-to-cloudkit-images/image34.png "容器保存資料庫，並在資料庫內不會有記錄")](intro-to-cloudkit-images/image34.png#lightbox)

記錄會公開在透過 CloudKit Framework`CKRecord`類別，包裝金鑰-值組。 應用程式中物件的執行個體相當於`CKRecord`CloudKit 中。 此外，每個`CKRecord`擁有記錄類型，這相當於物件的類別。

記錄都有在 just-in-time 結構描述，因此資料就會描述 CloudKit 中前所交付的處理。 從該時間點，CloudKit 將解譯的資訊，並處理儲存和擷取記錄的後勤。

`CKRecord`類別也支援各種不同的中繼資料。 比方說，記錄會包含建立時，並建立它之使用者的相關資訊。 記錄也會包含上次修改並修改它的使用者相關資訊。

記錄包含變更標記的概念。 這是修訂的舊版的特定記錄。 變更標記做為輕量級方法可以判斷如果用戶端和伺服器有相同版本的特定記錄。

如上所述，`CKRecords`包裝金鑰-值組，並在此情況下，下列類型的資料可以儲存在記錄：

1.   `NSString`
1.   `NSNumber`
1.   `NSData`
1.   `NSDate`
1.   `CLLocation`
1.   `CKReferences`
1.   `CKAssets`


單一數值類型，除了一筆記錄可以包含任何上述列出的類型的同質性陣列。

下列程式碼可以用來建立新的記錄，並將它儲存在資料庫中：

```csharp
using CloudKit;
...

private const string ReferenceItemRecordName = "ReferenceItems";
...

var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;
await CloudManager.SaveAsync (newRecord);
```

### <a name="record-zones"></a>記錄區域

記錄不存在自己本身全部放在給定資料庫內，記錄群組在一起存在記錄區域內。 記錄區域可以視為資料表中傳統的關聯式資料庫：

 [![](intro-to-cloudkit-images/image35.png "記錄群組在一起存在內記錄區域")](intro-to-cloudkit-images/image35.png#lightbox)

可以指定的記錄區域內的多筆記錄，在給定資料庫內的多個記錄區域。 每個資料庫都包含預設記錄區域：

 [![](intro-to-cloudkit-images/image36.png "每個資料庫包含的預設記錄區域和自訂區域")](intro-to-cloudkit-images/image36.png#lightbox)

這是預設記錄的儲存位置。 此外，您可以建立自訂記錄區域。 記錄是在哪一個不可部分完成的認可和變更追蹤的基底資料粒度的區域代表。

## <a name="record-identifiers"></a>記錄識別碼

記錄識別項會表示成 tuple、 包含這兩個用戶端提供的記錄名稱和記錄所在的區域。 記錄識別碼具有下列特性：

-  它們會建立用戶端應用程式。
-  他們完全正規化，並代表記錄的特定位置。
-  將外部索引的資料庫中記錄的唯一識別碼指派給記錄名稱中，它們可以用來橋接不會儲存在 CloudKit 的本機資料庫。


當開發人員建立新的記錄時，他們可以選擇傳入記錄識別碼。 如果未指定記錄識別項，UUID 會自動建立並指派給記錄。

當開發人員建立新的記錄識別碼時，他們可以選擇指定每一筆記錄都將屬於記錄區域。 如果未指定，就會使用預設記錄區域。

資料錄的識別項會公開在 CloudKit 架構透過`CKRecordID`類別。 下列程式碼可用來建立新的記錄識別碼：

```csharp
var recordID =  new CKRecordID("My Record");
```

### <a name="references"></a>參考

參考會提供在給定資料庫內的相關記錄之間的關聯性：

 [![](intro-to-cloudkit-images/image37.png "參考提供在給定資料庫內的相關記錄之間的關聯性")](intro-to-cloudkit-images/image37.png#lightbox)

在上述範例中，父代會擁有子系，以便的子系是父記錄的子記錄。 關聯性會從子記錄變成父記錄和指*回參考*。

參考會公開在透過 CloudKit Framework`CKReference`類別。 它們是一種讓 iCloud 伺服器了解記錄之間的關聯性。

參考提供了串聯刪除背後的機制。 如果從資料庫刪除父記錄，從資料庫以及將自動刪除使用 （如指定關聯性中） 的任何子記錄。

> [!NOTE]
> 懸空指標是使用 CloudKit 時的可能性。 比方說，應用程式已擷取的記錄指標的清單，選取 一筆記錄，而且接著要求記錄的時間，記錄可能不再存在資料庫中。 應用程式必須正常地處理這種情況下進行編碼。

雖然並非必要，傳回參考，則建議採用使用 CloudKit Framework。 Apple 已微調系統以將此參考的最有效的類型。

當建立參考，開發人員可以提供已在記憶體中的記錄或建立記錄識別碼的參考。 如果使用記錄識別碼和指定的參考不存在資料庫中，就會建立懸空的指標。

建立參考，以針對已知的記錄的範例如下：

```csharp
var reference = new CKReference(newRecord, new CKReferenceAction());
```

### <a name="assets"></a>資產

資產允許大型、 非結構化資料上傳到 iCloud 並與指定的記錄相關聯的檔案：

 [![](intro-to-cloudkit-images/image38.png "資產允許大型、 非結構化資料上傳到 iCloud 並與指定的記錄相關聯的檔案")](intro-to-cloudkit-images/image38.png#lightbox)

在用戶端，`CKRecord`建立，描述要上傳到 iCloud 的伺服器上的檔案。 A`CKAsset`建立包含該檔案，並且連結到描述它的記錄。

檔案上傳至伺服器，記錄會放在資料庫中，並將檔案複製至特殊的大量儲存體資料庫。 上傳的檔案指標的記錄之間建立連結。

資產會公開在透過 CloudKit Framework`CKAsset`類別，並且用來儲存大量非結構化資料。 因為開發人員永遠不會想要在記憶體中有大型、 非結構化資料，資產是使用磁碟上的檔案來實作。

允許從 iCloud 的指標使用記錄擷取資產的記錄所擁有的資產。 以這種方式在伺服器記憶體回收收集資產時可以擁有資產的記錄遭到刪除。

因為`CKAssets`是處理大型資料檔案內容，Apple 設計 CloudKit 能夠有效率地將上傳和下載的資產。

下列程式碼可以用來建立資產並將它產生關聯的記錄中：

```csharp
var fileUrl = new NSUrl("LargeFile.mov");
var asset = new CKAsset(fileUrl);
newRecord ["name"] = asset;
```

我們現在已涵蓋所有 CloudKit 內的基本物件。 容器應用程式相關聯，而且包含資料庫。 資料庫包含群組在成記錄的區域，並指向的記錄識別碼的記錄。 使用參考的記錄之間定義父子式關聯性。 最後，大型檔案可以上傳，並使用資產的記錄相關聯。

## <a name="cloudkit-convenience-api"></a>CloudKit Convenience API

Apple 提供兩個不同 CloudKit 所使用的 API 集：

-  **作業 API** – 提供 CloudKit 的每一項功能。 對於更複雜的應用程式，此 API 會提供 CloudKit 更細微的控制。
-  **便利 API** – 提供通用的預先設定的子集 CloudKit 功能。 它提供方便且輕鬆地取得方案的 iOS 應用程式包括 CloudKit 功能。


便利 API 通常是大部分的 iOS 應用程式的最佳選擇，它從開始，Apple 建議。 本節的其餘部分將討論下列便利 API 主題：

-  正在儲存記錄。
-  正在擷取記錄。
-  更新記錄。


### <a name="common-setup-code"></a>常見的安裝程式碼

開始之前使用 CloudKit 便利 API，沒有所需的一些標準安裝程式碼。 藉由修改應用程式的啟動`AppDelegate.cs`檔案，並使它看起來如下：

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

上述程式碼會公開公用和私用 CloudKit 資料庫，以更輕鬆地在應用程式的其餘部分中使用的捷徑。

接下來，新增下列程式碼的任何檢視或檢視容器將會使用 CloudKit 來：

```csharp
using CloudKit;
...

#region Computed Properties
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

這會將新增的捷徑，以前往`AppDelegate`並存取先前建立的公用和私用的資料庫快速鍵。

此程式碼就緒之後，讓我們來看看在 Xamarin iOS 8 應用程式中實作 CloudKit 便利 API。

### <a name="saving-a-record"></a>正在儲存記錄

使用討論記錄時，以上所顯示的模式，下列程式碼會建立新的記錄，並將它儲存至公用資料庫使用便利 API:

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

請注意有關上述程式碼的三個項目：

1.  藉由呼叫`SaveRecord`方法的`PublicDatabase`，開發人員不需要指定將資料傳送的方式，正在寫入等等哪些區域。便利 API 會處理所有這些詳細資料本身。
1.  呼叫是非同步的並在呼叫完成，不論是透過成功或失敗時提供回呼常式。 如果呼叫失敗，將會提供錯誤訊息。
1.  CloudKit 不會提供本機儲存體/持續性;它是只在傳輸媒體。 因此當要求進行時儲存記錄，它會立即傳送至 iCloud 伺服器。


> [!NOTE]
> 由於 「 遺失 」 的行動裝置的網路通訊，其中連線經常遭到刪除或中斷，開發人員必須先使用 CloudKit 時的錯誤處理的第一個考量的其中一個。

### <a name="fetching-a-record"></a>擷取資料錄

建立並已成功儲存在 iCloud 伺服器上的記錄，使用下列程式碼來擷取的記錄：

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

如同儲存記錄，上述程式碼非同步、 簡單且需要很好的錯誤處理。

### <a name="updating-a-record"></a>更新記錄

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

`FetchRecord`方法`PublicDatabase`傳回`CKRecord`如果呼叫成功。 接著，應用程式修改的記錄和呼叫`SaveRecord`一次將變更寫回資料庫。

本節顯示使用 CloudKit 便利 API 時，會使用應用程式的一般週期。 應用程式會將記錄儲存到 iCloud、 擷取這些記錄從 iCloud、 修改記錄並將這些變更儲存回 iCloud。

## <a name="designing-for-scalability"></a>針對延展性設計

到目前為止本文已討論過從儲存和擷取應用程式的整個物件模型 iCloud 伺服器中，每次將會處理與。 雖然這個方法也適用於少量的資料和基底非常小的使用者，它並未妥善延展時的資訊及/或使用者數量基底會增加。

### <a name="big-data-tiny-device"></a>巨量資料，很小的裝置

更多熱門應用程式會變成的資料庫中的詳細資料，而有該裝置上的整個資料的快取是不可行。 下列技術可用來解決此問題：

-  **在雲端中保留的大量資料**– CloudKit 設計用來有效率地處理大型資料。
-  **用戶端應該只能檢視該資料的配量**– 關閉資料在指定的時間處理任何工作所需的最低限度。
-  **可以變更用戶端檢視**– 因為每個使用者擁有不同的喜好設定、 要顯示的資料配量可以變更使用者和使用者的個別檢視任何指定的配量可能會不同。
-  **用戶端使用查詢來專注觀點來看**– 查詢可讓使用者檢視雲端內的一小部分的較大的資料集存在。


### <a name="queries"></a>查詢

如上所述，查詢可讓開發人員選取較大的資料集存在於雲端中的一小部分。 查詢會公開在透過 CloudKit Framework`CKQuery`類別。

查詢結合了三個不同的項目： 記錄類型 ( `RecordType`)，述詞 ( `NSPredicate`) 和 （選擇性） 將排序描述元 ( `NSSortDescriptors`)。 CloudKit 支援大部分`NSPredicate`。

#### <a name="supported-predicates"></a>支援的述詞

CloudKit 支援下列類型的`NSPredicates`處理查詢時：


1. 比對名稱等於儲存在變數值的記錄：
    
    ```
    NSPredicate.FromFormat(string.Format("name = '{0}'", recordName))
    ```
   
2. 可讓比對，以動態索引鍵值為基礎，所以索引鍵不需要在編譯時期會知道：
    
    ```
    NSPredicate.FromFormat(string.Format("{0} = '{1}'", key, value))
    ```
    
3. 比對記錄的值大於指定值的記錄：
   
    ```
    NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date))
    ```

4. 比對記錄的位置是 100 公尺內的指定位置的記錄：
    
    ```
    var location = new CLLocation(37.783,-122.404);
    var predicate = NSPredicate.FromFormat(string.Format("distanceToLocation:fromLocation(Location,{0}) < 100", location));
    ```

5. CloudKit 支援的語彙基元化的搜尋。 此呼叫將會建立兩個語彙基元，一個用於`after`，另一個用於`session`。 它會傳回包含這些兩個語彙基元的記錄：
    
    ```
    NSPredicate.FromFormat(string.Format("ALL tokenize({0}, 'Cdl') IN allTokens", "after session"))
    ```
    
 6. CloudKit 支援複合使用聯結的述詞`AND`運算子。
    
    ```
    NSPredicate.FromFormat(string.Format("start > {0} AND name = '{1}'", (NSDate)date, recordName))
    ```
    


#### <a name="creating-queries"></a>建立查詢

下列程式碼可用來建立`CKQuery`Xamarin iOS 8 應用程式中：

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = '{0}'", recordName));
var query = new CKQuery("CloudRecords", predicate);
```

首先，它會建立一個述詞，選取符合指定之名稱的記錄。 然後它會建立將會選取符合述詞指定的記錄類型的資料錄的查詢。

#### <a name="performing-a-query"></a>執行查詢

一旦已建立一個查詢，使用下列程式碼來執行查詢和處理傳回的記錄：

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

上述程式碼會採用上述所建立的查詢，並針對公用資料庫執行。 因為未記錄指定區域，則會搜尋所有區域。 如果不發生任何錯誤，陣列`CKRecords`將會傳回符合查詢的參數。

考慮查詢的方式是，它們是投票，並很適合用來透過大型資料集配量。 查詢，不過，不是很適合大型的大部分是靜態的資料集的原因如下：

-  也就是不正確的裝置的電池壽命。
-  也就是不正確的網路流量。
-  它們是不正確的使用者經驗，因為他們看到的資訊會受到應用程式都會輪詢資料庫的頻率。 使用者內容變更時，立即期待推播通知。


### <a name="subscriptions"></a>訂閱

當處理大型、 大部分是靜態的資料集時，不應在用戶端裝置上執行查詢時，它應該執行的伺服器代表用戶端上。 查詢應該在背景中執行，並儲存每個單一記錄，無論是目前的裝置或其他裝置觸碰相同的資料庫之後應該執行。

最後，將推播通知應該傳送至執行伺服器端查詢時，連接到資料庫的每個裝置。

訂用帳戶會公開在透過 CloudKit Framework`CKSubscription`類別。 結合記錄類型 ( `RecordType`)，述詞 ( `NSPredicate`) 和 Apple 推播通知 ( `Push`)。

> [!NOTE]
> CloudKit 推播會稍有增強，其中包含裝載包含 CloudKit 特有的資訊，例如造成推送至發生的原因。

#### <a name="how-subscriptions-work"></a>訂用帳戶的運作方式

實作中的訂用帳戶之前，先C#程式碼，讓我們先快速瀏覽訂用帳戶的運作方式：

 [![](intro-to-cloudkit-images/image39.png "訂用帳戶的運作方式的概觀")](intro-to-cloudkit-images/image39.png#lightbox)

上述圖表會顯示一般訂用帳戶的程序，如下所示：

1.  用戶端裝置會建立新的訂用帳戶包含的一組將會觸發訂閱，且會在觸發程序發生時傳送推播通知的條件。
2.  訂用帳戶會傳送到資料庫並新增至現有的訂用帳戶的集合。
3.  第二個裝置建立新的記錄，並將該記錄儲存到資料庫。
4.  資料庫會搜尋是否有新的記錄符合其條件的任何訂用帳戶的清單。
5.  如果找到相符項目，則推播通知會傳送至裝置註冊的訂用帳戶，導致觸發記錄的相關資訊。


具有這些知識，讓我們看看建立訂用帳戶中的 Xamarin iOS 8 的應用程式。

#### <a name="creating-subscriptions"></a>建立訂用帳戶

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

首先，它會建立提供訂用帳戶的觸發條件的述詞。 接下來，它會建立針對特定的記錄類型的訂用帳戶，並設定觸發程序會進行測試的選項。 最後，它會定義訂用帳戶，就會觸發，並將它連結的訂用帳戶時，會發生的通知的類型。

### <a name="saving-subscriptions"></a>正在儲存訂用帳戶

建立訂用帳戶，使用下列程式碼會將其儲存到資料庫：

```csharp
// Save the subscription to the database
ThisApp.PublicDatabase.SaveSubscription(subscription, (s, err) => {
    // Was there an error?
    if (err != null) {

    }
});
```

使用便利 API 時，呼叫是非同步且簡單，並提供簡單錯誤處理。

#### <a name="handling-push-notifications"></a>處理推播通知

如果開發人員之前已使用 Apple 推播通知 (AP)，則的 CloudKit 所產生的通知處理程序應該很熟悉。

在  `AppDelegate.cs`，覆寫`ReceivedRemoteNotification`類別，如下所示：

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

上述程式碼會要求 CloudKit 剖析 CloudKit 通知的使用者資訊。 接下來，擷取資訊的相關警示。 最後，在測試的通知類型，並據以處理通知。

本節顯示如何回答巨量資料、 查詢和訂用帳戶使用以上所顯示的小裝置問題。 應用程式會在雲端中，保留其大型的資料，並使用這些技術來提供此資料集檢視。

## <a name="cloudkit-user-accounts"></a>CloudKit 使用者帳戶

如本文開頭所述，CloudKit 是建置在現有的 iCloud 基礎結構之上。 下一節會討論，詳細資料，以使用 CloudKit API 的開發人員如何公開帳戶。

### <a name="authentication"></a>驗證

在處理使用者帳戶，第一個考量是驗證。 CloudKit 支援驗證的目前登入的 iCloud 使用者在裝置上。 驗證發生在幕後，而由 iOS 處理。 如此一來，開發人員永遠不需要擔心如何實作驗證的詳細資料。 它們只測試以查看若要將使用者登入。

### <a name="user-account-information"></a>使用者帳戶資訊

CloudKit 給開發人員提供下列的使用者資訊：

-  **身分識別**– 一種用來唯一識別使用者。
-  **中繼資料**– 能夠儲存及擷取使用者的相關資訊。
-  **隱私權**– 在隱私權的意識 manor 處理所有的資訊。 除非使用者已同意公開執行任何動作。
-  **探索**– 可讓使用者能夠探索他們使用相同的應用程式的朋友。


接下來，我們將探討這些主題的詳細資料。

#### <a name="identity"></a>身分識別

如上所述，CloudKit 可以讓應用程式，來唯一識別指定的使用者：

 [![](intro-to-cloudkit-images/image40.png "唯一會找出指定的使用者")](intro-to-cloudkit-images/image40.png#lightbox)

沒有使用者的裝置和所有 CloudKit 容器內的特定使用者的私用資料庫上執行的用戶端應用程式。 您可以將用戶端應用程式連結至其中一個特定的使用者。 這根據使用者已登入 iCloud 在裝置本機上。

因為這來自 iCloud，還有豐富的備份存放區的使用者資訊。 和 iCloud 實際上裝載容器，因為它可以相互關聯的使用者。 在上述的圖中，使用者的 iCloud 帳戶`user@icloud.com`連結至目前的用戶端。

以容器的容器為基礎，唯一的隨機產生的使用者識別碼，建立並與使用者的 iCloud 帳戶 （電子郵件地址） 相關聯。 此使用者識別碼傳回應用程式，並可用於任何開發人員認為適合的方式。

> [!NOTE]
> 將不同的使用者識別碼相同的 iCloud 使用者相同的裝置上執行的不同應用程式，因為它們會連線到不同的 CloudKit 容器。

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

上述程式碼會要求 CloudKit 容器，以提供目前登入使用者的識別碼。 這項資訊來自伺服器 icloud 的功能，因為呼叫為非同步，並就需要錯誤處理。

#### <a name="metadata"></a>中繼資料

CloudKit 中的每位使用者已描述這些功能的特定中繼資料。 此中繼資料被以 CloudKit 記錄：

 [![](intro-to-cloudkit-images/image41.png "CloudKit 中的每位使用者已描述這些功能的特定中繼資料")](intro-to-cloudkit-images/image41.png#lightbox)

私用的資料庫內尋找特定使用者的容器有已定義該使用者的一筆記錄。 沒有公用的資料庫，其中每個使用者的容器內的許多使用者記錄。 其中會有一個記錄識別碼符合目前登入使用者的記錄識別碼。

公開資料庫中的使用者記錄都可讀取的世界。 它們會被視為，針對大多數情況下，與一般記錄，並有一種`CKRecordTypeUserRecord`。 這些記錄由系統保留，並不是可供查詢。

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

上述程式碼會要求要傳回使用者資料錄是我們存取上面的識別碼的使用者之公用資料庫。 這項資訊來自伺服器 icloud 的功能，因為呼叫為非同步，並就需要錯誤處理。

#### <a name="privacy"></a>隱私權

CloudKit 所設計，根據預設，以保護隱私權的目前登入的使用者。 沒有使用者的個人識別資訊會公開預設。 有某些情況下，應用程式需要使用者的有限的資訊。

在這些情況下，應用程式可以要求使用者揭露此資訊。 對話方塊會向使用者詢問 opt-in 以公開其帳戶資訊。

#### <a name="discovery"></a>探索

假設中選擇加入允許應用程式的使用者限制其使用者帳戶資訊的存取權，它們是可搜尋應用程式的其他使用者：

 [![](intro-to-cloudkit-images/image42.png "使用者可以是應用程式的其他使用者探索")](intro-to-cloudkit-images/image42.png#lightbox)

用戶端應用程式進行通訊的容器和容器正與其溝通 iCloud 存取使用者資訊。 使用者可以提供電子郵件地址，並探索可用來取得回使用者的相關資訊。 （選擇性） 使用者識別碼也可用來探索使用者的相關資訊。

CloudKit 也會提供方法來探索資訊的任何使用者都可能是朋友的目前登入 iCloud 使用者藉由查詢整個通訊錄。 CloudKit 程序將使用者的連絡人活頁簿中提取，並使用電子郵件地址，來查看 是否可以找到其他使用者的應用程式符合這些位址。

這可讓應用程式以利用使用者的連絡人活頁簿，而不需要提供其存取權，或詢問使用者要核准的存取權的連絡人。 在任何時間的連絡資訊開放給應用程式時，只有 CloudKit 程序具有存取權。

若要總而言之，有三種不同的輸入可供使用者探索：

-  **使用者記錄識別碼**– 探索可以運用使用者識別碼的目前登入 CloudKit 使用者。
-  **使用者電子郵件地址**– 使用者可以提供電子郵件地址，並用於探索。
-  **請連絡書籍**– 使用者的通訊錄可用來探索使用者的應用程式具有相同的電子郵件地址，如下所示他們的連絡人。


使用者探索，會傳回下列資訊：

-  **使用者記錄識別碼**-公用資料庫中的使用者的唯一識別碼。
-  **第一個和最後一個名稱**-儲存在公用資料庫中。


這項資訊只會傳回已選擇加入至探索的使用者。

下列程式碼會探索目前登入 iCloud 在裝置上的使用者的相關資訊：

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

在本節中，我們已涵蓋 CloudKit 可以提供給應用程式的使用者帳戶的存取權的四個主要區域。 取得使用者的身分識別和中繼資料，隱私權原則，是內建 CloudKit 和最後，來探索應用程式的其他使用者的能力。

## <a name="the-development-and-production-environments"></a>開發和生產環境

CloudKit 提供個別的開發和生產環境應用程式的記錄類型和資料。 在開發環境是更有彈性的環境，僅提供給開發小組的成員。 當應用程式會將新欄位加入至記錄，並將該記錄儲存在開發環境中時，伺服器會自動更新結構描述資訊。

開發人員可以使用這項功能，來進行結構描述中的變更，在開發期間，進而省下時間。 一個需要注意的是，欄位加入至記錄之後，該欄位相關聯的資料類型無法變更以程式設計的方式。 若要變更欄位的類型，開發人員必須先刪除中的欄位[CloudKit 儀表板](https://icloud.developer.apple.com/dashboard/)並將它重新使用新的類型。

在之前部署的應用程式，開發人員可以將移轉其結構描述和資料至生產環境使用**CloudKit 儀表板**。 當執行對生產環境時，伺服器會防止應用程式，以程式設計方式變更結構描述。 開發人員仍可使用的變更**CloudKit 儀表板**，但嘗試將欄位加入至實際執行環境會導致錯誤中的記錄。

> [!NOTE]
> IOS 模擬器只適用於**開發環境**。 當開發人員就可以開始測試中的應用程式**生產環境**，在實體 iOS 裝置為必要。


## <a name="shipping-a-cloudkit-enabled-app"></a>出貨 CloudKit 啟用的應用程式

然後才運送使用 CloudKit 的應用程式，它必須設定為目標**生產 CloudKit 環境**或 Apple 將拒絕應用程式。

請執行下列動作：

1. 在 Ma 的 Visual Studio 中編譯的應用程式**Release** > **iOS 裝置**: 

    [![](intro-to-cloudkit-images/shipping01.png "編譯應用程式版本")](intro-to-cloudkit-images/shipping01.png#lightbox)

2. 從**建置**功能表上，選取**封存**: 

    [![](intro-to-cloudkit-images/shipping02.png "選取 封存")](intro-to-cloudkit-images/shipping02.png#lightbox)

3. **封存**會建立並顯示在 Visual Studio for Mac: 

    [![](intro-to-cloudkit-images/shipping03.png "會建立並顯示封存")](intro-to-cloudkit-images/shipping03.png#lightbox)

4. 啟動 **Xcode**。
5. 從 **視窗** 功能表上，選取 **召集人**: 

    [![](intro-to-cloudkit-images/shipping04.png "選取 組合管理")](intro-to-cloudkit-images/shipping04.png#lightbox)

6. 選取應用程式的封存，然後按一下**匯出...** 按鈕： 

    [![](intro-to-cloudkit-images/shipping05.png "應用程式的封存")](intro-to-cloudkit-images/shipping05.png#lightbox)
    
7. 選取匯出的方法，然後按一下**下一步**按鈕： 

    [![](intro-to-cloudkit-images/shipping06.png "選取匯出的方法")](intro-to-cloudkit-images/shipping06.png#lightbox)

8. 選取 **開發團隊**從下拉式清單中，然後按一下**選擇**按鈕： 

    [![](intro-to-cloudkit-images/shipping07.png "從下拉式清單中選取開發小組")](intro-to-cloudkit-images/shipping07.png#lightbox)

9. 選取 [**生產**從下拉式清單中，然後按一下**下一步]** 按鈕： 

    [![](intro-to-cloudkit-images/shipping08.png "從下拉式清單中選取 生產環境")](intro-to-cloudkit-images/shipping08.png#lightbox)

10. 檢閱設定，並按一下**匯出**按鈕： 

    [![](intro-to-cloudkit-images/shipping09.png "檢閱設定")](intro-to-cloudkit-images/shipping09.png#lightbox)

11. 選擇要產生產生的應用程式的位置`.ipa`檔案。

此程序是類似的應用程式，直接至 iTunes Connect 提交，只要按一下**提交...** 而不是 [匯出] 後選取 [組合管理] 視窗中的 [封存] 按鈕。

## <a name="when-to-use-cloudkit"></a>何時使用 CloudKit

如我們所見這篇文章中，則 CloudKit 會提供簡單的方法來儲存和擷取資訊從 iCloud 伺服器應用程式。 也就是說，CloudKit 不會淘汰或取代任何現有的工具或架構。

### <a name="use-cases"></a>使用案例

下列使用案例應該有助於決定何時使用特定的 iCloud framework 或技術的開發人員：

-  **iCloud 索引鍵-值存放區**-以非同步方式保留少量的資料保持最新狀態，且適合用來處理應用程式喜好設定。 不過，它會受到限制的極少量的資訊。
-  **iCloud Drive** – 建置在現有的 iCloud 文件的 Api，並提供一個簡單的 API，可從檔案系統的非結構化的資料同步處理。 它在 Mac OS X 上提供完整的離線快取，而且是適用於文件中心的應用程式。
-  **iCloud 核心資料**– 可讓所有使用者的裝置之間進行複寫的資料。 單一使用者 」 和 「 適用於保留私用、 結構化資料一致的資料。
-  **CloudKit** ： 這兩個結構會提供公用資料和大量，並且能夠處理大型資料集和大型非結構化的檔案。 其繫結到使用者的 iCloud 帳戶，並提供用戶端導向的資料傳輸。


保留這些使用案例在心，開發人員應該挑選正確的 iCloud 技術來提供這兩個目前必要的應用程式的功能，並提供很好的延展性，為未來的成長。

## <a name="summary"></a>總結

這篇文章已涵蓋 CloudKit API 的快速簡介。 它示範了如何佈建及設定使用 CloudKit Xamarin iOS 應用程式。 它涵蓋 CloudKit 便利 API 的功能。 其顯示如何設計 CloudKit 啟用使用查詢和訂用帳戶的延展性的應用程式。 而且它有在最後顯示 CloudKit 所公開的應用程式的使用者帳戶資訊。

## <a name="related-links"></a>相關連結

- [CloudKitAtlas (sample)](https://developer.xamarin.com/samples/monotouch/ios8/CloudKitAtlas/)
- [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)
- [建立佈建設定檔](~/ios/get-started/installation/device-provisioning/index.md)
