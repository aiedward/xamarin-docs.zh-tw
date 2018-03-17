---
title: Touch ID
description: "Apple 的指紋生物識別驗證技術 touch ID。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 3564b4f7d41822fdd9ab167fb3e756f26678a17b
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="touch-id"></a>Touch ID

IOS 7 已引入 touch ID 來驗證使用者的密碼類似。 不過，它是限制為解除鎖定裝置、 使用應用程式存放區、 使用 iTunes 和 iCloud keychain 只進行驗證。

現在有兩種方式可使用 Touch ID 做為驗證機制使用本機的驗證 API iOS 8 應用程式中。 您正在不可能使用本機驗證遠端驗證。

若要完全了解 Touch ID 和其價值，我們應該探索 Keychain 服務與這些新的變更表示您的使用者資料。 金鑰鏈存取也已經在擴充 iOS 8 透過新的存取控制清單 (Acl) 功能的使用中。

## <a name="keychain--secure-enclave"></a>Keychain & 安全 Enclave

金鑰鏈，大型的資料庫的密碼、 金鑰、 憑證和附註的安全儲存體提供一個個別的 Apple id。 在 iOS 8 應用程式一律存取自己唯一的金鑰鏈的項目，且無法存取其他應用程式的任何 keychain 項目。 這不同於 OS X 而 keychain 已解除鎖定使用單一密碼，讓任何 Keychain 服務感知的應用程式使用金鑰鏈。 本文章中我們將著重在 keychain iOS 8 中的運作方式。

Keychain 是特殊的資料庫，其中每個資料列就所謂_Keychain 項目_。 每個項目 keychain 屬性所描述，並且是加密的值所組成。 若要允許有效率地使用金鑰鏈，它適合小項目，或_密碼_。
每個金鑰鏈項目受到使用者密碼和唯一的裝置密碼。 Keychain 的項目應該受到保護，即使使用者不使用他們的裝置。 這藉由在 iOS 中只允許裝置鎖定時變成可用的項目，當裝置鎖定而無法使用。 它們也可以儲存在加密的備份。 Keychain 的重要功能之一是強制執行存取控制。應用程式可以存取它的 keychain 的部分，將會防止所有其他應用程式。 下圖說明應用程式互動 keychain 的方式：

