---
title: "繫結 iOS 的程式庫"
description: "如何讓 iOS 原生程式庫 （CocoaPods） 中的 Xamarin 應用程式存取。"
ms.topic: article
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 3afe1a03299e600502d49b1db039af4c6642e131
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="binding-ios-libraries"></a>繫結 iOS 的程式庫

_如何讓 iOS 原生程式庫 （CocoaPods） 中的 Xamarin 應用程式存取。_

請遵循下列連結，以了解 Xamarin.iOS 和 Xamarin.Mac Objective C 程式庫和 CocoaPods 繫結：

- [**概觀**](~/cross-platform/macios/binding/overview.md) -
  說明繫結的運作方式。
- [**繫結 Objective C 程式庫**](~/cross-platform/macios/binding/objective-c-libraries.md) -
  如何繫結於 Xamarin 專案中使用的 Objective C 程式庫的指示。
- [**輸入定義參考指南**](~/cross-platform/macios/binding/binding-types-reference.md) -
  描述所有繫結至磁碟機繫結產生程序的作者可以使用的屬性。

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

目標 Sharpie 是協助啟動繫結的第一次傳遞的命令列工具。
可藉由剖析對應到公用 API 的原生程式庫的標頭檔來運作[繫結定義](~/cross-platform/macios/binding/objective-c-libraries.md)（否則手動完成此程序）。 目標 Sharpie 不會單獨使用時，建立繫結，但它可以幫助您開始使用 ！

目標的 Sharpie 3.0 引進了直接繫結 Cocoapods 能力 ！

## <a name="walkthrough---binding-an-ios-objective-c-librarywalkthroughmd"></a>[逐步解說-繫結 iOS Objective C 程式庫](walkthrough.md)

此頁面提供的逐步解說使用開放原始碼 iOS 繫結專案建立[ **InfColorPicker** ](https://github.com/InfinitApps/InfColorPicker) Objective C 專案做為範例。 **InfColorPicker**程式庫提供可重複使用的檢視控制站可讓使用者選取根據其 HSB 表示法，讓更方便使用的色彩選擇的色彩。
目標 Sharpie 將用來協助進行繫結程序。



## <a name="related-links"></a>相關連結

- [繫結 Objective-C](~/cross-platform/macios/binding/index.md)
- [Mac 繫結](~/mac/platform/binding.md)