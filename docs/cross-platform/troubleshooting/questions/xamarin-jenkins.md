---
title: 為什麼 Microsoft 不支援 Jenkins？
description: 本檔以高階方式說明 Xamarin 與 Jenkins CI 系統的互動。 它也會討論使用 Jenkins 時所出現的一些常見問題。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9951F980-2C6C-47C0-8A35-A78F06C20BEB
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 7be60eaa1135284522ef1e6ce81c911a68ff9915
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73012220"
---
# <a name="why-isnt-jenkins-supported-by-microsoft"></a>為什麼 Microsoft 不支援 Jenkins？

## <a name="jenkins-support-explanation"></a>Jenkins 支援說明

Jenkins 是開放原始碼 CI 套件;因為這許多由 Jenkins*本身*直接造成的問題，都必須針對您的程式碼所在位置提出問題。例如[主要 Jenkins](https://github.com/jenkinsci/jenkins)存放庫，或[Jenkins 應用程式](https://github.com/stisti/jenkins-app)的存放庫。

例外狀況是針對可隔離到 Xamarin 工具中特定 bug 的問題;如果您懷疑這是這種情況，您可以檢查您的[支援選項](~/cross-platform/troubleshooting/support-options.md)，不過問題可能是 Xamarin 支援小組無法*直接*協助的東西。

## <a name="setup-jenkins-with-xamarin"></a>使用 Xamarin 設定 Jenkins

如先前所述，我們的小組不直接支援 Jenkins 問題，搭配[使用 Jenkins 與 xamarin](~/tools/ci/jenkins-walkthrough.md)指南，可以用來設定與 xamarin 整合的 Jenkins CI 伺服器。 

## <a name="fixes-for-common-issues"></a>常見問題的修正

### <a name="jenkins-is-unable-to-find-the-android-sdk"></a>Jenkins 找不到 Android SDK

此問題的錯誤訊息如下所示：

> 錯誤 XA5205：找不到 Android SDK 目錄。 請透過/p： AndroidSdkDirectory 設定

您用來設定 SDK 位置的選項可能會根據您所使用的確切 Jenkins Android 外掛程式而有所不同;您可以在外掛程式指南中找到如何設定此項的最佳位置。 例如，[Android Emulator 外掛程式](https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin#AndroidEmulatorPlugin-Systemconfiguration)會自動尋找 SDK，但如果找不到，則為;您也可以透過該外掛程式的 [Jenkins 系統組態] 頁面來設定位置。 

## <a name="deprecated-errors"></a>已淘汰的錯誤

> [!IMPORTANT]
> 此問題已在最新版本的 Xamarin 中解決。 不過，如果軟體的最新版本發生問題，請使用完整版本設定資訊和完整組建記錄檔輸出來提出[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。

### <a name="jenkins-reports-an-invalid-xamarin-license"></a>Jenkins 報告不正確 Xamarin 授權
此問題的錯誤訊息通常類似于

> XA9008 錯誤：從命令列建立需要商務授權

或

> 錯誤： Xamarin 的入門版不支援在 Xamarin Studio 外部建立 

此案例最常見的原因是使用 Jenkins，方法是以與 Xamarin 授權沒有關聯的使用者帳戶登入。 解決此情況最簡單的方式，就是直接透過使用者帳戶將 Jenkins 安裝為應用程式。 該程式和一些其他考慮如下所述： [https://forums.xamarin.com/discussion/comment/99397/#Comment_99397](https://forums.xamarin.com/discussion/comment/99397/#Comment_99397)
