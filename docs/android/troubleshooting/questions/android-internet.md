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
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027014"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>為何我的 Android 發行組建無法連線到網際網路？

## <a name="cause"></a>原因

此問題最常見的原因是**網際網路**許可權會自動包含在 debug 組建中，但必須針對發行組建手動設定。 這是因為網際網路許可權是用來允許偵錯工具附加至進程，如[這裡](~/android/deploy-test/building-apps/build-process.md)的「DebugSymbols」所述。

## <a name="fix"></a>修正

若要解決此問題，您可以在 Android 資訊清單中要求網際網路許可權。 這可以透過資訊清單編輯器或資訊清單的 sourcecode 來完成：

- 在編輯器中修正：在您的 Android 專案中，移至 **屬性-> androidmanifest.xml-> 必要許可權** 和 檢查**網際網路**

- 在 Sourcecode 中修正：在原始檔編輯器中開啟 Androidmanifest.xml，並在 `<Manifest>` 標記內新增許可權標記：

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
