---
title: 使用 JAVAdoc 具名引數
description: 本文說明如何使用 JAVA 專案所產生的 JAVAdoc 來復原 JAVA 系結專案中的參數名稱。
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/20/2017
ms.openlocfilehash: bcfc5778ed4e5486d188f4eefbd32811792b44e5
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85852988"
---
# <a name="naming-parameters-with-javadoc"></a>使用 JAVAdoc 具名引數

> [!IMPORTANT]
> 我們目前正在調查 Xamarin 平臺上的自訂系結使用方式。 請接受[**這份問卷調查**](https://www.surveymonkey.com/r/KKBHNLT)，以通知未來的開發工作。

_本文說明如何使用 JAVA 專案所產生的 JAVAdoc 來復原 JAVA 系結專案中的參數名稱。_

## <a name="overview"></a>概觀

系結現有的 JAVA 程式庫時，會遺失一些關於系結 API 的中繼資料。 特別是方法的參數名稱。 參數名稱將會顯示為 `p0` 、 `p1` 等。這是因為 JAVA 檔案 `.class` 不會保留 java 原始碼中使用的參數名稱。 

如果 Xamarin 的 JAVA 系結專案可從原始程式庫存取 JAVAdoc HTML，則可以提供參數名稱。 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>將 JAVAdoc HTML 整合到 JAVA 系結專案

將 JAVAdoc HTML 整合到 JAVA 系結專案中，是由下列步驟組成的手動程式： 

1. 下載文件庫的 JAVAdoc
2. 編輯檔案 `.csproj` 並新增 `<JavaDocPaths>` 屬性：
3. 清除並重建專案

完成這項作業之後，原始 JAVA 參數名稱應該會出現在 JAVA 系結專案所系結的 Api 中。 

> [!NOTE]
> JAVADoc 輸出中有大量的變異數。 該.JAR 系結工具鏈不支援每一種可能的排列，因此某些參數可能無法正確命名。

## <a name="summary"></a>摘要

本文涵蓋如何在 JAVA 系結專案中使用 JAVAdoc，為系結的 Api 提供意義的參數名稱。 
