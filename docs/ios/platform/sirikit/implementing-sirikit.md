---
title: 在 Xamarin.iOS 中實作 SiriKit
description: 本文件說明在 Xamarin.iOS 應用程式中實作 SiriKit 支援所需的步驟。 它討論 Intents 延伸模組和 Intents UI 延伸模組。
ms.prod: xamarin
ms.assetid: 20FFB981-EB10-48BA-BF79-40F37F0291EB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: 2c3bddc89348b46c9bba277580071cb8ac3d6943
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61434421"
---
# <a name="implementing-sirikit-in-xamarinios"></a>在 Xamarin.iOS 中實作 SiriKit

_本文涵蓋在 Xamarin.iOS 應用程式中實作 SiriKit 支援所需的步驟。_

新增至 iOS 10，SiriKit 可讓 Xamarin.iOS 應用程式提供的 iOS 裝置上使用 Siri 和對應的應用程式的使用者可以存取的服務。 本文涵蓋在 Xamarin.iOS 應用程式中實作 SiriKit 支援將必要的 Intents 延伸模組、 Intents UI 延伸模組和詞彙所需的步驟。

使用 Siri 的運作方式的概念**網域**，群組知道相關工作的動作。 每個應用程式有使用 Siri 的互動必須分為其已知的服務網域，如下所示：

- 音訊或視訊通話。
- 預約好好體驗吧。
- 管理健身。
- 訊息傳遞。
- 正在搜尋相片。
- 傳送或接收付款。

當使用者提出的要求使用 Siri 牽涉到的其中一個應用程式擴充功能的服務時，SiriKit 傳送延伸模組**意圖**物件，描述使用者的要求，以及任何支援的資料。 應用程式擴充功能則會產生適當**回應**物件的給定**意圖**，詳細說明如何擴充功能來處理要求。

本指南將提供簡單的範例包括 SiriKit 到現有的應用程式的支援。 基於此範例中，我們將使用假的 MonkeyChat 應用程式：

[![](implementing-sirikit-images/monkeychat01.png "MonkeyChat 圖示")](implementing-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat 會保留它自己連絡活頁簿的使用者的好友，每個畫面名稱 （例如 Bobo 例如) 相關聯，並可讓使用者傳送給每一位朋友的文字聊天室的畫面名稱。

## <a name="extending-the-app-with-sirikit"></a>擴充應用程式與 SiriKit

中所示[了解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md)指南中，有參與擴充 SiriKit 應用程式的三個主要部分：

