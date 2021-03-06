---
title: 如何降級 NuGet 套件？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2375F833-A630-471E-B8E9-5AD2CB81F264
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: 0c70859845915a821bb83b0f9d29528634b1a5de
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013615"
---
# <a name="how-do-i-downgrade-a-nuget-package"></a>如何降級 NuGet 套件？

Visual Studio for Mac & Visual Studio 都具有選取舊版套件並自動安裝的功能;與更新封裝的運作方式類似。 這些步驟如下所述。

## <a name="visual-studio"></a>Visual Studio

1. 移至 [**工具] > NuGet 套件管理員 > 套件管理員主控台**
2. 在 [**預設專案**] 底下設定專案
3. 使用此語法：

    > 安裝-套件 [PackageName]-版本功能表的索引標籤]

您也可以從套件的 NuGet 頁面複製/貼上正確的命令。 Xamarin 的範例： [https://www.nuget.org/packages/Xamarin.Forms/](https://www.nuget.org/packages/Xamarin.Forms/)

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
> 如果您在 `version:` & 版本號碼之間加上空格，搜尋的行為會如同未指定任何版本。
