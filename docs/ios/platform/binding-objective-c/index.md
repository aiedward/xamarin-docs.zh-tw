---
title: 繫結 iOS 程式庫
description: 本文件說明如何建立C#繫結到 OBJECTIVE-C 程式碼，讓您可以在 Xamarin.iOS 應用程式中使用原生程式庫和 CocoaPods。
ms.prod: xamarin
ms.assetid: EBDC50DC-B44B-4003-AB2B-1EEB868A5E01
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: e599d4f99877e24e06de2c26ed2cafe48526f6f5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122994"
---
# <a name="binding-ios-libraries"></a>繫結 iOS 程式庫

請遵循下列連結以深入了解 Xamarin.iOS 和 Xamarin.Mac 的繫結 Objective C 程式庫和 CocoaPods:

- [**概觀**](~/cross-platform/macios/binding/overview.md) -
  說明繫結的運作方式。
- [**繫結 Objective C 程式庫**](~/cross-platform/macios/binding/objective-c-libraries.md) -
  如何繫結 Objective C 程式庫，Xamarin 專案中使用的指示。
- [**輸入定義參考指南**](~/cross-platform/macios/binding/binding-types-reference.md) -
  說明所有的驅動程式繫結產生處理序的繫結作者可以使用的屬性。

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

目標 Sharpie 是協助啟動程序繫結的第一個階段的命令列工具。
它藉由剖析對應到公用 API 的原生程式庫的標頭檔的運作方式[繫結定義](~/cross-platform/macios/binding/objective-c-libraries.md)（否則手動完成這個程序）。 目標 Sharpie 不會建立繫結本身，但它可以幫助您開始使用 ！

目標 Sharpie 3.0 引進直接繫結 Cocoapods 能力 ！

## <a name="walkthrough---binding-an-ios-objective-c-librarywalkthroughmd"></a>[逐步解說-繫結 iOS OBJECTIVE-C 程式庫](walkthrough.md)

此頁面提供建立 iOS 繫結專案使用開放原始碼的逐步解說[ **InfColorPicker** ](https://github.com/InfinitApps/InfColorPicker) Objective C 專案，做為範例。 **InfColorPicker**程式庫提供可重複使用檢視控制器可讓使用者選取根據其 HSB 表示法，讓使用者更容易使用的色彩選取範圍的色彩。
目標 Sharpie 將用來協助進行繫結程序中。

## <a name="xamarin-university-lightning-lecture"></a>Xamarin University Lightning Lecture

> [!VIDEO https://youtube.com/embed/ZUoPLcmnf1o]

**iOS 繫結的 C/c + +，由[Xamarin University](https://university.xamarin.com/)**

## <a name="related-links"></a>相關連結

- [繫結 Objective-C](~/cross-platform/macios/binding/index.md)
- [Mac 繫結](~/mac/platform/binding.md)
- [Xamarin University 課程： 建置 OBJECTIVE-C 繫結程式庫](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University 課程： 建置目標 Sharpie OBJECTIVE-C 繫結程式庫](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)