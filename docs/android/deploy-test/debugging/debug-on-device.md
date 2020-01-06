---
title: 在裝置上進行偵錯
description: 這篇文章說明如何在實體 Android 裝置上進行 Xamarin.Android 應用程式偵錯。
ms.prod: xamarin
ms.assetid: 153D3746-A27F-198B-48FE-D219C0133A79
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 3ca524e451a7a4eb838805c839b33c4b9dd6bddd
ms.sourcegitcommit: 5821c9709bf5e06e6126233932f94f9cf3524577
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2019
ms.locfileid: "75556531"
---
# <a name="debug-on-an-android-device"></a>在 Android 裝置上進行偵錯工具

_本文說明如何在實體 Android 裝置上進行 Xamarin.Android 應用程式偵錯。_

您可以使用 Visual Studio for Mac 或 Visual Studio，為 Android 裝置上的 Xamarin.Android App 進行偵錯。 在於裝置上進行偵錯之前，必須[設定裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md)，並連接至 PC 或 Mac。

## <a name="debug-application"></a>進行應用程式偵錯

在裝置連接到您的電腦之後，執行 Xamarin.Android 應用程式偵錯的方式就與任何其他 Xamarin 產品或 .NET 應用程式相同。 請確定在 IDE 中已選取 [偵錯] 組態和外部裝置，這可確保必要的偵錯符號可供使用，且 IDE 可以連線到執行中的應用程式： 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![已選取 [偵錯] 組態](debug-on-device-images/image1-vs.png)

接著，在程式碼中設定中斷點：

![在程式碼行設定中斷點](debug-on-device-images/image2-vs.png)

選取裝置之後，Xamarin.Android 將會連線至裝置、部署應用程式，然後執行應用程式。 到達中斷點時，偵錯工具會停止應用程式，以允許以類似任何其他 C# 應用程式的方式來進行應用程式偵錯： 

![到達中斷點](debug-on-device-images/image3-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![已選取 [偵錯] 組態](debug-on-device-images/image1-xs.png)

接著，在程式碼中設定中斷點：

![在程式碼行設定中斷點](debug-on-device-images/image2-xs.png)

選取裝置之後，Xamarin.Android 將會連線至裝置、部署應用程式，然後執行應用程式。 到達中斷點時，偵錯工具會停止應用程式，以允許以類似任何其他 C# 應用程式的方式來進行應用程式偵錯： 

![到達中斷點](debug-on-device-images/image3-xs.png)

-----

## <a name="summary"></a>總結

本文件探討了如何藉由設定中斷點和選取目標裝置，進行 Xamarin.Android 應用程式偵錯。

## <a name="related-links"></a>相關連結

- [設定您的裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md)
- [設定可偵錯的屬性](~/android/deploy-test/debuggable-attribute.md)
