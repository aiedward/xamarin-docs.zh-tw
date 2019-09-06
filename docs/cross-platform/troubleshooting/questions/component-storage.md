---
title: 元件儲存在我電腦上的何處？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
author: conceptdev
ms.author: crdun
ms.date: 05/08/2018
ms.openlocfilehash: 7725dbff994ffcef9734ad07c6b506064d9c5b2b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285045"
---
# <a name="where-are-the-components-stored-on-my-machine"></a>元件儲存在我電腦上的何處？

每當您在應用程式專案中安裝 Xamarin 元件時，它會放在兩個位置：

1. 位於方案資料夾根層級的 [元件] 資料夾中。 如果您從方案中的所有專案移除該元件，它也會從這個資料夾中移除。

2. 複本也會儲存在下列位置：
    - Windows：`%LocalAppData%\Xamarin\Cache\Components`
    - Mac`~/Library/Caches/Xamarin/Components`

因此，若要從您的系統中完全移除元件，請將它從您的專案/解決方案中刪除，並從上方的快取資料夾中刪除。
