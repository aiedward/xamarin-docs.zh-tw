---
title: 自訂繫結
description: 您可以通過編輯控制綁定過程的元數據來自定義 Xamarin.Android 綁定。 這些手動修改通常是解決生成錯誤和塑造生成的 API 以便使其與 C#/.NET 更一致所必需的。 這些指南解釋了此元數據的結構、如何修改元數據以及如何使用 JAvaDoc 恢復方法參數的名稱。
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 04f3720d8684129476c955819390e91330a7800a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020647"
---
# <a name="customizing-bindings"></a>自訂繫結

_您可以通過編輯控制綁定過程的元數據來自定義 Xamarin.Android 綁定。這些手動修改通常是解決生成錯誤和塑造生成的 API 以便使其與 C#/.NET 更一致所必需的。這些指南解釋了此元數據的結構、如何修改元數據以及如何使用 JAvaDoc 恢復方法參數的名稱。_

## <a name="overview"></a>概觀

Xamarin.Android 自動執行大部分綁定過程;但是,在某些情況下,需要手動修改才能解決以下問題:

- 解決因缺少類型、混淆類型、重複名稱、類可見性問題以及 Xamarin.Android 工具無法解決的其他情況而導致的生成錯誤。 

- 更改 Xamarin.Android 用於將 Android API 綁定到 C# 中不同類型的映射(`int`例如,許多開發`enum`人員更願意 將 Java 常量映射到 C# 常量)。

- 刪除不需要綁定的未使用的類型。 

- 添加基礎 Java API 中沒有對應的類型。 

您可以透過修改控制綁定過程的中繼資料來進行部分或全部更改。

## <a name="guides"></a>手冊

以下指南描述了控制綁定過程的元數據,並說明如何修改此元數據以解決這些問題:

- [Java 綁定元數據](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)提供了進入 JAVA 綁定的元數據的概述。
    它描述了完成 JAVA 綁定庫時需要的各種手動步驟,並解釋了如何塑造綁定公開的 API,以便更密切地遵循 .NET 設計指南。

- [使用 Javadoc 命名參數](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md)說明如何使用從綁定 Java 專案生成的 Javadoc 在 Java 綁定專案中恢復參數名稱。
