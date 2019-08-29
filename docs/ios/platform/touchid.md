---
title: Xamarin 中的 Touch ID
description: 本檔說明如何在 Xamarin iOS 應用程式中使用 Touch ID (Apple 的生物識別指紋驗證技術)。
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: cb71fc75b01621ed381f42b41df68fc80309f22c
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121293"
---
# <a name="touch-id-in-xamarinios"></a>Xamarin 中的 Touch ID

在 iOS 7 中引進了 Touch ID, 做為驗證使用者的方法-類似于密碼。 不過, 它僅限於使用 App Store 解除鎖定裝置、使用 iTunes 並驗證 iCloud keychain。

現在有兩種方式可以使用 Touch ID 作為 iOS 8 應用程式中使用本機驗證 API 的驗證機制。 目前無法使用本機驗證從遠端進行驗證。

若要完全瞭解 Touch ID 和其價值, 我們應該探索 Keychain 服務, 以及這些新變更對於您的使用者資料有何意義。 Keychain Access 也已在 iOS 8 中使用新的存取控制清單 (Acl) 功能進行擴充。

## <a name="keychain--secure-enclave"></a>Keychain & Secure 記憶體保護區

Keychain 是一個大型資料庫, 為一個個別的 Apple ID 提供密碼、金鑰、憑證和附注的安全儲存體。 在 iOS 8 中, 應用程式一律可以存取自己唯一的 keychain 專案, 而且無法存取其他應用程式的任何 keychain 專案。 這與使用單一密碼解除鎖定 keychain 的 OS X 不同, 讓任何 Keychain 服務感知應用程式都能使用 keychain。 在本文中, 我們將著重于 keychain 在 iOS 8 中的運作方式。

Keychain 是特製化的資料庫, 其中每個資料列都稱為_Keychain 專案_。 每個專案都是由 keychain 屬性來描述, 而且是由加密值所組成。 為了讓您有效率地使用 keychain, 它已針對小型專案或_秘密_進行優化。
每個 keychain 專案都會受到使用者密碼和唯一的裝置密碼所保護。 即使使用者未使用其裝置, Keychain 專案也應該受到保護。 這是在 iOS 中實行, 其只允許在裝置解除鎖定時, 讓專案可供使用, 而當裝置鎖定時, 則會變成無法使用。 它們也可以儲存在加密的備份中。 Keychain 的其中一個主要功能是強制執行存取控制;應用程式可以存取其 keychain 部分, 而所有其他應用程式將會被禁止。 下圖說明應用程式如何與 keychain 互動:

