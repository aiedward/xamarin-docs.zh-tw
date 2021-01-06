---
title: Xamarin.Forms 常見問題
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 89364175-53BA-4A09-B3E2-44AC67DD971C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e520d222029e0d29a25f16aacfb6273da52cbf9e
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940209"
---
# <a name="no-locxamarinforms-frequently-asked-questions"></a>Xamarin.Forms 常見問題

## <a name="how-do-i-migrate-my-app-to-no-locxamarinforms-50"></a>[如何? 將我的應用程式遷移至 Xamarin.Forms 5.0？](forms5-migration.md)

將應用程式遷移至 Xamarin.Forms 5.0 需要知道其重大變更。

## <a name="can-i-update-the-no-locxamarinforms-default-template-to-a-newer-nuget-package"></a>[我可以將 Xamarin.Forms 預設範本更新為較新的 NuGet 套件嗎？](update-forms-template.md)

本指南使用 Xamarin.Forms .NET Standard 程式庫範本作為範例，但相同的一般方法也適用于 Xamarin.Forms 共用的專案範本。

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-no-locxamarinforms-xaml-files"></a>[為什麼 Visual Studio XAML 設計工具不適用於 Xamarin.Forms xaml 檔案？](forms-xaml-designer.md)

Xamarin.Forms 目前不支援 XAML 檔案的視覺化設計工具。

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedly"></a>[Android 組建錯誤： "LinkAssemblies" 工作意外失敗](android-linkassemblies-error.md)

`The "LinkAssemblies" task failed unexpectedly`當您建立使用表單的 Xamarin. Android 專案時，可能會看到錯誤訊息。 當連結器為作用中時，就會發生這種情況 (通常是在 *發行* 組建中，以減少應用程式封裝的大小) ;因為 Android 目標未更新為最新的架構，所以會發生這種情況。

## <a name="why-does-my-no-locxamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-error"></a>[「我 Xamarin.Forms 的Maps Android 專案失敗，發生因為 COMPILETODALVIK：未預期的最上層錯誤？」](maps-compiletodalvik-error.md)

這項錯誤可能會出現在 Visual Studio for Mac 的錯誤面板或 Visual Studio 的 [組建輸出] 視窗中。使用的 Android 專案 Xamarin.Forms 。地圖。 最常見的解決方式是為您的 Xamarin Android 專案增加 JAVA 堆積大小。
