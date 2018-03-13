---
title: "實作 SiriKit"
description: "本文涵蓋 Xamarin.iOS 應用程式中實作 SiriKit 支援所需的步驟。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 20FFB981-EB10-48BA-BF79-40F37F0291EB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 0e271fb78cfd225f9ccdae9a515685e89bfd7ac2
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="implementing-sirikit"></a>實作 SiriKit

_本文涵蓋 Xamarin.iOS 應用程式中實作 SiriKit 支援所需的步驟。_



新增 ios 10 SiriKit 允許 Xamarin.iOS 應用程式提供的 iOS 裝置上使用 Siri 和對應的應用程式的使用者可以存取的服務。 本文涵蓋 Xamarin.iOS 應用程式中實作 SiriKit 支援將所需的對應方式擴充功能，目的 UI 延伸模組與詞彙所需的步驟。

Siri 搭配的概念**網域**，群組的知道相關工作的動作。 每個應用程式具有 Siri 的互動必須歸類到其中一個其已知的服務網域，如下所示：

- 音訊或視訊電話。
- 預約的賽車。
- 管理健身。
- 訊息。
- 搜尋相片。
- 傳送或接收付款。

當使用者提出要求的 Siri 涉及其中一個應用程式擴充功能的服務時，SiriKit 傳送副檔名**意圖**物件，描述使用者的要求，以及任何支援的資料。 應用程式擴充功能會產生適當**回應**物件指定**意圖**，詳細說明如何擴充功能來處理要求。

本指南將會顯示簡單的範例包括 SiriKit 到現有應用程式的支援。 這是因為此範例中，我們將使用假 MonkeyChat 應用程式：

[![](implementing-sirikit-images/monkeychat01.png "MonkeyChat 圖示")](implementing-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat 會保留自己的使用者的好友連絡活頁簿，每個螢幕名稱 （例如 Bobo 例如) 相關聯，並可讓使用者在其顯示名稱，將文字聊天室傳送至每個 friend。

## <a name="extending-the-app-with-sirikit"></a>擴充應用程式與 SiriKit

中所示[了解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md)指南中，有三個主要部分以擴充應用程式，但 SiriKit:

