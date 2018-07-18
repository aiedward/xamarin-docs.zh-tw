---
title: 可以在哪裡找到要 symbolicate iOS 當機記錄檔的.dSYM 檔案？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/09/2018
ms.openlocfilehash: 60d897be8739ff5b78a322bc4ea3f43011785bb5
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
ms.locfileid: "33920651"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>可以在哪裡找到要 symbolicate iOS 當機記錄檔的.dSYM 檔案？

建置 iOS 應用程式與 Visual Studio for Mac 或 Visual Studio 2017，當.dSYM 檔案所需 symbolicate 當機報告將會置於您的應用程式專案檔 (.csproj) 相同的目錄階層。 確切的位置取決於您的專案組建設定：

- 如果您已啟用裝置的特定組建.dSYM 位於下列目錄：

    **&lt;專案目錄&gt;/bin/&lt;平台&gt;/&lt;組態&gt;/device-builds /&lt;裝置&gt;- &lt;os 版本&gt;/**

    例如: 
  
    **TestApp/bin/iPhone/Release/device-builds/iphone8.4-11.3.1/**

- 如果您未啟用裝置的特定組建，.dSYM 位於下列目錄：

    **&lt;專案目錄&gt;/bin/&lt;平台&gt;/&lt;組態&gt;/**

    例如: 

    **TestApp/bin/iPhone/Release /**

> [!NOTE]
> 建置程序的一部分，Visual Studio 2017 會從 Mac 建置主應用程式視窗複製.dSYM 檔案。 如果看不到在 Windows 上的.dSYM 檔案，是確定您已設定您的應用程式組建設定，以[建立.ipa 檔](~/ios/deploy-test/app-distribution/ipa-support.md)。

## <a name="see-also"></a>另請參閱

- [Symbolicating iOS 損毀的檔案 (Xamarin.iOS)](http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Demystifying iOS 應用程式當機記錄檔](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)

