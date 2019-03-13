---
title: 指紋驗證指引
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 535eabe07cb4f4d36e6a6f918b5717efcc99185d
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898820"
---
# <a name="fingerprint-authentication-guidance"></a>指紋驗證指引

## <a name="fingerprint-authentication-guidance"></a>指紋驗證指引

既然我們已經看到的概念和 Api 周圍 Android 6.0 指紋驗證，讓我們討論使用指紋辨識 Api 的一些一般建議。

1. **使用 Android 支援程式庫 v4 相容性 Api** &ndash;這將會從程式碼中移除 API 檢查，以簡化應用程式程式碼，並允許應用程式為目標的最可能的裝置。
2. **提供指紋驗證的替代方案**&ndash;指紋驗證是很棒、 快速的方法，讓應用程式驗證使用者，不過，它無法假設它將一律運作或可用。 它可能是指紋掃描器可能會失敗、 lens 可能會中途，使用者可能未設定成使用指紋驗證，裝置或指紋已因為遺失。 此外，也可以使用者可能不希望您的應用程式中使用指紋驗證。 基於這些理由，Android 應用程式應該提供替代的驗證處理程序，例如使用者名稱和密碼。
3. **使用 Google 的指紋圖示**&ndash;所有應用程式應該使用由 Google 提供的相同指紋圖示。 使用標準的圖示，輕鬆辨識使用指紋驗證應用程式的位置的 Android 使用者： 
    
    ![Android 指紋圖示](summary-images/ic-fp-40px.png)
    
4. **通知使用者**&ndash;應用程式應該會顯示某種類型的使用者的通知指紋掃描器是作用中和等待觸控或撥動。 

## <a name="summary"></a>總結

指紋驗證是適合用來允許快速確認使用者使用，讓使用者更輕鬆地互動敏感功能，例如應用程式內購買的 Xamarin.Android 應用程式。 本指南會討論的概念和才能併入 Android 6.0 指紋 API 的 Xamarin.Android 應用程式中的程式碼。

我們先討論 API 的本身，指紋`FingerprintManager`(和`FingerprintManagerCompat`)。 我們檢查如何`FingerprintManager.AuthenticationCallbacks`抽象類別必須擴充應用程式，並做為指紋硬體與應用程式本身之間的媒介。 然後，我們檢查如何使用 Java 將指紋掃描器結果的完整性`Cipher`物件。 最後，我們接觸到有點上測試說明如何註冊裝置上的指紋，並使用**adb**來模擬在模擬器上的指紋撥動。 

如果您尚未這麼做，您應該查看[範例應用程式](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)隨附本指南。 [指紋對話方塊範例](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)已經從 Java 移植到 Xamarin.Android，並提供有關如何將指紋驗證新增至 Android 應用程式的另一個範例。



## <a name="related-links"></a>相關連結

- [指紋指南範例應用程式](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [指紋對話方塊範例](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [指紋圖示](https://raw.githubusercontent.com/xamarin/monodroid-samples/master/FingerprintGuide/FingerprintSampleApp/Resources/drawable-hdpi/ic_fp_40px.png)
