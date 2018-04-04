---
title: 與 Javadoc 命名參數
description: 本文說明如何使用 Java 專案所產生的 Javadoc 復原 Java 繫結專案中的參數名稱。
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/20/2017
ms.openlocfilehash: 7517e46c5b66123dc4e12fb5562c59f569f249aa
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="naming-parameters-with-javadoc"></a>與 Javadoc 命名參數

_本文說明如何使用 Java 專案所產生的 Javadoc 復原 Java 繫結專案中的參數名稱。_


## <a name="overview"></a>總覽

當繫結現有的 Java 程式庫，一些關於繫結 API 的中繼資料將會遺失。 特別是方法的參數名稱。 參數名稱會顯示為`p0`，`p1`等等。這是因為 Java`.class`檔案不會保留在 Java 程式碼中所使用的參數名稱。 

如果它擁有存取權的 Javadoc HTML 從原始程式庫，Xamarin.Android Java 繫結專案可以提供參數名稱。 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>整合專案繫結 Java 的 Javadoc HTML

整合 Java 繫結的專案中的 Javadoc HTML 是手動程序包含下列步驟： 

1.  下載文件庫 Javadoc
2.  編輯`.csproj`檔案，然後加入`<JavaDocPaths>`屬性：
3.  清除並重建專案

完成之後，原始的 Java 參數名稱應該會出現在 Java 繫結的專案所繫結的 Api。 


> [!NOTE]
> 沒有更多的 JavaDoc 輸出中的變異數。 。JAR 繫結工具鏈不支援每一個可能的排列，因此某些參數不正確的名稱可能。


## <a name="summary"></a>總結

本文涵蓋如何使用 Java 繫結的專案中的 Javadoc，提供繫結的應用程式開發介面的意義參數名稱。 

