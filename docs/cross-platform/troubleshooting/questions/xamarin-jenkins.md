---
title: 為什麼不支援 Jenkins microsoft？
description: 本文件說明，在高的層級，與 Jenkins CI 系統的 Xamarin 的互動。 它也會討論一些常見的問題時使用 Jenkins 出現。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9951F980-2C6C-47C0-8A35-A78F06C20BEB
author: asb3993
ms.author: amburns
ms.date: 06/05/2018
ms.openlocfilehash: c2e409b796d5ef2525079e02aafdd0c6e8db5d81
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61158932"
---
# <a name="why-isnt-jenkins-supported-by-microsoft"></a>為什麼不支援 Jenkins microsoft？

## <a name="jenkins-support-explanation"></a>Jenkins 支援說明

Jenkins 是開放原始碼 CI 套件;因為這麼多直接由 Jenkins 所造成的問題*本身*需要被歸類為問題對您取得程式碼的位置; 例如[主要 Jenkins 儲存機制](https://github.com/jenkinsci/jenkins)，或存放庫[Jenkins.app](https://github.com/stisti/jenkins-app)。

此例外的是可隔離至 Xamarin 的工具; 中的特定錯誤的問題如果懷疑此情況下您可以檢查您[支援選項](~/cross-platform/troubleshooting/support-options.md)，不過同樣地，問題可能是項目外部的 Xamarin 支援小組可以*直接*協助。

## <a name="setup-jenkins-with-xamarin"></a>設定 Jenkins 搭配 Xamarin

雖然直接由我們的小組，如先前所述不支援 Jenkins 問題[使用的 Jenkins 搭配 Xamarin](~/tools/ci/jenkins-walkthrough.md)指南可以用來設定與 Xamarin 整合的 Jenkins CI 伺服器。 

## <a name="fixes-for-common-issues"></a>修正常見的問題

### <a name="jenkins-is-unable-to-find-the-android-sdk"></a>Jenkins 是找不到 Android SDK

此問題的錯誤訊息會是如下：

> 錯誤 XA5205:找不到 Android SDK 目錄。 Please set via /p:AndroidSdkDirectory

設定 SDK 位置的選項而有所不同的確切的 Jenkins Android 外掛程式，您使用;在外掛程式指南中，是要尋找如何設定此的好地方。 例如，[Android 模擬器外掛程式](https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin#AndroidEmulatorPlugin-Systemconfiguration)會自動尋找 SDK，但如果它找不到它，也可以透過該外掛程式的 Jenkins 系統組態 頁面設定的位置。 


## <a name="deprecated-errors"></a>已被取代的錯誤

> [!IMPORTANT]
> 這個問題已經解決最新版 Xamarin。 不過，如果最新版本的軟體，就會發生問題，請指導，申請[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)您完整的版本控制資訊和完整建置記錄檔輸出。



### <a name="jenkins-reports-an-invalid-xamarin-license"></a>Jenkins 會報告無效的 Xamarin 授權
此問題的錯誤訊息通常是類似的內容

> XA9008 錯誤：從命令列建置需要企業授權

或

> 錯誤：Starter Edition 的 Xamarin.iOS 不支援建置 Xamarin Studio 之外 

此案例的最常見的原因是未與您的 Xamarin 授權相關聯的使用者帳戶登入 Jenkins 使用。 最簡單的方式解析這，是為應用程式的使用者帳戶透過直接安裝 Jenkins。 該處理程序和一些其他考量如下所示： [https://forums.xamarin.com/discussion/comment/99397/#Comment_99397](https://forums.xamarin.com/discussion/comment/99397/#Comment_99397)
