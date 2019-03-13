---
title: 運用 watchOS 應用程式群組，在 Xamarin 中使用
description: 本文件說明應用程式群組及 watchOS 應用程式中的如何使用它們。 它討論如何設定應用程式群組，佈建需求、 Entitlements.plist 考量，以及部署。
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 6968606B-C287-424F-A321-2492E12BC0BB
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 78f6c03f73f0e4d8a74f826dd7bc25bbe325d545
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103669"
---
# <a name="working-with-watchos-app-groups-in-xamarin"></a>運用 watchOS 應用程式群組，在 Xamarin 中使用


「應用程式群組」可讓不同的應用程式 (或應用程式及其擴充功能) 存取共用檔案儲存體位置。 「應用程式群組」可用於資料下列資料：

- Apple Watch[設定](~/ios/watchos/app-fundamentals/settings.md)。
- 共用[NSUserDefaults](~/ios/watchos/app-fundamentals/parent-app.md#nsuserdefaults)。
- 共用[檔案](~/ios/watchos/app-fundamentals/parent-app.md#files)。

## <a name="configure-an-app-group"></a>設定應用程式群組

使用設定共用的位置[應用程式群組](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)，在已設定**憑證、 識別碼與設定檔**區段[iOS 開發人員中心](https://developer.apple.com/devcenter/ios/)。 此值也必須在每個專案的參考**Entitlements.plist**。

### <a name="provisioning"></a>佈建

應用程式群組會有識別碼，這通常是您的套件組合識別碼與`group.`前置詞。 比方說，我們可以使用 「 套件組合識別碼`com.xamarin.WatchSettings`和應用程式群組`group.com.xamarin.WatchSettings`。

[![](app-groups-images/app-group-sml.png "使用套件組合識別碼 com.xamarin.WatchSettings 和應用程式群組 group.com.xamarin.WatchSettings")](app-groups-images/app-group.png#lightbox)

### <a name="entitlementsplist"></a>Entitlements.plist

設定佈建設定檔，以及**啟用應用程式群組**中**Entitlements.plist**並輸入您所選擇的識別碼：

[![](app-groups-images/entitlements-sml.png "設定 plist 及輸入的識別碼")](app-groups-images/entitlements.png#lightbox)


### <a name="deployment"></a>部署

請確定您設定應用程式群組正確地在您[部署](~/ios/watchos/deploy-test/index.md#App_Groups)佈建。


如需詳細資訊，請參閱[應用程式群組功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)文件。


## <a name="related-links"></a>相關連結

- [Apple 的應用程式包含共用資料](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
- [Apple 的應用程式群組文件](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)
