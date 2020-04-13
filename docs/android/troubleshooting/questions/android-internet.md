---
title: 為何我的 Android 發行組建無法連線到網際網路？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 5996cfa3c0a18fc186ea862a2b3d7910594e1281
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027014"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>為何我的 Android 發行組建無法連線到網際網路？

## <a name="cause"></a>原因

此問題的最常見原因是**INTERNET**許可權自動包含在調試生成中,但必須手動設置發佈版本。 這是因為 Internet 權限用於允許除錯器附加到行程,此處的「除錯[符號」 的錯誤](~/android/deploy-test/building-apps/build-process.md)。

## <a name="fix"></a>修正

要解決此問題,您可以在 Android 清單中請求 Internet 許可權。 這可以通過清單編輯器或清單的原始碼完成:

- 在編輯器中修復:在 Android 專案中,轉到**屬性 -> AndroidManifest.xml ->必需的权限**並檢查**互聯網**

- 在原始碼中修復:在原始碼中開啟 AndroidManifest,並在`<Manifest>`標記中加入權限標記:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
