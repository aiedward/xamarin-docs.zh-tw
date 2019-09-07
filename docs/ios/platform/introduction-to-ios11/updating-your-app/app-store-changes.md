---
title: IOS 11 中的 App Store 變更
description: 本檔探討 iOS 11 中 App Store 的變更。 它討論應用程式的商店圖示、升級的應用程式內購買、重新設計的產品頁面、客戶通訊和分階段發行。
ms.prod: xamarin
ms.assetid: 4A7A03FD-B4F2-4969-8676-A17260730FD6
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/13/2016
ms.openlocfilehash: 0ac9b486defb74cac7ccd946d2b35b283e6aeca5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752319"
---
# <a name="app-store-changes-in-ios-11"></a>IOS 11 中的 App Store 變更

IOS App Store 已經完成重新設計，不僅允許使用者有效率地導覽存放區，還可讓您以開發人員的身分將您的應用程式推廣給使用者。 這些升級包括應用程式內購買和產品頁面更新的更新。 iOS 11 也會新增有關如何與使用者通訊、如何新增您的應用程式圖示，以及如何將應用程式發行至公用的更新。

![新增 app store 版面配置](app-store-changes-images/image3.jpg)

重新設計的應用程式存放區具有下列區段：

- **今天**–此索引標籤包含屬於「編輯者挑選」或精選應用程式的應用程式。 若要在此升級您的應用程式，請在 [[升級](https://developer.apple.com//contact/app-store/promote/)] 頁面上填入資訊。
- **遊戲**–在 iTunes Connect 中設定為**遊戲**類別的任何應用程式，都可以在此索引標籤下找到。
- **應用程式**–此索引標籤會提供所有其他應用程式。 使用者可以依精選應用程式、類別、熱門付費/免費進行流覽。
- **更新**–此應用程式會顯示您應用程式的更新。 即使您透過[分階段發行](#Phased_Release)來發行應用程式，使用者仍然可以前往此索引標籤，並下載最新版本。
- **搜尋**–此索引標籤可讓使用者搜尋您的應用程式。

## <a name="store-icon"></a>儲存圖示

不會再于 iTunes Connect 中管理儲存圖示（或行銷圖示），而是必須在應用程式二進位檔中包含為[資產目錄](~/ios/app-fundamentals/images-icons/app-icons.md)，類似于應用程式圖示。 具有 PNG 格式的 1024 x 1024 存放區圖示必須包含在資產目錄中，才能成功提交 iOS 11 應用程式。

應用程式 thinning 可確保此額外的資產目錄不會增加應用程式大小。

## <a name="in-app-purchases-promoted-in-the-app-store"></a>在 App Store 中升級的應用程式內購買

Apple 已在 App Store 中更容易找到應用程式內購買專案。 您現在可以新增最多20個_App Store 升級_的應用程式內購買專案，現在可以在應用程式頁面、應用程式的產品頁面上找到，或藉由搜尋。

若要在 App Store 中顯示您的應用程式內購買，您必須包含下列資料：

- **影像**–您需要提供特別設計的影像，以供描述應用程式內購買功能的圖示使用。 此映射必須不同于應用程式圖示，而且不能是螢幕擷取畫面。
- **名稱**–此名稱最多隻能有30個字元。
- **描述**–描述最多隻能有45個字元。

任何應用程式內購買升級都受限於 app store 審查後，才能加以發行。

若要讓您的應用程式內購買可供升級，請開啟您的應用程式，並流覽至 [**功能] > 應用程式內購買**。 前往**App Store 升級（選擇性）** 區段並新增 1024 x 1024 映射並加以**儲存**，如下圖所示：

![ITune Connect 中的 App Store 升級區段](app-store-changes-images/image4.png)

您也需要將`ShouldAddStorePayment`方法新增至應用程式中的`SKPaymentTransactionObserver`通訊協定。

如需有關應用程式內購買升級的詳細資訊，請參閱 Apple 的[推廣您的應用程式內購買](https://developer.apple.com/app-store/promoting-in-app-purchases/)頁面。

## <a name="redesigned-product-page"></a>重新設計的產品頁面

[產品] 頁面已進行下列變更：

- 標題現在已設定為最多30個字元
- 已新增子標題
  - 它應該是補充標題的簡短摘要。
  - 子標題最多隻能有30個字元
- 應用程式預覽
  - 您現在可以有三個影片或螢幕擷取畫面。
  - 影片會在使用者造訪頁面時自動播放。
  - 如需詳細資訊，請參閱 Apple 的[應用程式預覽](https://developer.apple.com/app-store/app-previews/)頁面。
- 促銷文字
  - 這項新功能提供170個字元的文字，可讓您描述經常變更的應用程式資訊。
  - 它可以隨時更新，而不需要提交新版的應用程式。

## <a name="customer-communication"></a>客戶通訊

在10.3 中，Apple 推出了一種新方式，讓開發人員能夠透過回應評論的能力，直接與應用程式使用者進行通訊。 您可以流覽至**應用程式 > 活動 > 評等和評論**，以在 iTunes connect 中存取這項新功能，如下圖所示：

![顯示要輸入回復批註之區域的對話方塊](app-store-changes-images/image5.png)

當您回復使用者時，需要注意幾件事：

- 您只能回復一次，但雙方都可以視需要編輯其批註。
- 當您回應留言時，使用者會收到通知。
- 已在 iTunes connect 中建立新的客戶支援角色。 具有此角色或系統管理員角色的使用者可以回應留言。

如需詳細資訊，請參閱 Apple 的[回應評論](https://developer.apple.com/app-store/responding-to-reviews/)頁面。

<a name="Phased_Release"/>

## <a name="phased-release"></a>階段式發行

在 iOS 11 中，Apple 已針對您應用程式的更新執行階段式發行版本的選項。 您可以啟用分階段發行，讓您的應用程式更新逐漸發行給客戶，確保需求不會多載您的生產環境。

在 iTunes Connect 中啟用分階段發行。 在提要欄位中，按一下您的應用程式，並移至底部自動更新區段的**階段式發行**，然後選取 [**使用分階段發行的7天內發行更新**]：

![顯示自動更新之階段式發行的選項](app-store-changes-images/image6.png)

您的更新可以立即在 App Store 的 [更新] 索引標籤中下載。 階段式發行僅適用于已選取自動下載的使用者。

## <a name="related-links"></a>相關連結

- [IOS 11 （Apple）的新功能](https://developer.apple.com/ios/)
- [已更新 App Store 產品頁面（Apple）](https://developer.apple.com/app-store/product-page/)
- [更新 iOS 11 應用程式（WWDC）（影片）](https://developer.apple.com/videos/play/wwdc2017/204/)
