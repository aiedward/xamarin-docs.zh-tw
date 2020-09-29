---
title: 指紋驗證指導方針
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: aaa67972afeffd10c038a145a5703e917647b0fb
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453866"
---
# <a name="fingerprint-authentication-guidance"></a>指紋驗證指導方針

## <a name="fingerprint-authentication-guidance"></a>指紋驗證指導方針

既然我們已瞭解 Android 6.0 指紋驗證的概念和 Api，讓我們來討論一些使用指紋 Api 的一般建議。

1. **使用 Android 支援程式庫 V4 相容性 api** &ndash; 這會從程式碼移除 API 檢查，並允許應用程式以最可能的裝置為目標，以簡化應用程式程式碼。
2. **提供指紋驗證** &ndash; 的替代方案指紋驗證是讓應用程式驗證使用者的絕佳快速方法，不過，它無法假設它一律可以運作或可供使用。 指紋掃描器可能會失敗、鏡頭可能已中途損壞、使用者可能尚未設定裝置使用指紋驗證，或指紋已遺失。 使用者也可能不會想要對您的應用程式使用指紋驗證。 基於這些理由，Android 應用程式應該提供替代的驗證程式，例如使用者名稱和密碼。
3. **使用 Google 的指紋圖示** &ndash; 所有應用程式都應該使用 Google 提供的相同指紋圖示。 使用標準圖示可讓 Android 使用者輕鬆辨識應用程式指紋驗證的使用位置： 
    
    ![Android 指紋圖示](summary-images/ic-fp-40px.png)
    
4. **通知使用者** &ndash; 應用程式應該會顯示一種通知給使用者，指出指紋掃描器正在使用中，並等候觸控或滑動。 

## <a name="summary"></a>摘要

指紋驗證是讓 Xamarin Android 應用程式快速驗證使用者的絕佳方法，可讓使用者更輕鬆地與應用程式內購買等敏感性功能互動。 本指南討論在您的 Xamarin Android 應用程式中併入 Android 6.0 指紋 API 所需的概念和程式碼。

首先，我們討論了指紋 API 本身、 `FingerprintManager` (和 `FingerprintManagerCompat`) 。 我們檢查了 `FingerprintManager.AuthenticationCallbacks` 抽象類別如何由應用程式擴充，並用作指紋硬體與應用程式本身之間的媒介。 接著，我們會探討如何使用 JAVA 物件來確認指紋掃描器結果的完整性 `Cipher` 。 最後，我們會說明如何在裝置上註冊指紋，以及如何使用 **adb** 來模擬模擬器上的指紋，藉以稍微接觸測試。 

如果您尚未這麼做，您應該查看本指南隨附的 [範例應用程式](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide) 。 [指紋對話方塊範例](/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)已從 JAVA 移植到 Xamarin，並提供另一個範例，說明如何將指紋驗證新增至 android 應用程式。

## <a name="related-links"></a>相關連結

- [指紋指南範例應用程式](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [指紋對話方塊範例](/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [指紋圖示](https://raw.githubusercontent.com/xamarin/monodroid-samples/master/FingerprintGuide/FingerprintSampleApp/Resources/drawable-hdpi/ic_fp_40px.png)