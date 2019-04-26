---
title: Xamarin.Forms 常見問題集
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 89364175-53BA-4A09-B3E2-44AC67DD971C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 24e2ae456e478585f30aa704917f66bb0bf11da9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61247786"
---
# <a name="xamarinforms-frequently-asked-questions"></a>Xamarin.Forms 常見問題集

## <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-packageupdate-forms-templatemd"></a>[我可以將 Xamarin.Forms 預設範本更新為較新的 NuGet 套件嗎？](update-forms-template.md)
本指南使用 Xamarin.Forms.NET Standard 程式庫範本做為範例，但相同的一般方法也適用於 Xamarin.Forms 共用專案範本。

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-filesforms-xaml-designermd"></a>[為什麼 Visual Studio XAML 設計工具不適用於 Xamarin.Forms XAML 檔案？](forms-xaml-designer.md)
Xamarin.Forms 目前不支援視覺化設計工具的 XAML 檔案。

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedlyandroid-linkassemblies-errormd"></a>[Android 建置錯誤："LinkAssemblies" 工作意外失敗](android-linkassemblies-error.md)
您可能會看到一則錯誤訊息`The "LinkAssemblies" task failed unexpectedly`建置 Xamarin.Android 專案時使用的表單。 啟用連結器時，發生這種的情況 (通常是在*版本*組建，以減少應用程式套件的大小);，它就會發生的 Android 目標不會更新為最新的架構。 

## <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-errormaps-compiletodalvik-errormd"></a>[「 為什麼我的 Xamarin.Forms.Maps Android 專案失敗 compiletodalvik:未預期的最上層錯誤嗎？ 」](maps-compiletodalvik-error.md)
此錯誤可能會出現在 Visual studio 的 [錯誤] 面板中，for Mac 或 Visual Studio; 的 [建置輸出] 視窗中在使用 Xamarin.Forms.Maps Android 專案。 它最常見的解決方式為您的 Xamarin.Android 專案的 Java 堆積大小增加。
