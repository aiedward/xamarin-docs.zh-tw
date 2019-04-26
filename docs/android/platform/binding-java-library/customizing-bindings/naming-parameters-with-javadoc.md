---
title: 在 Javadoc 命名參數
description: 這篇文章說明如何使用 Java 專案從產生的 Javadoc 復原 Java 繫結專案中的參數名稱。
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/20/2017
ms.openlocfilehash: e394377043953a297afed36a3ce0747a3e6d1512
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60955867"
---
# <a name="naming-parameters-with-javadoc"></a>在 Javadoc 命名參數

_這篇文章說明如何使用 Java 專案從產生的 Javadoc 復原 Java 繫結專案中的參數名稱。_


## <a name="overview"></a>總覽

當繫結現有的 Java 程式庫，一些有關繫結的 API 中繼資料將會遺失。 特別是方法參數的名稱。 參數名稱會成為`p0`，`p1`等等。這是因為 Java`.class`檔案不會保留在 Java 程式碼中所使用的參數名稱。 

如果它可以存取的 Javadoc html 從原始的程式庫，Xamarin.Android Java 繫結專案可以提供參數名稱。 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>整合繫結專案的 Java 的 Javadoc HTML

整合 Java 繫結的專案中的 Javadoc HTML 是手動程序包含下列步驟： 

1.  下載程式庫的 Javadoc
2.  編輯`.csproj`檔案，並新增`<JavaDocPaths>`屬性：
3.  清除並重建專案

完成之後，原始的 Java 參數名稱應該會出現在 繫結 Java 繫結專案的 Api。 


> [!NOTE]
> 有很多的 JavaDoc 輸出中的變異數。 。JAR 繫結工具鏈不支援每個單一可能的排列，因此某些參數可能不正確命名。


## <a name="summary"></a>總結

本文涵蓋如何使用 Java 繫結專案中 Javadoc，提供意義參數名稱，繫結的 api。 

