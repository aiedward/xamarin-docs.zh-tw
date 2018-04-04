---
title: 我要如何降級 NuGet 封裝？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2375F833-A630-471E-B8E9-5AD2CB81F264
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 2eec947ab748eccd45971054f2f5b232cf788cfa
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="how-do-i-downgrade-a-nuget-package"></a>我要如何降級 NuGet 封裝？

Visual Studio for Mac 和 Visual Studio 有選取封裝的舊版本，以及自動; 安裝這些功能類似於更新封裝的運作方式。 以下將詳述這些步驟。

## <a name="visual-studio"></a>Visual Studio
1. 移至**工具 > NuGet 套件管理員 > Package Manager Console**
2. 設定下的專案**預設專案**
3. 使用此語法：

    > 安裝套件 [PackageName]-版本 [版本] 功能表 索引標籤

您可以也複製/貼上從 NuGet 封裝 頁面的確切命令。 Xamarin.Forms 的範例： [https://www.nuget.org/packages/Xamarin.Forms/](https://www.nuget.org/packages/Xamarin.Forms/)

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac
1. 在專案中，以滑鼠右鍵按一下 [packages] 資料夾 （& s) 選取**新增套件**
2. 在 searchbar，您可以使用下列語法來搜尋所需的封裝：

    `[PackageName] version:*`

### <a name="examples"></a>範例 
- 列出所有 Xamarin.Forms 套件： 

    `Xamarin.Forms version:`
- 列出所有 Xamarin.Forms 1.4.x 套件： 

    `Xamarin.Forms version:1.4`

*注意： 如果您將加入之間的空格`version:`（& s) 的版本號碼，搜尋會表現成未指定任何版本。*

