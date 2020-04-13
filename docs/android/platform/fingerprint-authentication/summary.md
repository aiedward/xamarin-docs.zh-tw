---
title: 指紋認證指南
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e955d4f96724bd5682e7d0e6db2c36fa1b7810f4
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027424"
---
# <a name="fingerprint-authentication-guidance"></a>指紋認證指南

## <a name="fingerprint-authentication-guidance"></a>指紋認證指南

現在,我們已經看到了圍繞Android 6.0指紋身份驗證的概念和API,讓我們討論一些使用指紋API的一般建議。

1. **使用 Android 支援庫 v4 相容性 API**&ndash;這將透過從代碼中刪除 API 檢查來簡化應用程式代碼,並允許應用程式盡可能針對大多數設備。
2. **提供指紋身份驗證**&ndash;指紋身份驗證的替代方法是應用程式對用戶進行身份驗證的一種很好、快速的方法,但是,不能假定它始終工作或可用。 指紋掃描器可能出現故障,鏡頭可能髒污,使用者可能沒有將設備配置為使用指紋身份驗證,或者指紋從此丟失。 使用者也可能不希望對應用程式使用指紋身份驗證。 出於這些原因,Android 應用程式應提供替代身份驗證過程,如使用者名和密碼。
3. **使用谷歌的指紋圖示**&ndash;所有應用程式都應使用谷歌提供的相同指紋圖示。 使用標準圖示使 Android 使用者能夠輕鬆辨識應用中指紋身份驗證的使用位置: 
    
    ![安卓指紋圖示](summary-images/ic-fp-40px.png)
    
4. **通知用戶**&ndash;應用程式應向使用者顯示某種通知,通知指紋掃描器處於活動狀態並等待觸摸或輕掃。 

## <a name="summary"></a>總結

指紋身份驗證是允許 Xamarin.Android 應用程式快速驗證使用者的好方法,使用戶能夠更輕鬆地與敏感功能(如應用內購買)進行交互。 本指南討論了在 Xamarin.Android 應用程式中集成 Android 6.0 指紋 API 所需的概念和代碼。

首先,我們討論了指紋 API`FingerprintManager`本身`FingerprintManagerCompat`( 和 )。 我們研究了如何由`FingerprintManager.AuthenticationCallbacks`應用程式擴展抽象類,並將其用作指紋硬體和應用程式本身之間的仲介。 然後,我們檢查了如何使用 JAVA`Cipher`物件驗證指紋掃描結果的完整性。 最後,我們通過描述如何在設備上註冊指紋並使用**adb**模擬模擬器上的指紋輕掃來嘗試測試。 

如果尚未這樣做,則應查看本指南附帶[的範例應用程式](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)。 [指紋對話方塊示例](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)已從 JAVA 移植到 Xamarin.Android,並提供了有關如何向 Android 應用程式添加指紋身份驗證的另一個範例。

## <a name="related-links"></a>相關連結

- [指紋指南範例應用程式](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [指紋對話框範例](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [指紋圖示](https://raw.githubusercontent.com/xamarin/monodroid-samples/master/FingerprintGuide/FingerprintSampleApp/Resources/drawable-hdpi/ic_fp_40px.png)
