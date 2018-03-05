---
title: "命令列模擬器"
ms.topic: article
ms.prod: xamarin
ms.assetid: E592AA32-5E83-B7E5-1753-12416551B23C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.openlocfilehash: b8e8ec3de3afccab15d54f666974af678c1b8c33
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="command-line-emulator"></a>命令列模擬器


## <a name="running-the-android-emulator-from-the-command-line"></a>從命令列執行 Android 模擬器

若要啟用從命令列執行 Android 模擬器，您可以使用 Android SDK 提供的「模擬器」工具。 此工具可用於從 OS X 上的終端機或 Windows 電腦上的命令提示字元來執行模擬器。

若要啟動特定的 Android 模擬器，請從 Android SDK 位置的 tools 目錄 (例如 C:\android-sdk-windows\tools) 執行下列命令：

在 Windows 上

```cmd
emulator.exe -avd NameOfYourEmulator -partition-size 512
```

在 macOS 上

```bash
./emulator -avd NameOfYourEmulator -partition-size 512
```

需要分割區大小的理由是要允許模擬器具備足夠的空間在模擬器上安裝 Xamarin.Android 平台，因為模擬器的預設大小很小。

您可以在位於此處的 Android 網站找到更多關於額外參數的詳細資訊 - [http://developer.android.com/guide/developing/tools/emulator.html](http://developer.android.com/guide/developing/tools/emulator.html)
