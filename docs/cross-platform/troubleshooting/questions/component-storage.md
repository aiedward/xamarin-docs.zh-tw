---
title: 元件儲存我的電腦？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: f0dad6e6219d373eaa9f8410aea7d96c81eceb6b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="where-are-the-components-stored-on-my-machine"></a>元件儲存我的電腦？

每當您 Xamarin 元件安裝到應用程式專案時，它會取得放在兩個地方：

1. 在您的方案資料夾的根層級的元件資料夾。 如果您從方案中的所有專案中移除元件，它會從這個資料夾也移除。

2. 複本也會儲存在下列位置：
    - Windows：`%LocalAppData%\Xamarin\Cache\Components`
    - Mac: `~/Library/Caches/Xamarin/Components`

因此若要從您的系統完全移除元件，它從您的專案/方案和刪除上述的快取資料夾。
