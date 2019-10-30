---
title: 自訂繫結
description: 您可以藉由編輯控制系結程式的中繼資料來自訂 Xamarin. Android 系結。 這些手動修改通常需要用來解決組建錯誤，並形成產生的 API，使其與C#/.NET. 更一致 這些指南說明此中繼資料的結構、如何修改中繼資料，以及如何使用 JAVADoc 來復原方法參數的名稱。
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 04f3720d8684129476c955819390e91330a7800a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020647"
---
# <a name="customizing-bindings"></a>自訂繫結

_您可以藉由編輯控制系結程式的中繼資料來自訂 Xamarin. Android 系結。這些手動修改通常需要用來解決組建錯誤，並形成產生的 API，使其與C#/.NET. 更一致這些指南說明此中繼資料的結構、如何修改中繼資料，以及如何使用 JAVADoc 來復原方法參數的名稱。_

## <a name="overview"></a>總覽

Xamarin 會自動化大部分的系結程式;不過，在某些情況下，需要手動修改才能解決下列問題：

- 解決因遺漏類型、模糊類型、重複名稱、類別可見度問題，以及無法由 Xamarin 工具解決的其他情況而造成的組建錯誤。 

- 變更 Xamarin 用來將 Android API 系結至不同類型的對應C# （例如，許多開發人員偏好將 JAVA `int`常數對應至C#`enum`常數）。

- 移除不需要系結的未使用型別。 

- 在基礎 JAVA API 中加入沒有對應的類型。 

您可以藉由修改控制系結程式的中繼資料，來進行部分或全部的變更。

## <a name="guides"></a>輔助線

下列指南說明控制系結程式的中繼資料，並說明如何修改此中繼資料來解決這些問題：

- [JAVA 系結中繼資料](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)提供進入 JAVA 系結之中繼資料的總覽。
    其中說明了完成 JAVA 系結程式庫有時需要的各種手動步驟，並說明如何塑造系結所公開的 API，以更密切地遵循 .NET 設計方針。

- [使用 JAVAdoc 具名引數](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md)說明如何使用系結的 java 專案所產生的 JAVAdoc 來復原 JAVA 系結專案中的參數名稱。
