---
title: 在哪裡可以找到用來將 iOS 當機記錄檔符號化的 .dSYM 檔案？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/09/2018
ms.openlocfilehash: 480ec0d4a815d62422e31a4110af52ca1628d1f7
ms.sourcegitcommit: d4d293174a8324ce82b8f961ae6eadce294cafd7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98194896"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>在哪裡可以找到用來將 iOS 當機記錄檔符號化的 .dSYM 檔案？

使用 Visual Studio for Mac 或 Visual Studio 2017 建立 iOS 應用程式時，將損毀報告所需的 dSYM 檔案將會放在與應用程式專案檔 ( .csproj) 相同的目錄階層中。 確切的位置取決於您專案的組建設定：

- 如果您已啟用裝置特定組建，dSYM 可以在下列目錄中找到：

    **&lt;專案目錄 &gt; /bin/ &lt; 平臺設定 &gt; / &lt; &gt; /device-builds/ &lt; 裝置 &gt; - &lt; 作業系統-版本&gt;/**

    例如：
  
    **TestApp/bin/iPhone/Release/裝置-組建/iphone 8.4-11.3.1/**

- 如果您尚未啟用裝置特定組建，可以在下列目錄中找到 dSYM：

    **&lt;專案目錄 &gt; /bin/ &lt; 平臺設定 &gt; / &lt;&gt;/**

    例如：

    **TestApp/bin/iPhone/Release/**

> [!NOTE]
> 在建立過程中，Visual Studio 2017 會將 dSYM 檔案從 Mac 組建主機複製到 Windows。 如果您在 Windows 上沒有看到 dSYM 檔案，請確定您已設定應用程式的組建設定來 [建立 ipa](~/ios/deploy-test/app-distribution/ipa-support.md)檔案。

## <a name="see-also"></a>請參閱

- [Symbolicating iOS 損毀檔案 (Xamarin) ](https://www.jmillerdev.com/symbolicating-ios-crash-files-xamarin-ios/)
- [揭開 iOS 應用程式損毀記錄](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)
