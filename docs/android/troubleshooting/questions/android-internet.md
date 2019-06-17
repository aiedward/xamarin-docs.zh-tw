---
title: 為何我的 Android 發行組建無法連線到網際網路？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: cd27d5c884086cd0fade4364851039fd0cd915a0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60945457"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>為何我的 Android 發行組建無法連線到網際網路？

## <a name="cause"></a>原因

此問題最常見的原因在於**網際網路**權限會自動包含在偵錯組建，但必須手動設定的發行組建。 這是因為網際網路權限用來允許偵錯工具附加至處理序，如"DebugSymbols 」 所述[此處](~/android/deploy-test/building-apps/build-process.md)。


## <a name="fix"></a>修正

若要解決此問題，您可以要求網際網路權限，在 Android 資訊清單。 這可以透過資訊清單編輯器] 或 [資訊清單的 sourcecode 完成：

-   修正在編輯器中：在您的 Android 專案中，移至**內容]-> [AndroidManifest.xml]-> [必要權限**，並檢查**網際網路**

-   修正在 Sourcecode:在 原始檔編輯器中開啟 AndroidManifest 並加入標記內的權限`<Manifest>`標記：

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
