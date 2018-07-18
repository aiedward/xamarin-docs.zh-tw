---
title: 自訂繫結
description: 您可以編輯控制項繫結程序的中繼資料，以自訂 Xamarin.Android 繫結。 這些手動修改通常會需要解決建置錯誤，並可用來形成產生的應用程式開發介面，使其更一致，使用 C# /.NET。 這些指南將說明此中繼資料的結構、 如何修改中繼資料，以及如何使用 JavaDoc 復原的方法參數的名稱。
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 09/25/2017
ms.openlocfilehash: bb4f3b24be2072cb8b33893899a23951ace63607
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763133"
---
# <a name="customizing-bindings"></a>自訂繫結

_您可以編輯控制項繫結程序的中繼資料，以自訂 Xamarin.Android 繫結。這些手動修改通常會需要解決建置錯誤，並可用來形成產生的應用程式開發介面，使其更一致，使用 C# /.NET。這些指南將說明此中繼資料的結構、 如何修改中繼資料，以及如何使用 JavaDoc 復原的方法參數的名稱。_


## <a name="overview"></a>總覽
 
Xamarin.Android 自動化大部分的繫結程序。不過，在某些情況下手動修改，才能解決下列問題：

-   解決建置錯誤，因缺少類型、 模糊化的型別、 重複的名稱、 類別可見性問題，以及其他情況下，無法解析的 Xamarin.Android 工具。 

-   Xamarin.Android 使用繫結至 C# 中的不同類型的 Android 應用程式開發介面的對應變更 (例如，許多開發人員想要對應 Java`int`常數至 C#`enum`常數)。

-   移除未使用不需要繫結的型別。 

-   新增具有基礎 Java API 中沒有對應項目類型。 

您可以修改控制繫結程序的中繼資料，以讓部分或所有的這些變更。


## <a name="guides"></a>輔助線

下列指南描述控制繫結程序的中繼資料，並說明如何修改中繼資料來解決這些問題：

-   [Java 繫結中繼資料](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)提供進入 Java 繫結的中繼資料的概觀。
    它說明完成 Java 繫結的程式庫，有時需要的不同手動步驟，並說明如何使用圖形 API 公開的繫結，以更仔細地遵循.NET 設計指導方針。

-   [參數與 Javadoc 命名](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md)說明如何使用繫結的 Java 專案所產生的 Javadoc 復原 Java 繫結的專案中的參數名稱。


 

