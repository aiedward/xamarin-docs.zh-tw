---
title: 為什麼不 Jenkins 支援 Xamarin？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9951F980-2C6C-47C0-8A35-A78F06C20BEB
author: asb3993
ms.author: amburns
ms.openlocfilehash: 37fc134f7e97af74f5bb019f3262972273f0c4cf
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="why-isnt-jenkins-supported-by-xamarin"></a>為什麼不 Jenkins 支援 Xamarin？

## <a name="jenkins-support-explanation"></a>Jenkins 支援說明

Jenkins 是一個開放原始碼 CI 組合。因為這許多直接 Jenkins 所造成的問題，所以*本身*需要被歸類為針對您從哪裡取得程式碼; 這類問題[主要 Jenkins 儲存機制](https://github.com/jenkinsci/jenkins)，或為儲存機制[Jenkins.app](https://github.com/stisti/jenkins-app)。

此例外的是隔離到 Xamarin 的工具; 中的特定錯誤的問題如果您懷疑這種情況您可以檢查您[支援選項](~/cross-platform/troubleshooting/support-options.md)，不過同樣地，則問題可能是項目之外的 Xamarin 支援小組可以*直接*幫助。

## <a name="setup-jenkins-with-xamarin"></a>使用 Xamarin 的安裝程式 Jenkins

雖然如上所述 Jenkins 問題不直接支援我們的團隊。[使用 Jenkins xamarin](~/tools/ci/jenkins-walkthrough.md)指南可以用來設定整合 Xamarin 與 Jenkins CI 伺服器。 

## <a name="fixes-for-common-issues"></a>修正常見的問題
### <a name="jenkins-is-unable-to-find-the-android-sdk"></a>Jenkins 是找不到 Android SDK

此問題的錯誤訊息會像下面這樣：

> 錯誤 XA5205： 找不到的 Android SDK 目錄。 請透過 /p:AndroidSdkDirectory 設定

設定 SDK 位置的選項會視您所使用; 確切 Jenkins Android 外掛程式若要尋找如何設定這最好外掛程式指南中。 例如，[Android 模擬器外掛程式](https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin#AndroidEmulatorPlugin-Systemconfiguration)會自動尋找 SDK，但是，如果找不到它，也可以透過該外掛程式的 Jenkins 系統設定頁面設定的位置。 


## <a name="deprecated-errors"></a>已被取代的錯誤

> [!IMPORTANT]
> 這個問題已經解決 Xamarin 的最新版的。 但是，若發生此問題，軟體的最新版本，請檔案[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)您完整的版本資訊和完整建置記錄檔輸出。



### <a name="jenkins-reports-an-invalid-xamarin-license"></a>Jenkins 報告無效的 Xamarin 授權
此問題的錯誤訊息通常會類似

> XA9008 錯誤： 從命令列建置需要 business 授權

或

> 錯誤： Xamarin.iOS Starter Edition 不支援建置 Xamarin Studio 外部 

此案例的最常見的原因是沒有與您的 Xamarin 授權相關聯的使用者帳戶登入的 Jenkins 使用。 解決這項目，最簡單的方式是安裝 Jenkins 成直接透過使用者帳戶的應用程式。 該處理程序和一些其他考量如下所示： [https://forums.xamarin.com/discussion/comment/99397/#Comment_99397](https://forums.xamarin.com/discussion/comment/99397/#Comment_99397)
