---
title: 系結 iOS 程式庫
description: 本檔說明如何建立C#目標 C 程式碼的系結，讓您可以在 Xamarin iOS 應用程式中使用原生程式庫和 CocoaPods。
ms.prod: xamarin
ms.assetid: EBDC50DC-B44B-4003-AB2B-1EEB868A5E01
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 61d1adfc997b34302f1f89f56653af906ca90135
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022215"
---
# <a name="binding-ios-libraries"></a>系結 iOS 程式庫

請遵循下列連結，以瞭解如何系結 Xamarin. iOS 和 Xamarin 的目標-C 程式庫和 CocoaPods：

- [**總覽**](~/cross-platform/macios/binding/overview.md)-
  說明系結的運作方式。
- 系結[**目標-c 程式庫**](~/cross-platform/macios/binding/objective-c-libraries.md)-
  如何系結目標 C 程式庫以在 Xamarin 專案中使用的指示。
- [**類型定義參考指南**](~/cross-platform/macios/binding/binding-types-reference.md)-
  描述系結作者用來驅動系結產生進程的所有屬性。

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

目標 Sharpie 是一種命令列工具，可協助啟動程式的第一次傳遞。
其運作方式是剖析原生程式庫的標頭檔，將公用 API 對應至系結[定義](~/cross-platform/macios/binding/objective-c-libraries.md)（以手動方式完成的進程）。 目標 Sharpie 不會自行建立系結，但可協助您開始著手！

目標 Sharpie 3.0 引進了直接系結 Cocoapods 的能力！

## <a name="walkthrough---binding-an-ios-objective-c-librarywalkthroughmd"></a>[逐步解說-系結 iOS 目標-C 程式庫](walkthrough.md)

此頁面提供逐步解說，說明如何使用開放原始碼[**InfColorPicker**](https://github.com/InfinitApps/InfColorPicker)目標-C 專案作為範例來建立 iOS 系結專案。 **InfColorPicker**程式庫提供可重複使用的 view controller，可讓使用者根據其 HSB 標記法來選取色彩，讓色彩選擇更方便使用者使用。
目標 Sharpie 將用來協助進行系結程式。

## <a name="video"></a>視訊

> [!VIDEO https://youtube.com/embed/ZUoPLcmnf1o]

**C/C++ video 中的 iOS 系結**

## <a name="related-links"></a>相關連結

- [繫結 Objective-C](~/cross-platform/macios/binding/index.md)
- [Mac 系結](~/mac/platform/binding.md)
