---
title: 指紋驗證指引
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e955d4f96724bd5682e7d0e6db2c36fa1b7810f4
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027424"
---
# <a name="fingerprint-authentication-guidance"></a>指紋驗證指引

## <a name="fingerprint-authentication-guidance"></a>指紋驗證指引

既然我們已瞭解關於 Android 6.0 指紋驗證的概念和 Api，讓我們來討論一些使用指紋 Api 的一般建議。

1. **使用 Android 支援程式庫 V4 相容性 api** &ndash; 這會從程式碼移除 API 檢查，並允許應用程式以最多可能的裝置為目標，藉此簡化應用程式程式碼。
2. **提供指紋驗證的替代方案**&ndash; 指紋驗證是讓應用程式驗證使用者的絕佳快速方法，不過，它無法假設它一律可以使用或可供使用。 指紋掃描器可能會失敗、鏡頭可能已變更、使用者可能未設定裝置使用指紋驗證，或指紋已遺失的情況。 使用者也可能不想要對您的應用程式使用指紋驗證。 基於這些理由，Android 應用程式應該提供替代的驗證流程，例如使用者名稱和密碼。
3. **使用 google 的指紋圖示**&ndash; 所有應用程式都應該使用 google 所提供的相同指紋圖示。 使用標準圖示可讓 Android 使用者輕鬆辨識應用程式指紋驗證的使用位置： 
    
    ![Android 指紋圖示](summary-images/ic-fp-40px.png)
    
4. **通知使用者**&ndash; 應用程式應該會向使用者顯示指紋掃描器作用中並等候觸控或滑動的通知。 

## <a name="summary"></a>摘要

指紋驗證是讓 Xamarin Android 應用程式快速驗證使用者的絕佳方式，可讓使用者更輕鬆地與機密功能互動，例如應用程式內購買。 本指南討論在您的 Xamarin. Android 應用程式中納入 Android 6.0 指紋 API 所需的概念和程式碼。

首先，我們討論了指紋 API 本身，`FingerprintManager` （和 `FingerprintManagerCompat`）。 我們檢查了 `FingerprintManager.AuthenticationCallbacks` 抽象類別如何由應用程式延伸，並在指紋硬體和應用程式本身之間做為媒介。 然後我們會探討如何使用 JAVA `Cipher` 物件來確認指紋掃描器結果的完整性。 最後，我們藉由說明如何在裝置上註冊指紋，以及如何使用**adb**在模擬器上模擬指紋，來觸及測試。 

如果您尚未這麼做，請參閱本指南隨附的[範例應用程式](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)。 [指紋對話方塊範例](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)已從 JAVA 移植到 Xamarin，並提供另一個範例，說明如何將指紋驗證新增至 android 應用程式。

## <a name="related-links"></a>相關連結

- [指紋指南範例應用程式](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [指紋對話方塊範例](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [指紋圖示](https://raw.githubusercontent.com/xamarin/monodroid-samples/master/FingerprintGuide/FingerprintSampleApp/Resources/drawable-hdpi/ic_fp_40px.png)
