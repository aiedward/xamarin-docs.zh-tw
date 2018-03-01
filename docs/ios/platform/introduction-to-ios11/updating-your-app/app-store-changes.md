---
title: "變更應用程式市集"
description: "瀏覽 iOS 11 的新功能"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4A7A03FD-B4F2-4969-8676-A17260730FD6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: d75c1393f2b5701226433235010a41da9c1aeb03
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="app-store-changes"></a>變更應用程式市集

_瀏覽 iOS 11 的新功能_

IOS App Store 已完全重新設計，這不只可讓使用者有效率地巡覽存放區，但也可讓您，身為開發人員，以升級您的應用程式的使用者。 這些升級包含在應用程式內購買的更新和更新的產品頁面。 iOS 11 也會加入有關如何與使用者通訊、 如何新增您的應用程式圖示和如何發行應用程式公開的更新。

![新的應用程式存放區版面配置](app-store-changes-images/image3.jpg)

重新設計應用程式存放區包含下列各節：

- **現今**– 此索引標籤包含 [編輯器] 的選擇 」 或精選應用程式的應用程式。 若要升級您應用程式填入資訊上[升級](https://developer.apple.com//contact/app-store/promote/)頁面。
- **遊戲**– 設定為任何應用程式**遊戲**類別，在 iTunes Connect 中的可以找到此索引標籤底下。
- **應用程式**– 此索引標籤功能的其他所有應用程式。 使用者可以瀏覽熱門應用程式、 類別、 最上層的付費/釋放。
- **更新**– 此應用程式顯示應用程式的更新。 即使您發行應用程式透過[階段發行](#Phased_Release)，使用者仍可以前往此索引標籤及下載最新版本。
- **搜尋**– 此索引標籤可讓使用者搜尋應用程式。

## <a name="store-icon"></a>存放區圖示

市集圖示 （或行銷圖示） 無法再管理在 iTunes Connect，並改為必須包含做為[資產目錄](~/ios/app-fundamentals/images-icons/app-icons.md)在您的應用程式的二進位檔，類似於應用程式圖示。 1024 x 1024 存放區中的圖示 PNG 格式必須包含資產目錄中，為 iOS 11 應用程式的成功送出。

細微的應用程式可確保此額外的資產目錄不會增加應用程式大小。


## <a name="in-app-purchases-promoted-in-the-app-store"></a>升級在 windows 市集應用程式內購買

Apple 已在應用程式內購買更容易找到應用程式存放區中。 您現在可以新增最多 20 個_升級的 App Store_現在可以在應用程式頁面上，在您的應用程式產品頁面上，或藉由搜尋找到的應用程式內購買。

具有應用程式存放區中顯示您應用程式內購買項目必須包含下列資料：

- **映像**– 您必須提供特別設計的映像描述應用程式內購買的用途的圖示。 此映像必須是不同的應用程式圖示，而且不能螢幕擷取畫面。
- **名稱**– 名稱只能是最多 30 個字元。
- **描述**– 描述只可以有最大值為 45 個字元。

任何應用程式內購買升級受限於應用程式存放區檢閱才可以將它發行。

若要讓您可將升級的應用程式內採購，開啟您的應用程式，並瀏覽至**功能 > 應用程式內購買**。 移至**應用程式存放區升級 （選用）**區段，並新增 1024 x 1024 映像和**儲存**，如下列影像所示：

![ITune 連線升級應用程式市集 > 一節](app-store-changes-images/image4.png)

您也需要加入`ShouldAddStorePayment`方法`SKPaymentTransactionObserver`應用程式中的通訊協定。

如需有關在應用程式內購買升級的詳細資訊，請參閱 Apple[升級您的應用程式內購買](https://developer.apple.com/app-store/promoting-in-app-purchases/)頁面。

## <a name="redesigned-product-page"></a>重新設計的產品頁面

具有產品頁面已進行下列變更：

- 標題現在都設定為 30 個字元的最大值
- 已加入子標題
    - 它應該是可補充標題的簡短摘要。
    - 副標題應該有 30 個字元的最大值
- 應用程式預覽
    - 您現在可以有三個影片或螢幕擷取畫面。
    - 當使用者瀏覽網頁時，自動視訊播放。
    - 如需詳細資訊，請參閱 Apple[應用程式預覽](https://developer.apple.com/app-store/app-previews/)頁面。
- 促銷的文字
    - 這項新功能提供了 170 個字元的文字，可讓您描述有關您的應用程式經常變更的資訊。
    - 它可以更新在任何時間而不需要提交新版本的應用程式。

## <a name="customer-communication"></a>客戶通訊

10.3，在 Apple 會啟動新的方法讓開發人員直接通訊的應用程式的使用者可透過回應程式檢閱的能力。 您可以存取這項新功能 iTunes 中的瀏覽至連接**應用程式 > 活動 > 評分和評論**，如下列影像所示：

![顯示區域來輸入回覆註解對話方塊](app-store-changes-images/image5.png)

有幾件事要注意的回覆給使用者時：

- 您可以只回應一次，但就像他們想要這兩個合作對象可以編輯他們的註解。
- 當您存回註解時，使用者會收到通知。
- 連接新的客戶支援，在 iTunes 中已建立角色。 註解可以回應使用者與此角色或系統管理員角色。

如需詳細資訊，請參閱 Apple[回應檢閱](https://developer.apple.com/app-store/responding-to-reviews/)頁面。


## <a name="phased-release"></a>分階段的版本

使用 iOS 11，Apple 已實作分階段的版本更新，更新您的應用程式的選項。 您可以啟用逐漸釋出以確保要求沒有多載您的生產環境的客戶，您的應用程式的更新，以便分階段的版本。

在 iTunes Connect 中啟用分階段的版本。 按一下 應用程式中的 資訊看板 上，捲動到**階段自動更新的版本**底部區段，然後選取**7 天期間使用的版本更新階段發行**:

![選項顯示階段自動更新的版本](app-store-changes-images/image6.png)

立即使用應用程式存放區的 [更新] 索引標籤以下載您的更新。 分階段的版本才可自動下載選取的使用者。


## <a name="related-links"></a>相關連結

- [什麼是 iOS (Apple) 11 中的新功能](https://developer.apple.com/ios/)
- [更新應用程式市集產品網頁 (Apple)](https://developer.apple.com/app-store/product-page/)
- [正在更新您的應用程式適用於 iOS 11 (WWDC) （影片）](https://developer.apple.com/videos/play/wwdc2017/204/)
