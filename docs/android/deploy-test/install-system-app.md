---
title: 將 Xamarin.Android 安裝為系統應用程式
description: 本指南會討論系統應用程式與使用者應用程式的差異，以及如何將 Xamarin.Android 應用程式安裝為系統應用程式。 本指南適用於自訂 Android ROM 映像的作者。 它不會說明如何建立自訂 ROM。
ms.prod: xamarin
ms.assetid: 0113143B-7D8D-4C4C-B2F5-B966A2E7CE1F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 5a6f950ec7c9af0422beeb2d1af2be602fcaf947
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113803"
---
# <a name="installing-xamarinandroid-as-a-system-app"></a>將 Xamarin.Android 安裝為系統應用程式

_本指南會討論系統應用程式與使用者應用程式的差異，以及如何將 Xamarin.Android 應用程式安裝為系統應用程式。本指南適用於自訂 Android ROM 映像的作者。它不會說明如何建立自訂 ROM。_

## <a name="system-app"></a>系統應用程式

自訂 Android ROM 映像的作者或 Android 裝置的製造商可能會想要在散發 ROM 或裝置時，將 Xamarin.Android 應用程式包含為_系統應用程式_。 系統應用程式是被視為對裝置運行來說非常重要，或是提供自訂 ROM 作者想要持續提供之功能的應用程式。

系統應用程式會安裝在資料夾 **/system/app/** (一個位於檔案系統上的唯讀目錄) 中，並且無法由使用者刪除或移動 (除非使用者具有 Root 存取權)。 相反的，由使用者安裝的應用程式 (通常來自 Google Play 或側載應用程式) 稱為_使用者應用程式_。 使用者應用程式可由使用者刪除，並且在許多案例中可移動到裝置上的不同位置 (例如某種外部儲存空間)。

系統應用程式與使用者應用程式的行為完全相同，但具有下列值得注意的例外狀況：

- 系統應用程式可進行升級，與一般_使用者應用程式_相同。 然而，由於應用程式的複本一律會儲存在 **/system/app/** 中，因此可一律將應用程式復原至原始版本。

- 系統應用程式可獲得授與特定僅限系統但無法由使用者應用程式取得的權限。 僅限系統的權限範例是 [`BLUETOOTH_PRIVILEGED`](https://developer.android.com/reference/android/Manifest.permission.html#BLUETOOTH_PRIVILEGED)，該權限可允許應用程式與藍牙裝置進行配對，而無須任何使用者互動。

您可以將 Xamarin.Android 應用程式作為系統應用程式散發。 除了提供 APK 給自訂 ROM 之外，還有兩個共用程式庫必須手動從 APK 複製到 ROM 映像的檔案系統：**libmonodroid.so** 及 **libmonosgen-2.0.so**。 本指南會說明相關步驟。

## <a name="restrictions"></a>限制

本指南適用於自訂 Android ROM 映像的作者。 它不會說明如何建立自訂 ROM。

本指南假設您熟悉[封裝 Xamarin.Android 的發行 APK](~/android/deploy-test/publishing/index.md)，並了解 Android 應用程式的 [CPU 架構](~/android/app-fundamentals/cpu-architectures.md)。

## <a name="install-a-xamarinandroid-app-as-a-system-app"></a>將 Xamarin.Android 應用程式安裝為系統應用程式

下列步驟描述了如何將 Xamarin.Android 應用程式安裝為系統應用程式。

1. **封裝 Xamarin.Android 應用程式的發行 APK** &ndash; [發佈應用程式](~/android/deploy-test/publishing/index.md)指南中提供關於這個主題的詳細資料。

2. **從 APK 擷取共用程式庫** &ndash; 使用任何 ZIP 公用程式，開啟 APK 檔案並檢查 **/lib/** 資料夾中的內容。 這個資料夾針對每個應用程式支援的_應用程式二進位介面_ (ABI) 都會具有一個子目錄，資料夾中的內容包含所有該特定 ABI 上應用程式所需的共用程式庫：

    ![taskypro.zip 中 armeabi-v7a 資料夾內 .so 檔案的螢幕擷取畫面](install-system-app-images/install-system-app-01.png)

   在先前的螢幕擷取畫面中，只有一個受支援的 ABI (**armeabi-v7a**) 保有兩個應用程式需要的 **.so** 檔案。 請注意，只需要擷取適用於裝置或裝置 ROM 目標架構的 ABI 檔案，也就是請不要將 **x86** 資料夾中的 **.so** 檔案複製到 **armeabi-v7a** 裝置或 ROM。

3. **將 .so 檔案複製到 /system/lib** &ndash; 將在先前步驟中從 APK 擷取的 **.so** 檔案複製到自訂 ROM 上的 **/system/lib/**。

4. **將 APK 檔案複製到 /system/app** &ndash; 最後一個步驟便是將 APK 檔案複製到 ROM 上的 **/system/app** 資料夾。


## <a name="summary"></a>總結

本指南討論_系統應用程式_與_使用者應用程式_的差異，並說明如何將 Xamarin.Android 應用程式安裝為系統應用程式。



## <a name="related-links"></a>相關連結

- [發行應用程式](~/android/deploy-test/publishing/index.md)
- [CPU 架構](~/android/app-fundamentals/cpu-architectures.md)
- [BLUETOOTH_PRIVILEGED](https://developer.android.com/reference/android/Manifest.permission.html#BLUETOOTH_PRIVILEGED)
- [API 管理](https://developer.android.com/ndk~/abis.html)
