---
title: "常見問題集"
ms.topic: article
ms.prod: xamarin
ms.assetid: 89364175-53BA-4A09-B3E2-44AC67DD971C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 95728bf6bf1009db1cc834bf1d9d0be6a8fc5ef5
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="frequently-asked-questions"></a>常見問題集


## <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-packageupdate-forms-templatemd"></a>[可以加入較新的 NuGet 套件更新 Xamarin.Forms 預設範本嗎？](update-forms-template.md)
本指南使用 Xamarin.Forms PCL 範本做為範例，但相同的一般方法也可用於 Xamarin.Forms 共用專案範本。 

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-filesforms-xaml-designermd"></a>[為什麼 Visual Studio XAML 設計工具不適用於 Xamarin.Forms XAML 檔案？](forms-xaml-designer.md)
Xamarin.Forms 目前不支援 XAML 檔案的視覺化設計工具。

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedlyandroid-linkassemblies-errormd"></a>[Android 的建置錯誤: 「 LinkAssemblies"工作意外失敗](android-linkassemblies-error.md)
您可能會看到一則錯誤訊息`The "LinkAssemblies" task failed unexpectedly`當建置 Xamarin.Android 專案時，使用表單。 這會連結器為作用中 (通常在*發行*組建，以減少應用程式套件的大小); 而且是發生在因為 Android 目標不更新為最新的架構。 


## <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-errormaps-compiletodalvik-errormd"></a>[「 我 Xamarin.Forms.Maps Android 專案失敗並 COMPILETODALVIK 的原因： 未預期的最高層級錯誤？ 」](maps-compiletodalvik-error.md)
可能會出現此錯誤在錯誤輸入板中的 Visual Studio for Mac，或在建置輸出視窗中的 Visual Studio;在使用 Xamarin.Forms.Maps Android 專案。 它最常見的解決方式增加 Xamarin.Android 專案 Java 堆積大小。

