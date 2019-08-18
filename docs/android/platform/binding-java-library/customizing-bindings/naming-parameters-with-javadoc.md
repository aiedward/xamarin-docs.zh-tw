---
title: 使用 JAVAdoc 具名引數
description: 本文說明如何使用 JAVA 專案所產生的 JAVAdoc 來復原 JAVA 系結專案中的參數名稱。
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/20/2017
ms.openlocfilehash: 77cd5ac86a24769947eefb6941229c56efc3297b
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524557"
---
# <a name="naming-parameters-with-javadoc"></a>使用 JAVAdoc 具名引數

_本文說明如何使用 JAVA 專案所產生的 JAVAdoc 來復原 JAVA 系結專案中的參數名稱。_


## <a name="overview"></a>總覽

系結現有的 JAVA 程式庫時, 會遺失一些關於系結 API 的中繼資料。 特別是方法的參數名稱。 參數名稱將會顯示`p0`為`p1`、等。這是因為 java `.class`檔案不會保留 java 原始碼中使用的參數名稱。 

如果 Xamarin 的 JAVA 系結專案可從原始程式庫存取 JAVAdoc HTML, 則可以提供參數名稱。 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>將 JAVAdoc HTML 整合到 JAVA 系結專案

將 JAVAdoc HTML 整合到 JAVA 系結專案中, 是由下列步驟組成的手動程式: 

1. 下載文件庫的 JAVAdoc
2. 編輯檔案並`<JavaDocPaths>`新增屬性: `.csproj`
3. 清除並重建專案

完成這項作業之後, 原始 JAVA 參數名稱應該會出現在 JAVA 系結專案所系結的 Api 中。 


> [!NOTE]
> JAVADoc 輸出中有大量的變異數。 該.JAR 系結工具鏈不支援每一種可能的排列, 因此某些參數可能無法正確命名。


## <a name="summary"></a>總結

本文涵蓋如何在 JAVA 系結專案中使用 JAVAdoc, 為系結的 Api 提供意義的參數名稱。 

