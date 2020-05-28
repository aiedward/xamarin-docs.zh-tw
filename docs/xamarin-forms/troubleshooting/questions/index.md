---
title: Xamarin.Forms常見問題
ms.topic: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: edd6cfefe18ff3d5cc97ec58f3bce867f11df7c8
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135872"
---
# <a name="xamarinforms-frequently-asked-questions"></a>Xamarin.Forms常見問題

## <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-packageupdate-forms-templatemd"></a>[我可以將 Xamarin.Forms 預設範本更新為較新的 NuGet 套件嗎？](update-forms-template.md)
本指南會使用 Xamarin.Forms .NET Standard 程式庫範本作為範例，但相同的一般方法也適用于共用的 Xamarin.Forms 專案範本。

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-filesforms-xaml-designermd"></a>[為什麼 xaml 檔案的 Visual Studio XAML 設計工具不適用 Xamarin.Forms ？](forms-xaml-designer.md)
Xamarin.Forms目前不支援 XAML 檔案的視覺化設計工具。

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedly"></a>[Android 組建錯誤： "LinkAssemblies" 工作意外失敗](android-linkassemblies-error.md)
`The "LinkAssemblies" task failed unexpectedly`當您建立使用表單的 Xamarin. Android 專案時，可能會看到錯誤訊息。 當連結器作用中（通常是在*發行*組建以縮減應用程式套件的大小時）時，就會發生這種情況;而且會發生這種情況，是因為 Android 目標並未更新為最新的架構。 

## <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-errormaps-compiletodalvik-errormd"></a>[「為什麼我這麼做 Xamarin.Forms 。地圖 Android 專案失敗，發生因為 COMPILETODALVIK：非預期的最上層錯誤？」](maps-compiletodalvik-error.md)
此錯誤可能會出現在 Visual Studio for Mac 的錯誤 pad 中，或在 Visual Studio 的 [組建輸出] 視窗中。在 Android 專案中使用 Xamarin.Forms 。繪. 最常見的方法是增加您的 Xamarin Android 專案的 JAVA 堆積大小。
