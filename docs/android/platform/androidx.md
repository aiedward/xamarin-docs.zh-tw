---
title: 安卓X
description: 如何使用 Xamarin.Android 開始使用 AndroidX 開發應用程式。
ms.assetid: CC21BD28-EF67-4132-8C0D-CF25B78BA78B
author: JonDouglas
ms.author: jodou
ms.date: 02/20/2020
ms.openlocfilehash: ad6ea2f68fc01183f7ed42e85094f6be5fb3d9f9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291622"
---
# <a name="androidx-with-xamarin"></a>安卓X與夏馬林

_如何使用 Xamarin.Android 開始使用 AndroidX 開發應用程式。_

AndroidX 是原始 Android 支援庫的重大改進,該庫不再維護。 **AndroidX**軟體包透過提供可在Android應用程式中使用的功能奇偶校驗和新的庫,完全取代了Android支援庫。

AndroidX 包括以下功能:

- AndroidX 中的所有包現在都有一個從`androidx`開始的一致命名空間。 這意味著所有 Android 支援庫包映射`androidx.*`到相應的 包。
- `androidx`包分別維護和更新。 這意味著您可以相互獨立地更新 AndroidX 庫。
- 截至 Android 支援庫的 v28,將不再發佈。 所有開發都將包含在其中`androidx`。

![安卓X徽標](~/android/platform/androidx-images/AndroidXLogo.png)

## <a name="requirements"></a>需求

在基於 Xamarin 的應用中使用 AndroidX 功能需要以下清單:

- **可視化工作室**- 在 Windows 上更新到 Visual Studio 2019 版本 16.4 或更高版本。 在 macOS 上,針對 Mac 版本 8.4 或更高版本更新至 Visual Studio 2019。
- **Xamarin.Android** - Xamarin.Android 10.0 或更高版本必須安裝到 Visual Studio(Xamarin.Android 自動安裝為**Windows 上的行動開發.NET**工作負載的一部分,並作為**Mac 安裝程式的視覺化工作室**的一部分安裝)
- **Java 開發人員工具組**- Xamarin.Android 10.0 開發需要 JDK 8。 微軟的 OpenJDK 的分發會自動作為可視化工作室的一部分安裝。
- **Android SDK** - Android SDK API 28 或更高版本必須通過 Android SDK 管理器安裝。

## <a name="get-started"></a>開始使用

你可以開始使用AndroidX包括任何Android專案內的[AndroidXNuGet 套件](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22)。 瞭解有關在[Mac 可視化工作室](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio)或[視覺工作室](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio-mac)中安裝和使用包的更多詳細資訊

## <a name="behavior-changes"></a>行為變更

由於 AndroidX 是 Android 支援庫的重新設計,因此它包括將影響使用 Android 支援庫構建的 Android 應用程式的遷移步驟。

### <a name="package-name-change"></a>套件名稱變更
新舊包之間已更改包名稱。 下面您可以看到這些變更的範例:

| 老                    | 新增                    |
| ---------------------- | ---------------------- |
| 安卓.支援。     | 安卓@             |
| android.設計。      | com.google.android.材料。@ |
| 安卓.支援.測試。 | 安卓x.測試。@       |
| 安卓.arch.*        | 安卓@             |
| 安卓.arch.堅持.房間。 | 安卓克斯.房間。@ |
| 安卓.arch.持久性。 | 安卓x.sqlite.@ |

關於套件命名的詳細資訊,[請參考以下文件](https://developer.android.com/jetpack/androidx/migrate#artifact_mappings)。

## <a name="migration-tooling"></a>移轉工具

對於應用程式,您需要瞭解三個遷移步驟。

1. 如果你的應用程式包括**Android支援庫命名空間,你想將它們遷移到AndroidX命名空間**,您可以使用我們的**遷移到AndroidX** IDE工具來照顧大多數命名空間方案。 

開啟**AndroidX 遷移器**透過**工具>選項> Xamarin > Android 設置**在視覺工作室 2019 (你可以跳過此步驟在 Visual Studio 為 Mac).

![開啟 AndroidX 移植器](~/android/platform/androidx-images/EnableAndroidXMigrator.png)

右鍵按下您的項目並**移到 AndroidX**。

![遷移到安卓X](~/android/platform/androidx-images/MigrateToAndroidX.png)

> [!NOTE] 
> 對於工具未涵蓋的方案,您需要進行一些手動命名空間更改。 雖然我們將為您映射正確的包,但建議您查看官方[工件映射](https://developer.android.com/jetpack/androidx/migrate/artifact-mappings)和[類映射](https://developer.android.com/jetpack/androidx/migrate/class-mappings),以説明您進行專案遷移。

2. 如果應用程式包含**尚未遷移到 AndroidX 命名空間的任何依賴項**,則必須使用[Android 支援庫到 AndroidX 遷移包。](https://www.nuget.org/packages/Xamarin.AndroidX.Migration)
3. 如果你的應用程式**不包含任何需要AndroidX命名空間遷移的依賴項**,今天你可以[在NuGet上使用AndroidX庫](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22)。

## <a name="troubleshooting"></a>疑難排解

- AndroidX 中的某些體系結構包將與支援庫版本衝突。 要解決此問題,應使用這些包的 AndroidX 版本並刪除支援庫版本。 例如,如果您在專案中引用`Xamarin.Android.Arch.Work.Runtime`,它將與新`AndroidX.Work`添加 的包的類型衝突。

## <a name="summary"></a>總結

本文介紹了 AndroidX,並解釋了如何使用 AndroidX 安裝和配置 Xamarin.Android 開發的最新工具和軟體包。 它提供了什麼是AndroidX的概述。 它包括指向 API 文件的連結和 Android 開發人員主題,以説明您開始使用 AndroidX 創建應用。 它還強調了可能影響現有應用的最重要 AndroidX 行為更改和故障排除主題。

## <a name="related-links"></a>相關連結

- [安卓X簡介 |夏馬林秀](https://www.youtube.com/watch?v=M_l3RjTev5A)
- [安卓X](https://developer.android.com/jetpack/androidx)
- [Xamarin AndroidX GitHub 儲存庫](https://github.com/xamarin/AndroidX)
- [Xamarin AndroidX 轉移 GitHub 儲存函式庫](https://github.com/xamarin/XamarinAndroidXMigration)
