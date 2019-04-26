---
title: 自訂繫結
description: 您可以自訂的 Xamarin.Android 繫結，藉由編輯控制項繫結程序的中繼資料。 這些手動修改通常是解決建置錯誤和成形產生的 API，使其更一致的必要條件C#/.NET。 這些指南將說明此中繼資料的結構、 如何修改中繼資料，以及如何使用 JavaDoc 復原方法參數的名稱。
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/25/2017
ms.openlocfilehash: 44bff372225ee1bf555eb3eeb34da918830980b4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60957560"
---
# <a name="customizing-bindings"></a>自訂繫結

_您可以自訂的 Xamarin.Android 繫結，藉由編輯控制項繫結程序的中繼資料。這些手動修改通常是解決建置錯誤和成形產生的 API，使其更一致的必要條件C#/.NET。這些指南將說明此中繼資料的結構、 如何修改中繼資料，以及如何使用 JavaDoc 復原方法參數的名稱。_


## <a name="overview"></a>總覽
 
Xamarin.Android 蒐集大部分的繫結程序;不過，在某些情況下手動修改，才能解決下列問題：

-   解決建置錯誤起因於缺少類型、 經過混淆處理的類型，重複的名稱、 類別可見性問題，以及其他情況下，不能解析由 Xamarin.Android 工具。 

-   變更對應，Xamarin.Android 會使用繫結中的不同類型的 Android API C# (比方說，許多開發人員想要將對應 Java`int`常數C#`enum`常數)。

-   移除未使用的型別，不需要繫結。 

-   加入在基礎 Java API 中有沒有對應的類型。 

您可以藉由修改控制項的繫結程序的中繼資料進行部分或所有這些變更。


## <a name="guides"></a>輔助線

下列指南說明可控制繫結程序的中繼資料，並說明如何修改中繼資料來解決這些問題：

-   [Java 繫結中繼資料](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)提供中繼資料會以進入 Java 繫結的概觀。
    它描述完成 Java 繫結程式庫，有時需要的各種手動步驟，並說明如何使用圖形 API 公開的繫結，以更密切遵循.NET 設計指導方針。

-   [Javadoc 將參數命名](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md)說明如何使用所產生的繫結的 Java 專案的 Javadoc 復原 Java 繫結專案中的參數名稱。


 

