---
title: Xamarin.Android 應用程式基本概念
description: 應用程式的核心概念
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 3ed616340dbb6dd64d85998d84e340ec381f6b54
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864080"
---
# <a name="xamarinandroid-application-fundamentals"></a>Xamarin.Android 應用程式基本概念

此章節提供一些較常見的工作項目或概念，開發人員需要開發 Android 應用程式時應該注意的指南。

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[協助工具選項](~/android/app-fundamentals/accessibility.md)

此頁面說明如何使用 Android 的協助工具 Api 來建置應用程式，根據[協助工具檢查清單](~/cross-platform/app-fundamentals/accessibility.md)。

## <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)

本指南說明 Android 來管理不同版本的 Android 應用程式相容性所使用的 API 層級，並說明如何設定 Xamarin.Android 專案設定來部署這些應用程式中的 API 層級。 此外，本指南說明如何撰寫不同的 API 層級，會處理的執行階段程式碼，並提供所有的 Android API 層級、 版本號碼 （例如 Android 8.0)、 Android 的程式碼名稱 （例如 Oreo) 和建置版本代碼的參考清單。



## <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[在 Android 中的資源](~/android/app-fundamentals/resources-in-android/index.md)

本文介紹如何使用它們的 Xamarin.Android 和文件中的 Android 資源的概念。 它涵蓋了如何使用 Android 應用程式中的資源，以支援應用程式的當地語系化，並包括各種螢幕大小和密度的多個裝置。




## <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[Activity 的生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)

Android 的 App 開發過程中， Activity 是 Android 的 App 在開發上很常使用的一個基本類別，並且能夠搭配產生執行畫面與使用者互動。而 Activity 可以在 Android 的 App 應用中存在於不同的狀態數目。 Activity 的從開始物件化執行到結束的過程中，會含許多狀態的異動。 當 Activtiy 的狀態異動時，會呼叫適當的生命週期的事件方法，通知即將發生的狀態變更的 Activity，並且執行對應該生命週期的事件方法程式碼後，再調整該 Activity 的狀態異動。 本文會說明與驗證 Activity 的生命週期過程中，這些狀態變更該負責 Activity 的每個階段的事件方法，以期建構出達成行為良好、可靠的 App 應用。

## <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[當地語系化](~/android/app-fundamentals/localization.md)

這篇文章說明如何將 Xamarin.Android 當地語系化為其他語言中，藉由將轉譯的字串，並提供替代的映像。

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[服務](~/android/app-fundamentals/services/index.md)

本文章涵蓋 Android 服務，這是允許在背景中完成工作的 Android 元件。 它說明不同的服務很適合的案例，並示範如何實作它們的執行長時間執行背景工作，以及遠端程序呼叫中提供的介面。

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[廣播接收器](~/android/app-fundamentals/broadcast-receivers.md)

本指南涵蓋如何建立和使用廣播的接收器，回應全系統的廣播，在 Xamarin.Android 中的 Android 元件。



## <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[Android 權限](~/android/app-fundamentals/permissions.md)

您可以使用內建於 Visual Studio for Mac 或 Visual Studio 的工具支援建立，並將權限新增至 Android 資訊清單。 本文件說明如何在 Visual Studio 和 Xamarin Studio 中新增權限。



## <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[圖形和動畫](~/android/app-fundamentals/graphics-and-animation.md)

Android 會提供一個非常豐富且多樣化的架構，以支援 2D 圖形和動畫。 本文件介紹這些架構，並討論如何建立自訂圖形和動畫，並將其用於 Xamarin.Android 應用程式。


## <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[CPU 架構](~/android/app-fundamentals/cpu-architectures.md)

Xamarin.Android 支援數種 CPU 架構，包括 32 位元和 64 位元的裝置。 這篇文章說明如何設定目標應用程式以一或多個 Android 支援的 CPU 架構。




## <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[處理旋轉](~/android/app-fundamentals/handling-rotation.md)

本文說明如何處理在 Xamarin.Android 中的裝置方向變更。 它涵蓋了解如何使用 Android 資源系統會自動載入資源的特定裝置方向，以及如何以程式設計方式處理方向變更。 然後，它會描述在旋轉裝置時，維護狀態的技術。



## <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Android 音訊](~/android/app-fundamentals/android-audio.md)

Android OS 可廣泛支援的多媒體，包含音訊和視訊。 本指南著重在 Android 中的音訊，並涵蓋播放及錄製音訊，使用內建的音訊播放器和記錄器類別，以及低階音訊 API。 其中也涵蓋處理音訊廣播由其他應用程式的事件，讓開發人員可以建置行為良好的應用程式。




## <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[通知](~/android/app-fundamentals/notifications/index.md)

本節說明如何在 Xamarin.Android 中實作本機及遠端通知。 它說明 Android 通知的各種不同的 UI 項目，並討論 API 的涉及建立和顯示通知。 遠端通知時，會說明和 Google Cloud Messaging Firebase 雲端通訊。 逐步解說和程式碼範例會包含項目。



## <a name="touchandroidapp-fundamentalstouchindexmd"></a>[觸控](~/android/app-fundamentals/touch/index.md)

本節說明的概念和實作詳細資料上的觸控筆勢 Android。 觸控 Api 導入，且後面接著探勘的筆勢辨識器的說明。



## <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[HttpClient 堆疊與 SSL/TLS](~/android/app-fundamentals/http-stack.md)

本章節會說明適用於 Android 的 HttpClient 堆疊與 SSL/TLS 實作的選取器。 這些設定會決定將由您的 Xamarin.Android 應用程式的 HttpClient 和 SSL/TLS 實作。


## <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[撰寫維持 UI 回應的 App 應用](writing-responsive-apps.md)

本篇文章討論當 App 的程式移到背景執行緒運作長時間執行工作時，如何在 Xamarin.Android 開發上維持 App 應用的 UI 回應性。
