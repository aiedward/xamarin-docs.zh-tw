---
title: 在 Xamarin 中使用 watchOS 應用程式群組
description: 本檔說明 watchOS 應用程式中的應用程式群組及其用法。 其中討論如何設定應用程式群組、布建需求、權利、plist 考慮和部署。
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 6968606B-C287-424F-A321-2492E12BC0BB
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: c75db8bd29b7a57c46610abdd5e4024938fc9e1b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70280335"
---
# <a name="working-with-watchos-app-groups-in-xamarin"></a>在 Xamarin 中使用 watchOS 應用程式群組


「應用程式群組」可讓不同的應用程式 (或應用程式及其擴充功能) 存取共用檔案儲存體位置。 「應用程式群組」可用於資料下列資料：

- Apple Watch[設定](~/ios/watchos/app-fundamentals/settings.md)。
- 共用[使用 nsuserdefaults](~/ios/watchos/app-fundamentals/parent-app.md#nsuserdefaults)。
- 共用[檔案](~/ios/watchos/app-fundamentals/parent-app.md#files)。

## <a name="configure-an-app-group"></a>設定應用程式群組

共用位置是使用[應用程式群組](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)進行設定，此群組是在[IOS 開發人員中心](https://developer.apple.com/devcenter/ios/)的 [**憑證]、[識別碼 & 設定檔**] 區段中設定。 您也必須在每個專案的權利中參考此值**plist**。

### <a name="provisioning"></a>佈建

應用程式群組會有識別碼，這通常是具有`group.`前置詞的套件組合識別碼。 例如，我們可以使用配套識別碼`com.xamarin.WatchSettings`和應用程式群組。 `group.com.xamarin.WatchSettings`

[![](app-groups-images/app-group-sml.png "使用 [套件組合識別碼] WatchSettings 和應用程式群組 WatchSettings。")](app-groups-images/app-group.png#lightbox)

### <a name="entitlementsplist"></a>Entitlements.plist

除了設定布建設定檔，請在 [ **plist** ] 中**啟用應用程式群組**，並輸入您所選擇的識別碼：

[![](app-groups-images/entitlements-sml.png "設定 plist 並輸入識別碼")](app-groups-images/entitlements.png#lightbox)


### <a name="deployment"></a>部署

請確定您已在[部署](~/ios/watchos/deploy-test/index.md#App_Groups)布建中正確設定應用程式群組。


如需詳細資訊，請參閱[應用程式群組功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)文件。


## <a name="related-links"></a>相關連結

- [Apple 的應用程式包含共用資料](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
- [Apple 的應用程式群組檔](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)