[![](touchid-images/image1.png "此圖說明應用程式如何與 keychain 互動")](touchid-images/image1.png#lightbox)

### <a name="secure-enclave"></a>安全 Enclave

Keychain 無法解密的金鑰鏈項目本身;而是在中完成*安全 Enclave*。 安全 enclave 是運算器內有責任判斷從 Touch ID 感應器針對已註冊的列印的指紋資料比對成功的 A7 晶片。 它接著解密的金鑰鏈項目，並返回 keychain 的解密的密碼。

### <a name="working-with-keychain"></a>使用 金鑰鏈

第一次您的應用程式應該查詢 Keychain 的密碼是否存在。 如果它不存在，您可能需要提示輸入密碼，因此使用者不持續要求。 如果更新需要密碼，提示使用者輸入新密碼，並傳入 keychain 更新的值。

> [!NOTE]
> 使用密碼從 Keychain 擷取之後，應該會從記憶體清除資料的所有參考。 永遠不會將它指派給全域變數。

## <a name="keychain-acl-and-touch-id"></a>Keychain ACL 和 Touch ID

存取控制清單是描述發生什麼事必須設為允許執行特定作業的相關資訊的 iOS 8 中的新金鑰鏈項目屬性。 這可能是格式為顯示 [警示] 對話方塊，或要求新的密碼。 ACL 可讓您設定的協助工具和驗證金鑰鏈項目。 下圖顯示這個新屬性如何結合其餘 keychain 項目：

[![](touchid-images/image2.png "這個圖表會顯示這個新屬性如何結合其餘 keychain 項目")](touchid-images/image2.png#lightbox)

從 iOS 8，開始有現在是新的使用者是否存在原則`SecAccessControl`，這會強制執行安全的 enclave 和更新版本上 iPhone 5 秒。 我們可以看到下面只如何裝置組態可能會影響原則評估資料表中：

|裝置設定|原則評估|備份機制|
|--- |--- |--- |
|沒有密碼的裝置|不允許存取|無|
|使用密碼的裝置|需要密碼|無|
|Touch id 的裝置|慣用 Touch ID|可讓密碼|

在安全的 Enclave 內的所有作業可以彼此都信任。 這表示我們可以使用 Touch ID 的驗證結果授權 Keychain 項目解密。 安全的 Enclave 也會保存失敗 Touch ID 的相符項目中案例的使用者將擁有要還原成使用密碼的計數器。
新的架構中 iOS 8，稱為_本機驗證_，支援的裝置中的驗證程序。 我們將探討這在下一節。

## <a name="local-authentication"></a>本機驗證

如同我們在上一節中所建立，應用程式可以使用本機驗證來驗證使用者遵循與已在裝置設定的安全性原則中。

目前，API 會提供只有兩個功能： 首先，它可以協助透過使用新的金鑰鏈存取控制清單 (Acl) 的現有金鑰鏈服務。 Keychain 資料可以是與成功驗證的使用者指紋解除鎖定。

其次，LocalAuthentication 提供兩種方法來驗證您的應用程式在本機。 開發人員應該使用`CanEvaluatePolicy`來判斷裝置是否能夠接受 Touch ID，然後`EvaluatePolicy`啟動驗證作業。

雖然這兩個功能會提供本機驗證，但不提供的機制來驗證遠端伺服器的 應用程式或使用者。
本機驗證會提供新的標準使用者介面進行驗證。 在 Touch ID，這是具有兩個按鈕，如下所示的警示檢視。 一個按鈕，以 [取消]，以及一到使用後援驗證-密碼的方式。 另外還有必須設定的自訂訊息。 它是很好的作法，這可以用來說明為何需要 Touch ID 驗證使用者。

[![](touchid-images/image12.png "Touch ID 驗證警示")](touchid-images/image12.png#lightbox)

### <a name="with-keychain-services"></a>Keychain 服務

我們探討了稍早的 keychain 項目是如何解密，使用安全 enclave 確認我們的密碼。 在 iOS 8 中，我們可以使用本機驗證要求的存取控制清單的功能，可提供實作後援機制或密碼搭配 Touch ID 驗證。
若要使用我們應該使用的 ACL`SecAccessControl`原則，並核取狀態的裝置使用`SecAccessible.WhenPasscodeSetThisDeviceOnly`或`SecAccessible.WhenUnlocked`。

#### <a name="considerations-with-acl"></a>ACL 的考量

有許多項目時使用的金鑰鏈的 ACL，我們應該謹記在心，以下列出的一些：

-   只能使用與前景應用程式 – 如果您呼叫任何 keychain 作業在背景執行緒呼叫將會失敗。
-   新增和更新的金鑰鏈的項目可能會需要驗證。
-   如果要求金鑰鏈中傳回多個相符項目，可能會需要驗證。
-   ACL 受保護項目都是裝置，並因此不同步處理或備份。

### <a name="using-local-authentication-without-keychain-services"></a>使用本機不使用金鑰鏈服務驗證

本機驗證已建立為一種方法收集認證，例如密碼或 Touch ID，以及使用以完成驗證使用者安全 Enclave。 將其視為您的應用程式與安全 Enclave，永遠不會直接與對方進行通訊之間的橋樑。 也可用為您的應用程式的原則評估。

若要這樣做應用程式會呼叫本機驗證，就會開始內安全 Enclave 作業內的原則評估。 您可以利用這驗證提供給您的應用程式，而不需直接查詢/存取安全的 Enclave。

[![](touchid-images/image13a.png "使用本機不使用金鑰鏈服務驗證")](touchid-images/image13a.png#lightbox)

應用程式中使用本機驗證提供簡單的方式來實作使用者驗證，例如若要解除鎖定僅供裝置擁有者，例如銀行應用程式，或以利家長監護的眼睛功能的人應用程式。 您也可以使用它做為延伸已經存在的驗證方法 – 使用者要其資訊安全，但他們也想要有選項。

本機驗證的安全性與不同的金鑰鏈。 例如，使用鑰匙圈，信任時作業系統與安全的 Enclave 之間。 使用本機驗證，就此應用程式與作業系統，這表示您只能存取安全 Enclave，不安全 Enclave 本身的結果。

上的安全性主體，務必也知道有**沒有存取**已註冊的手指或指紋影像。 安全的 Enclave 是這項資訊的擁有者，因此沒有其他系統元件可以存取它。

若要使用不含鑰匙圈 Touch ID，利用本機的驗證 API，有幾個函式，我們可以使用。 這些詳述如下：

*   `CanEvaluatePolicy` – 這只會檢查裝置可接受 Touch ID 的能力
*   `EvaluatePolicy` – 這會啟動驗證作業並顯示 UI，並傳回`true`或`false`回應。
*   `DeviceOwnerAuthenticationWithBiometrics` – 這是原則可以用來顯示 [Touch ID] 畫面。 值得注意的是，沒有任何密碼後援機制這裡，您應該改為實作此後援允許使用者略過 Touch ID 驗證您的應用程式中。

有幾個警告，使用本機驗證，如下所示：

*   如同鑰匙圈，它只能執行於前景。 在背景執行緒上呼叫它會導致失敗。
*   請注意，原則評估可能會失敗。 [密碼] 按鈕必須改為實作。
*   您必須提供`localizedReason`解釋為什麼需要驗證。 這可協助使用者使用來建置信任。

接下來，在下面的區段中，我們將探討如何實作應用程式開發介面，這些警告納入考量。

## <a name="adding-touch-id-to-your-application"></a>Touch ID 加入至應用程式

上一節中，我們探討了存取及驗證使用的金鑰鏈和本機驗證背後的理論。 我們現在會看看您可以將 Touch ID 中整合您的應用程式的方式。

### <a name="walkthrough"></a>逐步解說

讓我們查看我們的應用程式中加入一些 Touch ID 驗證。 在此逐步解說中我們會使用[分鏡腳本資料表](https://developer.xamarin.com/samples/StoryboardTable/)範例。 我們想要確定只有裝置擁有者可以將項目加入至這份清單，我們不想讓任何人都將項目弄亂 ！

1.  下載範例和 Visual Studio 中執行 for mac。
2.  按兩下`MainStoryboard.Storyboard`iOS 設計工具中開啟範例。 在此範例中，我們想要將新的畫面加入至我們的應用程式，這會控制驗證。 這將會移目前`MasterViewController`。
3.  拖曳新**檢視控制器**從**工具箱**至**設計介面**。 設定為**根檢視控制器**由**Ctrl + 拖曳**從**導覽控制站**:

    [![](touchid-images/image4.png "設定根檢視控制站")](touchid-images/image4.png#lightbox)
4.  新的檢視控制器`AuthenticationViewController`。
5.  下一步，拖曳按鈕，並將它放在`AuthenticationViewController`。 呼叫這個`AuthenticateButton`，並為它提供文字`Add a Chore`。
6.  在建立事件`AuthenticateButton`呼叫`AuthenticateMe`。
7.  建立手動從話題`AuthenticationViewController`按一下黑色列底部和**Ctrl + 拖曳**列從`MasterViewController`，然後選擇**發送**(或**顯示**如果使用大小類別）：

    [![](touchid-images/image5.png "從列拖曳至 MasterViewController 並選擇推入，或顯示")](touchid-images/image6.png#lightbox)
8.  按一下新建立的話題並予以識別項`AuthenticationSegue`，如下所示：

    [![](touchid-images/image7.png "設 AuthenticationSegue segue 識別碼")](touchid-images/image7.png#lightbox)
9.  將下列程式碼新增至 `AuthenticationViewController`：

    ```
    partial void AuthenticateMe (UIButton sender)
        {
            var context = new LAContext();
            NSError AuthError;
            var myReason = new NSString("To add a new chore");


            if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out AuthError)){
                var replyHandler = new LAContextReplyHandler((success, error) => {

                    this.InvokeOnMainThread(()=>{
                        if(success){
                            Console.WriteLine("You logged in!");
                            PerformSegue("AuthenticationSegue", this);
                        }
                        else{
                            //Show fallback mechanism here
                        }
                    });

                });
                context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, myReason, replyHandler);
            };
        }
    ```

這是您需要實作使用本機驗證 Touch ID 驗證的程式碼。 下圖中反白顯示的線條顯示使用本機驗證：

[![](touchid-images/image8.png "反白顯示的線會顯示使用本機驗證")](touchid-images/image8.png#lightbox)

首先，我們需要建立裝置是否可接受輸入時，使用 Touch ID 的能力`CanEvaluatePolicy`並傳入原則`DeviceOwnerAuthenticationWithBiometrics`。 如果這是 true，則我們可以使用，以顯示 Touch ID UI `EvaluatePolicy`。 有三組資訊，我們必須將傳遞至`EvaluatePolicy`– 原則本身、 字串，用以說明為何需要，驗證和回覆的處理常式。 回覆處理常式會告訴應用程式，因此應該在成功，或不成功，驗證的情況下執行的。 讓我們來看靠近回覆處理常式：

[![](touchid-images/image9.png "回應處理常式")](touchid-images/image9.png#lightbox)

回應處理常式會指定型別的`LAContextReplyHandler`，其可接受的參數成功 –`bool`值，和`NSError`呼叫`error`。 如果成功，這是我們實際執行的地方不論它是我們想要進行驗證-在此情況下顯示畫面，讓我們將加入新的例行工作。 請記住的本機驗證警告的其中一個是，必須在前景執行，因此請務必使用`InvokeOnMainThread`:

[![](touchid-images/image10.png "用於本機驗證 InvokeOnMainThread")](touchid-images/image10.png#lightbox)

最後，當驗證是否已順利完成，我們想要轉換為`MasterViewController`。 `PerformSegue`方法可以用來執行這項操作：

[![](touchid-images/image11.png "呼叫轉換至 MasterViewController PerformSegue 方法")](touchid-images/image11.png#lightbox)

## <a name="summary"></a>總結
本指南中我們探討了鑰匙圈，這在 iOS 中的運作方式。 我們也已探索 keychain ACL，並變更這在 iOS 中的。 接下來，我們採用查看本機驗證架構，其 iOS 8 的新功能，並查看我們的應用程式中實作 Touch ID 的驗證。

## <a name="related-links"></a>相關連結

- [Touch ID 的範例](https://developer.xamarin.com/samples/StoryboardTable_LocalAuthentication)
- [Keychain WWDC 範例](https://developer.xamarin.com/samples/KeychainTouchID/)
- [Keychain （範例）](https://developer.xamarin.com/samples/Keychain/)
