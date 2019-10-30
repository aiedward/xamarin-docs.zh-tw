---
title: 在哪裡可以找到用來將 iOS 當機記錄檔符號化的 .dSYM 檔案？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/09/2018
ms.openlocfilehash: 418a0196849099da03983085aca9ceed2077207b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030911"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>在哪裡可以找到用來將 iOS 當機記錄檔符號化的 .dSYM 檔案？

建立具有 Visual Studio for Mac 或 Visual Studio 2017 的 iOS 應用程式時，將損毀報告所需的 dSYM 檔案將會放在與您應用程式的專案檔（.csproj）相同的目錄階層中。 確切的位置取決於您專案的組建設定：

- 如果您已啟用裝置特定組建，則可以在下列目錄中找到 dSYM：

    **&lt;專案目錄&gt;/bin/&lt;平臺&gt;/&lt;configuration&gt;/device-builds/&lt;裝置&gt;-&lt;os 版本&gt;/**

    例如:
  
    **TestApp/bin/iPhone/Release/裝置-組建/iphone 8.4-11.3.1/**

- 如果您尚未啟用裝置特定組建，則可以在下列目錄中找到 dSYM：

    **&lt;專案目錄&gt;/bin/&lt;平臺&gt;/&lt;configuration&gt;/**

    例如:

    **TestApp/bin/iPhone/Release/**

> [!NOTE]
> 在建立程式期間，Visual Studio 2017 會將 dSYM 檔案從 Mac 組建主機複製到 Windows。 如果您在 Windows 上看不到 dSYM 檔案，請確定您已設定應用程式的組建設定，以[建立 .ipa](~/ios/deploy-test/app-distribution/ipa-support.md)檔案。

## <a name="see-also"></a>請參閱

- [Symbolicating iOS 損毀檔案（Xamarin. iOS）](https://www.jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [揭密 iOS 應用程式損毀記錄](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)
