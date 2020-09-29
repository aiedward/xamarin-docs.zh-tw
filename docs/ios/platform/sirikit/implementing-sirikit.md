---
title: 在 Xamarin 中執行 SiriKit
description: 本檔說明在 Xamarin iOS 應用程式中執行 SiriKit 支援所需的步驟。 它會討論意圖延伸和意圖 UI 延伸。
ms.prod: xamarin
ms.assetid: 20FFB981-EB10-48BA-BF79-40F37F0291EB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: ed31e0262fecccf8974961d45d02388aeb8e85ea
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434423"
---
# <a name="implementing-sirikit-in-xamarinios"></a>在 Xamarin 中執行 SiriKit

_本文涵蓋在 Xamarin iOS 應用程式中執行 SiriKit 支援所需的步驟。_

SiriKit 是 iOS 10 的新功能，可讓 Xamarin iOS 應用程式提供可讓使用者使用 Siri 和 iOS 裝置上的 Maps 應用程式存取的服務。 本文說明如何藉由新增必要的意圖延伸模組、意圖 UI 延伸模組和詞彙，在 Xamarin 應用程式中執行 SiriKit 支援所需的步驟。

Siri 適用于 **領域**的概念、相關工作的已知動作群組。 應用程式與 Siri 之間的每個互動都必須屬於其中一個已知的服務網域，如下所示：

- 正在呼叫音訊或影片。
- 預約。
- 管理 workouts。
- 消息。
- 正在搜尋相片。
- 傳送或接收款項。

當使用者要求包含其中一個應用程式延伸模組服務的 Siri 時，SiriKit 會傳送一個 **意圖** 物件給延伸模組，該物件會描述使用者的要求以及任何支援的資料。 然後，應用程式延伸模組會為指定的**意圖**產生適當的**回應**物件，詳述延伸模組如何處理要求。

本指南將提供將 SiriKit 支援加入現有應用程式的快速範例。 基於此範例，我們將使用假的 MonkeyChat 應用程式：

