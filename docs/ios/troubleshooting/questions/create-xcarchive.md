---
title: 可以從 Visual Studio 建立建立 .xcarchive 封存嗎？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 6d35827b00a4ccc9bbe3e71444536425e4e1c3b1
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70288115"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>可以從 Visual Studio 建立建立 .xcarchive 封存嗎？

## <a name="for-xamarin-4"></a>適用于 Xamarin 4

`.xcarchive`從 Xamarin 4.x 開始，現在可以藉由`ArchiveOnBuild`將屬性設定為`true`，從 Windows 建立。 例如，在命令`MSBuild`行上使用：

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

會放在 Mac 組建主機上的目錄中，這兩個Xcode和XamarinStudio搜尋以顯示先前建立的封存。`$HOME/Library/Developer/Xcode/Archives` `.xcarchive`

請參閱這[篇 Xamarin 論壇文章](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635)，以取得有關屬性`ArchiveOnBuild`的一些簡短額外注意事項。 如需`ServerAddress`和屬性的其他詳細資料， `ServerUser`請參閱[Windows 上的 Xamarin. iOS 命令列組建](~/ios/get-started/installation/windows/connecting-to-mac/index.md)的相關檔。

* * *

## <a name="for-xamarin-3-and-earlier"></a>適用于 Xamarin 3 和更早版本

Xamarin 3.x Visual Studio 延伸模組並未提供產生`.xcarchive`封存的機制。 話雖如此，在 Mac 上用`.xcarchive`來建立封存的邏輯[如下所述](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5)，因此您可能會想要的話`.xcarchive` ，手動建立自己的 Xamarin Studio。

但值得注意的是，您不需要`.xcarchive`提交至 App Store。 您可以提交 .IPA 檔案，只要它是以 App Store 散發設定檔（而非臨機操作散發設定檔）簽署即可。

事實上，您甚至可以只壓縮`.app`配套（這是以 App store 散發設定檔簽署），然後將該`.zip`檔案提交至 app store。

不論是哪一種情況，您都可以使用應用程式載入器應用程式來提交應用程式（而不是 Xcode）。

