---
title: 在 Xamarin.iOS 中的 touch ID
description: 本文件說明如何使用 Touch ID 時，Apple 的生物特徵辨識指紋驗證技術，在 Xamarin.iOS 應用程式。
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 2d67bc71361e335515cfba8b5a20e157ed6b6b05
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61087793"
---
# <a name="touch-id-in-xamarinios"></a>在 Xamarin.iOS 中的 touch ID

Touch ID 是在 iOS 7 引進，做為驗證使用者-密碼類似。 不過，已限制為解除鎖定裝置、 使用應用程式存放區、 使用 iTunes 及驗證只 iCloud 鑰匙圈。

現在有兩種方式可使用 Touch ID 做為驗證機制使用本機的驗證 API 的 iOS 8 應用程式。 您目前不可以使用本機驗證遠端驗證。

若要完全了解 Touch ID 和其價值，我們應該探索 Keychain 服務，這些新的變更表示您的使用者資料。 金鑰鏈存取也已在 iOS 8 透過使用新的存取控制清單 (Acl) 功能中擴充。

## <a name="keychain--secure-enclave"></a>金鑰鏈與安全飛地

金鑰鏈為大型資料庫的密碼、 金鑰、 憑證和附註的安全的儲存體提供一個個別的 Apple id。 在 iOS 8 中應用程式，一律可以存取它自己唯一的金鑰鏈的項目，且無法存取其他應用程式的任何金鑰鏈項目進行相關的設定。 這不同於 OS X 而 keychain 已解除鎖定使用單一密碼，讓任何 Keychain Services 感知的應用程式使用金鑰鏈。 在本文中，我們將著重在金鑰鏈在 iOS 8 中的運作方式。

金鑰鏈是特製化的資料庫，其中每個資料列就所謂_鑰匙圈項目_。 每個項目 keychain 屬性所描述，並且是加密的值所組成。 若要允許有效率地使用金鑰鏈，它適合小項目，或_祕密_。
每個金鑰鏈項目受使用者密碼和唯一裝置祕密。 金鑰鏈的項目應受到保護，即使使用者未使用其裝置。 這藉由在 iOS 中只允許裝置解除鎖定時變成可用的項目，這些裝置鎖定時變成無法使用。 它們也可以儲存在加密的備份。 其中一個金鑰鏈的主要功能是強制執行存取控制;應用程式可以存取它的金鑰鏈中的部分，將會防止所有其他應用程式。 下圖說明使用 keychain 應用程式互動的方式：

