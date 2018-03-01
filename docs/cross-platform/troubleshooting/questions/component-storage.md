---
title: "元件儲存我的電腦？"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 9549363d7aeb5ff7a5cfa79eb38443aaa80b7019
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="where-are-the-components-stored-on-my-machine"></a>元件儲存我的電腦？

每當您 Xamarin 元件安裝到應用程式專案時，它會取得放在兩個地方：

1. 在您的方案資料夾的根層級的元件資料夾。 如果您從方案中的所有專案中移除元件，它會從這個資料夾也移除。

2. 複本也會儲存在下列位置：
    - Windows: `%LocalAppData%\Xamarin\Cache\Components`
    - Mac: `~/Library/Caches/Xamarin/Components`

因此若要從您的系統完全移除元件，它從您的專案/方案和刪除上述的快取資料夾。