[![](implementing-sirikit-images/elements01.png "擴充應用程式與 SiriKit 圖表")](implementing-sirikit-images/elements01.png#lightbox)

它們包括：

1. **Intents 延伸模組**-驗證使用者的回應、 確認應用程式可以處理的要求，並實際執行工作，以滿足使用者的要求。
2. **Intents UI 延伸模組** - *選擇性*，提供自訂的 UI，可以在 Siri 環境中的回應可以使應用程式 UI 和商標 Siri 豐富使用者體驗到。
3. **應用程式**-應用程式提供使用者特定的詞彙，以協助 Siri 使用它。 

所有這些項目並將它們包含在應用程式的步驟將會在下列各節中詳細說明。


## <a name="preparing-the-app"></a>準備應用程式

SiriKit 經擴充功能，不過，再將任何延伸模組新增至 應用程式，開發人員需要協助的 SiriKit 採用的幾件事。

### <a name="moving-common-shared-code"></a>移動通用的共用程式碼 

首先，開發人員可以將共用的通用程式碼的一些之間移動應用程式和延伸模組至_共用專案_，_可攜式類別庫 (Pcl)_ 或_原生程式庫_。

延伸模組必須要能夠執行所有應用程式會執行的項目。 在詞彙中，範例 MonkeyChat 應用程式，像是尋找連絡人，新增新的連絡人、 傳送訊息，並擷取訊息歷程記錄。

將此通用程式碼移到共用的專案、 PCL 或原生程式庫它可讓您更容易維護一個常見的位置中的程式碼，並確保擴充功能和父應用程式，使用者也提供統一的體驗與功能。

在範例應用程式 MonkeyChat，資料模型和處理程式碼，例如網路與資料庫存取權會移入原生程式庫。

請執行下列動作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 啟動 Visual Studio for Mac，並開啟 MonkeyChat 應用程式。
2. 中的方案名稱上按一下滑鼠右鍵**Solution Pad** ，然後選取**新增** > **新專案...**: 

    [![](implementing-sirikit-images/prep01.png "加入新的專案")](implementing-sirikit-images/prep01.png#lightbox)
3. 選取 [ **iOS** > **程式庫** > **類別庫**然後按一下**下一步]** 按鈕： 

    [![](implementing-sirikit-images/prep02.png "選取 類別庫")](implementing-sirikit-images/prep02.png#lightbox)
4. 請輸入`MonkeyChatCommon`for**名稱**然後按一下**建立**按鈕： 

    [![](implementing-sirikit-images/prep03.png "輸入名稱 MonkeyChatCommon")](implementing-sirikit-images/prep03.png#lightbox)
5. 以滑鼠右鍵按一下**參考**資料夾中的主應用程式**方案總管**，然後選取**編輯參考...**.請檢查**MonkeyChatCommon**專案，然後按一下**確定**按鈕： 

    [![](implementing-sirikit-images/prep05.png "MonkeyChatCommon 專案簽入")](implementing-sirikit-images/prep05.png#lightbox)
6. 在 [**方案總管] 中**，將主要應用程式的通用的共用程式碼拖曳至原生程式庫。
7. 如果 MonkeyChat，拖曳**DataModels**並**處理器**從主應用程式，原生程式庫的資料夾： 

    [![](implementing-sirikit-images/prep06.png "在 [方案總管] 中的 [DataModels 及處理器] 資料夾")](implementing-sirikit-images/prep06.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 啟動 Visual Studio，並開啟 MonkeyChat 應用程式。
2. 中的方案名稱上按一下滑鼠右鍵**方案總管**，然後選取**新增** > **新專案...**.
3. 選取  **Visual C#**   > **共用專案**，按一下 **下一步**按鈕： 

    [![](implementing-sirikit-images/prep02.w157-sml.png "選取 類別庫")](implementing-sirikit-images/prep02.w157.png#lightbox)
4. 請輸入`MonkeyChatCommon`for**名稱**然後按一下**建立** 按鈕。
5. 以滑鼠右鍵按一下**參考**資料夾中的主應用程式**方案總管**，然後選取**編輯參考...**.請檢查**MonkeyChatCommon**專案，然後按一下**確定**按鈕： 

    [![](implementing-sirikit-images/prep05w.png "MonkeyChatCommon 專案簽入")](implementing-sirikit-images/prep05w.png#lightbox)
6. 在 **方案總管 中**，將主要應用程式的通用的共用程式碼拖曳至 共用的專案。
7. 如果 MonkeyChat，拖曳**DataModels**並**處理器**從主應用程式，原生程式庫的資料夾。

-----

編輯任何已移至原生程式庫的檔案，並變更以符合程式庫的命名空間。 例如，變更`MonkeyChat`至`MonkeyChatCommon`:

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

接下來，請回到主要應用程式，並新增`using`原生程式庫的命名空間陳述式任何位置應用程式會使用其中一個已移動的類別：

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

### <a name="architecting-the-app-for-extensions"></a>架構延伸模組的應用程式

一般而言，應用程式將會註冊多個對應方式，開發人員必須確保應用程式的架構是針對適當數目的意圖延伸模組。

在應用程式需要一個以上的意圖的情況下，開發人員可以選擇將所有其意圖處理放入一個意圖延伸模組，或為每個項目的建立個別的 「 意圖 」 擴充功能。

如果選擇此選項，為每個項目的建立個別的 「 意圖 」 擴充功能，開發人員可能需要複製大量的未定案程式碼中每個延伸模組，並建立大量的處理器和記憶體額外負荷。

為了協助在兩個選項之間做選擇，請參閱是否任何這些意圖自然彼此互屬。 例如，進行音訊和視訊呼叫的應用程式可能想要這兩個這些意圖包含單一的 「 意圖 」 延伸模組中，因為它們會處理類似的工作，而無法提供大部分的程式碼重複使用。

任何的意圖或群組的色彩對應方式可容納不下現有的群組，來包含它們的應用程式的方案中建立新的 「 意圖 」 延伸模組。


### <a name="setting-the-required-entitlements"></a>設定必要的權利

包括 SiriKit 整合任何 Xamarin.iOS 應用程式，必須有正確的權利設定。 如果開發人員未正確設定這些必要的權利，他們將無法安裝或測試真實 iOS 10 （或更新版本） 的硬體，這也是因為 iOS 10 的需求上的應用程式模擬器不支援 SiriKit。

請執行下列動作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 按兩下`Entitlements.plist`檔案中**方案總管 中**以開啟它進行編輯。
2. 若要切換**來源**索引標籤。
3. 新增`com.apple.developer.siri`**屬性**，將**類型**來`Boolean`而**值**到`Yes`: 

    [![](implementing-sirikit-images/setup01.png "新增 com.apple.developer.siri 屬性")](implementing-sirikit-images/setup01.png#lightbox)
4. 將變更儲存到檔案。
5. 按兩下**專案檔**中**方案總管中**以開啟它進行編輯。
6. 選取 **iOS 套件組合簽署**，並確定`Entitlements.plist`中選取檔案**自訂權利**欄位： 

    [![](implementing-sirikit-images/setup02.png "選取 [自訂權利] 欄位中的 Entitlements.plist 檔案")](implementing-sirikit-images/setup02.png#lightbox)
7. 按一下 [確定] 按鈕以儲存所做的變更。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 按兩下`Entitlements.plist`檔案中**方案總管 中**以開啟它進行編輯。
3. 新增`com.apple.developer.siri`**屬性**，將**類型**來`Boolean`而**值**到`Yes`: 

    [![](implementing-sirikit-images/setup01w.png "新增 com.apple.developer.siri 屬性")](implementing-sirikit-images/setup01w.png#lightbox)
4. 將變更儲存到檔案。
5. 按兩下**專案檔**中**方案總管中**以開啟它進行編輯。
6. 選取  **iOS 套件組合簽署**，並確定`Entitlements.plist`中選取檔案**自訂權利**欄位。

-----

完成時，應用程式的`Entitlements.plist`檔案應該如下所示 （在中開啟外部編輯器中）：

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

由於 Apple 已周圍 SiriKit 架構中，任何實作 SiriKit 的 Xamarin.iOS 應用程式的嚴格安全性_必須_有正確的應用程式識別碼與權利 （請參閱上一節），而且必須以適當簽署佈建設定檔。

依下列方式在 Mac 上：

1. 在網頁瀏覽器，瀏覽至[ https://developer.apple.com ](https://developer.apple.com)和登入您的帳戶。
2. 按一下 **憑證**，**識別項**並**設定檔**。
3. 選取 **佈建設定檔**，然後選取 **應用程式識別碼**，然後按一下 **+** 按鈕。
4. 請輸入**名稱**新設定檔。
5. 請輸入**套件組合識別碼**遵循 Apple 的命名建議。
6. 向下捲動至**應用程式服務**區段中，選取**SiriKit**然後按一下**繼續**按鈕： 

    [![](implementing-sirikit-images/setup03.png "選取 SiriKit")](implementing-sirikit-images/setup03.png#lightbox)
7. 確認所有設定，然後**送出**應用程式識別碼。
8. 選取 **佈建設定檔** > **開發**，按一下 **+** 按鈕，選取 **Apple ID**，然後按一下 **繼續**。
9. 按一下 選取**所有**，然後按一下**繼續**。
10. 按一下 **全選**再次強調，然後按一下**繼續**。
11. 請輸入**設定檔名稱**使用 Apple 的命名建議，然後按一下**繼續**。
12. 啟動 Xcode。
13. 從 Xcode 功能表選取 **喜好設定...**
14. 選取 **帳號**，然後按一下 **檢視詳細資料...** 按鈕： 

    [![](implementing-sirikit-images/setup04.png "選取帳戶")](implementing-sirikit-images/setup04.png#lightbox)
15. 按一下 **下載所有設定檔**中較低的左下角的按鈕： 

    [![](implementing-sirikit-images/setup05.png "下載所有設定檔")](implementing-sirikit-images/setup05.png#lightbox)
16. 請確認**佈建設定檔**建立更新版本已安裝在 Xcode 中。
17. 開啟專案，以便將 SiriKit 支援，以在 Visual Studio 中，for mac。
18. 按兩下`Info.plist`檔案中**方案總管 中**。
18. 請確認**套件組合識別碼**符合上述的 Apple 開發人員入口網站中建立： 

    [![](implementing-sirikit-images/setup06.png "套件組合識別碼")](implementing-sirikit-images/setup06.png#lightbox)
18. 在**方案總管**，選取**專案**。
19. 以滑鼠右鍵按一下專案，然後選取**選項**。
21. 選取 **iOS 套件組合簽署**，選取**簽署身分識別**並**佈建設定檔**上面所建立： 

    [![](implementing-sirikit-images/setup07.png "選取 [簽署識別] 和 佈建設定檔")](implementing-sirikit-images/setup07.png#lightbox)
22. 按一下 [確定] 按鈕以儲存所做的變更。

> [!IMPORTANT]
> 測試 SiriKit 僅適用於真實的 iOS 10 的硬體裝置而不是在 iOS 10 模擬器。 如果有安裝 SiriKit 的問題啟用實際的硬體上的 Xamarin.iOS 應用程式，請確定，必要的權利，應用程式識別碼、 簽署的識別碼和佈建設定檔已正確設定 Apple 開發人員入口網站和 Visual Studio for mac。

### <a name="requesting-siri-authorization"></a>要求使用 Siri 授權

應用程式會新增任何使用者特定詞彙或 Intents 延伸模組會連線到 Siri 之前，它就必須從使用者存取 Siri 要求的授權。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

編輯應用程式的`Info.plist`檔案中，切換到**來源**檢視以及新增`NSSiriUsageDescription`索引鍵字串值，描述應用程式如何使用 Siri 以及將傳送的資料類型。 比方說，MonkeyChat 應用程式可能會說 「 MonkeyChat 連絡人會傳送至 Siri":

[![](implementing-sirikit-images/request01.png "Info.plist 編輯器中 NSSiriUsageDescription")](implementing-sirikit-images/request01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

編輯應用程式`Info.plist`檔案，並新增`NSSiriUsageDescription`索引鍵字串值，描述應用程式如何使用 Siri 以及將傳送的資料類型。 比方說，MonkeyChat 應用程式可能會說 「 MonkeyChat 連絡人會傳送至 Siri":

[![](implementing-sirikit-images/request01w.png "Info.plist 編輯器中 NSSiriUsageDescription")](implementing-sirikit-images/request01w.png#lightbox)

-----

呼叫`RequestSiriAuthorization`方法的`INPreferences`類別，在第一次啟動應用程式。 編輯`AppDelegate.cs`類別，並讓`FinishedLaunching`方法外觀如下所示：


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

第一次呼叫此方法時，警示則會顯示提示使用者允許應用程式存取 Siri。 開發人員新增至訊息`NSSiriUsageDescription`上方會顯示在此警示。 如果使用者一開始會拒絕存取，他們可以使用**設定**授與存取權的應用程式的應用程式。

在任何時間，應用程式可以檢查應用程式能夠藉由呼叫存取 Siri`SiriAuthorizationStatus`方法的`INPreferences`類別。

### <a name="localization-and-siri"></a>當地語系化和 Siri

在 iOS 裝置，使用者可選取不同的 Siri 則系統預設值的語言。 當使用當地語系化的資料，應用程式必須使用`SiriLanguageCode`方法的`INPreferences`Siri 從取得語言的程式碼的類別。 例如：

```csharp
var language = INPreferences.SiriLanguageCode();

// Take action based on language
if (language == "en-US") {
    // Do something...
}
```

### <a name="adding-user-specific-vocabulary"></a>新增使用者特定詞彙

您可以將使用者特定詞彙提供單字或片語，且是唯一的應用程式的個別使用者。 這些會提供在執行階段從主要的應用程式 （不應用程式擴充功能） 做為詞彙，排序的最大顯著性使用量優先順序的使用者，最重要的條款，在清單開頭的已排序集合。

使用者特定詞彙必須屬於下列類別之一：

- 連絡人的名稱 （也就不受連絡人 framework）。
- 相片標記。
- 相片專輯名稱。
- 健身名稱。

選取要註冊為自訂詞彙的詞彙，只選擇條款，可能不熟悉應用程式的其他人誤解。 永遠不會註冊常見術語 「 我健身"或"My Album"等。 比方說，MonkeyChat 應用程式將會註冊相關聯使用者的通訊錄中的每位連絡人的暱稱。

應用程式提供使用者特定詞彙，藉由呼叫`SetVocabularyStrings`方法`INVocabulary`類別，並傳入`NSOrderedSet`主要應用程式的集合。 應用程式應該一律呼叫`RemoveAllVocabularyStrings`方法第一，若要移除任何現有的條款，然後才能加入新的。 例如: 

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

使用此程式碼就緒之後，它可能會呼叫，如下所示：

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
> Siri 視為提示中的自訂詞彙，並將加入盡可能術語的。 不過，空間是有限，因此務必註冊的自訂詞彙_只_可能會造成混淆，因此保持為最小值的 已註冊的詞彙總數的術語。

如需詳細資訊，請參閱我們[使用者特定的詞彙參考](~/ios/platform/sirikit/understanding-sirikit.md)與 Apple[指定自訂詞彙參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1)。

### <a name="adding-app-specific-vocabulary"></a>新增應用程式特定詞彙

應用程式特定詞彙定義的特定單字和片語，就是所有的應用程式的使用者，例如車輛類型或健身名稱。 由於這些是應用程式的一部分，以定義它們`AppIntentVocabulary.plist`檔案做為主要的應用程式套件組合的一部分。 此外，應該當地語系化這些單字和片語。

應用程式特定詞彙必須屬於下列類別之一：

- 寫選項。
- 健身名稱。

應用程式特定詞彙檔案包含兩個根層級的索引鍵：

- `ParameterVocabularies` **所需**-定義應用程式的自訂詞彙和它們套用至的目的參數。
- `IntentPhrases` **選擇性**-包含使用自訂的條款中所定義的範例片語`ParameterVocabularies`。

在每個項目`ParameterVocabularies`必須指定的識別碼字串、 詞彙和適用於一詞的目的。 此外，單一的詞彙可能套用至多個對應方式。

如需可接受的值與所需的檔案結構的完整清單，請參閱 Apple[應用程式的詞彙檔案格式參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1)。

若要新增`AppIntentVocabulary.plist`應用程式專案檔案中，執行下列動作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 中的專案名稱上按一下滑鼠右鍵**方案總管**，然後選取**新增** > **新檔...**  >  **iOS**:

    [![](implementing-sirikit-images/plist01.png "新增的屬性清單")](implementing-sirikit-images/plist01.png#lightbox)
2. 按兩下`AppIntentVocabulary.plist`檔案中**方案總管 中**以開啟它進行編輯。
3. 按一下  **+** 新增機碼，設定**名稱**來`ParameterVocabularies`而**類型**來`Array`:

    [![](implementing-sirikit-images/plist02.png "將名稱設為 ParameterVocabularies 和陣列的型別")](implementing-sirikit-images/plist02.png#lightbox)
4. 依序展開`ParameterVocabularies`，按一下  **+** 按鈕，然後將**型別**至`Dictionary`:

    [![](implementing-sirikit-images/plist03.png "將類型設定為字典")](implementing-sirikit-images/plist03.png#lightbox)
5. 按一下 **+** 若要加入新的金鑰，請設定 **名稱** 來 `ParameterNames` 而 **類型** 到 `Array`:

    [![](implementing-sirikit-images/plist04.png "將名稱設為 ParameterNames 和陣列的型別")](implementing-sirikit-images/plist04.png#lightbox)
6. 按一下  **+** 以新增新的金鑰，與**類型**的`String`和做為其中一個可用的參數名稱的值。 比方說， `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05.png "The INStartWorkoutIntent.workoutName key")](implementing-sirikit-images/plist05.png#lightbox)
7. 新增`ParameterVocabulary`機碼`ParameterVocabularies`索引鍵**型別**的`Array`:

    [![](implementing-sirikit-images/plist06.png "將 ParameterVocabulary 金鑰新增至 ParameterVocabularies 具有的索引鍵類型的陣列")](implementing-sirikit-images/plist06.png#lightbox)
8. 新增具有新的金鑰**型別**的`Dictionary`:

    [![](implementing-sirikit-images/plist07.png "加入新的金鑰與字典的型別")](implementing-sirikit-images/plist07.png#lightbox)
9. 新增`VocabularyItemIdentifier`索引鍵**型別**的`String`並指定唯一識別碼的字詞：

    [![](implementing-sirikit-images/plist08.png "新增 VocabularyItemIdentifier 索引鍵的字串類型，並指定唯一識別碼")](implementing-sirikit-images/plist08.png#lightbox)
10. 新增`VocabularyItemSynonyms`索引鍵**型別**的`Array`:

    [![](implementing-sirikit-images/plist09.png "加入陣列的型別 VocabularyItemSynonyms 鍵")](implementing-sirikit-images/plist09.png#lightbox)
11. 新增具有新的金鑰**型別**的`Dictionary`:

    [![](implementing-sirikit-images/plist10.png "加入新的金鑰與字典的型別")](implementing-sirikit-images/plist10.png#lightbox)
12. 新增`VocabularyItemPhrase`索引鍵**型別**的`String`和應用程式所定義的詞彙：

    [![](implementing-sirikit-images/plist11.png "加入 VocabularyItemPhrase 鍵 String 型別和應用程式所定義的詞彙")](implementing-sirikit-images/plist11.png#lightbox)
13. 新增`VocabularyItemPronunciation`索引鍵**型別**的`String`和語音發音的字詞：

    [![](implementing-sirikit-images/plist12.png "加入 VocabularyItemPronunciation 鍵 String 型別和詞彙的語音發音")](implementing-sirikit-images/plist12.png#lightbox)
14. 新增`VocabularyItemExamples`索引鍵**型別**的`Array`:

    [![](implementing-sirikit-images/plist13.png "加入陣列的型別 VocabularyItemExamples 鍵")](implementing-sirikit-images/plist13.png#lightbox)
15. 新增一些`String`一詞的範例使用的索引鍵：

    [![](implementing-sirikit-images/plist14.png "新增與詞彙的範例會使用幾個字串索引鍵")](implementing-sirikit-images/plist14.png#lightbox)
16. 重複上述步驟的其他應用程式需要定義的自訂詞彙。
17. 摺疊`ParameterVocabularies`索引鍵。
18. 新增`IntentPhrases`索引鍵**型別**的`Array`:

    [![](implementing-sirikit-images/plist15.png "加入陣列的型別 IntentPhrases 鍵")](implementing-sirikit-images/plist15.png#lightbox)
19. 新增具有新的金鑰**型別**的`Dictionary`:

    [![](implementing-sirikit-images/plist16.png "加入新的金鑰與字典的型別")](implementing-sirikit-images/plist16.png#lightbox)
20. 新增`IntentName`索引鍵**型別**的`String`和意圖範例：

    [![](implementing-sirikit-images/plist17.png "加入 IntentName 鍵 String 型別和意圖範例")](implementing-sirikit-images/plist17.png#lightbox)
21. 新增`IntentExamples`索引鍵**型別**的`Array`:

    [![](implementing-sirikit-images/plist18.png "加入陣列的型別 IntentExamples 鍵")](implementing-sirikit-images/plist18.png#lightbox)
22. 新增一些`String`一詞的範例使用的索引鍵：

    [![](implementing-sirikit-images/plist19.png "新增與詞彙的範例會使用幾個字串索引鍵")](implementing-sirikit-images/plist19.png#lightbox)
23. 重複上述步驟的任何應用程式必須提供的使用方式範例的意圖。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 中的專案名稱上按一下滑鼠右鍵**方案總管**，然後選取**新增 > 新增項目 > Apple > 屬性清單 > Info.plist**:

    [![](implementing-sirikit-images/plist01.w157-sml.png "加入新的 Info.plist")](implementing-sirikit-images/plist01.w157.png#lightbox)

2. 按兩下`AppIntentVocabulary.plist`檔案中**方案總管 中**以開啟它進行編輯。
3. 按一下  **+** 新增機碼，設定**名稱**來`ParameterVocabularies`而**類型**來`Array`:

    [![](implementing-sirikit-images/plist02w.png "將名稱設為 ParameterVocabularies 和陣列的型別")](implementing-sirikit-images/plist02w.png#lightbox)
4. 依序展開`ParameterVocabularies`，按一下  **+** 按鈕，然後將**型別**至`Dictionary`:

    [![](implementing-sirikit-images/plist03w.png "將類型設定為字典")](implementing-sirikit-images/plist03w.png#lightbox)
5. 按一下 **+** 若要加入新的金鑰，請設定 **名稱** 來 `ParameterNames` 而 **類型** 到 `Array`:

    [![](implementing-sirikit-images/plist04w.png "將名稱設為 ParameterNames 和陣列的型別")](implementing-sirikit-images/plist04w.png#lightbox)
6. 按一下  **+** 以新增新的金鑰，與**類型**的`String`和做為其中一個可用的參數名稱的值。 比方說， `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05w.png "The INStartWorkoutIntent.workoutName key")](implementing-sirikit-images/plist05w.png#lightbox)
7. 新增`ParameterVocabulary`機碼`ParameterVocabularies`索引鍵**型別**的`Array`:

    [![](implementing-sirikit-images/plist06w.png "將 ParameterVocabulary 金鑰新增至 ParameterVocabularies 具有的索引鍵類型的陣列")](implementing-sirikit-images/plist06w.png#lightbox)
8. 新增具有新的金鑰**型別**的`Dictionary`:

    [![](implementing-sirikit-images/plist07w.png "加入新的金鑰與字典的型別")](implementing-sirikit-images/plist07w.png#lightbox)
9. 新增`VocabularyItemIdentifier`索引鍵**型別**的`String`並指定唯一識別碼的字詞：

    [![](implementing-sirikit-images/plist08w.png "新增 VocabularyItemIdentifier 索引鍵的字串類型，並指定詞彙的唯一識別碼")](implementing-sirikit-images/plist08w.png#lightbox)
10. 新增`VocabularyItemSynonyms`索引鍵**型別**的`Array`:

    [![](implementing-sirikit-images/plist09w.png "加入陣列的型別 VocabularyItemSynonyms 鍵")](implementing-sirikit-images/plist09w.png#lightbox)
11. 新增具有新的金鑰**型別**的`Dictionary`:

    [![](implementing-sirikit-images/plist10w.png "加入新的金鑰與字典的型別")](implementing-sirikit-images/plist10w.png#lightbox)
12. 新增`VocabularyItemPhrase`索引鍵**型別**的`String`和應用程式所定義的詞彙：

    [![](implementing-sirikit-images/plist11w.png "加入 VocabularyItemPhrase 鍵 String 型別和應用程式所定義的詞彙")](implementing-sirikit-images/plist11w.png#lightbox)
13. 新增`VocabularyItemPronunciation`索引鍵**型別**的`String`和語音發音的字詞：

    [![](implementing-sirikit-images/plist12w.png "加入 VocabularyItemPronunciation 鍵 String 型別和詞彙的語音發音")](implementing-sirikit-images/plist12w.png#lightbox)
14. 新增`VocabularyItemExamples`索引鍵**型別**的`Array`:

    [![](implementing-sirikit-images/plist13w.png "加入陣列的型別 VocabularyItemExamples 鍵")](implementing-sirikit-images/plist13w.png#lightbox)
15. 新增一些`String`一詞的範例使用的索引鍵：

    [![](implementing-sirikit-images/plist14w.png "新增與詞彙的範例會使用幾個字串索引鍵")](implementing-sirikit-images/plist14w.png#lightbox)
16. 重複上述步驟的其他應用程式需要定義的自訂詞彙。
17. 摺疊`ParameterVocabularies`索引鍵。
18. 新增`IntentPhrases`索引鍵**型別**的`Array`:

    [![](implementing-sirikit-images/plist15w.png "加入陣列的型別 IntentPhrases 鍵")](implementing-sirikit-images/plist15w.png#lightbox)
19. 新增具有新的金鑰**型別**的`Dictionary`:

    [![](implementing-sirikit-images/plist16w.png "加入新的金鑰與字典的型別")](implementing-sirikit-images/plist16w.png#lightbox)
20. 新增`IntentName`索引鍵**型別**的`String`和意圖範例：

    [![](implementing-sirikit-images/plist17w.png "加入 IntentName 鍵 String 型別和意圖範例")](implementing-sirikit-images/plist17w.png#lightbox)
21. 新增`IntentExamples`索引鍵**型別**的`Array`:

    [![](implementing-sirikit-images/plist18w.png "加入陣列的型別 IntentExamples 鍵")](implementing-sirikit-images/plist18w.png#lightbox)
22. 新增一些`String`一詞的範例使用的索引鍵：

    [![](implementing-sirikit-images/plist19w.png "新增與詞彙的範例會使用幾個字串索引鍵")](implementing-sirikit-images/plist19w.png#lightbox)
23. 重複上述步驟的任何應用程式必須提供的使用方式範例的意圖。

-----

> [!IMPORTANT]
> `AppIntentVocabulary.plist`會註冊與測試上的 Siri 期間開發和它的裝置可能需要一些時間 Siri 納入自訂的詞彙。 如此一來，測試人員必須等候幾分鐘，再嘗試將已更新時，測試應用程式特定詞彙。

如需詳細資訊，請參閱我們[應用程式特定的詞彙參考](~/ios/platform/sirikit/understanding-sirikit.md)與 Apple[指定自訂詞彙參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1)。

## <a name="adding-an-intents-extension"></a>新增 Intents 延伸模組

既然已經備妥應用程式採用 SiriKit，開發人員必須將一個 （或以上） 的 Intents 延伸模組新增至方案，來處理 Siri 整合所需的意圖。

如需每個 Intents 延伸模組，執行下列作業：

- Intents 延伸模組，將專案加入至 Xamarin.iOS 應用程式方案。
- 設定 Intents 延伸模組`Info.plist`檔案。
- 修改 Intents 延伸模組的主要類別。

如需詳細資訊，請參閱我們[Intents 延伸模組參考](~/ios/platform/sirikit/understanding-sirikit.md)與 Apple[建立 Intents 延伸模組參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CreatingtheIntentsExtension.html#//apple_ref/doc/uid/TP40016875-CH4-SW1)。

### <a name="creating-the-extension"></a>建立擴充功能

若要將 Intents 延伸模組加入至方案中，執行下列作業：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 以滑鼠右鍵按一下**方案名稱**中**Solution Pad** ，然後選取**新增** > **加入新的專案...**.
2. 從對話方塊中選取**iOS** > **延伸模組** > **意圖擴充**然後按一下**下一步**按鈕： 

    [![](implementing-sirikit-images/intents05.png "選取意圖的延伸模組")](implementing-sirikit-images/intents05.png#lightbox)
3. 接下來輸入**名稱**意圖延伸模組，然後按一下 [**下一步]** 按鈕： 

    [![](implementing-sirikit-images/intents06.png "輸入的意圖的延伸模組的名稱")](implementing-sirikit-images/intents06.png#lightbox)
4. 最後，按一下**建立**按鈕以新增至應用程式方案的意圖延伸模組： 

    [![](implementing-sirikit-images/intents07.png "將意圖延伸模組新增至應用程式方案")](implementing-sirikit-images/intents07.png#lightbox)
5. 中**方案總管**，以滑鼠右鍵按一下**參考**新建的意圖延伸模組的資料夾。 檢查一般的共用程式碼程式庫專案 （也就上面建立的應用程式） 的名稱，然後按一下**確定**按鈕： 

    [![](implementing-sirikit-images/intents08.png "選取通用的共用程式碼程式庫專案的名稱")](implementing-sirikit-images/intents08.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 以滑鼠右鍵按一下**方案名稱**中**方案總管**，然後選取**新增** > **加入新的專案...**.
2. 從對話方塊中選取**Visual C# > iOS 延伸模組 > 意圖延伸模組**然後按一下**下一步**按鈕：

    [![](implementing-sirikit-images/intents05.w157-sml.png "選取意圖的延伸模組")](implementing-sirikit-images/intents05.w157.png#lightbox)
3. 接下來輸入**名稱**意圖延伸模組，然後按一下 [**確定**] 按鈕。
1. 中**方案總管**，以滑鼠右鍵按一下**參考**新建的 Intents 延伸模組的資料夾，然後選擇 **新增 > 參考**。 檢查一般的共用程式碼程式庫專案 （也就上面建立的應用程式） 的名稱，然後按一下**確定**按鈕：

    [![](implementing-sirikit-images/intents08w.png "選取通用的共用程式碼程式庫專案的名稱")](implementing-sirikit-images/intents08w.png#lightbox)
    
-----

重複這些步驟的意圖延伸模組的數目 (根據[架構擴充功能的應用程式](#architecting-the-app-for-extensions)上一節) 會要求應用程式。

### <a name="configuring-the-infoplist"></a>設定 Info.plist

針對每個新增到應用程式的方案 Intents 延伸模組，必須設定在`Info.plist`才能使用應用程式的檔案。

就像任何一般的應用程式擴充功能，應用程式會有的現有索引鍵`NSExtension`和`NSExtensionAttributes`。 Intents 延伸模組有兩個新的屬性必須設定：

[![](implementing-sirikit-images/intents01.png "必須設定兩個新屬性")](implementing-sirikit-images/intents01.png#lightbox)

- **IntentsSupported** -需要及想要從意圖延伸模組所支援的應用程式的意圖類別名稱的陣列所組成。
- **IntentsRestrictedWhileLocked** -是指定延伸模組的鎖定螢幕行為的應用程式的選擇性索引鍵。 它包含應用程式想要要求使用者登入，從目的延伸模組所使用的意圖類別名稱的陣列。

若要設定意圖延伸模組的`Info.plist`檔案中，按兩下該**方案總管 中**以開啟它進行編輯。 接下來，切換至**來源**檢視，然後展開`NSExtension`和`NSExtensionAttributes`在編輯器中的索引鍵：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](implementing-sirikit-images/intents02.png "在編輯器中 NSExtension 和 NSExtensionAttributes 金鑰")](implementing-sirikit-images/intents02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents02w.png "在編輯器中 NSExtension 和 NSExtensionAttributes 金鑰")](implementing-sirikit-images/intents02w.png#lightbox)

-----

展開`IntentsSupported`鍵，並加入此延伸模組將支援的任何目的類別名稱。 範例 MonkeyChat 應用程式中，它支援`INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](implementing-sirikit-images/intents09.png "INSendMessageIntent 金鑰")](implementing-sirikit-images/intents09.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents09w.png "INSendMessageIntent 金鑰")](implementing-sirikit-images/intents09w.png#lightbox)

-----

如果應用程式 （選擇性） 需要使用者要登入到裝置，以使用指定的目的，請展開`IntentRestrictedWhileLocked`索引鍵，並新增已限制存取權的意圖的類別名稱。 範例 MonkeyChat 應用程式中，使用者必須登入傳送的交談訊息，因此我們新增了`INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](implementing-sirikit-images/intents10.png "新增的 INSendMessageIntent 金鑰")](implementing-sirikit-images/intents10.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents10w.png "新增的 INSendMessageIntent 金鑰")](implementing-sirikit-images/intents10w.png#lightbox)

-----


如需可用的意圖定義域的完整清單，請參閱 Apple[意圖網域參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)。

### <a name="configuring-the-main-class"></a>設定的主要類別

接下來，開發人員必須設定成 Siri 做為主要進入點意圖延伸模組的主要類別。 它必須是子類別的`INExtension`其符合`IINIntentHandler`委派。 例如: 

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

應用程式意圖延伸模組的主要類別，都必須實作一個單獨方法`GetHandler`方法。 這個方法會由 SiriKit 傳遞意圖，而且應用程式必須傳回**意圖的處理常式**符合特定目的的型別。

因為範例 MonkeyChat 應用程式只會處理一個意圖，它會傳回本身在`GetHandler`方法。 如果延伸模組會處理一個以上的意圖，開發人員會加入每個意圖類型的類別，並傳回根據執行個體`Intent`傳遞至方法。

### <a name="handling-the-resolve-stage"></a>處理解析階段

在解析階段意圖延伸模組會釐清並驗證參數，傳入 Siri 從，並已設定透過使用者的交談。

取得傳送 Siri 從每個參數，針對沒有`Resolve`方法。 應用程式必須實作這個方法，每個參數的應用程式可能需要使用 Siri 的協助，以從使用者取得正確的答案。

在範例 MonkeyChat 應用程式的情況下意圖擴充功能會要求來傳送訊息給一或多個收件者。 每個收件者清單中，延伸模組必須連絡搜尋可以有下列結果：

- 找不到一個符合的連絡人。
- 您可以找到兩個或多個相符的連絡人。
- 您可以不找到任何相符的連絡人。

此外，MonkeyChat 需要訊息的本文內容。 如果使用者沒有提供這，Siri 必須提示使用者輸入內容。

意圖延伸模組必須適當地處理每個案例。


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

如需詳細資訊，請參閱我們[解析階段參考](~/ios/platform/sirikit/understanding-sirikit.md)與 Apple [Resolving 和處理意圖參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ResolvingandHandlingIntents.html#//apple_ref/doc/uid/TP40016875-CH5-SW1)。

### <a name="handling-the-confirm-stage"></a>處理在確認階段

在確認階段是資訊的其中意圖延伸模組會檢查以查看它有所有來滿足使用者的要求。 應用程式想要傳送確認，以及將所有支援的詳細資料的功能即將發生 Siri 來讓它可以確認與使用者，這是預期的動作。

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

如需詳細資訊，請參閱我們[確認階段參考](~/ios/platform/sirikit/understanding-sirikit.md)。

### <a name="processing-the-intent"></a>處理目的

這是意圖延伸模組實際執行的工作，以滿足使用者的要求，並將結果傳遞回 Siri，讓使用者可以收到通知的點。


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

如需詳細資訊，請參閱我們[處理階段參考](~/ios/platform/sirikit/understanding-sirikit.md)。

## <a name="adding-an-intents-ui-extension"></a>新增 Intents UI 延伸模組

選擇性 Intents UI 延伸模組提供的機會，可讓應用程式的 UI 和商標 Siri 體驗，並讓使用者可以連線到應用程式。 與此延伸模組的品牌以及文字記錄視覺化和其他資訊，可以讓應用程式。

[![](implementing-sirikit-images/intentsui01.png "Intents UI 延伸模組的輸出範例")](implementing-sirikit-images/intentsui01.png#lightbox)

如同 Intents 延伸模組，開發人員將會執行下一個步驟的 Intents UI 延伸模組：

- Intents UI 延伸模組將專案加入至 Xamarin.iOS 應用程式方案。
- 設定 Intents UI 延伸模組`Info.plist`檔案。
- 修改 Intents UI 延伸模組的主要類別。

如需詳細資訊，請參閱我們[Intents UI 延伸模組參考](~/ios/platform/sirikit/understanding-sirikit.md)與 Apple[提供的自訂介面參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ProvidingaCustomInterface.html#//apple_ref/doc/uid/TP40016875-CH7-SW1)。

### <a name="creating-the-extension"></a>建立擴充功能

若要將 Intents UI 延伸模組加入至方案中，執行下列作業：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 以滑鼠右鍵按一下**方案名稱**中**Solution Pad** ，然後選取**新增** > **加入新的專案...**.
2. 從對話方塊中選取**iOS** > **延伸模組** > **意圖 UI 延伸模組**然後按一下**下一步**按鈕： 

    [![](implementing-sirikit-images/intents11.png "選取的意圖 UI 延伸模組")](implementing-sirikit-images/intents11.png#lightbox)
3. 接下來輸入**名稱**意圖延伸模組，然後按一下 [**下一步]** 按鈕： 

    [![](implementing-sirikit-images/intents12.png "輸入的意圖的延伸模組的名稱")](implementing-sirikit-images/intents12.png#lightbox)
4. 最後，按一下**建立**按鈕以新增至應用程式方案的意圖延伸模組： 

    [![](implementing-sirikit-images/intents13.png "將意圖延伸模組新增至應用程式方案")](implementing-sirikit-images/intents13.png#lightbox)
5. 中**方案總管**，以滑鼠右鍵按一下**參考**新建的意圖延伸模組的資料夾。 檢查一般的共用程式碼程式庫專案 （也就上面建立的應用程式） 的名稱，然後按一下**確定**按鈕： 

    [![](implementing-sirikit-images/intents14.png "選取通用的共用程式碼程式庫專案的名稱")](implementing-sirikit-images/intents14.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 以滑鼠右鍵按一下**方案名稱**中**方案總管**，然後選取**新增** > **加入新的專案...**
2. 從對話方塊中選取**iOS** > **延伸模組** > **意圖 UI 延伸模組**然後按一下**下一步**按鈕。
3. 接下來輸入**名稱**意圖延伸模組，然後按一下 [**確定**] 按鈕。
5. 中**方案總管**，以滑鼠右鍵按一下**參考**新建的意圖延伸模組的資料夾。 檢查一般的共用程式碼程式庫專案 （也就上面建立的應用程式） 的名稱，然後按一下**確定** 按鈕。
    
-----

### <a name="configuring-the-infoplist"></a>設定 Info.plist

設定 Intents UI 延伸模組的`Info.plist`才能使用應用程式的檔案。

就像任何一般的應用程式擴充功能，應用程式會有的現有索引鍵`NSExtension`和`NSExtensionAttributes`。 Intents 延伸模組，還有一個新的屬性必須設定：

[![](implementing-sirikit-images/intents03.png "必須設定一個新屬性")](implementing-sirikit-images/intents03.png#lightbox)

**IntentsSupported**需要及想要從意圖延伸模組所支援的應用程式的意圖類別名稱的陣列所組成。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要設定意圖 UI 延伸模組`Info.plist`檔案中，按兩下該**方案總管 中**以開啟它進行編輯。 接下來，切換至**來源**檢視，然後展開`NSExtension`和`NSExtensionAttributes`在編輯器中的索引鍵：

[![](implementing-sirikit-images/intents04.png "在編輯器中 NSExtension 和 NSExtensionAttributes 金鑰")](implementing-sirikit-images/intents04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要設定意圖 UI 延伸模組`Info.plist`檔案中，按兩下該**方案總管 中**以開啟它進行編輯。 依序展開`NSExtension`和`NSExtensionAttributes`在編輯器中的索引鍵：

[![](implementing-sirikit-images/intents04w.png "在編輯器中的 t NSExtension 和 NSExtensionAttributes 金鑰")](implementing-sirikit-images/intents04w.png#lightbox)

-----

展開`IntentsSupported`鍵，並加入此延伸模組將支援的任何目的類別名稱。 範例 MonkeyChat 應用程式中，它支援`INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](implementing-sirikit-images/intents15.png "INSendMessageIntent 金鑰")](implementing-sirikit-images/intents15.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents15w.png "INSendMessageIntent 金鑰")](implementing-sirikit-images/intents15w.png#lightbox)

-----

如需可用的意圖定義域的完整清單，請參閱 Apple[意圖網域參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)。

### <a name="configuring-the-main-class"></a>設定的主要類別

設定成 Siri 做為主要進入點意圖 UI 延伸模組的主要類別。 它必須是子類別的`UIViewController`其符合`IINUIHostedViewController`介面。 例如: 

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

Siri 經歷`INInteraction`類別執行個體`Configure`方法`UIViewController`意圖 UI 延伸模組內的執行個體。

`INInteraction`物件提供三個關鍵擴充功能的資訊：

1. 正在處理之意圖 object。
2. 意圖回應物件從`Confirm`和`Handle`意圖擴充功能的方法。
3. 定義應用程式與 Siri 之間互動的狀態互動狀態。

`UIViewController`執行個體是 Siri 互動的主要類別，因為它繼承自`UIViewController`，才能夠存取所有的 UIKit 功能。

當呼叫 Siri`Configure`方法的`UIViewController`傳入檢視控制器是裝載 Siri Snippit 或 Maps 卡片中，指出檢視內容。

Siri 也將會傳入完成處理常式的應用程式需要應用程式已完成設定之後，傳回檢視的所需的大小。

### <a name="design-the-ui-in-ios-designer"></a>IOS 設計工具中的 UI 設計

將 Intents UI 延伸模組的使用者介面版面配置中 iOS 設計工具中。 按兩下副檔名`MainInterface.storyboard`檔案中**方案總管 中**以開啟它進行編輯。 拖曳在所有必要的 UI 項目，以建置使用者介面，並儲存變更。

> [!IMPORTANT]
> 雖然您可以加入互動式元素，例如`UIButtons`或是`UITextFields`意圖 UI 延伸模組的`UIViewController`這些嚴格禁止為意圖 UI 以非互動，使用者將無法與它們互動。

### <a name="wire-up-the-user-interface"></a>網路總使用者介面

在 iOS 設計工具中建立 Intents UI 延伸模組的使用者介面，請編輯`UIViewController`子類別並覆寫`Configure`方法，如下所示：

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

Intents UI 延伸模組將一律顯示以及其他 Siri 內容，例如應用程式圖示和名稱，在 UI 的上方或，根據的目的，可能會顯示在底部的按鈕 （例如 [傳送] 或 [取消]）。

有一些執行個體，其中應用程式可以取代 Siri 根據預設，例如訊息向使用者顯示的資訊或對應，應用程式時，可以使用其中一個應用程式量身打造取代預設體驗。

如果 Intents UI 延伸模組需要覆寫預設 Siri UI 項目`UIViewController`子類別必須實作`IINUIHostedViewSiriProviding`介面和 opt-in 以顯示在特定介面項目。

將下列程式碼加入`UIViewController`告訴 Siri 意圖 UI 延伸模組已顯示的訊息內容的子類別：

```csharp
public bool DisplaysMessage {
    get {return true;}
}
```

### <a name="considerations"></a>考量

開發人員，需要下列項目設計和實作意圖 UI 延伸模組時，Apple 建議：

- **是刻意的記憶體使用量**-因為意圖 UI 延伸模組是暫時性的並只顯示一小段時間，系統會使用完整的應用程式所使用的記憶體條件約束更緊密。
- **請考慮最小值和最大的檢視大小**-確保意圖 UI 擴充功能是否在每個 iOS 裝置類型、 大小和方向正確無誤。 此外，應用程式會傳送回到 Siri 所需的大小可能無法授與。
- **使用具彈性且彈性的版面配置模式**-同樣地，以確保 UI 看起來很棒的每個裝置上。

## <a name="summary"></a>總結

這篇文章已涵蓋 SiriKit，並顯示如何將它新增至 Xamarin.iOS 應用程式提供的 iOS 裝置上使用 Siri 和對應的應用程式的使用者可以存取的服務。




## <a name="related-links"></a>相關連結

- [ElizaChat 範例](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [SiriKit 程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Intents Framework 參考](https://developer.apple.com/reference/intents)
- [Intents UI 架構參考](https://developer.apple.com/reference/intentsui)
- [意圖網域參考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)
