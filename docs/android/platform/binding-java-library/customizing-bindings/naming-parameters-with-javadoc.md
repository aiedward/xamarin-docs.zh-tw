---
title: 使用 Javadoc 命名參數
description: 本文介紹如何使用從 JAVA 專案生成的 Javadoc 在 Java 綁定專案中恢復參數名稱。
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/20/2017
ms.openlocfilehash: 060c4759d39bc3b8c424ce46dc615644540fe9c2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027671"
---
# <a name="naming-parameters-with-javadoc"></a>使用 Javadoc 命名參數

_本文介紹如何使用從 JAVA 專案生成的 Javadoc 在 Java 綁定專案中恢復參數名稱。_

## <a name="overview"></a>概觀

綁定現有 JAVA 庫時,有關綁定 API 的某些元數據將丟失。 特別是方法的參數名稱。 參數名稱將顯示為`p0``p1`, 等。這是因為 Java`.class`檔不保留 Java 原始程式碼中使用的參數名稱。 

Xamarin.Android JAva 綁定專案如果可以訪問原始庫中的 Javadoc HTML,則可以提供參數名稱。 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>將 Javadoc HTML 整合到 Java 連結項目

將 Javadoc HTML 整合到 Java 綁定項目中是一個手動過程,包括以下步驟: 

1. 下載函式庫的 Javadoc
2. 編輯`.csproj`檔案並新增`<JavaDocPaths>`屬性 :
3. 清除和重建項目

完成此操作後,原始 JAva 參數名稱應存在於由 JAVA 綁定專案綁定的 API 中。 

> [!NOTE]
> JavaDoc 輸出存在很大差異。 .JAR 綁定工具鏈不支援每一個可能的排列,因此某些參數可能無法正確命名。

## <a name="summary"></a>總結

本文介紹了如何在 JAVA 綁定專案中使用 JAvadoc 為綁定 API 提供含義參數名稱。 
