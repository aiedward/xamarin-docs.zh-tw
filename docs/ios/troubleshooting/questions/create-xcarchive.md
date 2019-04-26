---
title: 是否可以從 Visual Studio 建立.xcarchive 封存？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 952777b3178b82657c8f64d5882c532c188c7a79
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61422018"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>是否可以從 Visual Studio 建立.xcarchive 封存？

## <a name="for-xamarin-4"></a>適用於 Xamarin 4

截至 Xamarin 4.x，就可以建立`.xcarchive`從 Windows 藉由設定`ArchiveOnBuild`屬性設`true`。 例如，使用`MSBuild`命令列上：

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

`.xcarchive`將置於`$HOME/Library/Developer/Xcode/Archives`Xcode 和 Xamarin Studio 搜尋先前建立的顯示封存在 Mac 組建主機上的目錄。

請參閱此[Xamarin 論壇張貼](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635)的一些簡短的相關的其他注意事項`ArchiveOnBuild`屬性。 請參閱的文件[Xamarin.iOS 命令列是根據 Windows](~/ios/get-started/installation/windows/connecting-to-mac/index.md)如需其他詳細資料`ServerAddress`和`ServerUser`屬性。

* * *

## <a name="for-xamarin-3-and-earlier"></a>適用於 Xamarin 3 或更早版本

Xamarin 3.x Visual Studio 擴充功能不提供一個機制來產生`.xcarchive`封存。 話雖如此，用來建立的邏輯`.xcarchive`在 Mac 上的 Xamarin Studio 中封存[此處所述](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5)，因此可能無法建立您自己`.xcarchive`手動如果您想。

但值得注意的是，您不需要`.xcarchive`提交至 App Store。 您可以提交的 IPA 檔案，只要它使用 App Store 散發設定檔 (而非臨機操作散發 Profile) 簽署。

事實上，您甚至只是壓縮`.app`（也就帶正負號與應用程式儲存發佈設定檔） 的組合，把它提交出去`.zip`至 app store 的檔案。

在任一情況下，您可以使用應用程式載入器應用程式來提交應用程式 （而非 Xcode）。

