---
title: 錯誤 MT1009：無法複製元件
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F9FEDFF5-C84C-42B4-8F25-E34846E7315A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 1ccf85c03ab1f2cff6428a0120a8ceb4de250761
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031153"
---
# <a name="error-mt1009-could-not-copy-the-assembly"></a>錯誤 MT1009：無法複製元件

> [!IMPORTANT]
> 這個問題已在最新版本的 Xamarin 中解決。 不過，如果軟體的最新版本發生問題，請使用完整版本設定資訊和完整組建記錄檔輸出來提出[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。

如我們的[版本](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_7/xamarin.ios_7.2/index.md)資訊所述，這是7.2.6 中的已知問題。 此問題的原因是，當使用不同的使用者帳戶安裝 Xamarin 時，需要較高許可權的檔案許可權，然後是開發人員的主要帳戶。

若要解決此問題，請開啟 Mac 工作站上的 Terminal 應用程式，然後執行下列命令：

`sudo chmod 0644 /Developer/MonoTouch/usr/lib/mono/2.1/monotouch.dll.mdb`
