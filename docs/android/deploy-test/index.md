---
title: 測試、最佳化及部署 Xamarin.Android 應用程式
description: 本節所包含的指南說明如何測試應用程式、將其效能最佳化、準備發行、使用憑證進行簽署，以及將它發佈至應用程式市集
ms.prod: xamarin
ms.assetid: 568C0B85-EFF3-AF6F-5605-95055193D367
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 86447c2338b8dd43e37170fa547883e47cbf6b4c
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864218"
---
# <a name="deployment-and-testing"></a>部署及測試

本節所包含的指南說明如何測試應用程式、最佳化其效能、準備發行、使用憑證進行簽署，以及將它發佈至應用程式市集。


## <a name="application-package-sizesapp-package-sizemd"></a>[應用程式的套件大小](app-package-size.md)

這篇文章將探討 Xamarin.Android 應用程式套件的組成組件，以及在開發過程的偵錯和發行階段所能進行有效率套件部署的相關策略。

## <a name="building-appsbuilding-appsindexmd"></a>[建置應用程式](building-apps/index.md)

此小節描述建置流程的運作方式，並說明如何建置 ABI 特定 APK。

## <a name="command-line-emulatorcommand-line-emulatormd"></a>[命令列模擬器](command-line-emulator.md)

這篇文章簡要地說明如何透過命令列啟動模擬器。

## <a name="debuggingandroiddeploy-testdebuggingindexmd"></a>[偵錯](~/android/deploy-test/debugging/index.md)

本節中的指南可協助您使用 Android Emulator、實際 Android 裝置和偵錯記錄檔，來為應用程式進行偵錯。

## <a name="setting-the-debuggable-attributeandroiddeploy-testdebuggable-attributemd"></a>[設定可偵錯的屬性](~/android/deploy-test/debuggable-attribute.md)

這篇文章說明如何設定可偵錯的屬性，以便讓 `adb` 之類的工具能夠與 JVM 通訊。

## <a name="environmentenvironmentmd"></a>[環境](environment.md)

本文描述 Xamarin.Android 的執行環境和會影響程式執行的 Android 系統屬性。

## <a name="gdbgdbmd"></a>[GDB](gdb.md)

這篇文章說明如何使用 `gdb` 針對 Xamarin.Android 應用程式進行偵錯。

## <a name="installing-a-system-appinstall-system-appmd"></a>[安裝系統應用程式](install-system-app.md)

此指南說明如何在 Android 裝置上將 Xamarin.Android 應用程式安裝作為系統應用程式，或作為自訂 ROM 的一部分。

## <a name="linking-on-androidlinkermd"></a>[在 Android 上連結](linker.md)

這篇文章探討 Xamarin.Android 所使用的連結程序，以減少應用程式的最終大小。 文中描述可執行的各種連結層級，並提供一些指引和疑難排解建議，以減少使用連結器時可能會產生的錯誤。

## <a name="xamarinandroid-performanceandroiddeploy-testperformancemd"></a>[Xamarin.Android 效能](~/android/deploy-test/performance.md)

有許多技巧可增加利用 Xamarin.Android 建置之應用程式的效能。 這些技巧可共同大幅減少由 CPU 所執行的工作量，和由應用程式所耗用的記憶體數量。

## <a name="profiling-android-appsandroiddeploy-testprofilingmd"></a>[分析 Android 應用程式](~/android/deploy-test/profiling.md)

本指南說明如何使用分析工具來檢查 Android 應用程式的效能和記憶體使用量。


## <a name="preparing-an-application-for-releaseandroiddeploy-testrelease-prepindexmd"></a>[準備可供發行的應用程式](~/android/deploy-test/release-prep/index.md)

應用程式完成編碼和測試之後，必須準備可供散發的套件。 準備此套件的第一項工作是建置可供發行的應用程式，主要是設定一些應用程式屬性。

## <a name="signing-the-android-application-packageandroiddeploy-testsigningindexmd"></a>[簽署 Android 應用程式套件](~/android/deploy-test/signing/index.md)

了解如何建立 Android 簽署身分識別、建立適用於 Android 應用程式的新簽署憑證，並使用此簽署憑證來簽署應用程式。 此外，本主題還說明如何將應用程式匯出至磁碟，以進行「臨機操作」  散發。 產生的 APK 可以側載到 Android 裝置，而不需透過應用程式市集 。

## <a name="publishing-an-applicationandroiddeploy-testpublishingindexmd"></a>[發行應用程式](~/android/deploy-test/publishing/index.md)

這系列文章說明公開散發以 Xamarin.Android 建立之應用程式的步驟。 散發可以透過電子郵件、私人網頁伺服器、Google Play 或適用於 Android 的 Amazon App Store 等管道進行。
