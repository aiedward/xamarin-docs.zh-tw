---
title: 使用 JAVAdoc 具名引數
description: 本文說明如何使用 JAVA 專案所產生的 JAVAdoc 來復原 JAVA 系結專案中的參數名稱。
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/20/2017
ms.openlocfilehash: 060c4759d39bc3b8c424ce46dc615644540fe9c2
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027671"
---
# <a name="naming-parameters-with-javadoc"></a>使用 JAVAdoc 具名引數

_本文說明如何使用 JAVA 專案所產生的 JAVAdoc 來復原 JAVA 系結專案中的參數名稱。_

## <a name="overview"></a>概觀

系結現有的 JAVA 程式庫時，會遺失一些關於系結 API 的中繼資料。 特別是方法的參數名稱。 參數名稱將會顯示為 `p0`、`p1`等。這是因為 JAVA `.class` 檔案不會保留 JAVA 原始程式碼中使用的參數名稱。 

如果 Xamarin 的 JAVA 系結專案可從原始程式庫存取 JAVAdoc HTML，則可以提供參數名稱。 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>將 JAVAdoc HTML 整合到 JAVA 系結專案

將 JAVAdoc HTML 整合到 JAVA 系結專案中，是由下列步驟組成的手動程式： 

1. 下載文件庫的 JAVAdoc
2. 編輯 `.csproj` 檔案，並新增 `<JavaDocPaths>` 屬性：
3. 清除並重建專案

完成這項作業之後，原始 JAVA 參數名稱應該會出現在 JAVA 系結專案所系結的 Api 中。 

> [!NOTE]
> JAVADoc 輸出中有大量的變異數。 該.JAR 系結工具鏈不支援每一種可能的排列，因此某些參數可能無法正確命名。

## <a name="summary"></a>摘要

本文涵蓋如何在 JAVA 系結專案中使用 JAVAdoc，為系結的 Api 提供意義的參數名稱。 
