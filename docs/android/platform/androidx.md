---
title: AndroidX
description: 如何使用 AndroidX 開始使用開發應用程式。
ms.assetid: CC21BD28-EF67-4132-8C0D-CF25B78BA78B
author: JonDouglas
ms.author: jodou
ms.date: 02/20/2020
ms.openlocfilehash: 05736fda26e5fd391d977481b340b3744f224c2f
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454204"
---
# <a name="androidx-with-xamarin"></a>Xamarin 的 AndroidX

_如何使用 AndroidX 開始使用開發應用程式。_

AndroidX 是原始 Android 支援程式庫的重大改進，不再維持。 **AndroidX** 套件藉由提供可在 android 應用程式中使用的功能同位和新程式庫，來完全取代 android 支援程式庫。

AndroidX 包含下列功能：

- AndroidX 內的所有套件現在都有一個一致的命名空間，從開始 `androidx` 。 這表示所有 Android 支援程式庫套件都對應至對應的 `androidx.*` 套件。
- `androidx` 封裝會分開維護和更新。 這表示您可以獨立更新 AndroidX 程式庫。
- 從 Android 支援程式庫 v28，將不再提供任何版本。 所有開發都會改為包含在中 `androidx` 。

![AndroidX 標誌](~/android/platform/androidx-images/AndroidXLogo.png)

## <a name="requirements"></a>需求

在以 Xamarin 為基礎的應用程式中使用 AndroidX 功能時，需要下列清單：

- **Visual Studio** Visual Studio 2019 16.4 版或更新版本的 Windows update。 在 macOS 上，更新至適用于 Mac 8.4 版或更新版本的 Visual Studio 2019。
- **Xamarin** . android-Xamarin. android 10.0 或更新版本必須隨 Visual Studio 安裝 (Xamarin。 Android 會自動安裝為在 Windows 上 **使用 .net** 工作負載進行行動裝置開發的一部分，並安裝為 **Visual Studio for Mac 安裝程式** 的一部分) 
- **JAVA 開發人員套件** -Xamarin. Android 10.0 開發需要 JDK 8。 Microsoft 散發的 OpenJDK 會自動安裝為 Visual Studio 的一部分。
- **Android SDK** Android SDK API 28 或更高版本必須透過 Android SDK 管理員安裝。

## <a name="get-started"></a>開始使用

您可以在 Android 專案內包含任何 [AndroidX NuGet 套件](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22) ，以開始使用 AndroidX。 深入瞭解如何在[Visual Studio](/nuget/quickstart/install-and-use-a-package-in-visual-studio)或[Visual Studio for Mac](/nuget/quickstart/install-and-use-a-package-in-visual-studio-mac)中安裝和使用套件

## <a name="behavior-changes"></a>行為變更

因為 AndroidX 是 Android 支援程式庫的重新設計，所以它包含的遷移步驟將會影響使用 Android 支援程式庫所建立的 Android 應用程式。

### <a name="package-name-change"></a>封裝名稱變更
舊套件和新封裝之間的封裝名稱已經變更。 您可以在下方看到這些變更的範例：

| 老                    | 新增                    |
| ---------------------- | ---------------------- |
| android. 支援。 * *     | androidx.@             |
| android. 設計. * *      | .com. android。@ |
| android. support. test. * * | androidx。@       |
| android. * *        | androidx.@             |
| android.... 房間. * * | androidx 聊天室。@ |
| android.。 | androidx。@ |

如需套件命名的詳細資訊， [請參閱下列檔](https://developer.android.com/jetpack/androidx/migrate#artifact_mappings)。

## <a name="migration-tooling"></a>遷移工具

您會想要知道您的應用程式有三個遷移步驟。

1. 如果您的應用程式包含 **Android 支援程式庫命名空間，而且您想要將它們遷移至 AndroidX 命名空間**，您可以使用我們的「 **遷移至 AndroidX** IDE」工具來處理大部分的命名空間案例。 

透過**工具 > 選項 > Xamarin >** Visual Studio 2019 內的 Android 設定來啟用**AndroidX 遷移**工具 (您可以在 Visual Studio for Mac) 上略過此步驟。

![啟用 AndroidX 遷移](~/android/platform/androidx-images/EnableAndroidXMigrator.png)

以滑鼠右鍵按一下您的專案，然後 **遷移至 AndroidX**。

![遷移至 AndroidX](~/android/platform/androidx-images/MigrateToAndroidX.png)

> [!NOTE] 
> 您將需要針對工具未涵蓋的案例進行一些手動命名空間變更。 雖然我們會為您對應正確的封裝，但建議您看看官方構件對應和[類別](https://developer.android.com/jetpack/androidx/migrate/class-mappings)[對應，以](https://developer.android.com/jetpack/androidx/migrate/artifact-mappings)協助您進行專案遷移。

2. 如果您的應用程式包含尚未 **遷移至 AndroidX 命名空間的任何**相依性，您必須使用 [Android 支援程式庫來 AndroidX 遷移套件。](https://www.nuget.org/packages/Xamarin.AndroidX.Migration)
3. 如果您的應用程式不 **包含任何需要 AndroidX 命名空間遷移**的相依性，您 [現在可以使用 NuGet 上的 AndroidX 程式庫](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22)。

## <a name="troubleshooting"></a>疑難排解

- AndroidX 內的某些架構套件會與支援程式庫版本相衝突。 若要修正此問題，您應該使用這些套件的 AndroidX 版本，並移除支援程式庫版本。 例如，如果您 `Xamarin.Android.Arch.Work.Runtime` 在專案中參考，它會與新加入之封裝的類型衝突 `AndroidX.Work` 。

## <a name="summary"></a>摘要

本文介紹了 AndroidX，並說明如何安裝及設定適用于 Xamarin 的最新工具和套件。 Android 開發與 AndroidX。 它提供了 AndroidX 的總覽。 其中包含 API 檔和 Android 開發人員主題的連結，可協助您開始使用 AndroidX 建立應用程式。 它也強調了可能會影響現有應用程式的最重要 AndroidX 行為變更和疑難排解主題。

## <a name="related-links"></a>相關連結

- [AndroidX 簡介 |Xamarin 節目](https://www.youtube.com/watch?v=M_l3RjTev5A)
- [AndroidX](https://developer.android.com/jetpack/androidx)
- [Xamarin AndroidX GitHub 存放庫](https://github.com/xamarin/AndroidX)
- [Xamarin AndroidX 遷移 GitHub 儲存機制](https://github.com/xamarin/XamarinAndroidXMigration)