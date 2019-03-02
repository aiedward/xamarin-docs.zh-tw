---
title: Android 建置錯誤 – LinkAssemblies 工作意外失敗
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 552ad4d2de6e2560dc4301a9b5cc0ce6a5edb348
ms.sourcegitcommit: d62732ce6f3f9d8dc929d72d4acac3e592cba073
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/01/2019
ms.locfileid: "57197105"
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Android 建置錯誤 – LinkAssemblies 工作意外失敗

您可能會看到一則錯誤訊息`The "LinkAssemblies" task failed unexpectedly`建置 Xamarin.Android 專案時使用的表單。 啟用連結器時，發生這種的情況 (通常是在*版本*組建，以減少應用程式套件的大小);，它就會發生的 Android 目標不會更新為最新的架構。 (詳細資訊：[Android 的需求的 Xamarin.Forms](~/get-started/requirements.md#android))

此問題的解決方法是確定您已經支援最新 Android SDK 版本，並設定**目標 Framework**要**最新安裝的使用平台**。 也建議您設定**目標 Android 版本**要**使用目標 Framework 版本**並**最低 Android 版本**為 API 15 或更高版本。 這會被視為支援的設定。

## <a name="setting-in-visual-studio-for-mac"></a>在 Visual Studio for Mac 中的設定

1.  以滑鼠右鍵按一下 Android 專案。
2.  移至**建置 > 一般 > 目標 Framework**。
3.  設定**Framework 為目標：使用最新安裝的平台**。
4.  仍在專案選項 中，移至**建置 > Android 應用程式**。
5.  設定**最低 Android 版本**到 15 個或更高版本的 API 層級 &**目標 Android 版本**來**自動-使用目標 framework 版本**。

## <a name="setting-in-visual-studio"></a>在 Visual Studio 中的設定

1.  以滑鼠右鍵按一下 Android 專案。
2.  移至**應用程式**專案選項中。
3.  設定**使用 Android 版本編譯** & **目標 Android 版本**設定**使用最新的平台** / **使用使用 SDK 版本編譯**。
4.  設定**最低 Android 目標**設定為 API 19 或更高。

一旦您已更新這些設定，請清除並重建您的專案，以確保會挑選您的變更。
