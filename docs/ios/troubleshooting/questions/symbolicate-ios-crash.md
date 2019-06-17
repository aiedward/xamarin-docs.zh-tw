---
title: 在哪裡可以找到用來將 iOS 當機記錄檔符號化的 .dSYM 檔案？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/09/2018
ms.openlocfilehash: 0b8f3aa736cba6e70fbf346766499c23a9bbe270
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61418204"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>在哪裡可以找到用來將 iOS 當機記錄檔符號化的 .dSYM 檔案？

建置 iOS 應用程式與 Visual Studio for Mac 或 Visual Studio 2017，當需要當機報告符號化的.dSYM 檔案會置於相同的目錄階層，做為您的應用程式專案檔 (.csproj)。 確切的位置取決於您的專案組建設定：

- 如果您已啟用裝置專用的組建，.dSYM 位於下列目錄：

    **&lt;project directory&gt;/bin/&lt;platform&gt;/&lt;configuration&gt;/device-builds/&lt;device&gt;-&lt;os-version&gt;/**

    例如：
  
    **TestApp/bin/iPhone/Release/device-builds/iphone8.4-11.3.1/**

- 如果您尚未啟用裝置專用的組建，.dSYM 位於下列目錄：

    **&lt;專案目錄&gt;/bin/&lt;平台&gt;/&lt;組態&gt;/**

    例如: 

    **TestApp/bin/iPhone/Release/**

> [!NOTE]
> 建置程序的一部分，Visual Studio 2017，請從 Windows 到 Mac 組建主機複製符號化的.dSYM 檔案。 如果看不到在 Windows 上的符號化的.dSYM 檔案，請確定您已設定您的應用程式建置設定，以[建立.ipa 檔](~/ios/deploy-test/app-distribution/ipa-support.md)。

## <a name="see-also"></a>另請參閱

- [Symbolicating iOS 當機的檔案 (Xamarin.iOS)](http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Demystifying iOS 應用程式當機記錄檔](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)

