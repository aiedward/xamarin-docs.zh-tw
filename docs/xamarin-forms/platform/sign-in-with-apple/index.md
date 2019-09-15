---
title: 在 Xamarin 中使用 Apple 登入
description: 使用 Apple 登入可為使用者提供身分識別保護，並可針對 Xamarin 中的跨平臺行動案例執行。
ms.prod: xamarin
ms.assetid: 414465F8-0CC8-4078-B3D1-F36983069658
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 09/10/2019
ms.openlocfilehash: f188350ff441ec16a333035c78ee0082b320d8ff
ms.sourcegitcommit: cf56d2bae34dc0f8e94c2d3d28d5f460d59807bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985992"
---
# <a name="sign-in-with-apple-in-xamarinforms"></a>在 Xamarin 中使用 Apple 登入

![此 API 目前為預覽狀態](~/media/shared/preview.png)

使用 Apple 登入引進了一項新服務，可為使用者提供身分識別保護。 從 iOS 13 版，Apple 要求任何使用協力廠商驗證提供者的應用程式也會提供使用 Apple 的登入。 如需搭配使用此版與 Xamarin 的指示，請[參閱這裡](~/ios/platform/ios13/sign-in.md)。

在 Xamarin Forms 解決方案中支援以 Apple 登入時，需要考慮 Android 和 UWP 的其他考慮。 針對這些平臺，Apple 提供不同的工作流程。

## <a name="setup-for-iosiosplatformios13sign-inmd"></a>[IOS 的設定](~/ios/platform/ios13/sign-in.md)

本指南會逐步解說啟用使用 Apple for iOS 應用程式登入所需的設定。

## <a name="setup-for-other-platformssetupmd"></a>[其他平臺的設定](setup.md)

本指南會逐步解說針對非 iOS 應用程式啟用使用 Apple 登入所需的設定，包括 Xamarin. Forms Android 和 UWP。

## <a name="use-sign-in-with-appleandroid-ios-sign-inmd"></a>[搭配 Apple 使用登入](android-ios-sign-in.md)

有幾項服務，您可以支援跨平臺 Xamarin. 表單應用程式中的 Apple 登入。 本指南將說明必要的步驟。
