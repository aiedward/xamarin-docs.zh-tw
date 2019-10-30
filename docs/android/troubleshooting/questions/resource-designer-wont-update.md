---
title: 我的 Android Resource.designer.cs 檔案不會更新
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/19/2017
ms.openlocfilehash: 4683bbaa5aa48c7b5de5fb9a87a4cd3fbc0aeada
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019518"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>我的 Android Resource.designer.cs 檔案不會更新

> [!NOTE]
> 此問題已在 Xamarin Studio 5.1.4 和更新版本中解決。 不過，如果 Visual Studio for Mac 發生問題，請使用完整版本設定資訊和完整組建記錄檔輸出，提出[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。

5\.1 Xamarin 中的 bug 先前已損毀 .csproj 檔案中的 xml 程式碼，或在 .csproj 檔案中完全刪除該檔案。 這會導致 Android 組建系統的重要部分（例如，更新 Android Resource.designer.cs）失敗。 從5.1.4 穩定版本于7月15日起，已修正此 bug;但在許多情況下，專案檔必須手動修復，如下所述。

## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>有兩種可能的方法可以修正專案檔案

**任意**

1. 建立全新的 Xamarin Android 應用程式專案，設定所有專案屬性以符合舊專案，並將您的所有資源、原始程式檔等等加入專案。

   **或**

2. 建立原始專案 .csproj 檔案的備份複本，然後在文字編輯器中開啟它，然後從完全產生的 .csproj 檔案中，加回遺漏的元素。

### <a name="if-this-does-not-solve-the-problem"></a>如果這樣無法解決問題

在試驗這些元素之後，您可能會注意到，在您將專案加回之後，Resource.designer.cs 檔案就會更新，但您可能還是必須關閉並重新開啟方案，才能讓程式碼完成辨識包含在 Resource.designer.cs 中的新類型。 
