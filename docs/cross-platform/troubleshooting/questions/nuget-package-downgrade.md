---
title: 如何降級 NuGet 套件？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2375F833-A630-471E-B8E9-5AD2CB81F264
author: conceptdev
ms.author: crdun
ms.date: 05/08/2018
ms.openlocfilehash: fc504d1d7a9e990b3bcd90b3404e7db3d9fe7527
ms.sourcegitcommit: 13e43f510da37ad55f1c2f5de1913fb0aede6362
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2019
ms.locfileid: "71021137"
---
# <a name="how-do-i-downgrade-a-nuget-package"></a>如何降級 NuGet 套件？

Visual Studio for Mac & Visual Studio 都具有選取舊版套件並自動安裝的功能;與更新封裝的運作方式類似。 這些步驟如下所述。

## <a name="visual-studio"></a>Visual Studio

1. 移至 [**工具] > NuGet 套件管理員 > 套件管理員主控台**
2. 在 [**預設專案**] 底下設定專案
3. 使用此語法：

    > 安裝套件 [PackageName]-版本 [版本] 功能表 索引標籤

您也可以從套件的 NuGet 頁面複製/貼上正確的命令。 Xamarin 的範例：[https://www.nuget.org/packages/Xamarin.Forms/](https://www.nuget.org/packages/Xamarin.Forms/)

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 在您的專案中，以滑鼠右鍵按一下 [套件] 資料夾，& 選取 [**新增套件**]
2. 在搜尋列中，您可以使用下列語法來搜尋您所需的套件：

    `[PackageName] version:*`

### <a name="examples"></a>範例 
- 列出所有的 Xamarin. Forms 套件： 

    `Xamarin.Forms version:`

- 列出所有的1.x 套件： 

    `Xamarin.Forms version:1.4`

> [!NOTE]
> 如果您在 & 版本號碼`version:`之間加上空格，搜尋的行為會如同未指定任何版本。
