---
title: 為何我的 Android 版本組建無法網際網路連線？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 7f956defd0243e1927746a53e6b3b1b05d98f8d2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>為何我的 Android 版本組建無法網際網路連線？

## <a name="cause"></a>原因

此問題最常見的原因在於**網際網路**權限會自動包含在偵錯組建，但必須手動設定發行組建。 這是因為網際網路權限可讓偵錯工具附加至處理序，如"DebugSymbols 」 所述[這裡](~/android/deploy-test/building-apps/build-process.md)。


## <a name="fix"></a>修正

若要解決此問題，您可以要求網際網路中的權限 Android 資訊清單。 這可以透過資訊清單編輯器] 或 [資訊清單的 sourcecode 完成：

-   在編輯器中修正： 在 Android 專案中，移至**屬性]-> [AndroidManifest.xml 必要權限]-> [**並檢查**網際網路**

-   修正在 Sourcecode： 原始檔編輯器中開啟 AndroidManifest 並加入標記內的權限`<Manifest>`標記：

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
