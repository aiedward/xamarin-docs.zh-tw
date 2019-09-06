---
title: 為什麼 Visual Studio 不在我的組建中包括參考的程式庫專案？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: b9009db8-e716-43aa-b40e-6f28a8eb1b82
author: conceptdev
ms.author: crdun
ms.date: 12/02/2016
ms.openlocfilehash: 37fa93ef7377456d61d1a5f5de56d5de6b0f3c7f
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282916"
---
# <a name="why-doesnt-visual-studio-include-my-referenced-library-project-in-my-build"></a>為什麼 Visual Studio 不在我的組建中包括參考的程式庫專案？

Visual Studio 會使用**Configuration Manager**來判斷解決方案中的哪些專案會自動包含在指定的組建或部署設定中。

某些使用參考的程式庫專案所產生的範本，將已包含在設定中的參考程式庫。但否則必須手動設定。

## <a name="how-to-use-the-configuration-manager"></a>如何使用 Configuration Manager

1. 開啟**組建 > Configuration Manager**
2. 選取要自訂的設定，例如**Debug | iPhone**
3. 選取您想要包含之專案的核取方塊。

> [!NOTE]
> 呈現灰色的方塊會自動處理，而且不需要任何變更。

這些步驟的螢幕錄製影片：[http://screencast.com/t/zLoQOpEn](http://screencast.com/t/zLoQOpEn)