[![MonkeyChat 圖示](implementing-sirikit-images/monkeychat01.png)](implementing-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat 會保留自己的使用者朋友的連絡人，每個都與螢幕名稱相關聯 () 例如 Bobo），並可讓使用者以螢幕名稱將文字聊天室傳送給每位朋友。

## <a name="extending-the-app-with-sirikit"></a>使用 SiriKit 擴充應用程式

如 [瞭解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md) 指南中所示，使用 SiriKit 擴充應用程式牽涉到三個主要部分：

[![使用 SiriKit 圖表擴充應用程式](implementing-sirikit-images/elements01.png)](implementing-sirikit-images/elements01.png#lightbox)

這些包括：

1. **意圖擴充** 功能-驗證使用者的回應、確認應用程式可以處理要求，並實際執行工作來完成使用者的要求。
2. **意圖 UI 延伸**  -  模組*選擇性*，提供自訂 Ui 給 Siri 環境中的回應，並可將應用程式 ui 和商標帶到 Siri 中，以豐富使用者的體驗。
3. **應用** 程式-為應用程式提供使用者專屬的詞彙，以協助 Siri 使用它。 

下列各節將詳細說明這些元素以及將它們包含在應用程式中的步驟。

## <a name="preparing-the-app"></a>準備應用程式

SiriKit 是以延伸模組為基礎，不過在將任何延伸模組新增至應用程式之前，開發人員必須進行幾件事，以協助採用 SiriKit。

### <a name="moving-common-shared-code"></a>移動一般共用程式碼 

首先，開發人員可以將應用程式與延伸模組之間共用的一些通用程式碼移至 _共用專案_、 _可移植類別庫 (Pcl) _ 或 _原生程式庫_。

擴充功能必須能夠執行應用程式所執行的所有作業。 以範例 MonkeyChat 應用程式的條款而言，像是尋找連絡人、加入新的連絡人、傳送訊息，以及取得訊息歷程記錄等專案。

藉由將此通用程式碼移至共用專案、PCL 或原生程式庫，可讓您輕鬆地在一個共同的位置維護該程式碼，並確保延伸模組和父應用程式能為使用者提供一致的體驗和功能。

在範例應用程式 MonkeyChat 的案例中，資料模型和處理常式代碼（例如網路和資料庫存取）將會移至原生程式庫。

執行下列動作：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 啟動 Visual Studio for Mac，然後開啟 MonkeyChat 應用程式。
2. 在**Solution Pad**中，以滑鼠右鍵按一下方案名稱，然後選取 [**加入**  >  **新專案 ...**： 

    [![新增專案](implementing-sirikit-images/prep01.png)](implementing-sirikit-images/prep01.png#lightbox)
3. 選取 [ **iOS**連結  >  **庫**  >  **類別庫**]，然後按 [**下一步]** 按鈕： 

    [![選取類別庫](implementing-sirikit-images/prep02.png)](implementing-sirikit-images/prep02.png#lightbox)
4. 輸入 `MonkeyChatCommon` **名稱** ，然後按一下 [ **建立** ] 按鈕： 

    [![輸入 MonkeyChatCommon 作為名稱](implementing-sirikit-images/prep03.png)](implementing-sirikit-images/prep03.png#lightbox)
5. 在**方案總管**中，以滑鼠右鍵按一下主應用程式的 [**參考**] 資料夾，然後選取 [**編輯參考 ...**]。檢查**MonkeyChatCommon**專案，然後按一下 [**確定]** 按鈕： 

    [![檢查 MonkeyChatCommon 專案](implementing-sirikit-images/prep05.png)](implementing-sirikit-images/prep05.png#lightbox)
6. 在 **方案總管**中，將一般的共用程式碼從主應用程式拖曳到原生程式庫。
7. 在 MonkeyChat 的案例中，將 **DataModels** 和 **處理器** 資料夾從主要應用程式拖曳到原生程式庫： 

    [![方案總管中的 [DataModels] 和 [處理器] 資料夾](implementing-sirikit-images/prep06.png)](implementing-sirikit-images/prep06.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 啟動 Visual Studio，然後開啟 MonkeyChat 應用程式。
2. 以滑鼠右鍵按一下**方案總管**中的方案名稱，然後選取 [**加入**  >  **新專案**]。
3. 選取 [ **Visual c #**  >  **共用專案**]，然後按 [**下一步]** 按鈕： 

    [![選取類別庫](implementing-sirikit-images/prep02.w157-sml.png)](implementing-sirikit-images/prep02.w157.png#lightbox)
4. 輸入 `MonkeyChatCommon` **名稱** ，然後按一下 [ **建立** ] 按鈕。
5. 在**方案總管**中，以滑鼠右鍵按一下主應用程式的 [**參考**] 資料夾，然後選取 [**編輯參考 ...**]。檢查**MonkeyChatCommon**專案，然後按一下 [**確定]** 按鈕： 

    [![檢查 MonkeyChatCommon 專案](implementing-sirikit-images/prep05w.png)](implementing-sirikit-images/prep05w.png#lightbox)
6. 在 **方案總管**中，將一般的共用程式碼從主應用程式拖曳到共用的專案。
7. 在 MonkeyChat 的案例中，將 **DataModels** 和 **處理器** 資料夾從主要應用程式拖曳到原生程式庫。

-----

編輯任何已移至原生程式庫的檔案，並將命名空間變更為符合程式庫的名稱。 例如，變更 `MonkeyChat` 為 `MonkeyChatCommon` ：

```csharp
using System;
namespace MonkeyChatCommon
{
    /// <summary>
    /// A message sent from one user to another within a conversation.
    /// </summary>
    public class MonkeyMessage
    {
        public MonkeyMessage ()
        {
        }
        ...
    }
}
```

接下來，回到主要應用程式，並 `using` 為原生程式庫的命名空間新增語句，讓應用程式使用其中一個已移動的類別：

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;
using MonkeyChatCommon;

namespace MonkeyChat
{
    public partial class MasterViewController : UITableViewController
    {
        public DetailViewController DetailViewController { get; set; }

        DataSource dataSource;
        ...
    }
}
```

### <a name="architecting-the-app-for-extensions"></a>架構應用程式的擴充功能

一般而言，應用程式會註冊多個意圖，而開發人員必須確保應用程式是針對適當數量的意圖延伸模組所設計。

在應用程式需要多個意圖的情況下，開發人員可以選擇將其所有意圖處理放在一個意圖延伸中，或為每個意圖建立個別的意圖延伸模組。

如果選擇為每個意圖建立個別的意圖延伸，開發人員最終可能會在每個延伸模組中複製大量的未定案程式碼，並建立大量的處理器和記憶體額外負荷。

若要協助在兩個選項之間做選擇，請查看是否有任何意圖自然地在一起。 例如，製作音訊和影片呼叫的應用程式可能會想要在單一意圖延伸中包含這兩個意圖，因為它們會處理類似的工作，而且可以提供最多程式碼重複使用。

針對不符合現有群組的任何意圖或意圖群組，請在應用程式的解決方案中建立新的意圖延伸模組以包含這些專案。

### <a name="setting-the-required-entitlements"></a>設定必要權利

任何包含 SiriKit 整合的 Xamarin iOS 應用程式都必須有正確的權利組。 如果開發人員未正確設定這些必要權利，他們將無法在實際的 iOS 10 (或更高的) 硬體上安裝或測試應用程式，因為 iOS 10 模擬器不支援 SiriKit，所以這也是必要條件。

執行下列動作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 按兩下 `Entitlements.plist` **方案總管** 中的檔案，將它開啟以供編輯。
2. 切換至 [來源]**** 索引標籤。
3. 加入 `com.apple.developer.siri` **屬性**，將 **類型** 設定為， `Boolean` 並將 **值** 設定為 `Yes` ： 

    [![加入 siri 屬性。](implementing-sirikit-images/setup01.png)](implementing-sirikit-images/setup01.png#lightbox)
4. 將變更儲存至檔案。
5. 按兩下**方案總管**中的**專案**檔，以開啟它進行編輯。
6. 選取 [IOS 套件組合 **簽署** ]，並確定 `Entitlements.plist` 已在 [ **自訂權利** ] 欄位中選取檔案： 

    [![在 [自訂權利] 欄位中選取 plist 檔案](implementing-sirikit-images/setup02.png)](implementing-sirikit-images/setup02.png#lightbox)
7. 按一下 [確定]**** 按鈕以儲存所做的變更。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 按兩下 `Entitlements.plist` **方案總管** 中的檔案，將它開啟以供編輯。
2. 加入 `com.apple.developer.siri` **屬性**，將 **類型** 設定為， `Boolean` 並將 **值** 設定為 `Yes` ： 

    [![加入 siri 屬性。](implementing-sirikit-images/setup01w.png)](implementing-sirikit-images/setup01w.png#lightbox)
3. 將變更儲存至檔案。
4. 按兩下**方案總管**中的**專案**檔，以開啟它進行編輯。
5. 選取 [IOS 套件組合 **簽署** ]，並確定 `Entitlements.plist` 已在 [ **自訂權利** ] 欄位中選取該檔案。

-----

完成時，應用程式的檔案 `Entitlements.plist` 看起來應該像下列 (在外部編輯器中開啟) ：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.developer.siri</key>
    <true/>
</dict>
</plist>
```

### <a name="correctly-provisioning-the-app"></a>正確布建應用程式

由於 Apple 在 SiriKit 架構中所放置的嚴格安全性，任何實 SiriKit 的 Xamarin iOS 應用程式都 _必須_ 具有正確的應用程式識別碼和權利 (請參閱) 上一節，而且必須使用適當的布建設定檔進行簽署。

在您的 Mac 上執行下列步驟：

1. 在網頁瀏覽器中，流覽至 [https://developer.apple.com](https://developer.apple.com) 您的帳戶並登入。
2. 按一下 [ **憑證**]、[ **識別碼** 和 **設定檔**]。
3. 選取 [布建 **設定檔** ]，然後選取 [ **應用程式識別碼**]，再按一下 **+** 按鈕。
4. 輸入新設定檔的 **名稱** 。
5. 輸入遵循 Apple 命名建議的組合 **識別碼** 。
6. 向下滾動至 [ **應用程式服務** ] 區段，選取 [ **SiriKit** ]，然後按一下 [ **繼續** ] 按鈕： 

    [![選取 SiriKit](implementing-sirikit-images/setup03.png)](implementing-sirikit-images/setup03.png#lightbox)
7. 確認所有設定，然後 **提交** 應用程式識別碼。
8. 選取 [布建**設定檔**  >  **開發**]，按一下 **+** 按鈕，選取**Apple ID**，然後按一下 [**繼續**]。
9. 按一下 [ **全選]，然後**按一下 [ **繼續**]。
10. 按一下 [ **全部選取** ]，然後按一下 [ **繼續**]。
11. 使用 Apple 的命名建議輸入 **設定檔名稱** ，然後按一下 [ **繼續**]。
12. 啟動 Xcode。
13. 從 [Xcode] 功能表選取 [**喜好設定 ...** ]
14. 選取 [**帳戶**]，然後按一下 [**查看詳細資料**]。 按鈕： 

    [![選取帳戶](implementing-sirikit-images/setup04.png)](implementing-sirikit-images/setup04.png#lightbox)
15. 按一下左下角的 [ **下載所有設定檔** ] 按鈕： 

    [![下載所有設定檔](implementing-sirikit-images/setup05.png)](implementing-sirikit-images/setup05.png#lightbox)
16. 確定已在 Xcode 中安裝上述建立的布建 **設定檔** 。
17. 開啟專案，在 Visual Studio for Mac 中新增 SiriKit 支援。
18. 按兩下 `Info.plist` **方案總管**中的檔案。
19. 請確定套件組合識別碼符合在 Apple 開發人員入口網站中建立的套件組合 **識別碼** ： 

    [![套件組合識別碼](implementing-sirikit-images/setup06.png)](implementing-sirikit-images/setup06.png#lightbox)
20. 在 [ **方案總管**中，選取 **專案**。
21. 以滑鼠右鍵按一下專案，然後選取 [ **選項**]。
22. 選取 [IOS 套件組合 **簽署**]，選取先前建立的 **簽署身分識別** 和布建 **設定檔** ： 

    [![選取簽署身分識別和布建設定檔](implementing-sirikit-images/setup07.png)](implementing-sirikit-images/setup07.png#lightbox)
23. 按一下 [確定]**** 按鈕以儲存所做的變更。

> [!IMPORTANT]
> 測試 SiriKit 僅適用于實際的 iOS 10 硬體裝置，而不是在 iOS 10 模擬器中。 如果在實際的硬體上安裝 SiriKit 的 Xamarin iOS 應用程式發生問題，請確定已在 Apple 開發人員入口網站和 Visual Studio for Mac 中正確設定必要權利、應用程式識別碼、簽署識別碼和布建設定檔。

### <a name="requesting-siri-authorization"></a>要求 Siri 授權

在應用程式新增任何使用者特定詞彙或意圖延伸模組連接至 Siri 之前，它必須要求使用者授權才能存取 Siri。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

編輯應用程式的檔案 `Info.plist` ，切換至 [ **來源** ] 視圖，並 `NSSiriUsageDescription` 使用字串值來新增索引鍵，以描述應用程式將如何使用 Siri 以及將傳送的資料類型。 例如，MonkeyChat 應用程式可能會顯示「MonkeyChat 連絡人將傳送至 Siri」：

[![Plist 編輯器中的 NSSiriUsageDescription](implementing-sirikit-images/request01.png)](implementing-sirikit-images/request01.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

編輯應用程式的檔案 `Info.plist` ，並 `NSSiriUsageDescription` 使用字串值來新增索引鍵，以描述應用程式將如何使用 Siri 以及要傳送的資料類型。 例如，MonkeyChat 應用程式可能會顯示「MonkeyChat 連絡人將傳送至 Siri」：

[![Plist 編輯器中的 NSSiriUsageDescription](implementing-sirikit-images/request01w.png)](implementing-sirikit-images/request01w.png#lightbox)

-----

`RequestSiriAuthorization` `INPreferences` 當應用程式第一次啟動時，呼叫類別的方法。 編輯 `AppDelegate.cs` 類別，讓 `FinishedLaunching` 方法看起來如下所示：

```csharp
using Intents;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{

    // Request access to Siri
    INPreferences.RequestSiriAuthorization ((INSiriAuthorizationStatus status) => {
        // Respond to returned status
        switch (status) {
        case INSiriAuthorizationStatus.Authorized:
            break;
        case INSiriAuthorizationStatus.Denied:
            break;
        case INSiriAuthorizationStatus.NotDetermined:
            break;
        case INSiriAuthorizationStatus.Restricted:
            break;
        }
    });

    return true;
}
```

第一次呼叫此方法時，會顯示警示，提示使用者允許應用程式存取 Siri。 開發人員新增至上面的訊息 `NSSiriUsageDescription` 會顯示在此警示中。 如果使用者一開始拒絕存取，他們可以使用 [ **設定** ] 應用程式來授與應用程式的存取權。

應用程式隨時都可以藉由呼叫類別的方法，來檢查應用程式存取 Siri 的能力 `SiriAuthorizationStatus` `INPreferences` 。

### <a name="localization-and-siri"></a>當地語系化與 Siri

在 iOS 裝置上，使用者可以選取與系統預設不同的 Siri 語言。 使用當地語系化的資料時，應用程式必須使用 `SiriLanguageCode` 類別的方法， `INPreferences` 從 Siri 取得語言代碼。 例如：

```csharp
var language = INPreferences.SiriLanguageCode();

// Take action based on language
if (language == "en-US") {
    // Do something...
}
```

### <a name="adding-user-specific-vocabulary"></a>新增使用者特定詞彙

使用者特定詞彙將提供應用程式個別使用者專屬的單字或片語。 這些將會從主要應用程式在執行時間提供， (不是應用程式延伸模組) 為一組已排序的條款，並以使用者最重要的使用優先順序排序，且在清單開頭最重要的詞彙。

使用者特定詞彙必須屬於下列其中一個類別：

- 不是由 contact framework) 管理 (連絡人名稱。
- 相片標記。
- 相片專輯名稱。
- 測驗名稱。

選取要註冊為自訂詞彙的術語時，請只選擇不熟悉應用程式的人可能會誤解的詞彙。 絕對不要註冊一般詞彙，例如「我的測驗」或「我的專輯」。 例如，MonkeyChat 應用程式將會註冊與使用者通訊錄中的每個連絡人相關聯的昵稱。

應用程式會藉由呼叫 `SetVocabularyStrings` 類別的方法 `INVocabulary` ，並 `NSOrderedSet` 從主應用程式傳入集合，來提供使用者特定詞彙。 應用程式應該一律先呼叫 `RemoveAllVocabularyStrings` 方法，以移除任何現有的詞彙，然後再新增新的詞彙。 例如：

```csharp
using System;
using System.Linq;
using System.Collections.Generic;
using Foundation;
using Intents;

namespace MonkeyChatCommon
{
    public class MonkeyAddressBook : NSObject
    {
        #region Computed Properties
        public List<MonkeyContact> Contacts { get; set; } = new List<MonkeyContact> ();
        #endregion

        #region Constructors
        public MonkeyAddressBook ()
        {
        }
        #endregion

        #region Public Methods
        public NSOrderedSet<NSString> ContactNicknames ()
        {
            var nicknames = new NSMutableOrderedSet<NSString> ();

            // Sort contacts by the last time used
            var query = Contacts.OrderBy (contact => contact.LastCalledOn);

            // Assemble ordered list of nicknames by most used to least
            foreach (MonkeyContact contact in query) {
                nicknames.Add (new NSString (contact.ScreenName));
            }

            // Return names
            return new NSOrderedSet<NSString> (nicknames.AsSet ());
        }

        // This method MUST only be called on a background thread!
        public void UpdateUserSpecificVocabulary ()
        {
            // Clear any existing vocabulary
            INVocabulary.SharedVocabulary.RemoveAllVocabularyStrings ();

            // Register new vocabulary
            INVocabulary.SharedVocabulary.SetVocabularyStrings (ContactNicknames (), INVocabularyStringType.ContactName);
        }
        #endregion
    }
}
```

使用此程式碼時，可能會呼叫它，如下所示：

```csharp
using System;
using System.Threading;
using UIKit;
using MonkeyChatCommon;
using Intents;

namespace MonkeyChat
{
    public partial class ViewController : UIViewController
    {
        #region AppDelegate Access
        public AppDelegate ThisApp {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do we have access to Siri?
            if (INPreferences.SiriAuthorizationStatus == INSiriAuthorizationStatus.Authorized) {
                // Yes, update Siri's vocabulary
                new Thread (() => {
                    Thread.CurrentThread.IsBackground = true;
                    ThisApp.AddressBook.UpdateUserSpecificVocabulary ();
                }).Start ();
            }
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion
    }
}
```

> [!IMPORTANT]
> Siri 會將自訂詞彙視為提示，並盡可能納入許多術語。 不過，自訂詞彙的空間有限，因此很重要的一點是 _只要_ 註冊可能會令人困惑的術語，也就是將已註冊的條款總數保持在最小值。

如需詳細資訊，請參閱我們的 [使用者專屬詞彙參考](~/ios/platform/sirikit/understanding-sirikit.md) 和 Apple 的 [指定自訂詞彙參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1)。

### <a name="adding-app-specific-vocabulary"></a>新增應用程式特定詞彙

應用程式特定詞彙會定義所有應用程式使用者都知道的特定單字和片語，例如車輛類型或健身名稱。 因為這些都是應用程式的一部分，所以會在檔案中定義為主要應用程式套件組合的 `AppIntentVocabulary.plist` 一部分。 此外，這些字組和片語都應該當地語系化。

應用程式特定詞彙必須屬於下列其中一個類別：

- 選項。
- 測驗名稱。

應用程式特定的詞彙檔案包含兩個根層級金鑰：

- `ParameterVocabularies`**必要**-定義應用程式的自訂字詞和意圖參數。
- `IntentPhrases`**選擇性**-包含使用中所定義之自訂詞彙的範例片語 `ParameterVocabularies` 。

中的每個專案都 `ParameterVocabularies` 必須指定識別碼字串、詞彙以及套用該詞彙的意圖。 此外，單一詞彙可能適用于多個意圖。

如需可接受值和必要檔案結構的完整清單，請參閱 Apple 的 [應用程式詞彙檔案格式參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1)。

若要將檔案新增 `AppIntentVocabulary.plist` 至應用程式專案，請執行下列動作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在**方案總管**中的專案名稱上按一下滑鼠右鍵，然後選取 [**加入**  >  **新**  >  檔案]。**iOS**：

    [![加入屬性清單](implementing-sirikit-images/plist01.png)](implementing-sirikit-images/plist01.png#lightbox)
2. 按兩下 `AppIntentVocabulary.plist` **方案總管** 中的檔案，將它開啟以供編輯。
3. 按一下 **+** 以加入索引鍵，將 **名稱** 設定為， `ParameterVocabularies` 並將 **類型** 設定為 `Array` ：

    [![將名稱設為 ParameterVocabularies，並將類型設定為數組](implementing-sirikit-images/plist02.png)](implementing-sirikit-images/plist02.png#lightbox)
4. 展開 `ParameterVocabularies` 並按一下 **+** 按鈕，並將 **類型** 設定為 `Dictionary` ：

    [![將類型設定為字典](implementing-sirikit-images/plist03.png)](implementing-sirikit-images/plist03.png#lightbox)
5. 按一下 **+** 以加入新的索引鍵，將 **名稱** 設定為， `ParameterNames` 並將 **類型** 設定為 `Array` ：

    [![將名稱設為 ParameterNames，並將類型設定為數組](implementing-sirikit-images/plist04.png)](implementing-sirikit-images/plist04.png#lightbox)
6. 按一下 **+** 以加入具有 **類型** 的新索引鍵 `String` ，並將值設為其中一個可用的參數名稱。 例如，`INStartWorkoutIntent.workoutName`：

    [![INStartWorkoutIntent. workoutName 鍵](implementing-sirikit-images/plist05.png)](implementing-sirikit-images/plist05.png#lightbox)
7. 將機 `ParameterVocabulary` 碼新增至 `ParameterVocabularies` 具有下列 **類型** 的金鑰 `Array` ：

    [![將 ParameterVocabulary 索引鍵新增至具有陣列類型的 ParameterVocabularies 索引鍵](implementing-sirikit-images/plist06.png)](implementing-sirikit-images/plist06.png#lightbox)
8. 新增具有下列 **類型** 的新機碼 `Dictionary` ：

    [![新增具有字典類型的新機碼](implementing-sirikit-images/plist07.png)](implementing-sirikit-images/plist07.png#lightbox)
9. 加入 `VocabularyItemIdentifier` 具有 **類型** 的索引鍵 `String` ，並指定該詞彙的唯一識別碼：

    [![新增具有字串類型的 VocabularyItemIdentifier 索引鍵，並指定唯一的識別碼](implementing-sirikit-images/plist08.png)](implementing-sirikit-images/plist08.png#lightbox)
10. 新增 `VocabularyItemSynonyms` 具有下列 **類型** 的金鑰 `Array` ：

    [![新增具有陣列類型的 VocabularyItemSynonyms 索引鍵](implementing-sirikit-images/plist09.png)](implementing-sirikit-images/plist09.png#lightbox)
11. 新增具有下列 **類型** 的新機碼 `Dictionary` ：

    [![新增具有字典類型的新機碼](implementing-sirikit-images/plist10.png)](implementing-sirikit-images/plist10.png#lightbox)
12. 使用的 `VocabularyItemPhrase` **類型** `String` 和應用程式所定義的詞彙來新增金鑰：

    [![新增具有字串類型和應用程式所定義詞彙的 VocabularyItemPhrase 索引鍵](implementing-sirikit-images/plist11.png)](implementing-sirikit-images/plist11.png#lightbox)
13. 新增 `VocabularyItemPronunciation` 具有 **類型** 的索引鍵 `String` 和詞彙的拼音發音：

    [![新增具有字串類型的 VocabularyItemPronunciation 索引鍵，以及詞彙的拼音發音](implementing-sirikit-images/plist12.png)](implementing-sirikit-images/plist12.png#lightbox)
14. 新增 `VocabularyItemExamples` 具有下列 **類型** 的金鑰 `Array` ：

    [![新增具有陣列類型的 VocabularyItemExamples 索引鍵](implementing-sirikit-images/plist13.png)](implementing-sirikit-images/plist13.png#lightbox)
15. `String`使用「詞彙」的範例用法來新增一些金鑰：

    [![使用詞彙的範例用法來新增一些字串索引鍵](implementing-sirikit-images/plist14.png)](implementing-sirikit-images/plist14.png#lightbox)
16. 針對應用程式需要定義的任何其他自訂字詞，重複上述步驟。
17. 折迭索引 `ParameterVocabularies` 鍵。
18. 新增 `IntentPhrases` 具有下列 **類型** 的金鑰 `Array` ：

    [![新增具有陣列類型的 IntentPhrases 索引鍵](implementing-sirikit-images/plist15.png)](implementing-sirikit-images/plist15.png#lightbox)
19. 新增具有下列 **類型** 的新機碼 `Dictionary` ：

    [![新增具有字典類型的新機碼](implementing-sirikit-images/plist16.png)](implementing-sirikit-images/plist16.png#lightbox)
20. 在範例中新增 `IntentName` 具有和意圖 **類型** 的索引鍵 `String` ：

    [![使用範例的字串和意圖類型來新增 IntentName 索引鍵](implementing-sirikit-images/plist17.png)](implementing-sirikit-images/plist17.png#lightbox)
21. 新增 `IntentExamples` 具有下列 **類型** 的金鑰 `Array` ：

    [![新增具有陣列類型的 IntentExamples 索引鍵](implementing-sirikit-images/plist18.png)](implementing-sirikit-images/plist18.png#lightbox)
22. `String`使用「詞彙」的範例用法來新增一些金鑰：

    [![使用詞彙的範例用法來新增一些字串索引鍵](implementing-sirikit-images/plist19.png)](implementing-sirikit-images/plist19.png#lightbox)
23. 針對應用程式為了提供範例使用方式所需的任何意圖重複上述步驟。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 **方案總管** 中，以滑鼠右鍵按一下專案名稱，然後選取 [ **加入 > 新專案] > > 屬性清單 > 資訊. plist**：

    [![新增資訊。 plist](implementing-sirikit-images/plist01.w157-sml.png)](implementing-sirikit-images/plist01.w157.png#lightbox)

2. 按兩下 `AppIntentVocabulary.plist` **方案總管** 中的檔案，將它開啟以供編輯。
3. 按一下 **+** 以加入索引鍵，將 **名稱** 設定為， `ParameterVocabularies` 並將 **類型** 設定為 `Array` ：

    [![將名稱設為 ParameterVocabularies，並將類型設定為數組](implementing-sirikit-images/plist02w.png)](implementing-sirikit-images/plist02w.png#lightbox)
4. 展開 `ParameterVocabularies` 並按一下 **+** 按鈕，並將 **類型** 設定為 `Dictionary` ：

    [![將類型設定為字典](implementing-sirikit-images/plist03w.png)](implementing-sirikit-images/plist03w.png#lightbox)
5. 按一下 **+** 以加入新的索引鍵，將 **名稱** 設定為， `ParameterNames` 並將 **類型** 設定為 `Array` ：

    [![將名稱設為 ParameterNames，並將類型設定為數組](implementing-sirikit-images/plist04w.png)](implementing-sirikit-images/plist04w.png#lightbox)
6. 按一下 **+** 以加入具有 **類型** 的新索引鍵 `String` ，並將值設為其中一個可用的參數名稱。 例如，`INStartWorkoutIntent.workoutName`：

    [![INStartWorkoutIntent. workoutName 鍵](implementing-sirikit-images/plist05w.png)](implementing-sirikit-images/plist05w.png#lightbox)
7. 將機 `ParameterVocabulary` 碼新增至 `ParameterVocabularies` 具有下列 **類型** 的金鑰 `Array` ：

    [![將 ParameterVocabulary 索引鍵新增至具有陣列類型的 ParameterVocabularies 索引鍵](implementing-sirikit-images/plist06w.png)](implementing-sirikit-images/plist06w.png#lightbox)
8. 新增具有下列 **類型** 的新機碼 `Dictionary` ：

    [![新增具有字典類型的新機碼](implementing-sirikit-images/plist07w.png)](implementing-sirikit-images/plist07w.png#lightbox)
9. 加入 `VocabularyItemIdentifier` 具有 **類型** 的索引鍵 `String` ，並指定該詞彙的唯一識別碼：

    [![新增具有字串類型的 VocabularyItemIdentifier 索引鍵，並指定該詞彙的唯一識別碼](implementing-sirikit-images/plist08w.png)](implementing-sirikit-images/plist08w.png#lightbox)
10. 新增 `VocabularyItemSynonyms` 具有下列 **類型** 的金鑰 `Array` ：

    [![新增具有陣列類型的 VocabularyItemSynonyms 索引鍵](implementing-sirikit-images/plist09w.png)](implementing-sirikit-images/plist09w.png#lightbox)
11. 新增具有下列 **類型** 的新機碼 `Dictionary` ：

    [![新增具有字典類型的新機碼](implementing-sirikit-images/plist10w.png)](implementing-sirikit-images/plist10w.png#lightbox)
12. 使用的 `VocabularyItemPhrase` **類型** `String` 和應用程式所定義的詞彙來新增金鑰：

    [![新增具有字串類型和應用程式所定義詞彙的 VocabularyItemPhrase 索引鍵](implementing-sirikit-images/plist11w.png)](implementing-sirikit-images/plist11w.png#lightbox)
13. 新增 `VocabularyItemPronunciation` 具有 **類型** 的索引鍵 `String` 和詞彙的拼音發音：

    [![新增具有字串類型的 VocabularyItemPronunciation 索引鍵，以及詞彙的拼音發音](implementing-sirikit-images/plist12w.png)](implementing-sirikit-images/plist12w.png#lightbox)
14. 新增 `VocabularyItemExamples` 具有下列 **類型** 的金鑰 `Array` ：

    [![新增具有陣列類型的 VocabularyItemExamples 索引鍵](implementing-sirikit-images/plist13w.png)](implementing-sirikit-images/plist13w.png#lightbox)
15. `String`使用「詞彙」的範例用法來新增一些金鑰：

    [![使用詞彙的範例用法來新增一些字串索引鍵](implementing-sirikit-images/plist14w.png)](implementing-sirikit-images/plist14w.png#lightbox)
16. 針對應用程式需要定義的任何其他自訂字詞，重複上述步驟。
17. 折迭索引 `ParameterVocabularies` 鍵。
18. 新增 `IntentPhrases` 具有下列 **類型** 的金鑰 `Array` ：

    [![新增具有陣列類型的 IntentPhrases 索引鍵](implementing-sirikit-images/plist15w.png)](implementing-sirikit-images/plist15w.png#lightbox)
19. 新增具有下列 **類型** 的新機碼 `Dictionary` ：

    [![新增具有字典類型的新機碼](implementing-sirikit-images/plist16w.png)](implementing-sirikit-images/plist16w.png#lightbox)
20. 在範例中新增 `IntentName` 具有和意圖 **類型** 的索引鍵 `String` ：

    [![使用範例的字串和意圖類型來新增 IntentName 索引鍵](implementing-sirikit-images/plist17w.png)](implementing-sirikit-images/plist17w.png#lightbox)
21. 新增 `IntentExamples` 具有下列 **類型** 的金鑰 `Array` ：

    [![新增具有陣列類型的 IntentExamples 索引鍵](implementing-sirikit-images/plist18w.png)](implementing-sirikit-images/plist18w.png#lightbox)
22. `String`使用「詞彙」的範例用法來新增一些金鑰：

    [![使用詞彙的範例用法來新增一些字串索引鍵](implementing-sirikit-images/plist19w.png)](implementing-sirikit-images/plist19w.png#lightbox)
23. 針對應用程式為了提供範例使用方式所需的任何意圖重複上述步驟。

-----

> [!IMPORTANT]
> 在 `AppIntentVocabulary.plist` 開發期間，將會在測試裝置上向 Siri 註冊，而且可能需要一些時間，Siri 才能併入自訂詞彙。 因此，測試人員必須等待幾分鐘的時間，然後再嘗試測試應用程式特定詞彙（若已更新）。

如需詳細資訊，請參閱我們的 [應用程式專屬詞彙參考](~/ios/platform/sirikit/understanding-sirikit.md) 和 Apple 的 [指定自訂詞彙參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1)。

## <a name="adding-an-intents-extension"></a>新增意圖延伸模組

現在應用程式已準備好採用 SiriKit，開發人員必須將一個 (或多個) 意圖延伸模組新增至解決方案，以處理 Siri 整合所需的意圖。

針對每個需要的意圖延伸，請執行下列動作：

- 將意圖延伸專案新增至 Xamarin 應用程式解決方案。
- 設定意圖擴充檔 `Info.plist` 。
- 修改意圖擴充功能的主要類別。

如需詳細資訊，請參閱我們 [的意圖延伸模組參考](~/ios/platform/sirikit/understanding-sirikit.md) 和 Apple 的 [建立意圖延伸模組參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CreatingtheIntentsExtension.html#//apple_ref/doc/uid/TP40016875-CH4-SW1)。

### <a name="creating-the-extension"></a>建立延伸模組

若要將意圖延伸新增至方案，請執行下列動作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 以滑鼠右鍵按一下**Solution Pad**中的**方案名稱**，然後選取 [**加入**  >  **新專案**]。
2. 從對話方塊中選取 [ **iOS**  >  **延伸**模組  >  **意圖擴充**功能]，然後按 [**下一步]** 按鈕： 

    [![選取意圖延伸模組](implementing-sirikit-images/intents05.png)](implementing-sirikit-images/intents05.png#lightbox)
3. 接下來輸入意圖延伸的 **名稱** ，然後按 [ **下一步]** 按鈕： 

    [![輸入意圖延伸模組的名稱](implementing-sirikit-images/intents06.png)](implementing-sirikit-images/intents06.png#lightbox)
4. 最後，按一下 [ **建立** ] 按鈕，將意圖延伸新增至應用程式解決方案： 

    [![將意圖擴充功能新增至應用程式解決方案](implementing-sirikit-images/intents07.png)](implementing-sirikit-images/intents07.png#lightbox)
5. 在 **方案總管**中，以滑鼠右鍵按一下新建立之意圖延伸模組的 [ **參考** ] 資料夾。 檢查上面建立的應用程式 (常見的共用程式碼程式庫專案的名稱) 然後按一下 [ **確定]** 按鈕： 

    [![選取通用共用程式碼程式庫專案的名稱](implementing-sirikit-images/intents08.png)](implementing-sirikit-images/intents08.png#lightbox)
    
# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 以滑鼠右鍵按一下**方案總管**中的**方案名稱**，然後選取 [**加入**  >  **新專案**]。
2. 從對話方塊中，選取 [ **Visual c # > IOS 延伸模組] > 意圖擴充** 功能，然後按 [ **下一步]** 按鈕：

    [![選取意圖延伸模組](implementing-sirikit-images/intents05.w157-sml.png)](implementing-sirikit-images/intents05.w157.png#lightbox)
3. 接下來輸入意圖延伸模組的 **名稱** ，然後按一下 [ **確定]** 按鈕。
4. 在 [ **方案總管**中，以滑鼠右鍵按一下新建立之意圖延伸模組的 [ **參考** ] 資料夾，然後選擇 [ **加入 > 參考**]。 檢查上面建立的應用程式 (常見的共用程式碼程式庫專案的名稱) 然後按一下 [ **確定]** 按鈕：

    [![選取通用共用程式碼程式庫專案的名稱](implementing-sirikit-images/intents08w.png)](implementing-sirikit-images/intents08w.png#lightbox)
    
-----

針對應用程式所需) 上述延伸模組的 [應用程式架構，](#architecting-the-app-for-extensions) 重複這些步驟來 (意圖延伸模組的數目。

### <a name="configuring-the-infoplist"></a>設定資訊. plist

針對已新增至應用程式解決方案的每個意圖延伸，必須在檔案中設定， `Info.plist` 才能使用應用程式。

就像任何一般的應用程式擴充功能一樣，應用程式也會有和的現有金鑰 `NSExtension` `NSExtensionAttributes` 。 對於意圖延伸，必須設定兩個新屬性：

[![必須設定的兩個新屬性](implementing-sirikit-images/intents01.png)](implementing-sirikit-images/intents01.png#lightbox)

- **IntentsSupported** -是必要的，而且是由應用程式想要從意圖延伸模組支援的意圖類別名稱陣列所組成。
- **IntentsRestrictedWhileLocked** -這是應用程式用來指定延伸鎖定畫面行為的選擇性索引鍵。 它是由應用程式想要要求使用者登入以從意圖延伸模組使用的意圖類別名稱陣列所組成。

若要設定意圖延伸模組的檔案 `Info.plist` ，請在 **方案總管** 中按兩下該檔案以開啟它進行編輯。 接下來，切換至 **來源** 視圖，然後 `NSExtension` `NSExtensionAttributes` 在編輯器中展開和索引鍵：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![編輯器中的 NSExtension 和 NSExtensionAttributes 索引鍵](implementing-sirikit-images/intents02.png)](implementing-sirikit-images/intents02.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![編輯器中的 NSExtension 和 NSExtensionAttributes 索引鍵](implementing-sirikit-images/intents02w.png)](implementing-sirikit-images/intents02w.png#lightbox)

-----

展開 `IntentsSupported` 金鑰，並新增此延伸模組將支援之任何意圖類別的名稱。 針對範例 MonkeyChat 應用程式，它支援 `INSendMessageIntent` ：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![INSendMessageIntent 鍵](implementing-sirikit-images/intents09.png)](implementing-sirikit-images/intents09.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![INSendMessageIntent 鍵](implementing-sirikit-images/intents09w.png)](implementing-sirikit-images/intents09w.png#lightbox)

-----

如果應用程式選擇性地要求使用者必須登入裝置才能使用指定的意圖，請展開 `IntentRestrictedWhileLocked` 金鑰，並新增具有限制存取之意圖的類別名稱。 針對範例 MonkeyChat 應用程式，使用者必須登入才能傳送聊天訊息，我們已新增 `INSendMessageIntent` ：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![新增的 INSendMessageIntent 索引鍵](implementing-sirikit-images/intents10.png)](implementing-sirikit-images/intents10.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![新增的 INSendMessageIntent 索引鍵](implementing-sirikit-images/intents10w.png)](implementing-sirikit-images/intents10w.png#lightbox)

-----

如需可用意圖網域的完整清單，請參閱 Apple 的 [意圖網域參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)。

### <a name="configuring-the-main-class"></a>設定主要類別

接下來，開發人員必須設定主要類別，作為意圖延伸至 Siri 的主要進入點。 它必須是符合委派的子類別 `INExtension` `IINIntentHandler` 。 例如：

```csharp
using System;
using System.Collections.Generic;

using Foundation;
using Intents;

namespace MonkeyChatIntents
{
    [Register ("IntentHandler")]
    public class IntentHandler : INExtension, IINSendMessageIntentHandling, IINSearchForMessagesIntentHandling, IINSetMessageAttributeIntentHandling
    {
        #region Constructors
        protected IntentHandler (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override NSObject GetHandler (INIntent intent)
        {
            // This is the default implementation.  If you want different objects to handle different intents,
            // you can override this and return the handler you want for that particular intent.

            return this;
        }
        #endregion
        ...
    }
}
```

有一個單獨方法，應用程式必須在意圖擴充功能的主要類別（方法）上執行 `GetHandler` 。 這個方法會透過 SiriKit 傳遞意圖，而應用程式必須傳回符合指定意圖類型的 **意圖處理常式** 。

由於範例 MonkeyChat 應用程式只會處理一個意圖，因此它會在方法中傳回本身 `GetHandler` 。 如果延伸模組已處理多個意圖，則開發人員會為每個意圖型別加入一個類別，並根據 `Intent` 傳遞給方法的傳回實例。

### <a name="handling-the-resolve-stage"></a>處理解析階段

「解析」階段是意圖延伸模組將從 Siri 中解釋並驗證傳入的參數，並透過使用者的對話設定的參數。

對於從 Siri 傳送的每個參數，都有一個 `Resolve` 方法。 應用程式必須針對應用程式可能需要的每個參數來執行此方法，Siri 的協助才能取得使用者的正確答案。

在範例 MonkeyChat 應用程式的案例中，意圖延伸模組將需要一個或多個收件者來傳送訊息給。 針對清單中的每個收件者，此延伸模組將需要執行可能具有下列結果的連絡人搜尋：

- 只找到一個相符的連絡人。
- 找到兩個或多個相符的連絡人。
- 找不到相符的連絡人。

此外，MonkeyChat 需要訊息主體的內容。 如果使用者未提供此內容，Siri 需要提示使用者輸入內容。

意圖延伸模組將需要適當地處理每個案例。

```csharp
[Export ("resolveRecipientsForSearchForMessages:withCompletion:")]
public void ResolveRecipients (INSendMessageIntent intent, Action<INPersonResolutionResult []> completion)
{
    var recipients = intent.Recipients;
    // If no recipients were provided we'll need to prompt for a value.
    if (recipients.Length == 0) {
        completion (new INPersonResolutionResult [] { (INPersonResolutionResult)INPersonResolutionResult.NeedsValue });
        return;
    }

    var resolutionResults = new List<INPersonResolutionResult> ();

    foreach (var recipient in recipients) {
        var matchingContacts = new INPerson [] { recipient }; // Implement your contact matching logic here to create an array of matching contacts
        if (matchingContacts.Length > 1) {
            // We need Siri's help to ask user to pick one from the matches.
            resolutionResults.Add (INPersonResolutionResult.GetDisambiguation (matchingContacts));
        } else if (matchingContacts.Length == 1) {
            // We have exactly one matching contact
            resolutionResults.Add (INPersonResolutionResult.GetSuccess (recipient));
        } else {
            // We have no contacts matching the description provided
            resolutionResults.Add ((INPersonResolutionResult)INPersonResolutionResult.Unsupported);
        }
    }

    completion (resolutionResults.ToArray ());
}

[Export ("resolveContentForSendMessage:withCompletion:")]
public void ResolveContent (INSendMessageIntent intent, Action<INStringResolutionResult> completion)
{
    var text = intent.Content;
    if (!string.IsNullOrEmpty (text))
        completion (INStringResolutionResult.GetSuccess (text));
    else
        completion ((INStringResolutionResult)INStringResolutionResult.NeedsValue);
}
```

如需詳細資訊，請參閱我們 [的解析階段參考](~/ios/platform/sirikit/understanding-sirikit.md) 和 Apple 的 [解析和處理意圖參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ResolvingandHandlingIntents.html#//apple_ref/doc/uid/TP40016875-CH5-SW1)。

### <a name="handling-the-confirm-stage"></a>處理確認階段

[確認] 階段是意圖延伸模組檢查的位置，以查看是否有所有資訊可滿足使用者的要求。 應用程式想要將確認傳送至 Siri 的所有支援詳細資料，讓您可以向使用者確認這是預期的動作。

```csharp
[Export ("confirmSendMessage:completion:")]
public void ConfirmSendMessage (INSendMessageIntent intent, Action<INSendMessageIntentResponse> completion)
{
    // Verify user is authenticated and the app is ready to send a message.
    ...

    var userActivity = new NSUserActivity (nameof (INSendMessageIntent));
    var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Ready, userActivity);
    completion (response);
}
```

如需詳細資訊，請參閱我們 [的確認階段參考](~/ios/platform/sirikit/understanding-sirikit.md)。

### <a name="processing-the-intent"></a>處理意圖

這是意圖延伸實際執行工作以履行使用者要求的點，並將結果傳回給 Siri，讓使用者可以收到通知。

```csharp
public void HandleSendMessage (INSendMessageIntent intent, Action<INSendMessageIntentResponse> completion)
{
    // Implement the application logic to send a message here.
    ...

    var userActivity = new NSUserActivity (nameof (INSendMessageIntent));
    var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, userActivity);
    completion (response);
}

public void HandleSearchForMessages (INSearchForMessagesIntent intent, Action<INSearchForMessagesIntentResponse> completion)
{
    // Implement the application logic to find a message that matches the information in the intent.
    ...

    var userActivity = new NSUserActivity (nameof (INSearchForMessagesIntent));
    var response = new INSearchForMessagesIntentResponse (INSearchForMessagesIntentResponseCode.Success, userActivity);

    // Initialize with found message's attributes
    var sender = new INPerson (new INPersonHandle ("sarah@example.com", INPersonHandleType.EmailAddress), null, "Sarah", null, null, null);
    var recipient = new INPerson (new INPersonHandle ("+1-415-555-5555", INPersonHandleType.PhoneNumber), null, "John", null, null, null);
    var message = new INMessage ("identifier", "I am so excited about SiriKit!", NSDate.Now, sender, new INPerson [] { recipient });
    response.Messages = new INMessage [] { message };
    completion (response);
}

public void HandleSetMessageAttribute (INSetMessageAttributeIntent intent, Action<INSetMessageAttributeIntentResponse> completion)
{
    // Implement the application logic to set the message attribute here.
    ...

    var userActivity = new NSUserActivity (nameof (INSetMessageAttributeIntent));
    var response = new INSetMessageAttributeIntentResponse (INSetMessageAttributeIntentResponseCode.Success, userActivity);
    completion (response);
}
```

如需詳細資訊，請參閱我們 [的控制碼階段參考](~/ios/platform/sirikit/understanding-sirikit.md)。

## <a name="adding-an-intents-ui-extension"></a>新增意圖 UI 延伸模組

選擇性的意圖 UI 延伸模組提供了一個機會，讓應用程式的 UI 和商標進入 Siri 體驗，並讓使用者感覺能夠連線到應用程式。 使用此延伸模組，應用程式可以將品牌以及視覺效果和其他資訊帶入文字記錄中。

[![範例意圖 UI 延伸模組輸出](implementing-sirikit-images/intentsui01.png)](implementing-sirikit-images/intentsui01.png#lightbox)

就像意圖擴充功能，開發人員會針對意圖 UI 延伸進行下列步驟：

- 將意圖 UI 延伸專案新增至 Xamarin 應用程式解決方案。
- 設定意圖 UI 延伸模組檔案 `Info.plist` 。
- 修改意圖 UI 擴充功能的主要類別。

如需詳細資訊，請參閱我們 [的意圖 UI 延伸模組參考](~/ios/platform/sirikit/understanding-sirikit.md) 和 Apple [提供自訂介面參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ProvidingaCustomInterface.html#//apple_ref/doc/uid/TP40016875-CH7-SW1)。

### <a name="creating-the-extension"></a>建立延伸模組

若要將意圖 UI 延伸新增至方案，請執行下列動作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 以滑鼠右鍵按一下**Solution Pad**中的**方案名稱**，然後選取 [**加入**  >  **新專案**]。
2. 從對話方塊中選取 [ **iOS**  >  **擴充**功能  >  **意圖 UI 延伸**模組]，然後按一下 [**下一步]** 按鈕： 

    [![選取意圖 UI 延伸模組](implementing-sirikit-images/intents11.png)](implementing-sirikit-images/intents11.png#lightbox)
3. 接下來輸入意圖延伸的 **名稱** ，然後按 [ **下一步]** 按鈕： 

    [![輸入意圖延伸模組的名稱](implementing-sirikit-images/intents12.png)](implementing-sirikit-images/intents12.png#lightbox)
4. 最後，按一下 [ **建立** ] 按鈕，將意圖延伸新增至應用程式解決方案： 

    [![將意圖擴充功能新增至應用程式解決方案](implementing-sirikit-images/intents13.png)](implementing-sirikit-images/intents13.png#lightbox)
5. 在 **方案總管**中，以滑鼠右鍵按一下新建立之意圖延伸模組的 [ **參考** ] 資料夾。 檢查上面建立的應用程式 (常見的共用程式碼程式庫專案的名稱) 然後按一下 [ **確定]** 按鈕： 

    [![選取通用共用程式碼程式庫專案的名稱](implementing-sirikit-images/intents14.png)](implementing-sirikit-images/intents14.png#lightbox)
    
# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 以滑鼠右鍵按一下**方案總管**中的**方案名稱**，然後選取 [**加入**  >  **新專案**]。
2. 從對話方塊中選取 [ **iOS**  >  **延伸**模組  >  **意圖 UI 延伸**模組]，然後按一下 [**下一步]** 按鈕。
3. 接下來輸入意圖延伸模組的 **名稱** ，然後按一下 [ **確定]** 按鈕。
4. 在 **方案總管**中，以滑鼠右鍵按一下新建立之意圖延伸模組的 [ **參考** ] 資料夾。 檢查上面建立的應用程式 (常見的共用程式碼程式庫專案的名稱) 然後按一下 [ **確定]** 按鈕。
    
-----

### <a name="configuring-the-infoplist"></a>設定資訊. plist

設定意圖 UI 延伸模組的檔案 `Info.plist` 以使用應用程式。

就像任何一般的應用程式擴充功能一樣，應用程式也會有和的現有金鑰 `NSExtension` `NSExtensionAttributes` 。 在意圖延伸中，有一個必須設定的新屬性：

[![必須設定的一個新屬性](implementing-sirikit-images/intents03.png)](implementing-sirikit-images/intents03.png#lightbox)

**IntentsSupported** 是必要的，而且是由應用程式想要從意圖延伸模組支援的意圖類別名稱陣列所組成。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

若要設定意圖 UI 延伸模組的檔案 `Info.plist` ，請在 **方案總管** 中按兩下該檔案以開啟它進行編輯。 接下來，切換至 **來源** 視圖，然後 `NSExtension` `NSExtensionAttributes` 在編輯器中展開和索引鍵：

[![編輯器中的 NSExtension 和 NSExtensionAttributes 索引鍵](implementing-sirikit-images/intents04.png)](implementing-sirikit-images/intents04.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要設定意圖 UI 延伸模組的檔案 `Info.plist` ，請在 **方案總管** 中按兩下該檔案以開啟它進行編輯。 `NSExtension` `NSExtensionAttributes` 在編輯器中展開和索引鍵：

[![編輯器中的 NSExtension 和 NSExtensionAttributes 索引鍵](implementing-sirikit-images/intents04w.png)](implementing-sirikit-images/intents04w.png#lightbox)

-----

展開 `IntentsSupported` 金鑰，並新增此延伸模組將支援之任何意圖類別的名稱。 針對範例 MonkeyChat 應用程式，它支援 `INSendMessageIntent` ：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![INSendMessageIntent 鍵](implementing-sirikit-images/intents15.png)](implementing-sirikit-images/intents15.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![INSendMessageIntent 鍵](implementing-sirikit-images/intents15w.png)](implementing-sirikit-images/intents15w.png#lightbox)

-----

如需可用意圖網域的完整清單，請參閱 Apple 的 [意圖網域參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)。

### <a name="configuring-the-main-class"></a>設定主要類別

設定主要類別，作為意圖 UI 延伸至 Siri 的主要進入點。 它必須是符合介面的子類別 `UIViewController` `IINUIHostedViewController` 。 例如：

```csharp
using System;
using Foundation;
using CoreGraphics;
using Intents;
using IntentsUI;
using UIKit;

namespace MonkeyChatIntentsUI
{
    public partial class IntentViewController : UIViewController, IINUIHostedViewControlling
    {
        #region Constructors
        protected IntentViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }

        public override void DidReceiveMemoryWarning ()
        {
            // Releases the view if it doesn't have a superview.
            base.DidReceiveMemoryWarning ();

            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Public Methods
        [Export ("configureWithInteraction:context:completion:")]
        public void Configure (INInteraction interaction, INUIHostedViewContext context, Action<CGSize> completion)
        {
            // Do configuration here, including preparing views and calculating a desired size for presentation.

            if (completion != null)
                completion (DesiredSize ());
        }

        [Export ("desiredSize:")]
        public CGSize DesiredSize ()
        {
            return ExtensionContext.GetHostedViewMaximumAllowedSize ();
        }
        #endregion
    }
}
```

Siri 會將 `INInteraction` 類別實例傳遞給 `Configure` `UIViewController` 意圖 UI 延伸內的實例方法。

`INInteraction`物件會提供三個主要的資訊給延伸模組：

1. 正在處理的意圖物件。
2. 意圖延伸的和方法中的意圖回應物件 `Confirm` `Handle` 。
3. 互動狀態，定義應用程式與 Siri 之間互動的狀態。

`UIViewController`實例是與 Siri 互動的準則類別，因為它繼承自 `UIViewController` ，所以它可以存取 UIKit 的所有功能。

當 Siri 呼叫它的方法時， `Configure` `UIViewController` 它會在 view 內容中傳遞，指出 view 控制器會裝載于 Siri Snippit 或 Maps 卡中。

Siri 也會傳入完成處理常式，讓應用程式在應用程式完成設定之後，必須傳回所需的視圖大小。

### <a name="design-the-ui-in-ios-designer"></a>在 iOS 設計工具中設計 UI

在 iOS 設計工具中配置意圖 UI 延伸的使用者介面。 按兩下方案總管中的延伸模組檔案 `MainInterface.storyboard` ，將**Solution Explorer**它開啟以供編輯。 拖曳所有必要的 UI 元素，以建立消費者介面並儲存變更。

> [!IMPORTANT]
> 雖然可以將互動式專案（例如或）新增 `UIButtons` `UITextFields` 至意圖 ui 延伸模組，但 `UIViewController` 這些專案在非互動式的意圖 ui 中是完全禁止的，使用者將無法與其互動。

### <a name="wire-up-the-user-interface"></a>連接消費者介面

在 iOS 設計工具中建立意圖 UI 延伸模組的消費者介面，請編輯子 `UIViewController` 類別並覆寫方法，如下所示 `Configure` ：

```csharp
[Export ("configureWithInteraction:context:completion:")]
public void Configure (INInteraction interaction, INUIHostedViewContext context, Action<CGSize> completion)
{
    // Do configuration here, including preparing views and calculating a desired size for presentation.
    ...

    // Return desired size
    if (completion != null)
        completion (DesiredSize ());
}

[Export ("desiredSize:")]
public CGSize DesiredSize ()
{
    return ExtensionContext.GetHostedViewMaximumAllowedSize ();
}
```

### <a name="overriding-the-default-siri-ui"></a>覆寫預設的 Siri UI

意圖 UI 延伸模組一律會與其他 Siri 內容一起顯示，例如 UI 頂端的應用程式圖示和名稱，或者根據意圖，按鈕 (例如 [傳送] 或 [取消]) 可能會顯示在底部。

有些情況下，應用程式預設會將 Siri 顯示的資訊取代為使用者，例如訊息或對應，應用程式可以將預設體驗取代為應用程式量身訂做的預設體驗。

如果意圖 UI 延伸模組需要覆寫預設 Siri UI 的專案，子 `UIViewController` 類別將需要執行 `IINUIHostedViewSiriProviding` 介面，並加入以顯示特定的介面元素。

將下列程式碼新增至子 `UIViewController` 類別，以告訴 Siri 意圖 UI 延伸已顯示訊息內容：

```csharp
public bool DisplaysMessage {
    get {return true;}
}
```

### <a name="considerations"></a>考量

Apple 建議開發人員在設計和實行意圖 UI 延伸時，考慮下列事項：

- 請**關注記憶體使用量**-因為意圖 UI 延伸模組是暫時性的，而且只會在短時間內顯示，因此系統會強加比使用完整應用程式更緊密的記憶體限制。
- **考慮最小和最大的視圖大小** -確定意圖 UI 延伸模組在每個 iOS 裝置類型、大小和方向上都能美觀。 此外，應用程式傳回給 Siri 的所需大小可能無法被授與。
- 請再次**使用彈性和**自動調整的版面配置模式，以確保 UI 在每個裝置上都能美觀。

## <a name="summary"></a>摘要

本文涵蓋了 SiriKit，並示範如何將它新增至 Xamarin 應用程式，以提供可供使用者使用 Siri 和 iOS 裝置上的 Maps 應用程式存取的服務。

## <a name="related-links"></a>相關連結

- [ElizaChat 範例](/samples/xamarin/ios-samples/ios10-elizachat)
- [SiriKit 程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [意圖架構參考](https://developer.apple.com/reference/intents)
- [意圖 UI 架構參考](https://developer.apple.com/reference/intentsui)
- [意圖網域參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)