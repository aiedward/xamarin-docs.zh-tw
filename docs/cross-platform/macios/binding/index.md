---
title: 繫結 Objective C
description: 本檔提供各種指南的連結，說明如何建立C#目標 C 程式碼的系結，讓開發人員能夠在 Xamarin 應用程式中使用現成的程式庫。
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
author: conceptdev
ms.author: crdun
ms.date: 01/25/2016
ms.openlocfilehash: 8975bb899127d0761359bec695cd084f89c12972
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290564"
---
# <a name="binding-objective-c"></a>繫結 Objective C

本章節包含各種檔，其中涵蓋建立對目標 C 程式庫的系結，因此可以從C#以 Xamarin 或 xamarin 建立的應用程式呼叫。

## <a name="overviewcross-platformmaciosbindingoverviewmd"></a>[概觀](~/cross-platform/macios/binding/overview.md)

本檔包含系結髮生方式的部分內部。 這是包含一些技術資訊的先進檔。

## <a name="binding-objective-c-librariescross-platformmaciosbindingobjective-c-librariesmd"></a>[繫結 Objective-C 程式庫](~/cross-platform/macios/binding/objective-c-libraries.md)

本檔說明用來建立C#目標 c api 系結的程式，以及如何將目標 c 中的慣用語對應至 .net 中使用的慣用語。
如果您只是要系結 C Api，您應該使用標準 .NET 機制來執行此程式，也就是 P/Invoke 架構。

## <a name="binding-definition-reference-guidecross-platformmaciosbindingbinding-types-referencemd"></a>[系結定義參考指南](~/cross-platform/macios/binding/binding-types-reference.md)

這是參考指南，描述系結作者可用來驅動系結產生進程的所有屬性。


## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

目標 Sharpie 是一種命令列工具，可協助啟動程式的第一次傳遞。 其運作方式是剖析原生程式庫的標頭檔，將公用 API 對應至系結[定義](~/cross-platform/macios/binding/objective-c-libraries.md)（也可以手動完成的進程）。

## <a name="ios"></a>iOS

除了下列範例之外，[IOS 系結][頁面](~/ios/platform/binding-objective-c/index.md)也會連結回到這些常見的系結資源。

### <a name="walkthrough-binding-an-objective-c-libraryiosplatformbinding-objective-cwalkthroughmd"></a>[逐步解說：系結目標-C 程式庫](~/ios/platform/binding-objective-c/walkthrough.md)

本文提供逐步解說，說明如何使用開放原始碼[InfColorPicker](https://github.com/InfinitApps/InfColorPicker)目標-C 專案作為範例來建立系結專案。 InfColorPicker 程式庫提供可重複使用的 view controller，可讓使用者根據其 HSB 標記法來選取色彩，讓色彩選擇更方便使用者使用。 目標 Sharpie 將用來協助進行系結程式。

### <a name="binding-sampleshttpsgithubcommonomonotouch-bindings"></a>[系結範例](https://github.com/mono/monotouch-bindings)

當建立新的系結專案時，可以用來做為參考的協力廠商系結集合。

## <a name="mac"></a>Mac

在過去， [Mac 系](~/mac/platform/binding.md)結已經是非常手動的程式。 目前有[可下載](https://forums.xamarin.com/discussion/59760/xamarin-mac-binding-project-preview)的 Mac 系結專案支援，供未來的 Visual Studio for Mac 版本使用。

## <a name="related-links"></a>相關連結

- [iOS 系結](~/ios/platform/binding-objective-c/index.md)
- [Mac 系結](~/mac/platform/binding.md)
