---
title: 套用變更
description: 如何開始使用 Xamarin. Android 專案中的套用變更。
ms.assetid: 38950B0C-8880-448F-B12E-08D5BFE87D0D
author: JonDouglas
ms.author: jodou
ms.date: 03/09/2020
ms.openlocfilehash: 03bace97908a53ac6112cd2600b20d429fe2f521
ms.sourcegitcommit: ec112800a76089ab1db66fe24b8bbcc510e067b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "80215345"
---
# <a name="apply-changes"></a>套用變更

套用變更可讓您將資源變更推送至執行中的應用程式，而不需要重新開機應用程式。 這可協助您控制當您想要部署和測試小型、增量變更，同時保留裝置或模擬器的目前狀態時，您的應用程式會重新開機多少。

套用變更會使用[ANDROID JVMTI 執行](https://docs.oracle.com/javase/8/docs/platform/jvmti/jvmti.html#bci)中的功能，這是在執行 android 8.0 （API 層級26）或更高版本的裝置或模擬器上支援。

## <a name="requirements"></a>需求

下列清單顯示使用 [套用變更] 的需求：

- **Visual Studio**在 Windows 上，請更新至 Visual Studio 2019 16.5 或更新版本。 在 macOS 上，更新為 Visual Studio 2019 （適用于 Mac 8.5 版或更新版本）。
- **Xamarin** . android 10.2 或更新版本必須與 Visual Studio 一起安裝（Xamarin. android 會在 Windows 上自動安裝為**使用 .net**的行動裝置開發工作負載，並安裝為**Visual Studio for Mac 安裝程式**的一部分）。
- **Android SDK** -必須透過 Android SDK 管理員安裝 Android API 28 或更高版本。
- **目標裝置或模擬器**-您的裝置或模擬器必須執行 Android 8.0 （API 層級26）或更高版本。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="get-started"></a>開始使用

若要開始使用 [套用變更]，您必須確定裝置或模擬器正在執行 Android 8.0 （API 層級26）或更高版本。 然後執行您的 Android 應用程式，並使用或不進行任何偵錯工具。

接著，您可以使用下列方法與 [套用變更] 互動：

1. **工具列圖示。** 您可以按一下 [套用變更] 工具列圖示，將變更套用至您的目標裝置或模擬器。

    [![套用變更-工具列圖示](apply-changes-images/Apply-Changes-Toolbar.png)](apply-changes-images/Apply-Changes-Toolbar.png#lightbox)

2. **鍵盤快速鍵。** 您可以使用**Shift + Alt + F5**鍵盤快速鍵，將變更套用至您的目標裝置或模擬器。
3. **[調試] 功能表。** 您可以使用**Debug >** 的 [套用變更] 功能表項目，將變更套用至目標裝置或模擬器。

    [![套用變更-[調試] 功能表](apply-changes-images/Apply-Changes-Debug-Menu.png)](apply-changes-images/Apply-Changes-Debug-Menu.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

## <a name="get-started"></a>開始使用

若要開始使用 [套用變更]，您必須確定裝置或模擬器正在執行 Android 8.0 （API 層級26）或更高版本。 然後執行您的 Android 應用程式，並使用或不進行任何偵錯工具。

接著，您可以使用下列方法與 [套用變更] 互動：

1. **鍵盤快速鍵。** 您可以使用**⌥ + ⇧ + R**鍵盤快速鍵，將變更套用至您的目標裝置或模擬器。
2. **執行功能表。** 您可以使用 [**執行 > 套用變更**] 功能表項目，將變更套用至目標裝置或模擬器。

    [![套用變更-[調試] 功能表](apply-changes-images/Apply-Changes-Debug-Menu-Mac.png)](apply-changes-images/Apply-Changes-Debug-Menu-Mac.png#lightbox)

-----

## <a name="limitations"></a>限制

下列變更需要重新開機應用程式：

- 變更C#程式碼。
- 新增或移除資源。
- 變更 Androidmanifest.xml。
- 變更原生程式庫（. so files）。

## <a name="related-links"></a>相關連結

- [套用變更](https://developer.android.com/studio/run#apply-changes)
