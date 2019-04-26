---
title: IOS 11 中的 app Store 變更
description: 這份文件會探索變更至 App Store 中 iOS 11。 它討論的應用程式市集圖示、 升級的應用程式內購買項目、 重新設計的產品頁面、 客戶的通訊，以及分階段的版本。
ms.prod: xamarin
ms.assetid: 4A7A03FD-B4F2-4969-8676-A17260730FD6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: 022d6b5c3f85863352dd1343752e934240b357aa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61229598"
---
# <a name="app-store-changes-in-ios-11"></a>IOS 11 中的 app Store 變更

IOS App Store 已完全重新設計，不僅可讓使用者快速地瀏覽市集，但也可讓您的身為開發人員，以升級您的應用程式給使用者。 這些促銷活動包含在應用程式內購買的更新和更新的產品頁面。 iOS 11 也會加入有關如何與使用者通訊、 如何加入您的應用程式圖示及如何應用程式發行至公用的更新。

![新的應用程式儲存區配置](app-store-changes-images/image3.jpg)

重新設計的應用程式市集具有下列各節：

- **立即**– 此索引標籤包含 「 編者挑選 」 或精選應用程式的應用程式。 若要提升您應用程式填入資訊上[升階](https://developer.apple.com//contact/app-store/promote/)頁面。
- **遊戲**– 設定為任何應用程式**遊戲**在 iTunes Connect 中的類別可以找到這個索引標籤底下。
- **應用程式**– 此索引標籤功能的其他所有應用程式。 使用者可以瀏覽精選應用程式、 類別、 最上層的付費/free。
- **更新**– 此應用程式會顯示您的應用程式的更新。 即使您發行應用程式透過[淘汰發行](#Phased_Release)，使用者仍然可以移至這個索引標籤，並下載最新版本。
- **搜尋**– 此索引標籤可讓使用者搜尋應用程式。

## <a name="store-icon"></a>市集圖示

市集圖示 （或行銷圖示） 不再在 iTunes Connect 中管理，而是必須做為包含[資產目錄](~/ios/app-fundamentals/images-icons/app-icons.md)中您應用程式的二進位檔，類似於應用程式圖示。 1024 x 1024 市集圖示以 PNG 格式必須包含在 iOS 11 應用程式的成功送出的資產目錄。

細微的應用程式可確保此額外的資產目錄並不會增加應用程式大小就行了。


## <a name="in-app-purchases-promoted-in-the-app-store"></a>升級在 windows 市集的應用程式內購買

Apple 已在進行應用程式內購買更容易找到的應用程式存放區中。 您現在可以新增最多 20_升級的應用程式市集_現在可以在應用程式頁面上，在您的應用程式產品頁面上，或藉由搜尋找到的應用程式內購買。

若要讓您應用程式存放區中顯示的應用程式內購買您必須包含下列資料：

- **映像**– 您必須提供的特殊設計的映像描述應用程式內購買的作用的圖示。 此映像必須是不同的應用程式圖示，而且不能是螢幕擷取畫面。
- **名稱**– 名稱只能是最多 30 個字元。
- **描述**– 描述都只能最多 45 個。

任何應用程式內購買升級受限於應用程式市集審查之前可以將它發行。

若要讓您可用來升級的應用程式內購買，請開啟您的應用程式，並瀏覽至**功能 > 應用程式內購買**。 移至**應用程式市集促銷活動 （選擇性）** 區段，並新增為 1024 x 1024 影像並**儲存**，如下圖所示：

![要連接的應用程式市集促銷節](app-store-changes-images/image4.png)

您也需要新增`ShouldAddStorePayment`方法，以`SKPaymentTransactionObserver`應用程式中的通訊協定。

如需有關在應用程式內購買升級的詳細資訊，請參閱 Apple[升級您的應用程式內購買](https://developer.apple.com/app-store/promoting-in-app-purchases/)頁面。

## <a name="redesigned-product-page"></a>重新設計的產品頁面

下列變更進行了 [產品] 頁面：

- 項目現在都設定為最多 30 個字元
- 已加入子標題
    - 它應該是補充標題的簡短摘要。
    - 子標題應該具有最多 30 個字元
- 應用程式預覽
    - 您現在可以有三個影片或螢幕擷取畫面。
    - 當使用者瀏覽頁面時，自動影片播放。
    - 如需詳細資訊，請參閱 Apple[應用程式預覽](https://developer.apple.com/app-store/app-previews/)頁面。
- 促銷的文字
    - 這項新功能提供 170 個字元的文字，可讓您描述有關您的應用程式經常變更的資訊。
    - 您可以更新隨時都不必提交應用程式的新版本。

## <a name="customer-communication"></a>客戶的通訊

在 10.3，Apple 會啟動新的方法讓開發人員直接通訊的應用程式的使用者可透過檢閱回應能力。 您可以存取這項新功能在 iTunes 中的瀏覽至連線**應用程式 > 活動 > 評等和評論**，如下圖所示：

![顯示區域來輸入回覆註解對話方塊](app-store-changes-images/image5.png)

有幾件事要注意的回覆給使用者時：

- 您可以只回應一次，但這兩個合作對象可以編輯其註解，就像他們想。
- 當您回覆註解，使用者會收到通知。
- 新的客戶支援，角色已在 iTunes 中建立連線。 具有此角色或系統管理員角色的使用者可以回應註解。

如需詳細資訊，請參閱 Apple[回應檢閱](https://developer.apple.com/app-store/responding-to-reviews/)頁面。

<a name="Phased_Release"/>

## <a name="phased-release"></a>分階段的版本

Ios 11，Apple 已實作的分階段的版本更新，更新您的應用程式的選項。 您可以啟用分階段可讓您逐漸發行給客戶，確保要求未多載您的生產環境的應用程式更新的版本。

階段式的版本會在 iTunes Connect 中啟用。 按一下 資訊看板中的應用程式上，捲動到**階段自動更新的版本**底部區段，然後選取**版本更新，比起 7 天期間使用階段式版本**:

![選項顯示階段自動更新的版本](app-store-changes-images/image6.png)

您更新已可立即供下載 App Store 中的 [更新] 索引標籤。 分階段的版本僅適用於具有自動下載選取的使用者。


## <a name="related-links"></a>相關連結

- [新功能 iOS 11 (Apple)](https://developer.apple.com/ios/)
- [更新應用程式市集產品頁面 (Apple)](https://developer.apple.com/app-store/product-page/)
- [更新您的應用程式，適用於 iOS 11 (WWDC) （影片）](https://developer.apple.com/videos/play/wwdc2017/204/)
