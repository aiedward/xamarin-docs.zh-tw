---
title: 不會更新我的 Android Resource.designer.cs 檔案
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/19/2017
ms.openlocfilehash: 6d20c92fbb61ab11fcfeda3e9d2f63fdfc5a6d54
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762616"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>不會更新我的 Android Resource.designer.cs 檔案

> [!NOTE]
> Xamarin Studio 5.1.4 和更新版本中，已解決此問題。 但是，若 Mac 的 Visual Studio 就會發生問題，請檔案[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)您完整的版本資訊和完整建置記錄檔輸出。

Xamarin.Studio 5.1 中的 bug 之前損毀.csproj 檔案部分或完全刪除.csproj 檔案中的 xml 程式碼。 這會導致重要部分 Android 的建置系統 （例如更新 Android Resource.designer.cs） 失敗。 從穩定 5.1.4 開始發行版本上年 7 月 15，已修正這個問題;但在許多情況下的專案檔必須以手動方式，如下所述的修復。


## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>兩個可能的解決方法修正專案檔

### <a name="either"></a>以下其中之一：

1) 建立全新的 Xamarin.Android 應用程式專案，請設定所有的專案屬性，以符合您舊的專案，然後加入您的資源、 原始程式檔等等的所有專案放回。

### <a name="or"></a>OR

2) 製作您原始專案.csproj 檔案的備份複本，然後開啟它在文字編輯器中，並從完全產生的.csproj 檔案新增回中遺漏的項目。

### <a name="if-this-does-not-solve-the-problem"></a>如果這樣做無法解決問題

後在試驗這些項目，您可能會注意到，之後重新新增的項目，並重建專案，則會更新 Resource.designer.cs 檔案，但您可能仍必須關閉並重新開啟方案，以取得能夠辨識的程式碼完成功能包含在 Resource.designer.cs 的新類型。 
