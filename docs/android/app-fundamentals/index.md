---
title: Xamarin. Android 應用程式基本概念
description: 核心應用程式概念
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: af7ba83b9026a91028f4ffa9894d564d5ff13eb8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755306"
---
# <a name="xamarinandroid-application-fundamentals"></a>Xamarin. Android 應用程式基本概念

本節提供開發人員在開發 Android 應用程式時需要注意的一些常見工作或概念的指南。

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[協助工具選項](~/android/app-fundamentals/accessibility.md)

此頁面說明如何根據[協助工具檢查清單](~/cross-platform/app-fundamentals/accessibility.md)，使用 Android 協助工具 api 來建立應用程式。

## <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[瞭解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)

本指南說明 Android 如何使用 API 層級來管理不同 Android 版本的應用程式相容性，並說明如何設定 Xamarin Android 專案設定，以在您的應用程式中部署這些 API 層級。 此外，本指南也會說明如何撰寫處理不同 API 層級的執行時間程式碼，並提供所有 Android API 層級的參考清單、版本號碼（例如 Android 8.0）、Android 程式碼名稱（例如 Oreo）和組建版本代碼。

## <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[Android 中的資源](~/android/app-fundamentals/resources-in-android/index.md)

本文介紹 Xamarin 中 Android 資源的概念，並說明如何使用它們。 其中涵蓋如何使用 Android 應用程式中的資源來支援應用程式當地語系化，以及多個裝置，包括各種不同的螢幕大小和密度。

## <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[活動生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)

活動是 Android 應用程式的基本建置組塊，而且它們能以數種不同的狀態存在。 活動生命週期始於具現化、終止於毀損，而且在期間包括許多狀態。 當活動變更狀態時，會呼叫適當的生命週期事件方法，通知活動有即將發生的狀態變更並允許它執行程式碼以適應該變更。 此文章會檢查活動的生命週期，並說明為了成為行為良好的可靠應用程式，活動在這些狀態變更中每個狀態變更的責任。

## <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[當地語系化](~/android/app-fundamentals/localization.md)

本文說明如何藉由翻譯字串和提供替代影像，將 Xamarin 當地語系化為其他語言。

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[服務](~/android/app-fundamentals/services/index.md)

本文涵蓋 Android 服務，這是 Android 元件，可讓您在背景中完成工作。 其中說明服務適用的各種不同案例，並示範如何同時執行這些作業，以進行長時間執行的背景工作，以及提供介面來進行遠端程序呼叫。

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[廣播接收器](~/android/app-fundamentals/broadcast-receivers.md)

本指南涵蓋如何在 Xamarin 中建立和使用廣播接收器，這是回應全系統廣播的 Android 元件。

## <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[Permissions](~/android/app-fundamentals/permissions.md)

您可以使用內建在 Visual Studio for Mac 或 Visual Studio 中的工具支援，來建立和新增 Android 資訊清單的許可權。 本檔說明如何在 Visual Studio 和 Xamarin Studio 中新增許可權。

## <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[圖形和動畫](~/android/app-fundamentals/graphics-and-animation.md)

Android 提供非常豐富且多樣化的架構，可支援2D 圖形和動畫。 本檔將介紹這些架構，並討論如何建立自訂圖形和動畫，並在 Xamarin Android 應用程式中使用它們。

## <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[CPU 架構](~/android/app-fundamentals/cpu-architectures.md)

Xamarin 支援數種 CPU 架構，包括32位和64位的裝置。 本文說明如何將應用程式的目標設為一或多個支援 Android 的 CPU 架構。

## <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[處理旋轉](~/android/app-fundamentals/handling-rotation.md)

本文說明如何處理 Xamarin. Android 中的裝置方向變更。 其中涵蓋如何使用 Android 資源系統，自動載入特定裝置方向的資源，以及如何以程式設計方式處理方向變更。 然後，它會描述裝置旋轉時維護狀態的技術。

## <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Android 音訊](~/android/app-fundamentals/android-audio.md)

Android OS 為多媒體提供廣泛的支援，同時包含音訊和影片。 本指南著重于 Android 中的音訊，並說明如何使用內建的音訊播放機和錄製器類別，以及低層級的音訊 API 來播放和錄製音訊。 它也涵蓋了使用其他應用程式廣播的音訊事件，讓開發人員可以建立效能良好的應用程式。

## <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[通知](~/android/app-fundamentals/notifications/index.md)

本節說明如何在 Xamarin 中執行本機和遠端通知。 其中說明 Android 通知的各種 UI 元素，並討論與建立和顯示通知相關的 API。 針對遠端通知，會說明 Google 雲端通訊和 Firebase 雲端通訊。 包含逐步解說和程式碼範例。

## <a name="touchandroidapp-fundamentalstouchindexmd"></a>[觸控](~/android/app-fundamentals/touch/index.md)

本節說明如何在 Android 上執行觸控手勢的概念和詳細資料。 觸控 Api 的引進和說明，接著流覽手勢辨識器。

## <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[HttpClient 堆疊與 SSL/TLS](~/android/app-fundamentals/http-stack.md)

本節說明適用于 Android 的 HttpClient 堆疊和 SSL/TLS 實作為選取器。 這些設定會決定您的 Xamarin Android 應用程式將使用的 HttpClient 和 SSL/TLS 執行。

## <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[撰寫回應式應用程式](writing-responsive-apps.md)

此文章討論如何使用執行緒處理功能將長時間執行的工作移至背景執行緒，來維持 Xamarin.Android 應用程式的回應性。
