---
title: Android 建置錯誤 – LinkAssemblies 意外失敗的工作
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: de6e0b66cac688955d27ba2d0165d5a059d36c38
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30789536"
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Android 建置錯誤 – LinkAssemblies 意外失敗的工作

您可能會看到一則錯誤訊息`The "LinkAssemblies" task failed unexpectedly`當建置 Xamarin.Android 專案時，使用表單。 這會連結器為作用中 (通常在*發行*組建，以減少應用程式套件的大小); 而且是發生在因為 Android 目標不更新為最新的架構。 (詳細資訊： [Android 需求的 Xamarin.Forms](~/xamarin-forms/get-started/installation.md#android))

此問題的解決方法是確定您有最新支援 Android SDK 版本，並設定**目標 Framework**至**使用最新安裝的平台**。 也建議您設定**目標 Android 版本**至**使用目標 Framework 版本**和**最低的 Android 版本**API 15 為或更高。 這是支援的組態。

## <a name="setting-in-visual-studio-for-mac"></a>Visual Studio 中適用於 Mac 的設定

1.  以滑鼠右鍵按一下的 Android 專案。
2.  移至**建置 > 一般 > 目標 Framework**。
3.  設定**目標 Framework： 使用最新安裝的平台**。
4.  仍在 [專案] 選項，請移至**建置 > Android 應用程式**。
5.  設定**Android 最小版本**到 15 個或更高的應用程式開發介面層級 &**目標 Android 版本**至**Automatic-使用目標 framework 版本**。

## <a name="setting-in-visual-studio"></a>在 Visual Studio 中設定

1.  以滑鼠右鍵按一下的 Android 專案。
2.  移至**應用程式**專案選項中。
3.  設定**使用 Android 版本編譯** & **目標 Android 版本**設定**使用最新的平台** / **使用使用 SDK 版本編譯**。
4.  設定**最小值 Android 目標**設定 API 15 為或更高。

一旦您已更新這些設定，請清除並重建專案，以確保會挑選您的變更。
