---
title: 命令列模擬器
ms.prod: xamarin
ms.assetid: E592AA32-5E83-B7E5-1753-12416551B23C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: b1ca1c2b441a9c9ca26de5668f318312bea156a3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762067"
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

您可在以下 Android 網站找到有關額外參數的詳細資訊：[http://developer.android.com/guide/developing/tools/emulator.html](http://developer.android.com/guide/developing/tools/emulator.html)
