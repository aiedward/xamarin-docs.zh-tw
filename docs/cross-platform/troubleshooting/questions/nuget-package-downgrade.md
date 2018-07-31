---
title: 如何降級的 NuGet 套件？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2375F833-A630-471E-B8E9-5AD2CB81F264
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 72fdf7246b148fa95ea312284957072ecda47121
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351212"
---
# <a name="how-do-i-downgrade-a-nuget-package"></a>如何降級的 NuGet 套件？

Visual Studio for Mac 及 Visual Studio 已選取較舊版本的套件並將它們，而安裝的功能類似於如何更新封裝的運作方式。 以下說明這些步驟。

## <a name="visual-studio"></a>Visual Studio
1. 移至**工具 > NuGet 套件管理員 > 套件管理員主控台**
2. 設定專案下的**預設專案**
3. 使用此語法：

    > 安裝套件 [PackageName]-版本 [版本] 功能表 索引標籤

您可以也複製/貼上從套件的 NuGet 頁面的確切命令。 適用於 Xamarin.Forms 的範例： [https://www.nuget.org/packages/Xamarin.Forms/](https://www.nuget.org/packages/Xamarin.Forms/)

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac
1. 在專案中，以滑鼠右鍵按一下 packages 資料夾，並選取 **新增套件**
2. 在 searchbar，您可以使用下列語法，來搜尋所需的套件：

    `[PackageName] version:*`

### <a name="examples"></a>範例 
- 列出所有的 Xamarin.Forms 套件： 

    `Xamarin.Forms version:`
- 列出所有的 Xamarin.Forms 1.4.x 套件： 

    `Xamarin.Forms version:1.4`

*注意： 如果您新增之間有空白`version:`和版本號碼，搜尋會如同未指定任何版本。*