[![](touchid-images/image1.png "下圖說明應用程式如何與 keychain 互動")](touchid-images/image1.png#lightbox)

### <a name="secure-enclave"></a>安全記憶體保護區

Keychain 無法自行解密 Keychain 專案;相反地, 它是在*安全記憶體保護區*中完成。 安全記憶體保護區是 A7 晶片內的共同處理器, 負責根據已註冊的列印來判斷從 Touch 識別碼感應器成功符合的指紋資料。 然後, 它會將 keychain 專案解密, 並將解密的秘密傳回 keychain。

### <a name="working-with-keychain"></a>使用 Keychain

首先, 您的應用程式應該查詢至 Keychain, 以查看密碼是否存在。 如果不存在, 您可能需要提示輸入密碼, 才不會持續詢問使用者。 如果需要更新密碼, 請提示使用者輸入新密碼, 並將更新的值傳入 keychain。

> [!NOTE]
> 使用從 Keychain 抓取的秘密之後, 所有對資料的參考都應該從記憶體中清除。 絕對不要將它指派給全域變數。

## <a name="keychain-acl-and-touch-id"></a>Keychain ACL 和 Touch ID

存取控制清單是 iOS 8 中的新 keychain 專案屬性, 它會描述有關允許特定作業發生之情況的資訊。 這可能是顯示警示對話方塊或要求密碼的形式。 ACL 可讓您設定 keychain 專案的協助工具和驗證。 下圖顯示這個新屬性如何與 keychain 專案的其餘部分結合:

[![](touchid-images/image2.png "下圖顯示這個新屬性如何與 keychain 專案的其餘部分結合")](touchid-images/image2.png#lightbox)

從 iOS 8 開始, 現在有新的使用者目前狀態原則, `SecAccessControl`這是由 iPhone 5 秒和更新版本上的 secure 記憶體保護區強制執行。 在下表中, 我們可以看到裝置設定會如何影響原則評估:

|裝置設定|原則評估|備份機制|
|--- |--- |--- |
|不具密碼的裝置|無存取權|None|
|具有密碼的裝置|需要密碼|無|
|具有 Touch ID 的裝置|偏好 Touch ID|允許密碼|

安全記憶體保護區內的所有作業都可以互相信任。 這表示我們可以使用 Touch ID 驗證結果來授權 Keychain 專案解密。 安全記憶體保護區也會保留失敗 Touch ID 相符的計數器, 在此情況下, 使用者必須使用密碼還原為。
IOS 8 中稱為「_本機驗證_」的新架構, 可支援在裝置內進行此驗證程式。 我們將在下一節中探討。

## <a name="local-authentication"></a>本機驗證

如同我們在上一節中所建立的, 應用程式可以使用本機驗證來驗證使用者, 以遵循裝置上已設定的安全性原則。

目前, API 只提供兩個功能:首先, 它會透過使用新的 Keychain 存取控制清單 (Acl) 來協助現有的 Keychain 服務。 Keychain 資料可透過使用者指紋的成功驗證來解除鎖定。

其次, LocalAuthentication 提供兩種方法, 在本機驗證您的應用程式。 開發人員應該`CanEvaluatePolicy`使用來判斷裝置是否能夠接受 Touch ID, 然後再`EvaluatePolicy`啟動驗證作業。

雖然這兩種功能都提供本機驗證, 但不會提供應用程式或使用者對遠端伺服器進行驗證的機制。
本機驗證會提供新的標準使用者介面來進行驗證。 在 Touch ID 的案例中, 這是具有兩個按鈕的警示視圖, 如下所示。 一個按鈕可取消, 另一個則用來進行驗證, 也就是密碼。 另外還有一個必須設定的自訂訊息。 使用此方法向使用者說明為何需要觸控識別碼驗證是很好的作法。

[![](touchid-images/image12.png "Touch ID 驗證警示")](touchid-images/image12.png#lightbox)

### <a name="with-keychain-services"></a>使用 Keychain Services

我們稍早探討了如何使用安全的記憶體保護區來驗證我們的密碼, 以 keychain 專案解密。 在 iOS 8 中, 我們可以使用本機驗證來要求 Touch ID 驗證, 並搭配存取控制清單功能, 以提供回溯機制的執行或密碼。
若要使用 ACL, 我們應該使用`SecAccessControl`原則, 然後使用`SecAccessible.WhenPasscodeSetThisDeviceOnly`或`SecAccessible.WhenUnlocked`檢查裝置的狀態。

#### <a name="considerations-with-acl"></a>ACL 的考慮

在 keychain 中使用 ACL 時, 我們應該記住許多事項, 以下列出其中一些專案:

- 僅搭配前景應用程式使用–如果您在背景執行緒上呼叫任何 keychain 作業, 呼叫將會失敗。
- 新增和更新 keychain 專案可能需要驗證。
- 如果要求在 keychain 中傳回多個相符的專案, 則可能需要進行驗證。
- ACL 受保護專案是僅限裝置, 因此不會進行同步處理或備份。

### <a name="using-local-authentication-without-keychain-services"></a>使用本機驗證而不 Keychain 服務

本機驗證的建立方式, 是用來收集認證 (例如密碼或 Touch ID), 以及使用安全記憶體保護區來完成使用者驗證。 您可以把它想成是您的應用程式與安全記憶體保護區之間的橋樑, 這絕對不能彼此直接通訊。 它也可以用於應用程式的原則評估。

若要這樣做, 應用程式會在本機驗證內呼叫原則評估, 這會在安全記憶體保護區內啟動作業。 您可以利用此來提供應用程式的驗證, 而不需要直接查詢/存取安全記憶體保護區。

[![](touchid-images/image13a.png "使用本機驗證而不 Keychain 服務")](touchid-images/image13a.png#lightbox)

在您的應用程式中使用本機驗證提供簡單的方式來執行使用者驗證, 例如僅針對裝置擁有者的眼睛 (例如銀行應用程式) 解除鎖定功能, 或且有助於個人的家長監護。應用程式. 您也可以將它用來擴充已經存在的驗證–使用者和其資訊都是安全的, 但他們也想要有選項。

本機驗證的安全性與 keychain 的安全不同。 例如, 使用 keychain 時, 信任是在作業系統和安全記憶體保護區之間。 使用本機驗證時, 它會在應用程式和作業系統之間, 這表示您只能存取安全記憶體保護區的結果, 而不是安全記憶體保護區本身。

就安全性的主旨而言, 您也必須知道, 沒有已註冊手指或指紋映射的**存取權**, 也非常重要。 安全記憶體保護區是這項資訊的擁有者, 因此沒有其他系統元件可以存取它。

若要利用本機驗證 API 來使用 Touch ID 而不 keychain, 有幾個函數可供我們使用。 以下詳細說明如下:

- `CanEvaluatePolicy`–這只會查看裝置是否能夠接受 Touch ID。
- `EvaluatePolicy`–這會啟動驗證作業並顯示 UI, 並`true`傳回或`false`回答。
- `DeviceOwnerAuthenticationWithBiometrics`–這是可以用來顯示 [Touch ID] 畫面的原則。 值得注意的是, 此處沒有密碼回溯機制, 您應該改為在應用程式中執行此回復, 讓使用者略過觸控識別碼驗證。

使用本機驗證有幾個注意事項, 如下所示:

- 如同 Keychain, 它只能在前景中執行。 在背景執行緒上呼叫它會導致它失敗。
- 請記住, 原則評估可能會失敗。 [密碼] 按鈕將需要實作為回復。
- 您必須提供`localizedReason`來說明為何需要驗證。 這有助於與使用者建立信任關係。

接下來, 在下一節中, 我們將探討如何執行 API, 將這些注意事項納入考慮。

## <a name="adding-touch-id-to-your-application"></a>將 Touch ID 新增至您的應用程式

在先前的章節中, 我們探討了使用 Keychain 和本機驗證的存取和驗證背後的理論。 我們現在將探討如何將 Touch ID 整合到您的應用程式中。

### <a name="walkthrough"></a>逐步解說

我們來看一下如何將一些 Touch ID 驗證新增到我們的應用程式中。 在此逐步解說中, 我們將更新分鏡腳本[資料表](https://docs.microsoft.com/samples/xamarin/ios-samples/data/storyboardtable/)範例, 加入本機驗證, 使其運作方式類似于分鏡腳本[資料表–本機驗證](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable-localauthentication)範例, 這只允許已驗證的使用者將工作加入清單中。

1. 下載範例, 並在 Visual Studio for Mac 中執行。
2. `MainStoryboard.Storyboard`按兩下以在 iOS 設計工具中開啟範例。 在此範例中, 我們想要將新畫面新增至應用程式, 以控制驗證。 這會在目前`MasterViewController`的之前進行。
3. 將新的**View Controller**從 [**工具箱**] 拖曳至 [ **Design Surface**]。 將此設定為**根視圖控制器**, 方法是**Ctrl + 拖曳** **流覽控制器**:

    [![](touchid-images/image4.png "設定根視圖控制器")](touchid-images/image4.png#lightbox)
4. 將新的 View Controller `AuthenticationViewController`命名為。
5. 接下來, 拖曳按鈕, 並將它放`AuthenticationViewController`在上。 呼叫此`AuthenticateButton`, 並為它提供文字`Add a Chore`。
6. 在呼叫`AuthenticateButton` `AuthenticateMe`的上建立事件。
7. 若要建立手動 segue `AuthenticationViewController` , 請按一下底部的黑色列, 然後從列中按**Ctrl + 拖曳**至`MasterViewController` , 然後選擇 [**推送**] (或 [**顯示**是否使用大小] 類別):

    [![](touchid-images/image5.png "從橫條拖曳至 MasterViewController, 然後選擇 [push] 或 [show]")](touchid-images/image6.png#lightbox)
8. 按一下新建立的 segue, 並指定其識別碼`AuthenticationSegue`, 如下所示:

    [![](touchid-images/image7.png "將 segue 識別碼設定為 AuthenticationSegue")](touchid-images/image7.png#lightbox)
9. 將下列程式碼新增至 `AuthenticationViewController`：

    ```csharp
    partial void AuthenticateMe (UIButton sender)
    {
        var context = new LAContext();
        NSError AuthError;
        var myReason = new NSString("To add a new chore");

        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out AuthError)){
            var replyHandler = new LAContextReplyHandler((success, error) => {
                this.InvokeOnMainThread(()=> {
                    if(success)
                    {
                        Console.WriteLine("You logged in!");
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        // Show fallback mechanism here
                    }
                });
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, myReason, replyHandler);
        };
    }
    ```

這是使用本機驗證來執行 Touch ID 驗證所需的所有程式碼。 下圖中的反白顯示行顯示本機驗證的使用方式:

[![](touchid-images/image8.png "醒目提示的行會顯示本機驗證的使用")](touchid-images/image8.png#lightbox)

首先, 我們需要建立裝置是否能夠接受 Touch ID 輸入, 方法是使用`CanEvaluatePolicy`並傳入原則。 `DeviceOwnerAuthenticationWithBiometrics` 如果這是 true, 我們可以使用`EvaluatePolicy`來顯示 Touch ID UI。 我們必須傳入`EvaluatePolicy`三項資訊-原則本身、說明為何需要驗證的字串, 以及回復處理常式。 回復處理常式會告訴應用程式, 它應該在驗證成功或失敗時執行的動作。 讓我們更仔細看一下回複處理常式:

[![](touchid-images/image9.png "回復處理常式")](touchid-images/image9.png#lightbox)

回復處理`LAContextReplyHandler`程式的指定類型為, 其接受參數 success `bool` –值, 以及`NSError`呼叫`error`。 如果成功, 就會實際執行我們想要驗證的任何內容, 在此情況下會顯示畫面, 讓我們加入新的工作。 請記住, 本機驗證的其中一個警告是必須在前景上執行, 因此請務必使用`InvokeOnMainThread`:

[![](touchid-images/image10.png "使用 InvokeOnMainThread 進行本機驗證")](touchid-images/image10.png#lightbox)

最後, 當驗證成功時, 我們想要轉換到`MasterViewController`。 `PerformSegue`方法可以用來執行此動作:

[![](touchid-images/image11.png "呼叫 PerformSegue 方法以轉換至 MasterViewController")](touchid-images/image11.png#lightbox)

## <a name="summary"></a>總結

在本指南中, 我們探討了 Keychain 以及它在 iOS 中的運作方式。 我們也探討了 keychain ACL, 以及在 iOS 中的變更。 接下來, 我們將探討本機驗證架構, 這是 iOS 8 的新功能, 並探討如何在我們的應用程式中執行 Touch ID 驗證。

## <a name="related-links"></a>相關連結

- [分鏡腳本資料表–本機驗證](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable-localauthentication)
- [Keychain WWDC 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-keychaintouchid/)
- [Keychain (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/keychain/)
