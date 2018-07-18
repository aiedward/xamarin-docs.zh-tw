---
title: 指紋驗證指引
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 2b66c3660f6d8af9217089a7615784957fcc6ed7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763302"
---
# <a name="fingerprint-authentication-guidance"></a>指紋驗證指引

## <a name="fingerprint-authentication-guidance"></a>指紋驗證指引

既然我們已經看過的概念，且周圍 Android 6.0 Api 指紋驗證，讓我們來討論指紋應用程式開發介面使用的一些一般建議。

1. **使用 Android 支援程式庫 v4 相容性 Api** &ndash;這將會從程式碼中移除應用程式開發介面核取以簡化應用程式程式碼，並允許應用程式為目標的最可能的裝置。
2. **提供指紋驗證的替代方案**&ndash;指紋驗證是絕佳且快速的方法來驗證使用者的應用程式，不過，它無法假設它會一律運作或可供使用。 它可能是指紋掃描器可能會失敗，透鏡可能是已被變更，使用者可能未設定為使用指紋驗證裝置或指紋，因為已經遺失。 此外，也可以使用者可能不想要使用指紋驗證您的應用程式。 基於這些理由，Android 應用程式應該提供替代的驗證處理程序，例如使用者名稱和密碼。
3. **使用 Google 指紋圖示**&ndash;所有應用程式應該使用由 Google 提供的相同指紋圖示。 使用標準的圖示，輕鬆 Android 使用者辨識使用指紋驗證應用程式的位置： 
    
    ![Android 指紋圖示](summary-images/ic-fp-40px.png)
    
4. **通知使用者**&ndash;應用程式應該會顯示使用者的通知指紋掃描器是作用中的某種和等候觸控或撥動。 

## <a name="summary"></a>總結

指紋驗證是以允許快速地驗證使用者，讓使用者更輕鬆地互動敏感功能，例如應用程式內購買 Xamarin.Android 應用程式的好方法。 本指南所討論的概念和程式碼所需併入 Android 6.0 指紋 Xamarin.Android 應用程式中的 API。

首先我們討論過的 API 本身，指紋`FingerprintManager`(和`FingerprintManagerCompat`)。 我們檢驗如何`FingerprintManager.AuthenticationCallbacks`抽象類別必須擴充應用程式，並做為指紋硬體與應用程式本身之間的媒介。 然後我們檢驗如何使用 Java 的指紋掃描器結果的完整性`Cipher`物件。 最後，我們接觸到的位元測試，說明如何註冊裝置上的指紋，並使用**adb**來模擬在模擬器上的指紋撥動。 

如果您尚未這麼做，您應該查看[範例應用程式](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)，伴隨著本指南。 [指紋對話方塊範例](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)已從 Java 移植到 Xamarin.Android 及提供有關如何將指紋驗證新增至 Android 應用程式的另一個範例。



## <a name="related-links"></a>相關連結

- [指紋指南範例應用程式](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [指紋對話方塊範例](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [指紋圖示](https://developer.android.comhttps://developer.xamarin.com/samples/FingerprintDialog/res/drawable-hdpi/ic_fp_40px.html)
