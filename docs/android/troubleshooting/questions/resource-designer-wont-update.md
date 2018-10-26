---
title: 我的 Android Resource.designer.cs 檔案不會更新
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/19/2017
ms.openlocfilehash: ba3c2b07e7f35bf9fd84d10b74d034a02ca6a73d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106412"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>我的 Android Resource.designer.cs 檔案不會更新

> [!NOTE]
> 在 Xamarin Studio 5.1.4 和更新版本中，已解決此問題。 不過，如果問題發生在 Visual Studio for Mac，請指導，申請[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)您完整的版本控制資訊和完整建置記錄檔輸出。

在 Xamarin.Studio 5.1 的 bug 之前損毀部分或完全刪除.csproj 檔案中的 xml 程式碼的.csproj 檔案。 這會導致重要組件的 Android 建置系統 （例如更新 Android Resource.designer.cs） 失敗。 截至 5.1.4 穩定的版本在 7 月 15 日，已修正此錯誤;但在許多情況下的專案檔必須以手動方式，如下所述修復。


## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>兩個可能的解決方法修正專案檔案

### <a name="either"></a>以下其中之一：

1) 建立新的 Xamarin.Android 應用程式專案，設定符合舊的專案，並新增所有您的資源、 原始程式檔等回專案的所有專案屬性。

### <a name="or"></a>OR

2) 請您原始專案的.csproj 檔案的備份副本，然後在文字編輯器中開啟它，並再次新增遺漏的元素從完全產生的.csproj 檔案。

### <a name="if-this-does-not-solve-the-problem"></a>如果這樣做無法解決問題

這些項目之後，您可能會注意到，之後，將傳回的項目時，加入並重建專案，Resource.designer.cs 檔案會更新，但，您可能仍必須關閉再重新開啟方案，以取得可辨識的程式碼完成功能Resource.designer.cs 中包含新的類型。 
