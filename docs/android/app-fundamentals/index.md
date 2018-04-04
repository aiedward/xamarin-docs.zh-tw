---
title: 應用程式基本概念
description: 應用程式的核心概念
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: cfb31fa6cac7c4848054cd58a1e144c2ac944262
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="application-fundamentals"></a>應用程式基本概念

此章節提供一些較常見的工作項目或概念，開發人員需要開發 Android 應用程式時應該注意的指南。

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[協助工具選項](~/android/app-fundamentals/accessibility.md)

此頁面描述如何使用 Android 的協助工具應用程式開發介面建置應用程式根據[網頁可及性檢查清單](~/cross-platform/app-fundamentals/accessibility.md)。

##  <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)

本指南說明如何 Android 使用來管理不同版本的 Android 應用程式相容性的應用程式開發介面層級，並說明如何設定 Xamarin.Android 專案設定來部署這些應用程式中的應用程式開發介面層級。 此外，本指南說明如何撰寫處理不同的應用程式開發介面層級的執行階段程式碼，並提供所有 Android API 層級、 版本號碼 （例如 Android 8.0)、 Android （例如 Oreo) 的程式碼名稱和建置版本代碼的參考清單。



##  <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[Android 中的資源](~/android/app-fundamentals/resources-in-android/index.md)

本文介紹如何使用它們的 Android Xamarin.Android 和文件中資源的概念。 它涵蓋了如何使用 Android 應用程式中的資源，以支援應用程式當地語系化，與多個裝置，包括各種不同的螢幕大小和密度。




##  <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[活動生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)

活動是基本的建置組塊的 Android 應用程式，它們可以存在於不同的狀態數目。 活動的生命週期有具現化開始和結束解構而且之間包含許多狀態。 當活動變更狀態時，會呼叫適當的生命週期事件的方法，通知即將發生的狀態變更的活動，並讓它可以執行程式碼，該變更會隨著調整。 這篇文章會檢查活動的生命週期和說明責任活動有每個階段的行為良好、 可靠的應用程式一部分這些狀態變更。

##  <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[當地語系化](~/android/app-fundamentals/localization.md)

本文說明如何將 Xamarin.Android 當地語系化為其他語言中，透過翻譯字串，並提供替代的映像。

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[服務](~/android/app-fundamentals/services/index.md)

本文涵蓋 Android 服務，這是 Android 的元件，可讓要在背景中進行的工作。 它說明適用於服務之不同案例，並顯示如何將它們實作同時執行長時間執行背景工作，以及提供的遠端程序呼叫的介面。

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[廣播接收器](~/android/app-fundamentals/broadcast-receivers.md)

本指南涵蓋如何建立及使用廣播的接收者，回應全系統的廣播，在 Xamarin.Android Android 元件。



##  <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[權限](~/android/app-fundamentals/permissions.md)

您可以使用內建於 Visual Studio for Mac 或 Visual Studio 的工具支援建立和加入 Android 資訊清單的權限。 本文件說明如何在 Visual Studio 和 Xamarin Studio 新增權限。



##  <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[圖形和動畫](~/android/app-fundamentals/graphics-and-animation.md)

Android 會提供一個非常豐富，且不同的架構，以支援 2D 圖形和動畫。 本文件介紹這些架構，並討論如何建立自訂圖形和動畫，並將其用於 Xamarin.Android 應用程式。


##  <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[CPU 架構](~/android/app-fundamentals/cpu-architectures.md)

Xamarin.Android 支援數個 CPU 架構，包括 32 位元和 64 位元的裝置。 本文說明如何為目標的應用程式至一或多個的 Android 支援的 CPU 架構。




##  <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[處理旋轉](~/android/app-fundamentals/handling-rotation.md)

本文說明如何處理在 Xamarin.Android 裝置方向變更。 其中涵蓋如何搭配使用 Android 資源系統如何以程式設計方式處理方向變更自動載入資源的特定裝置方向。 然後它會描述旋轉裝置時，維護狀態的技術。



##  <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Android 的音訊](~/android/app-fundamentals/android-audio.md)

Android 作業系統提供更詳盡的支援多媒體，內含音訊和視訊。 本指南著重於 Android 中的音訊，並涵蓋播放及錄製音訊，使用內建的音訊播放器與記錄器類別及低階的音訊 API。 其中也涵蓋處理音訊事件廣播由其他應用程式，好讓開發人員可以建置行為良好的應用程式。




##  <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[通知](~/android/app-fundamentals/notifications/index.md)

本節說明如何在 Xamarin.Android 中實作本機和遠端的通知。 它描述 Android 的通知的各種 UI 項目，並討論應用程式開發介面的涉及建立和顯示通知。 遠端的通知，和 Google Cloud Messaging Firebase Cloud Messaging 說明。 逐步解說和程式碼範例會包含項目。



##  <a name="touchandroidapp-fundamentalstouchindexmd"></a>[觸控](~/android/app-fundamentals/touch/index.md)

本節說明的概念及實作詳細資料觸控在 Android 上的手勢。 觸控 Api 會導入，後面接著瀏覽的筆勢辨識器的說明。



##  <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[HttpClient 堆疊與 SSL/TLS](~/android/app-fundamentals/http-stack.md)

本章節將說明適用於 Android 的 HttpClient 堆疊與 SSL/TLS 實作的選取器。 這些設定會決定將由 Xamarin.Android 應用程式的 HttpClient 和 SSL/TLS 實作。


##  <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[撰寫應用程式的回應](writing-responsive-apps.md)

這篇文章會討論如何使用執行緒來保持 Xamarin.Android 應用程式移到背景執行緒的長時間執行工作的回應能力。