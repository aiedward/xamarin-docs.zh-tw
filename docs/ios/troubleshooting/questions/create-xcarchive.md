---
title: "是可以從 Visual Studio 建立.xcarchive 封存？"
ms.topic: article
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: bb8f9f8f58eb0b1251b31254b3798ba2c2b5c39b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>是可以從 Visual Studio 建立.xcarchive 封存？

## <a name="for-xamarin-4"></a>為 Xamarin 4

為準，Xamarin 4.x，就可以建立`.xcarchive`從藉由設定 Windows`ArchiveOnBuild`屬性`true`。 例如，使用`MSBuild`命令列上：

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

`.xcarchive`將放置於`$HOME/Library/Developer/Xcode/Archives`Mac 組建主機安裝 Xcode 和 Xamarin Studio 搜尋，以顯示先前建立的保存檔目錄。

請參閱此[Xamarin 論壇張貼](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635)的一些相關簡短的其他注意事項`ArchiveOnBuild`屬性。 請參閱的文件[Xamarin.iOS 命令列組建在 Windows 上](~/ios/get-started/installation/windows/connecting-to-mac/index.md)如需其他詳細資料`ServerAddress`和`ServerUser`屬性。

* * *

## <a name="for-xamarin-3-and-earlier"></a>Xamarin 3 及更早版本

Xamarin 3.x Visual Studio 擴充功能不提供一個機制來產生`.xcarchive`封存。 話雖如此，用來建立的邏輯`.xcarchive`封存在 Mac 上的 Xamarin Studio[此處描述](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5)，因此可能無法建立您自己`.xcarchive`手動如果您必須承擔更多。

但值得注意的是，您不需要`.xcarchive`以提交至應用程式市集。 只要已簽署的應用程式市集散發設定檔 （不臨機操作發佈設定檔），您可以提交 IPA 檔案。

事實上，您甚至只壓縮`.app`（亦即簽署應用程式儲存發佈設定檔） 的組合，並將該送出`.zip`檔案的應用程式市集。

在任一情況下，您可以使用應用程式載入器應用程式送出應用程式 （而非 Xcode）。