[![](touchid-images/image1.png "此圖說明如何使用 keychain 應用程式互動的方式")](touchid-images/image1.png#lightbox)

### <a name="secure-enclave"></a>安全飛地

金鑰鏈無法解密的金鑰鏈項目本身;而是在中完成*安全飛地*。 安全飛地是共同處理器會負責判斷成功的比對來自指紋 Touch ID 感應器，針對已註冊的列印資料的 A7 晶片中。 它接著解密的金鑰鏈項目，並傳回解密的密碼至金鑰鏈。

### <a name="working-with-keychain"></a>使用 金鑰鏈

第一次您的應用程式應該查詢至 Keychain 的密碼是否存在。 如果不存在，您可能需要讓系統持續要求使用者不是提示輸入密碼。 如果必須更新密碼，提示使用者輸入新密碼，並傳入至金鑰鏈的更新值。

> [!NOTE]
> 使用密碼從 Keychain 擷取之後，應該從記憶體中清除所有資料的參考。 永遠不會將它指派給全域變數。

## <a name="keychain-acl-and-touch-id"></a>金鑰鏈 ACL 及觸控裝置識別碼

存取控制清單為新的 keychain 項目屬性在 iOS 8，描述發生什麼事必須以允許發生的特定作業的相關資訊。 這可能是顯示警示對話方塊，或要求密碼格式。 ACL 可讓您設定的存取性和驗證金鑰鏈項目。 下圖顯示這個新屬性會繫結的其餘部分的 keychain 項目：

[![](touchid-images/image2.png "下圖顯示這個新屬性會繫結的其餘部分的 keychain 項目")](touchid-images/image2.png#lightbox)

IOS 8，截至目前沒有新的使用者是否存在原則`SecAccessControl`，這會強制執行安全飛地 iPhone 5 秒和更新版本。 我們可以看到在下表中，只是如何裝置設定可以影響原則評估：

|裝置設定|原則評估|備份機制|
|--- |--- |--- |
|沒有密碼的裝置|沒有存取權|None|
|使用密碼的裝置|需要密碼|None|
|使用 Touch ID 的裝置|偏好 Touch ID|可讓密碼|

安全飛地內的所有作業可以彼此都信任。 這表示我們可以使用 Touch ID 的驗證結果來授權金鑰鏈項目解密。 安全飛地也會保留失敗 Touch ID 的相符項目中案例的使用者擁有要還原成使用密碼的計數器。
新的架構，在 iOS 8 中稱為_本機驗證_，支援的裝置內的驗證此程序。 我們將探討這下一節。

## <a name="local-authentication"></a>本機驗證

如同我們在上一節中所建立，應用程式可以使用本機驗證來驗證使用者在裝置上已設定的安全性原則的遵循。

目前，API 會提供只有兩個功能：首先，它可協助透過使用新的金鑰鏈存取控制清單 (Acl) 的現有金鑰鏈服務。 可成功進行驗證的使用者指紋解除鎖定 Keychain 的資料。

其次，LocalAuthentication 提供兩種方法來驗證您的應用程式在本機。 開發人員應該使用`CanEvaluatePolicy`來判斷裝置是否能夠接受 Touch ID，然後`EvaluatePolicy`開始驗證作業。

雖然這兩個功能會提供本機驗證，但不會提供一種機制，來向遠端伺服器的 應用程式或使用者。
本機驗證會提供新的標準使用者介面進行驗證。 在 Touch ID 時，這是具有兩個按鈕，如下所示的警示檢視。 一個 [取消]，而若要使用的驗證 – 密碼的後援方法的另一個按鈕。 另外還有必須設定的自訂訊息。 它是很好的作法，這可以用來說明為何需要 Touch ID 驗證使用者。

[![](touchid-images/image12.png "Touch ID 驗證警示")](touchid-images/image12.png#lightbox)

### <a name="with-keychain-services"></a>使用金鑰鏈服務

我們有點稍早討論過的金鑰鏈項目有何解密時，會使用安全飛地驗證我們的密碼。 在 iOS 8 中，我們可以使用本機驗證要求的存取控制清單的功能，可提供實作後援機制或密碼搭配 Touch ID 驗證。
若要使用我們應該使用的 ACL`SecAccessControl`原則，並核取狀態的裝置使用`SecAccessible.WhenPasscodeSetThisDeviceOnly`或`SecAccessible.WhenUnlocked`。

#### <a name="considerations-with-acl"></a>使用 ACL 的考量

有許多項目時使用的金鑰鏈中的 ACL，我們應該謹記在心，以下列出其中一些：

-   只能使用前景應用程式 – 如果您呼叫任何 keychain 作業在背景執行緒呼叫將會失敗。
-   新增和更新的金鑰鏈的項目可能需要驗證。
-   如果要求傳回金鑰鏈中的多個相符項目，則可能需要驗證。
-   ACL 受保護的項目是裝置限用，並因此不同步處理或備份。

### <a name="using-local-authentication-without-keychain-services"></a>使用不含鑰匙圈 Services 的本機驗證

本機驗證已建立為一種方法來收集認證，例如密碼或 Touch ID，以及使用安全飛地，以完成驗證使用者。 將它視為您的應用程式與安全飛地，也就可以永遠不會直接彼此之間的橋樑。 也可用為您的應用程式的原則評估。

若要這樣做應用程式會呼叫本機驗證，它會啟動在安全飛地作業內的原則評估。 您可以利用此驗證提供給您的應用程式，而不需直接查詢/存取安全飛地。

[![](touchid-images/image13a.png "使用不含鑰匙圈 Services 的本機驗證")](touchid-images/image13a.png#lightbox)

在您的應用程式中使用本機的驗證提供簡單的方式，實作使用者驗證，例如若要解除鎖定為個別的功能僅供裝置擁有者，例如銀行應用程式，或以利家長監護的角度來看的應用程式。 您也可以使用它做為延伸已經存在的驗證方法 – 使用者喜歡他們的資訊是安全的但他們也想要有的選項。

本機驗證的安全性與不同的金鑰鏈。 比方說，使用金鑰鏈，信任時，作業系統與安全飛地之間。 使用本機驗證，就此應用程式與作業系統，這表示您只能存取安全飛地，不安全飛地本身的結果。

上的安全性主體，務必也知道會有**無法存取**已註冊的手指或指紋映像。 安全飛地是擁有者的這項資訊，且沒有其他系統元件因此可存取它。

若要使用 Touch ID，而 keychain 不利用本機的驗證 API，有幾個我們可以使用的函式。 這些詳述如下：

*   `CanEvaluatePolicy` – 這只會檢查裝置能夠接受 Touch id。
*   `EvaluatePolicy` – 這會啟動驗證作業並顯示 UI，並傳回`true`或`false`回應。
*   `DeviceOwnerAuthenticationWithBiometrics` – 這是可用來顯示 [Touch ID] 畫面的原則。 值得注意的是，沒有任何密碼後援機制這裡，您應該改為實作此後援中您的應用程式，以允許使用者略過 Touch ID 的驗證。

有幾個需要注意的事項與使用本機驗證，如下所示：

*   如同金鑰鏈，它只能執行於前景。 在背景執行緒上呼叫它，會導致失敗。
*   請記住，原則評估可能會失敗。 [密碼] 按鈕必須改為實作。
*   您必須提供`localizedReason`說明為何需要驗證。 這有助於建立信賴關係與使用者。

接下來，在下一節，我們將探討如何實作 API 注意下列事項納入考量。

## <a name="adding-touch-id-to-your-application"></a>將 Touch ID 新增至您的應用程式

在先前章節中，我們討論過存取和使用金鑰鏈和本機驗證的驗證背後的理論。 我們現在要了解如何整合中的 Touch ID，以及在您的應用程式。

### <a name="walkthrough"></a>逐步解說

現在讓我們來看看我們的應用程式中加入一些 Touch ID 驗證。 在本逐步解說我們即將更新[分鏡腳本表格](https://developer.xamarin.com/samples/StoryboardTable/)範例中，新增本機驗證，使其運作方式類似[分鏡腳本資料表 – 本機驗證](https://developer.xamarin.com/samples/monotouch/StoryboardTable_LocalAuthentication/)範例，只允許已驗證的使用者新增至清單的例行工作。

1. 下載範例，並執行它在 Visual Studio for mac。
2. 按兩下`MainStoryboard.Storyboard`iOS 設計工具中開啟範例。 此範例中，我們想要將新畫面新增至我們的應用程式，它會控制驗證。 這將移至目前`MasterViewController`。
3. 拖曳新**檢視控制器**從**工具箱**來**設計介面**。 設定為**根檢視控制器**依**Ctrl + 拖曳**從**瀏覽控制器**:

    [![](touchid-images/image4.png "設定根檢視控制器")](touchid-images/image4.png#lightbox)
4.  命名新的檢視控制器`AuthenticationViewController`。
5. 接下來，拖曳一個按鈕，並將它放在`AuthenticationViewController`。 稱之為`AuthenticateButton`，並為它提供文字`Add a Chore`。
6. 在 建立事件`AuthenticateButton`稱為`AuthenticateMe`。
7. 建立手動從的 segue`AuthenticationViewController`依序按一下底部的黑色列和**Ctrl + 拖曳**從列`MasterViewController`，然後選擇**推播**(或**顯示**如果使用大小類別）：

    [![](touchid-images/image5.png "從列拖曳至 MasterViewController 並選擇 推播，或顯示")](touchid-images/image6.png#lightbox)
8. 按一下新建立的 segue 並為它提供的識別項`AuthenticationSegue`，如下所示：

    [![](touchid-images/image7.png "將 segue 識別碼設 AuthenticationSegue")](touchid-images/image7.png#lightbox)
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

這是您需要實作使用本機驗證的 Touch ID 驗證的程式碼。 在下圖中反白顯示的線條顯示使用本機驗證：

[![](touchid-images/image8.png "反白顯示的線條顯示使用本機驗證")](touchid-images/image8.png#lightbox)

首先，我們要建立裝置是否能接受輸入，使用 Touch ID`CanEvaluatePolicy`並傳入原則`DeviceOwnerAuthenticationWithBiometrics`。 如果這是 true，則我們可以使用，以顯示 Touch ID UI `EvaluatePolicy`。 有三項資訊，我們必須將傳遞至`EvaluatePolicy`– 原則本身、 字串，用以說明為什麼只有在需要驗證，並回覆處理常式。 回覆處理常式會告訴應用程式，它應該在成功，或不成功，驗證的情況下。 我們來看更仔細地回覆處理常式：

[![](touchid-images/image9.png "回覆處理常式")](touchid-images/image9.png#lightbox)

回覆處理常式會指定型別的`LAContextReplyHandler`，後者會採用參數成功 –`bool`值，以及`NSError`稱為`error`。 如果成功，這是，我們就會執行任何它是我們想要驗證 – 在此情況下顯示畫面，讓我們將加入新的例行工作。 請記住其中一個本機驗證的注意事項是，它必須是在幕前執行，因此請務必使用`InvokeOnMainThread`:

[![](touchid-images/image10.png "InvokeOnMainThread 用於本機驗證")](touchid-images/image10.png#lightbox)

最後，當驗證是否已順利完成，我們想要轉換至`MasterViewController`。 `PerformSegue`方法可以用來執行這項操作：

[![](touchid-images/image11.png "呼叫至 MasterViewController 轉為 PerformSegue 方法")](touchid-images/image11.png#lightbox)

## <a name="summary"></a>總結
本指南中，我們討論過金鑰鏈，以及這在 iOS 中的運作方式。 我們也探索了 keychain ACL，並變更這在 iOS 中的。 接下來，我們介紹了本機驗證架構，這是 iOS 8 的新功能，並查看我們的應用程式中實作 Touch ID 的驗證。

## <a name="related-links"></a>相關連結

- [資料表 – 本機驗證分鏡腳本](https://developer.xamarin.com/samples/monotouch/StoryboardTable_LocalAuthentication/) 
- [金鑰鏈 WWDC 範例](https://developer.xamarin.com/samples/KeychainTouchID/)
- [金鑰鏈 （範例）](https://developer.xamarin.com/samples/Keychain/)
