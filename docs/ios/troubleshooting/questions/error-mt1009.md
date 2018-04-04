---
title: 錯誤 MT1009： 無法複製組件
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F9FEDFF5-C84C-42B4-8F25-E34846E7315A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: de1d7ea8ce4c358ad69150be3da6b38fb6c730ae
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="error-mt1009-could-not-copy-the-assembly"></a>錯誤 MT1009： 無法複製組件

> [!IMPORTANT]
> 這個問題已經解決 Xamarin.iOS 的最新版的。 但是，若發生此問題，軟體的最新版本，請檔案[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)您完整的版本資訊和完整建置記錄檔輸出。

中所述我們[版本資訊](https://developer.xamarin.com/releases/ios/xamarin.ios_7/xamarin.ios_7.2/)，這是 Xamarin.iOS 7.2.6 已知的問題。 此問題是因為 Xamarin.iOS 會隨不同的使用者帳戶時，需要更高權限的檔案權限然後開發人員的主要帳戶。

若要解決此問題，請開啟 Mac 工作站上的 Terminal.app 並執行下列命令：

`sudo chmod 0644 /Developer/MonoTouch/usr/lib/mono/2.1/monotouch.dll.mdb`

