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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019518"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>我的 Android Resource.designer.cs 檔案不會更新

> [!NOTE]
> 此問題已在 Xamarin Studio 5.1.4 和更高版本中得到解決。 但是,如果問題發生在 Mac 的 Visual Studio 中,請使用您的完整版本控制資訊和完整構建日誌輸出提交[新 Bug。](~/cross-platform/troubleshooting/questions/howto-file-bug.md)

Xamarin.Studio 5.1 中的一個 Bug 以前通過部分或全部刪除 .csproj 檔中的 xml 代碼而損壞了 .csproj 檔。 這將導致 Android 構建系統的重要部分(如更新 Android Resource.designer.cs)失敗。 截至7月15日5.1.4穩定發佈,此 Bug 已修復;但在許多情況下,專案檔必須手動修復,如下所述。

## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>修復項目檔案的兩種可能方法

**要麼:**

1. 創建一個全新的 Xamarin.Android 應用程式專案,設置與舊專案匹配的所有專案屬性,並將所有資源、源檔等重新添加到專案中。

   **OR**

2. 製作原始專案的 .csproj 檔案的備份複本,然後在文字編輯器中打開該檔,然後從乾淨生成的 .csproj 檔中添加回缺少的元素。

### <a name="if-this-does-not-solve-the-problem"></a>如果這不能解決問題

試用這些元素後,您可能會注意到,在添加回元素並重建專案後,Resource.designer.cs檔將更新,但隨後您可能仍必須關閉並重新打開解決方案,以獲得代碼完成以識別Resource.designer.cs中包含的新類型。 
