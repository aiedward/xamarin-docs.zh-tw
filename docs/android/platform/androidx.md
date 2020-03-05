---
title: AndroidX
description: 如何使用 AndroidX 開始開發使用 Xamarin 的應用程式。
ms.assetid: CC21BD28-EF67-4132-8C0D-CF25B78BA78B
author: JonDouglas
ms.author: jodou
ms.date: 02/20/2020
ms.openlocfilehash: ad6ea2f68fc01183f7ed42e85094f6be5fb3d9f9
ms.sourcegitcommit: 2836f2003a5b745b042ee6003a3d6a11b9139e44
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "78291622"
---
# <a name="androidx-with-xamarin"></a>以 Xamarin AndroidX

_如何使用 AndroidX 開始開發使用 Xamarin 的應用程式。_

AndroidX 是原始 Android 支援程式庫的重大改進，已不再維護。 **AndroidX**套件會提供功能同位，以及您可以在 android 應用程式中使用的新程式庫，完全取代 Android 支援程式庫。

AndroidX 包含下列功能：

- AndroidX 內的所有套件現在都具有一致的命名空間，從 `androidx`開始。 這表示所有 Android 支援程式庫套件會對應至相對應的 `androidx.*` 套件。
- `androidx` 套件會分開維護和更新。 這表示您可以個別更新 AndroidX 程式庫。
- 從 Android 支援程式庫的 v28，將不再有任何版本。 所有開發都會改為包含在 `androidx` 中。

![AndroidX 標誌](~/android/platform/androidx-images/AndroidXLogo.png)

## <a name="requirements"></a>需求

下列清單是在以 Xamarin 為基礎的應用程式中使用 AndroidX 功能的必要條件：

- **Visual Studio** Visual Studio 2019 16.4 版或更新版本的 Windows update。 在 macOS 上，更新為 Visual Studio 2019 （適用于 Mac 8.4 版或更新版本）。
- **Xamarin** . android 10.0 或更新版本必須與 Visual Studio 一起安裝（Xamarin. android 會在 Windows 上自動安裝為**使用 .net**的行動裝置開發工作負載，並安裝為**Visual Studio for Mac 安裝程式**的一部分）
- **JAVA 開發人員套件**-Xamarin. Android 10.0 開發需要 JDK 8。 Microsoft 的 OpenJDK 散發會自動安裝為 Visual Studio 的一部分。
- **Android SDK** Android SDK API 28 或更高版本，必須透過 Android SDK 管理員進行安裝。

## <a name="get-started"></a>開始使用

您可以在 Android 專案內包含任何[AndroidX NuGet 套件](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22)，以開始使用 AndroidX。 深入瞭解如何在[Visual Studio](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio)或[Visual Studio for Mac](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio-mac)中安裝和使用套件

## <a name="behavior-changes"></a>行為變更

由於 AndroidX 是 Android 支援程式庫的重新設計，因此包含的遷移步驟會影響以 Android 支援程式庫建立的 Android 應用程式。

### <a name="package-name-change"></a>封裝名稱變更
舊版和新封裝之間的封裝名稱已經變更。 您可以在下面看到這些變更的範例：

| 多久                    | 新增                    |
| ---------------------- | ---------------------- |
| android. 支援。 * *     | androidx.@             |
| android. 設計。 * *      | [.com]。@ |
| 支援。測試. * * | androidx。@       |
| android. * *        | androidx.@             |
| android. 的保存空間。 * * | androidx。@ |
| android. 的持續性。 * * | androidx. sqlite。@ |

如需封裝命名的詳細資訊，[請參閱下列檔](https://developer.android.com/jetpack/androidx/migrate#artifact_mappings)。

## <a name="migration-tooling"></a>遷移工具

您會想要知道您的應用程式有三個遷移步驟。

1. 如果您的應用程式包含**Android 支援程式庫命名空間，而您想要將它們遷移至 AndroidX 命名空間**，您可以使用我們的「**遷移至 AndroidX** IDE」工具來處理大部分的命名空間案例。 

在 Visual Studio 2019 內，透過 工具  **> 選項 > Xamarin > Android 設定** 中啟用**AndroidX 遷移**工具（您可以在 Visual Studio for Mac 上略過此步驟）。

![啟用 AndroidX 遷移](~/android/platform/androidx-images/EnableAndroidXMigrator.png)

以滑鼠右鍵按一下您的專案，並**遷移至 AndroidX**。

![遷移至 AndroidX](~/android/platform/androidx-images/MigrateToAndroidX.png)

> [!NOTE] 
> 您必須針對工具未涵蓋的案例進行一些手動的命名空間變更。 雖然我們會為您對應正確的套件，但建議您先參閱官方成品對應和[類別](https://developer.android.com/jetpack/androidx/migrate/class-mappings)[對應，以](https://developer.android.com/jetpack/androidx/migrate/artifact-mappings)協助您的專案遷移。

2. 如果您的應用程式包含尚未**遷移至 AndroidX 命名空間的任何**相依性，您就必須使用[Android 支援程式庫來 AndroidX 遷移封裝。](https://www.nuget.org/packages/Xamarin.AndroidX.Migration)
3. 如果您的應用程式不**包含任何需要 AndroidX 命名空間遷移**的相依性，您[現在可以在 NuGet 上使用 AndroidX 程式庫](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22)。

## <a name="troubleshooting"></a>疑難排解

- AndroidX 內的某些架構套件會與支援程式庫版本發生衝突。 若要修正此問題，您應該使用這些套件的 AndroidX 版本，並移除支援程式庫版本。 例如，如果您在專案中參考 `Xamarin.Android.Arch.Work.Runtime`，它會與新加入 `AndroidX.Work` 封裝的類型相衝突。

## <a name="summary"></a>摘要

本文引進了 AndroidX，並說明如何安裝和設定最新的工具和套件，以使用 AndroidX 進行 Android 開發。 它提供了 AndroidX 的總覽。 其中包含 API 檔和 Android 開發人員主題的連結，可協助您開始使用 AndroidX 建立應用程式。 它也會反白顯示可能影響現有應用程式的最重要 AndroidX 行為變更和疑難排解主題。

## <a name="related-links"></a>相關連結

- [AndroidX 簡介 |Xamarin 節目](https://www.youtube.com/watch?v=M_l3RjTev5A)
- [AndroidX](https://developer.android.com/jetpack/androidx)
- [Xamarin AndroidX GitHub 存放庫](https://github.com/xamarin/AndroidX)
- [Xamarin AndroidX 遷移 GitHub 存放庫](https://github.com/xamarin/XamarinAndroidXMigration)
