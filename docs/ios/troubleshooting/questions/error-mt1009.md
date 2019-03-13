---
title: 錯誤 MT1009： 無法複製組件
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F9FEDFF5-C84C-42B4-8F25-E34846E7315A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4a67537cc53aeecf1b86d11dbf041cea79587dd2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105390"
---
# <a name="error-mt1009-could-not-copy-the-assembly"></a>錯誤 MT1009： 無法複製組件

> [!IMPORTANT]
> 這個問題已經解決的 Xamarin.iOS 的最新版本中。 不過，如果最新版本的軟體，就會發生問題，請指導，申請[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)您完整的版本控制資訊和完整建置記錄檔輸出。

中所述我們[版本資訊](https://developer.xamarin.com/releases/ios/xamarin.ios_7/xamarin.ios_7.2/)，這是 Xamarin.iOS 7.2.6 的已知的問題。 此問題是因為 Xamarin.iOS 安裝使用不同的使用者帳戶時，需要較高的權限的檔案權限則開發人員的主要帳戶。

若要解決此問題，請開啟 Mac 工作站上的 Terminal.app，然後執行下列命令：

`sudo chmod 0644 /Developer/MonoTouch/usr/lib/mono/2.1/monotouch.dll.mdb`

