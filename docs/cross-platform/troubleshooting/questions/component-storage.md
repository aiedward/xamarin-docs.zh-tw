---
title: 元件儲存在我電腦上的何處？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 4152c8ef7eeba3748d9244e27e48f3f9a2c0019b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61359092"
---
# <a name="where-are-the-components-stored-on-my-machine"></a>元件儲存在我電腦上的何處？

只要您的 Xamarin 元件安裝到應用程式專案時，它位於兩個地方：

1. 在您的方案資料夾的根層級的 [元件] 資料夾中。 如果您從方案中的所有專案中移除元件，它會從這個資料夾也移除。

2. 複本也會儲存在下列位置：
    - Windows：`%LocalAppData%\Xamarin\Cache\Components`
    - Mac: `~/Library/Caches/Xamarin/Components`

因此若要從您的系統，完全移除元件，請刪除它從您的專案/方案和上述的快取資料夾。