[![](implementing-sirikit-images/elements01.png "擴充應用程式與 SiriKit 圖表")](implementing-sirikit-images/elements01.png#lightbox)

它們包括：

1. **對應方式擴充**-確認使用者回應、 確認應用程式來處理要求，並會實際執行工作，以符合使用者的要求。
2. **對應 UI 延伸模組** - *選擇性*、 提供可在 Siri 環境中的回覆的自訂 UI 和可以讓應用程式 UI 及商標到 Siri 豐富使用者經驗。
3. **應用程式**-應用程式提供使用者特定的詞彙，以協助 Siri 加以使用。 

在下列各節中詳細，將會涵蓋所有的這些項目，以及將它們包含在應用程式的步驟。


## <a name="preparing-the-app"></a>準備應用程式

SiriKit 建置在擴充功能，不過，將任何擴充功能加入至應用程式之前, 有開發人員必須協助 SiriKit 採用的幾件事。

### <a name="moving-common-shared-code"></a>移動共用通用程式碼 

首先，開發人員可以將共用的通用程式碼部分之間移動應用程式和擴充功能至_共用專案_，_可攜式類別庫 (PCLs)_或_原生程式庫_。

延伸模組必須能夠執行的所有應用程式的工作。 在範例 MonkeyChat 應用程式，像是尋找連絡人，加入新的連絡人、 使用條款傳送訊息及擷取訊息記錄。

將這個通用程式碼移到共用的專案、 PCL 或原生程式庫這樣可讓您更容易維護的一個常見的位置中的程式碼，並確定擴充功能與父應用程式，使用者也提供統一的體驗與功能。

範例應用程式 MonkeyChat，在資料模型和處理的程式碼，例如網路與資料庫存取將會移至原生程式庫。

請執行下列動作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 啟動 Visual Studio for Mac 並開啟 MonkeyChat 應用程式。
2. 以滑鼠右鍵按一下方案名稱在**方案板**選取**新增** > **新的專案...**: 

    [![](implementing-sirikit-images/prep01.png "加入新的專案")](implementing-sirikit-images/prep01.png#lightbox)
3. 選取**iOS** > **文件庫** > **類別庫**按一下**下一步**按鈕： 

    [![](implementing-sirikit-images/prep02.png "選取 類別庫")](implementing-sirikit-images/prep02.png#lightbox)
4. 輸入`MonkeyChatCommon`如**名稱**按一下**建立**按鈕： 

    [![](implementing-sirikit-images/prep03.png "請輸入名稱 MonkeyChatCommon")](implementing-sirikit-images/prep03.png#lightbox)
5. 以滑鼠右鍵按一下**參考**資料夾中的主應用程式**方案總管 中**選取**編輯參考...**.請檢查**MonkeyChatCommon**專案，然後按一下**確定**按鈕： 

    [![](implementing-sirikit-images/prep05.png "MonkeyChatCommon 專案簽入")](implementing-sirikit-images/prep05.png#lightbox)
6. 在**方案總管 中**，從主應用程式將通用的共用程式碼拖曳至原生程式庫。
7. 如果 MonkeyChat，拖曳**DataModels**和**處理器**從主應用程式至原生程式庫資料夾： 

    [![](implementing-sirikit-images/prep06.png "在 [方案總管] 中的 [DataModels 和處理器] 資料夾")](implementing-sirikit-images/prep06.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 啟動 Visual Studio，並開啟 MonkeyChat 應用程式。
2. 以滑鼠右鍵按一下方案名稱在**方案總管 中**選取**新增** > **新的專案...**.
3. 選取**Visual C#** > **共用專案**按一下**下一步**按鈕： 

    [![](implementing-sirikit-images/prep02w.png "選取 類別庫")](implementing-sirikit-images/prep02w.png#lightbox)
4. 輸入`MonkeyChatCommon`如**名稱**按一下**建立** 按鈕。
5. 以滑鼠右鍵按一下**參考**資料夾中的主應用程式**方案總管 中**選取**編輯參考...**.請檢查**MonkeyChatCommon**專案，然後按一下**確定**按鈕： 

    [![](implementing-sirikit-images/prep05w.png "MonkeyChatCommon 專案簽入")](implementing-sirikit-images/prep05w.png#lightbox)
6. 在**方案總管 中**，共用通用程式碼拖曳至共用專案的主要應用程式。
7. 如果 MonkeyChat，拖曳**DataModels**和**處理器**從主應用程式至原生程式庫的資料夾。

-----

編輯的任何檔案移至原生程式庫，並將變更以符合程式庫的命名空間。 例如，變更`MonkeyChat`至`MonkeyChatCommon`:

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

接下來，請回到主要的應用程式，新增`using`原生程式庫命名空間陳述式任何位置的應用程式會使用其中一個已移動的類別：

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

### <a name="architecting-the-app-for-extensions"></a>架構擴充功能的應用程式

一般而言，應用程式將會註冊多個對應方式和開發人員必須確保應用程式的適當數目的意圖擴充功能的架構。

在應用程式需要一個以上的意圖的情況下，開發人員可以選擇將所有的意圖處理放入一個意圖擴充功能，或為每個項目的建立個別的意圖擴充功能。

如果選擇為每個項目的建立個別的意圖擴充功能，開發人員可能會重複大量的未定案程式碼中每個延伸模組結束，並建立大量的處理器與記憶體額外負荷。

若要協助選擇下列兩個選項，請參閱是否任一項目的自然隸屬在一起。 比方說，進行音訊及視訊電話應用程式可能會想要納入這兩個這些項目的單一意圖擴充功能時處理類似的工作，並無法提供大部分的程式碼重複使用。

任何的意圖或群組不符合現有群組的對應方式，來包含它們的應用程式的方案中建立新的意圖延伸模組。


### <a name="setting-the-required-entitlements"></a>設定必要的權利

包含 SiriKit 整合任何 Xamarin.iOS 應用程式，必須有正確的權利設定。 如果開發人員未正確設定這些必要的權利，它們將無法安裝或測試實際 iOS （大於或等於 10） 的硬體上，這也是自 iOS 10 需求的應用程式模擬器不支援 SiriKit。

請執行下列動作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 按兩下`Entitlements.plist`檔案**方案總管 中**開啟進行編輯。
2. 切換至**來源** 索引標籤。
3. 新增`com.apple.developer.siri`**屬性**，將**類型**至`Boolean`和**值**至`Yes`: 

    [![](implementing-sirikit-images/setup01.png "加入 com.apple.developer.siri 屬性")](implementing-sirikit-images/setup01.png#lightbox)
4. 將變更儲存到檔案。
5. 按兩下**專案檔**中**方案總管 中**開啟進行編輯。
6. 選取**iOS 套件組合簽署**，並確定`Entitlements.plist`中選取檔案**自訂權利**欄位： 

    [![](implementing-sirikit-images/setup02.png "自訂權利欄位中選取 Entitlements.plist 檔案")](implementing-sirikit-images/setup02.png#lightbox)
7. 按一下 [確定] 按鈕以儲存所做的變更。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 按兩下`Entitlements.plist`檔案**方案總管 中**開啟進行編輯。
3. 新增`com.apple.developer.siri`**屬性**，將**類型**至`Boolean`和**值**至`Yes`: 

    [![](implementing-sirikit-images/setup01w.png "加入 com.apple.developer.siri 屬性")](implementing-sirikit-images/setup01w.png#lightbox)
4. 將變更儲存到檔案。
5. 按兩下**專案檔**中**方案總管 中**開啟進行編輯。
6. 選取**iOS 套件組合簽署**，並確定`Entitlements.plist`中選取檔案**自訂權利**欄位。

-----

完成時，應用程式的`Entitlements.plist`檔案應該看起來如下所示 （中開啟外部編輯器）：

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

### <a name="correctly-provisioning-the-app"></a>正確佈建應用程式

因為嚴格 Apple 已放置於周圍 SiriKit 架構，實作 SiriKit 任何 Xamarin.iOS 應用程式的安全性_必須_有正確的應用程式識別碼和權利 （請參閱上一節），而且必須經過適當的佈建設定檔。

依下列方式在 Mac 上：

1. 在 web 瀏覽器中，瀏覽至[http://developer.apple.com](http://developer.apple.com)和登入您的帳戶。
2. 按一下**憑證**，**識別碼**和**設定檔**。
3. 選取**佈建的設定檔**選取**應用程式識別碼**，然後按一下 [  **+**  ] 按鈕。
4. 輸入**名稱**新設定檔。
5. 輸入**套件組合識別碼**遵循 Apple 的命名建議。
6. 向下捲動至**應用程式服務**區段中，選取**SiriKit**按一下**繼續**按鈕： 

    [![](implementing-sirikit-images/setup03.png "選取 SiriKit")](implementing-sirikit-images/setup03.png#lightbox)
7. 驗證所有的設定，然後**送出**應用程式識別碼。
8. 選取**佈建的設定檔** > **開發**，按一下   **+** 按鈕，選取**Apple ID**，然後按一下 **繼續**。
9. 按一下 選取**所有**，然後按一下 **繼續**。
10. 按一下**全選**一次，然後按一下 **繼續**。
11. 輸入**設定檔名稱**使用 Apple 的命名建議，請按一下 **繼續**。
12. 啟動 Xcode。
13. 從 [Xcode] 功能表選取**喜好設定...**
14. 選取**帳戶**，然後按一下 **檢視詳細資料...** 按鈕： 

    [![](implementing-sirikit-images/setup04.png "選取帳戶")](implementing-sirikit-images/setup04.png#lightbox)
15. 按一下**下載所有設定檔**在左下角的按鈕： 

    [![](implementing-sirikit-images/setup05.png "下載的所有設定檔")](implementing-sirikit-images/setup05.png#lightbox)
16. 請確認**佈建設定檔**建立更新版本已安裝在 Xcode 中。
17. 開啟專案，以便將 Visual Studio 中的 SiriKit 支援以 for mac。
18. 按兩下`Info.plist`檔案**方案總管 中**。
18. 請確認**配套識別碼**符合上述的 Apple 開發人員入口網站中建立： 

    [![](implementing-sirikit-images/setup06.png "套件組合識別碼")](implementing-sirikit-images/setup06.png#lightbox)
18. 在**方案總管 中**，選取**專案**。
19. 以滑鼠右鍵按一下專案，然後選取**選項**。
21. 選取**iOS 套件組合簽署**，選取**簽署識別**和**佈建設定檔**上面所建立： 

    [![](implementing-sirikit-images/setup07.png "選取的簽署識別和佈建設定檔")](implementing-sirikit-images/setup07.png#lightbox)
22. 按一下 [確定] 按鈕以儲存所做的變更。

> [!IMPORTANT]
> **注意：**測試 SiriKit 只適用於真實 iOS 10 個硬體裝置而不是在 10 的 iOS 模擬器。 如果有安裝 SiriKit 的問題啟用實際的硬體上的 Xamarin.iOS 應用程式，請確認必要的權利、 應用程式識別碼、 簽章識別碼和佈建設定檔已正確設定 Apple 開發人員入口網站和 Visual Studio 中 for mac。

### <a name="requesting-siri-authorization"></a>要求 Siri 授權

應用程式將任何使用者特定詞彙或對應方式延伸模組會連接到 Siri 之前，它必須要求授權的使用者存取使用 Siri。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

編輯應用程式的`Info.plist`檔案中，切換至**來源**檢視和加入`NSSiriUsageDescription`索引鍵的字串值，描述應用程式如何使用 Siri，以及將傳送的資料類型。 例如，MonkeyChat 應用程式可能是說: 「 MonkeyChat 連絡人將會傳送至 Siri":

[![](implementing-sirikit-images/request01.png "Info.plist 編輯器中 NSSiriUsageDescription")](implementing-sirikit-images/request01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

編輯應用程式的`Info.plist`檔案，然後加入`NSSiriUsageDescription`索引鍵的字串值，描述應用程式如何使用 Siri，以及將傳送的資料類型。 例如，MonkeyChat 應用程式可能是說: 「 MonkeyChat 連絡人將會傳送至 Siri":

[![](implementing-sirikit-images/request01w.png "Info.plist 編輯器中 NSSiriUsageDescription")](implementing-sirikit-images/request01w.png#lightbox)

-----

呼叫`RequestSiriAuthorization`方法`INPreferences`類別時第一次啟動應用程式。 編輯`AppDelegate.cs`類別，並讓`FinishedLaunching`方法看起來像下列：


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

第一次呼叫此方法時，警示會顯示提示使用者允許存取 Siri 應用程式。 開發人員加入至訊息`NSSiriUsageDescription`上方將會顯示在此警示。 如果使用者一開始會拒絕存取，他們可以使用**設定**授與存取權的應用程式的應用程式。

在任何時候，應用程式可以檢查應用程式能夠藉由呼叫存取 Siri`SiriAuthorizationStatus`方法`INPreferences`類別。

### <a name="localization-and-siri"></a>因為當地語系化和 Siri

在 iOS 裝置，使用者就能選取的語言不同的 Siri 然後系統預設值。 當使用以當地語系化資料時，應用程式必須使用`SiriLanguageCode`方法`INPreferences`Siri 從取得的語言代碼的類別。 例如: 

```csharp
var language = INPreferences.SiriLanguageCode();

// Take action based on language
if (language == "en-US") {
    // Do something...
}
```

### <a name="adding-user-specific-vocabulary"></a>新增使用者特定詞彙

使用者特定詞彙即將提供給個別使用者的應用程式特有的單字或片語。 這些將會提供在執行階段主應用程式 （不應用程式擴充功能） 從做為已排序集合的詞彙，訂購的最大顯著性使用量優先順序的使用者，最重要的詞彙清單的開頭。

使用者特定詞彙必須屬於下列類別之一：

- 連絡人的名稱 （也就不受連絡人架構）。
- 相片的標記。
- 相片專輯名稱。
- 健身名稱。

選取時若要註冊為自訂的詞彙的詞彙，只能選擇條款，可能會造成誤解的其他人不熟悉的應用程式。 永遠不會註冊一般詞彙例如"My 健身 」 或 「 我的專輯 」。 例如，MonkeyChat 應用程式將會註冊在使用者通訊錄中的每一個連絡人與相關聯的暱稱。

應用程式提供使用者特定詞彙，藉由呼叫`SetVocabularyStrings`方法`INVocabulary`類別，並傳入`NSOrderedSet`從主應用程式的集合。 應用程式應該一律呼叫`RemoveAllVocabularyStrings`方法第一，若要移除任何現有的條款，然後再加入新的。 例如: 

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

使用這個程式碼的位置中，它可能會呼叫，如下所示：

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
> **注意：** Siri 視為提示的自訂的詞彙，並將會併入一樣多越好的術語。 不過，空間限制讓重要註冊自訂的詞彙為_只_可能會造成混淆，因此保持為最小值的已註冊的詞彙總數的術語。

如需詳細資訊，請參閱我們[使用者特定的詞彙參考](~/ios/platform/sirikit/understanding-sirikit.md)與 Apple[指定自訂詞彙參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1)。

### <a name="adding-app-specific-vocabulary"></a>新增應用程式特定詞彙

應用程式特定詞彙定義的特定的單字和片語，將已知的所有應用程式的使用者，例如一種載具，型別或健身名稱。 由於這些是應用程式的一部分，其定義位於`AppIntentVocabulary.plist`檔案做為主要的應用程式套件組合的一部分。 此外，應該當地語系化這些單字和片語。

應用程式特定詞彙必須屬於下列類別之一：

- 寫選項。
- 健身名稱。

應用程式特定詞彙檔案包含兩個根層級的索引鍵：

- `ParameterVocabularies` **需要**-定義應用程式的自訂的條款與它們會套用到目的參數。
- `IntentPhrases` **選擇性**-包含使用自訂的條款中所定義的範例片語`ParameterVocabularies`。

每個項目`ParameterVocabularies`必須指定的識別碼字串、 詞彙和一詞適用於的用途。 此外，單一詞彙可能會套用至多個對應方式。

如需可接受的值與所需的檔案結構的完整清單，請參閱 Apple[應用程式詞彙檔案格式參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1)。

若要加入`AppIntentVocabulary.plist`應用程式專案檔案中，執行下列動作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 以滑鼠右鍵按一下專案名稱中**方案總管 中**選取**新增** > **新的檔案...**  >  **iOS**:

    [![](implementing-sirikit-images/plist01.png "加入屬性清單")](implementing-sirikit-images/plist01.png#lightbox) 
2. 按兩下`AppIntentVocabulary.plist`檔案**方案總管 中**開啟進行編輯。
3. 按一下 **+** 新增機碼，設定**名稱**至`ParameterVocabularies`和**類型**至`Array`:

    [![](implementing-sirikit-images/plist02.png "將名稱設定為 ParameterVocabularies 和陣列類型")](implementing-sirikit-images/plist02.png#lightbox)
4. 展開`ParameterVocabularies`按一下 **+** 按鈕，然後設定**類型**至`Dictionary`:

    [![](implementing-sirikit-images/plist03.png "將類型設為字典")](implementing-sirikit-images/plist03.png#lightbox)
5. 按一下 **+** 若要加入新的金鑰，請設定**名稱**至`ParameterNames`和**類型**至`Array`:

    [![](implementing-sirikit-images/plist04.png "將名稱設定為 ParameterNames 和陣列類型")](implementing-sirikit-images/plist04.png#lightbox)
6. 按一下 **+** 要加入的新金鑰**類型**的`String`和做為其中一個可用的參數名稱的值。 例如， `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05.png "INStartWorkoutIntent.workoutName 金鑰")](implementing-sirikit-images/plist05.png#lightbox)
7. 新增`ParameterVocabulary`鍵`ParameterVocabularies`索引鍵**類型**的`Array`:

    [![](implementing-sirikit-images/plist06.png "新增 ParameterVocabulary 金鑰 ParameterVocabularies 索引鍵的陣列類型")](implementing-sirikit-images/plist06.png#lightbox)
8. 加入新的金鑰與**類型**的`Dictionary`:

    [![](implementing-sirikit-images/plist07.png "加入新的金鑰與字典的型別")](implementing-sirikit-images/plist07.png#lightbox)
9. 新增`VocabularyItemIdentifier`索引鍵**類型**的`String`和詞彙指定唯一的識別碼：

    [![](implementing-sirikit-images/plist08.png "加入 VocabularyItemIdentifier 鍵字串類型，並指定唯一識別碼")](implementing-sirikit-images/plist08.png#lightbox)
10. 新增`VocabularyItemSynonyms`索引鍵**類型**的`Array`:

    [![](implementing-sirikit-images/plist09.png "加入 VocabularyItemSynonyms 鍵，類型的陣列")](implementing-sirikit-images/plist09.png#lightbox)
11. 加入新的金鑰與**類型**的`Dictionary`:

    [![](implementing-sirikit-images/plist10.png "加入新的金鑰與字典的型別")](implementing-sirikit-images/plist10.png#lightbox)
12. 新增`VocabularyItemPhrase`索引鍵**類型**的`String`而且詞彙定義應用程式：

    [![](implementing-sirikit-images/plist11.png "新增 VocabularyItemPhrase 機碼與字串類型和應用程式定義的詞彙")](implementing-sirikit-images/plist11.png#lightbox)
13. 新增`VocabularyItemPronunciation`索引鍵**類型**的`String`和語音發音的詞彙：

    [![](implementing-sirikit-images/plist12.png "新增具有字串類型和詞彙的語音發音 VocabularyItemPronunciation 機碼")](implementing-sirikit-images/plist12.png#lightbox)
14. 新增`VocabularyItemExamples`索引鍵**類型**的`Array`:

    [![](implementing-sirikit-images/plist13.png "加入 VocabularyItemExamples 鍵，類型的陣列")](implementing-sirikit-images/plist13.png#lightbox)
15. 加入一些`String`範例會使用詞彙的索引鍵：

    [![](implementing-sirikit-images/plist14.png "新增詞彙的範例會使用與少數的 String 索引鍵")](implementing-sirikit-images/plist14.png#lightbox)
16. 重複上述步驟的其他應用程式需要定義的自訂詞彙。
17. 摺疊`ParameterVocabularies`索引鍵。
18. 新增`IntentPhrases`索引鍵**類型**的`Array`:

    [![](implementing-sirikit-images/plist15.png "加入 IntentPhrases 鍵，類型的陣列")](implementing-sirikit-images/plist15.png#lightbox)
19. 加入新的金鑰與**類型**的`Dictionary`:

    [![](implementing-sirikit-images/plist16.png "加入新的金鑰與字典的型別")](implementing-sirikit-images/plist16.png#lightbox)
20. 新增`IntentName`索引鍵**類型**的`String`和意圖範例：

    [![](implementing-sirikit-images/plist17.png "加入 IntentName 鍵 String 型別與意圖，範例")](implementing-sirikit-images/plist17.png#lightbox)
21. 新增`IntentExamples`索引鍵**類型**的`Array`:

    [![](implementing-sirikit-images/plist18.png "加入 IntentExamples 鍵，類型的陣列")](implementing-sirikit-images/plist18.png#lightbox)
22. 加入一些`String`範例會使用詞彙的索引鍵：

    [![](implementing-sirikit-images/plist19.png "新增詞彙的範例會使用與少數的 String 索引鍵")](implementing-sirikit-images/plist19.png#lightbox)
23. 重複上述步驟的任何應用程式必須提供的範例使用方式的對應方式。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 以滑鼠右鍵按一下專案名稱中**方案總管 中**選取**新增** > **新的檔案...**  >  **iOS**:

    [![](implementing-sirikit-images/plist01w.png "加入新的 Info.plist")](implementing-sirikit-images/plist01w.png#lightbox) 
2. 按兩下`AppIntentVocabulary.plist`檔案**方案總管 中**開啟進行編輯。
3. 按一下 **+** 新增機碼，設定**名稱**至`ParameterVocabularies`和**類型**至`Array`:

    [![](implementing-sirikit-images/plist02w.png "將名稱設定為 ParameterVocabularies 和陣列類型")](implementing-sirikit-images/plist02w.png#lightbox)
4. 展開`ParameterVocabularies`按一下 **+** 按鈕，然後設定**類型**至`Dictionary`:

    [![](implementing-sirikit-images/plist03w.png "將類型設為字典")](implementing-sirikit-images/plist03w.png#lightbox)
5. 按一下 **+** 若要加入新的金鑰，請設定**名稱**至`ParameterNames`和**類型**至`Array`:

    [![](implementing-sirikit-images/plist04w.png "將名稱設定為 ParameterNames 和陣列類型")](implementing-sirikit-images/plist04w.png#lightbox)
6. 按一下 **+** 要加入的新金鑰**類型**的`String`和做為其中一個可用的參數名稱的值。 例如， `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05w.png "INStartWorkoutIntent.workoutName 金鑰")](implementing-sirikit-images/plist05w.png#lightbox)
7. 新增`ParameterVocabulary`鍵`ParameterVocabularies`索引鍵**類型**的`Array`:

    [![](implementing-sirikit-images/plist06w.png "新增 ParameterVocabulary 金鑰 ParameterVocabularies 索引鍵的陣列類型")](implementing-sirikit-images/plist06w.png#lightbox)
8. 加入新的金鑰與**類型**的`Dictionary`:

    [![](implementing-sirikit-images/plist07w.png "加入新的金鑰與字典的型別")](implementing-sirikit-images/plist07w.png#lightbox)
9. 新增`VocabularyItemIdentifier`索引鍵**類型**的`String`和詞彙指定唯一的識別碼：

    [![](implementing-sirikit-images/plist08w.png "加入 VocabularyItemIdentifier 鍵字串類型，並指定詞彙的唯一識別碼")](implementing-sirikit-images/plist08w.png#lightbox)
10. 新增`VocabularyItemSynonyms`索引鍵**類型**的`Array`:

    [![](implementing-sirikit-images/plist09w.png "加入 VocabularyItemSynonyms 鍵，類型的陣列")](implementing-sirikit-images/plist09w.png#lightbox)
11. 加入新的金鑰與**類型**的`Dictionary`:

    [![](implementing-sirikit-images/plist10w.png "加入新的金鑰與字典的型別")](implementing-sirikit-images/plist10w.png#lightbox)
12. 新增`VocabularyItemPhrase`索引鍵**類型**的`String`而且詞彙定義應用程式：

    [![](implementing-sirikit-images/plist11w.png "新增 VocabularyItemPhrase 機碼與字串類型和應用程式定義的詞彙")](implementing-sirikit-images/plist11w.png#lightbox)
13. 新增`VocabularyItemPronunciation`索引鍵**類型**的`String`和語音發音的詞彙：

    [![](implementing-sirikit-images/plist12w.png "新增具有字串類型和詞彙的語音發音 VocabularyItemPronunciation 機碼")](implementing-sirikit-images/plist12w.png#lightbox)
14. 新增`VocabularyItemExamples`索引鍵**類型**的`Array`:

    [![](implementing-sirikit-images/plist13w.png "加入 VocabularyItemExamples 鍵，類型的陣列")](implementing-sirikit-images/plist13w.png#lightbox)
15. 加入一些`String`範例會使用詞彙的索引鍵：

    [![](implementing-sirikit-images/plist14w.png "新增詞彙的範例會使用與少數的 String 索引鍵")](implementing-sirikit-images/plist14w.png#lightbox)
16. 重複上述步驟的其他應用程式需要定義的自訂詞彙。
17. 摺疊`ParameterVocabularies`索引鍵。
18. 新增`IntentPhrases`索引鍵**類型**的`Array`:

    [![](implementing-sirikit-images/plist15w.png "加入 IntentPhrases 鍵，類型的陣列")](implementing-sirikit-images/plist15w.png#lightbox)
19. 加入新的金鑰與**類型**的`Dictionary`:

    [![](implementing-sirikit-images/plist16w.png "加入新的金鑰與字典的型別")](implementing-sirikit-images/plist16w.png#lightbox)
20. 新增`IntentName`索引鍵**類型**的`String`和意圖範例：

    [![](implementing-sirikit-images/plist17w.png "加入 IntentName 鍵 String 型別與意圖，範例")](implementing-sirikit-images/plist17w.png#lightbox)
21. 新增`IntentExamples`索引鍵**類型**的`Array`:

    [![](implementing-sirikit-images/plist18w.png "加入 IntentExamples 鍵，類型的陣列")](implementing-sirikit-images/plist18w.png#lightbox)
22. 加入一些`String`範例會使用詞彙的索引鍵：

    [![](implementing-sirikit-images/plist19w.png "新增詞彙的範例會使用與少數的 String 索引鍵")](implementing-sirikit-images/plist19w.png#lightbox)
23. 重複上述步驟的任何應用程式必須提供的範例使用方式的對應方式。

-----

> [!IMPORTANT]
> **注意：** `AppIntentVocabulary.plist`會註冊與測試上使用 Siri 在開發和它的裝置可能需要一些時間 Siri 納入自訂的詞彙。 如此一來，測試人員將需要等待幾分鐘，再嘗試時已更新測試應用程式特定詞彙。

如需詳細資訊，請參閱我們[應用程式特定的詞彙參考](~/ios/platform/sirikit/understanding-sirikit.md)與 Apple[指定自訂詞彙參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1)。

## <a name="adding-an-intents-extension"></a>加入對應方式擴充功能

現在，應用程式已經準備好採用 SiriKit，開發人員必須將一個 （或以上） 的對應方式延伸新增至的方案來處理 Siri 整合所需的對應方式。

針對每個所需的對應方式擴充功能，執行下列作業：

- 加入 Xamarin.iOS 應用程式方案中的對應方式擴充功能專案。
- 設定對應方式延伸`Info.plist`檔案。
- 修改的對應方式擴充的主要類別。

如需詳細資訊，請參閱我們[對應方式擴充功能參考](~/ios/platform/sirikit/understanding-sirikit.md)與 Apple[建立的對應方式擴充功能參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CreatingtheIntentsExtension.html#//apple_ref/doc/uid/TP40016875-CH4-SW1)。

### <a name="creating-the-extension"></a>建立擴充功能

若要將對應方式延伸加入至方案，執行下列作業：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 以滑鼠右鍵按一下**方案名稱**中**方案板**選取**新增** > **加入新的專案...**.
2. 從對話方塊中選取**iOS** > **延伸** > **意圖延伸**按一下**下一步**按鈕： 

    [![](implementing-sirikit-images/intents05.png "選取轉譯延伸模組")](implementing-sirikit-images/intents05.png#lightbox)
3. 接著輸入**名稱**意圖延伸模組，然後按一下**下一步**按鈕： 

    [![](implementing-sirikit-images/intents06.png "輸入的轉譯延伸模組的名稱")](implementing-sirikit-images/intents06.png#lightbox)
4. 最後，按一下 **建立**按鈕，將意圖擴充功能加入至應用程式的方案： 

    [![](implementing-sirikit-images/intents07.png "將意圖擴充功能加入至應用程式方案")](implementing-sirikit-images/intents07.png#lightbox)
5. 在**方案總管 中**，以滑鼠右鍵按一下**參考**新建的意圖擴充功能資料夾。 檢查通用的共用程式碼程式庫專案 （也就上面所建立的應用程式） 的名稱，然後按一下**確定**按鈕： 

    [![](implementing-sirikit-images/intents08.png "選取通用的共用程式碼程式庫專案的名稱")](implementing-sirikit-images/intents08.png#lightbox)
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 以滑鼠右鍵按一下**方案名稱**中**方案總管 中**選取**新增** > **加入新的專案...**.
2. 從對話方塊中選取**iOS** > **延伸** > **意圖延伸**按一下**下一步**按鈕： 

    [![](implementing-sirikit-images/intents05w.png "選取轉譯延伸模組")](implementing-sirikit-images/intents05w.png#lightbox)
3. 接著輸入**名稱**意圖延伸模組，然後按一下**確定** 按鈕。
5. 在**方案總管 中**，以滑鼠右鍵按一下**參考**新建的意圖擴充功能資料夾。 檢查通用的共用程式碼程式庫專案 （也就上面所建立的應用程式） 的名稱，然後按一下**確定**按鈕： 

    [![](implementing-sirikit-images/intents08w.png "選取通用的共用程式碼程式庫專案的名稱")](implementing-sirikit-images/intents08w.png#lightbox)
    
-----

重複這些步驟意圖延伸模組的數目 (根據[架構擴充功能的應用程式](#Architecting-the-App-for-Extensions)上一節)，將需要應用程式。

### <a name="configuring-the-infoplist"></a>設定 Info.plist

針對每個新增到應用程式的方案的對應方式擴充功能，必須在設定`Info.plist`檔案以用於應用程式。

就像任何一般的應用程式擴充功能，應用程式將會有現有的索引鍵`NSExtension`和`NSExtensionAttributes`。 對應方式延伸有兩個新的屬性必須設定：

[![](implementing-sirikit-images/intents01.png "您必須設定兩個新屬性")](implementing-sirikit-images/intents01.png#lightbox)

- **IntentsSupported** -需要，而想要從意圖延伸模組支援的應用程式的意圖類別名稱的陣列所組成。
- **IntentsRestrictedWhileLocked** -是指定的延伸模組的鎖定螢幕行為的應用程式的選擇性索引鍵。 其中包含應用程式想要要求使用者在登入，若要使用來自意圖延伸模組的意圖類別名稱的陣列。

若要設定意圖擴充功能的`Info.plist`檔案中，請在**方案總管 中**開啟進行編輯。 接下來，切換至**來源**檢視，然後展開`NSExtension`和`NSExtensionAttributes`在編輯器中的索引鍵：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](implementing-sirikit-images/intents02.png "在編輯器中 NSExtension 和 NSExtensionAttributes 索引鍵")](implementing-sirikit-images/intents02.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](implementing-sirikit-images/intents02w.png "在編輯器中 NSExtension 和 NSExtensionAttributes 索引鍵")](implementing-sirikit-images/intents02w.png#lightbox)

-----

展開`IntentsSupported`索引鍵，並新增這項擴充功能將會支援任何意圖類別的名稱。 範例 MonkeyChat 應用程式中，它支援`INSendMessageIntent`:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](implementing-sirikit-images/intents09.png "INSendMessageIntent 金鑰")](implementing-sirikit-images/intents09.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](implementing-sirikit-images/intents09w.png "INSendMessageIntent 金鑰")](implementing-sirikit-images/intents09w.png#lightbox)

-----

如果應用程式 （選擇性） 需要使用者登入裝置使用指定的目的，請展開`IntentRestrictedWhileLocked`鍵，然後新增具有限制存取的對應方式的類別名稱。 範例 MonkeyChat 應用程式中，使用者必須登入傳送聊天訊息，因此我們加入`INSendMessageIntent`:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](implementing-sirikit-images/intents10.png "加入的 INSendMessageIntent 機碼")](implementing-sirikit-images/intents10.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](implementing-sirikit-images/intents10w.png "加入的 INSendMessageIntent 機碼")](implementing-sirikit-images/intents10w.png#lightbox)

-----


如需可用的意圖定義域的完整清單，請參閱 Apple[意圖網域參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)。

### <a name="configuring-the-main-class"></a>設定的主要類別

接下來，開發人員必須設定成 Siri 做為主要進入點意圖延伸模組的主要類別。 它必須是子類別`INExtension`這符合`IINIntentHandler`委派。 例如: 

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

應用程式必須實作意圖擴充主要類別上的一個單獨方法`GetHandler`方法。 這個方法會由 SiriKit 傳遞意圖，而且應用程式必須傳回**意圖處理常式**符合特定目的類型。

因為範例 MonkeyChat 應用程式只會處理一個意圖，它會傳回本身中`GetHandler`方法。 如果延伸模組會處理一個以上的意圖，開發人員會新增每個意圖類型的類別，並傳回根據這裡執行個體`Intent`傳遞給方法。

### <a name="handling-the-resolve-stage"></a>處理解析階段

在解析階段會其中意圖延伸模組會釐清，而驗證的參數傳遞 Siri 從並已設定透過使用者的交談。

取得傳送 Siri 從每一個參數，還有`Resolve`方法。 應用程式必須實作這個方法的每個參數，應用程式可能需要從使用者取得正確答案 Siri 的說明。

在範例 MonkeyChat 應用程式的情況下意圖延伸模組會要求來傳送訊息給一或多個收件者。 每個收件者清單中，擴充功能需要連絡搜尋可以具有下列結果：

- 找不到一個符合的連絡人。
- 找到兩個或多個符合的連絡人。
- 找到沒有符合的連絡人。

此外，MonkeyChat 需要訊息的本文內容。 如果使用者未提供此，Siri 需要提示使用者輸入內容。

意圖延伸模組必須能夠順暢地處理每個案例。


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

如需詳細資訊，請參閱我們[解析階段 Reference](~/ios/platform/sirikit/understanding-sirikit.md)與 Apple[正在解析與處理方式參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ResolvingandHandlingIntents.html#//apple_ref/doc/uid/TP40016875-CH5-SW1)。

### <a name="handling-the-confirm-stage"></a>處理在確認階段

在確認階段是其中意圖延伸模組會檢查以查看它有全部符合使用者的要求資訊。 應用程式想要傳送確認沿著將所有支援的詳細資料的功能即將發生 Siri 來讓它可確認與使用者，這是預期的動作。

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

如需詳細資訊，請參閱我們[確認階段 Reference](~/ios/platform/sirikit/understanding-sirikit.md)。

### <a name="processing-the-intent"></a>處理意圖

這是意圖延伸實際執行工作，以符合使用者的要求，並將結果傳遞回 Siri，讓使用者可以收到通知的點。


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

如需詳細資訊，請參閱我們[處理階段 Reference](~/ios/platform/sirikit/understanding-sirikit.md)。

## <a name="adding-an-intents-ui-extension"></a>加入對應方式 UI 擴充功能

選擇性的對應方式 UI 延伸模組呈現的機會，讓應用程式的 UI 和商標 Siri 體驗，並讓使用者覺得已連線到應用程式。 這個延伸的品牌與文字記錄 visual 和其他資訊，可以讓應用程式。

[![](implementing-sirikit-images/intentsui01.png "對應 UI 延伸模組的輸出範例")](implementing-sirikit-images/intentsui01.png#lightbox)

如同對應方式擴充功能，開發人員將會執行下列步驟的對應方式 UI 延伸模組：

- 加入 Xamarin.iOS 應用程式方案中的對應方式 UI 延伸模組專案。
- 設定對應方式 UI 延伸模組`Info.plist`檔案。
- 修改的對應方式 UI 延伸模組的主要類別。

如需詳細資訊，請參閱我們[目的 UI 擴充功能參考](~/ios/platform/sirikit/understanding-sirikit.md)與 Apple[提供的自訂介面參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ProvidingaCustomInterface.html#//apple_ref/doc/uid/TP40016875-CH7-SW1)。

### <a name="creating-the-extension"></a>建立擴充功能

要加入方案的對應方式 UI 延伸模組，請執行下列作業：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 以滑鼠右鍵按一下**方案名稱**中**方案板**選取**新增** > **加入新的專案...**.
2. 從對話方塊選取**iOS** > **延伸** > **意圖 UI 延伸模組**按一下**下一步**按鈕： 

    [![](implementing-sirikit-images/intents11.png "選取的意圖 UI 延伸模組")](implementing-sirikit-images/intents11.png#lightbox)
3. 接著輸入**名稱**意圖延伸模組，然後按一下**下一步**按鈕： 

    [![](implementing-sirikit-images/intents12.png "輸入的轉譯延伸模組的名稱")](implementing-sirikit-images/intents12.png#lightbox)
4. 最後，按一下 **建立**按鈕，將意圖擴充功能加入至應用程式的方案： 

    [![](implementing-sirikit-images/intents13.png "將意圖擴充功能加入至應用程式方案")](implementing-sirikit-images/intents13.png#lightbox)
5. 在**方案總管 中**，以滑鼠右鍵按一下**參考**新建的意圖擴充功能資料夾。 檢查通用的共用程式碼程式庫專案 （也就上面所建立的應用程式） 的名稱，然後按一下**確定**按鈕： 

    [![](implementing-sirikit-images/intents14.png "選取通用的共用程式碼程式庫專案的名稱")](implementing-sirikit-images/intents14.png#lightbox)
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 以滑鼠右鍵按一下**方案名稱**中**方案總管 中**選取**新增** > **加入新的專案...**
2. 從對話方塊選取**iOS** > **延伸** > **意圖 UI 延伸模組**按一下**下一步**按鈕。
3. 接著輸入**名稱**意圖延伸模組，然後按一下**確定** 按鈕。
5. 在**方案總管 中**，以滑鼠右鍵按一下**參考**新建的意圖擴充功能資料夾。 檢查通用的共用程式碼程式庫專案 （也就上面所建立的應用程式） 的名稱，然後按一下**確定** 按鈕。
    
-----

### <a name="configuring-the-infoplist"></a>設定 Info.plist

設定對應方式 UI 延伸模組`Info.plist`檔案與應用程式運作。

就像任何一般的應用程式擴充功能，應用程式將會有現有的索引鍵`NSExtension`和`NSExtensionAttributes`。 對應方式擴充功能還有一個新的屬性必須設定：

[![](implementing-sirikit-images/intents03.png "您必須設定一個新屬性")](implementing-sirikit-images/intents03.png#lightbox)

**IntentsSupported**需要，而想要從意圖延伸模組支援的應用程式的意圖類別名稱的陣列所組成。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要設定意圖 UI 延伸模組`Info.plist`檔案中，請在**方案總管 中**開啟進行編輯。 接下來，切換至**來源**檢視，然後展開`NSExtension`和`NSExtensionAttributes`在編輯器中的索引鍵：

[![](implementing-sirikit-images/intents04.png "在編輯器中 NSExtension 和 NSExtensionAttributes 索引鍵")](implementing-sirikit-images/intents04.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要設定意圖 UI 延伸模組`Info.plist`檔案中，請在**方案總管 中**開啟進行編輯。 展開`NSExtension`和`NSExtensionAttributes`在編輯器中的索引鍵：

[![](implementing-sirikit-images/intents04w.png "在編輯器中的 t NSExtension 和 NSExtensionAttributes 金鑰")](implementing-sirikit-images/intents04w.png#lightbox)

-----

展開`IntentsSupported`索引鍵，並新增這項擴充功能將會支援任何意圖類別的名稱。 範例 MonkeyChat 應用程式中，它支援`INSendMessageIntent`:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](implementing-sirikit-images/intents15.png "INSendMessageIntent 金鑰")](implementing-sirikit-images/intents15.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](implementing-sirikit-images/intents15w.png "INSendMessageIntent 金鑰")](implementing-sirikit-images/intents15w.png#lightbox)

-----

如需可用的意圖定義域的完整清單，請參閱 Apple[意圖網域參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)。

### <a name="configuring-the-main-class"></a>設定的主要類別

設定成 Siri 做為主要進入點的意圖 UI 延伸模組的主要類別。 它必須是子類別`UIViewController`這符合`IINUIHostedViewController`介面。 例如: 

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

將會通過 Siri`INInteraction`類別執行個體的`Configure`方法`UIViewController`意圖 UI 延伸模組內的執行個體。

`INInteraction`物件提供三個關鍵的延伸模組的資訊：

1. 正在處理之意圖 object。
2. 從 Intent 回應物件`Confirm`和`Handle`意圖擴充功能的方法。
3. 定義應用程式與 Siri 之間互動的狀態互動狀態。

`UIViewController`執行個體是使用 Siri 互動的主要類別，而且因為它繼承自`UIViewController`，所以可存取的所有 UIKit 功能。

當呼叫 Siri`Configure`方法`UIViewController`傳入指出檢視控制器將會是裝載於 Siri Snippit 或對應卡檢視內容。

Siri 也將會傳入應用程式需要傳回檢視的所需的大小，當應用程式完成後，設定它的完成處理常式。

### <a name="design-the-ui-in-ios-designer"></a>IOS 設計工具中的 UI 設計

版面配置方式 UI 延伸模組的使用者介面 iOS 設計工具中。 按兩下副檔名`MainInterface.storyboard`檔案**方案總管 中**開啟進行編輯。 拖曳在所有必要的 UI 項目，以建置使用者介面，並儲存變更。

> [!IMPORTANT]
> **注意：**雖然您可以加入互動式項目，例如`UIButtons`或`UITextFields`意圖 UI 延伸模組來`UIViewController`這些嚴格禁止做為在非互動意圖 UI，使用者無法互動使用它們。

### <a name="wire-up-the-user-interface"></a>網路上的使用者介面

IOS 設計工具中建立的對應方式 UI 延伸模組的使用者介面，以編輯`UIViewController`子類別，然後覆寫`Configure`方法，如下所示：

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

### <a name="overriding-the-default-siri-ui"></a>覆寫預設 Siri UI

對應 UI 擴充功能一律顯示以及其他 Siri 內容，例如 UI 頂端的名稱與應用程式圖示或，根據的用途，可能會顯示在下方的按鈕 （例如 [傳送] 或 [取消] 5d;）。

有幾個執行個體，其中應用程式可以取代 Siri 向使用者顯示根據預設，例如訊息的資訊或對應，其中應用程式時，可以使用其中一個量身訂做的應用程式取代預設體驗。

如果目的 UI 延伸模組需要覆寫的預設 Siri UI 項目`UIViewController`子類別必須實作`IINUIHostedViewSiriProviding`介面，並選擇要顯示的特定介面項目。

將下列程式碼加入`UIViewController`告訴 Siri 意圖 UI 延伸模組已顯示的訊息內容的子類別：

```csharp
public bool DisplaysMessage {
    get {return true;}
}
```

### <a name="considerations"></a>考量

Apple 建議開發人員，考慮下列項目納入設計和實作意圖 UI 延伸模組時的考量：

- **要做出明智的記憶體使用量**-因為意圖 UI 延伸模組是暫時性的而且只顯示一小段時間，系統會加諸更嚴格的記憶體限制使用完整的應用程式所使用。
- **請考慮最小值和最大的檢視大小**-確認意圖 UI 延伸模組看起來好上每個 iOS 裝置類型、 大小和方向。 此外，可能無法授與應用程式會傳送回 Siri 所需的大小。
- **使用彈性和自動調整的版面配置模式**-一次，以確保 UI 看起來很棒的每個裝置上。

## <a name="summary"></a>總結

這篇文章已涵蓋 SiriKit，並顯示如何加入它的 Xamarin.iOS 應用程式時提供的 iOS 裝置上使用 Siri 和對應的應用程式的使用者可以存取的服務。




## <a name="related-links"></a>相關連結

- [ElizaChat 範例](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [SiriKit 程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [對應方式 Framework 參考](https://developer.apple.com/reference/intents)
- [對應 UI Framework 參考](https://developer.apple.com/reference/intentsui)
- [意圖網域參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)
