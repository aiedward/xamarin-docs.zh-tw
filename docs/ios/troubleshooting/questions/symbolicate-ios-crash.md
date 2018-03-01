---
title: "可以在哪裡找到要 symbolicate iOS 當機記錄檔的.dSYM 檔案？"
ms.topic: article
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 223e1977bb6229760d6428fdca2f5372b1e25c23
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>可以在哪裡找到要 symbolicate iOS 當機記錄檔的.dSYM 檔案？

建置時從 visual studio 的 iOS 應用程式，可用於 symbolicate 當機報告.dSYM 檔案最後會組建主機在路徑上：
```
    /Users/<username>/Library/Caches/Xamarin/mtbs/builds/<appname>/<guid>/bin/iPhone/<configuration>
```

請注意，`~/Library`隱藏資料夾預設會在搜尋工具，因此，如果需要可使用搜尋的**移 > 移至資料夾**功能表，然後輸入：`~/Library/Caches/Xamarin/mtbs/builds/`開啟資料夾。  

或者，您可以取消`~/Library`資料夾使用**顯示檢視選項**面板的 主資料夾。 如果您在搜尋中的 資訊看板 中選取主資料夾，並使用 搜尋 功能表**檢視 > 顯示 檢視選項**（或 cmd j），您將會看到核取方塊，以**顯示文件庫資料夾**。


### <a name="see-also"></a>請參閱
- 擴充的步驟 symbolicating iOS 當機報告： [http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/](http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Demystifying iOS 應用程式當機記錄檔](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)
