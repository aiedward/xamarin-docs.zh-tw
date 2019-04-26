---
title: Android 建置錯誤 – LinkAssemblies 工作意外失敗
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/07/2019
ms.openlocfilehash: f517aaa770fa7b2f1463954638f0afc95168bf65
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250735"
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Android 建置錯誤 – LinkAssemblies 工作意外失敗

您可能會看到一則錯誤訊息`The "LinkAssemblies" task failed unexpectedly`建置 Xamarin.Android 專案時使用的表單。 啟用連結器時，發生這種的情況 (通常是在*版本*組建，以減少應用程式套件的大小);，它就會發生的 Android 目標不會更新為最新的架構。 (詳細資訊：[Android 的需求的 Xamarin.Forms](~/get-started/requirements.md#android))

此問題的解決方法是確定您已經支援最新 Android SDK 版本，並設定**目標 Framework**到最新已安裝平台。 也建議您設定**目標 Android 版本**最新已安裝平台，而**最低 Android 版本**API 19 或更高版本。 這會被視為支援的設定。

## <a name="setting-in-visual-studio-for-mac"></a>在 Visual Studio for Mac 中的設定

1.  以滑鼠右鍵按一下 Android 專案，然後選取**選項**功能表中。
2.  在 **專案選項**對話方塊中，移至**建置 > 一般**。
3.  設定**使用 Android 版本編譯：(目標 Framework)** 到最新已安裝平台。
4.  在 **專案選項**對話方塊中，移至**建置 > Android 應用程式**。
5.  設定**最低 Android 版本**API 層級 19 或更高版本，而**目標 Android 版本**到最新已安裝您選擇平台 (3)。

## <a name="setting-in-visual-studio"></a>在 Visual Studio 中的設定

1.  以滑鼠右鍵按一下 Android 專案，然後選取**屬性**功能表中。
2.  在 專案屬性中，移至**應用程式**。
3.  設定**使用 Android 版本編譯：(目標 Framework)** 到最新已安裝平台。
4.  在 專案屬性中，移至**Android 資訊清單**。
5.  設定**最低 Android 版本**API 層級 19 或更高版本，而**目標 Android 版本**到最新已安裝您選擇平台 (3)。

一旦您已更新這些設定，請清除並重建您的專案，以確保會挑選您的變更。
