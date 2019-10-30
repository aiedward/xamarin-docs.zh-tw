---
title: 可以從 Visual Studio 建立建立 .xcarchive 封存嗎？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 27697223735c4074dd508d3f603ef6aa4e47b1be
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031179"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>可以從 Visual Studio 建立建立 .xcarchive 封存嗎？

## <a name="for-xamarin-4"></a>適用于 Xamarin 4

從 Xamarin 4.x 開始，您現在可以藉由將 `ArchiveOnBuild` 屬性設定為 [`true`]，從 Windows 建立 `.xcarchive`。 例如，在命令列上使用 `MSBuild`：

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

`.xcarchive` 將會放在 Mac 組建主機上的 `$HOME/Library/Developer/Xcode/Archives` 目錄中，這兩個檔案都 Xcode 和 Xamarin Studio 搜尋，以顯示先前建立的封存。

請參閱這[篇 Xamarin 論壇文章](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635)，以取得有關 `ArchiveOnBuild` 屬性的一些簡短額外注意事項。 如需有關 `ServerAddress` 和 `ServerUser` 屬性的其他詳細資料，請參閱[Windows 上有關 Xamarin 的命令列組建](~/ios/get-started/installation/windows/connecting-to-mac/index.md)的檔。

* * *

## <a name="for-xamarin-3-and-earlier"></a>適用于 Xamarin 3 和更早版本

Xamarin 3.x Visual Studio 延伸模組並未提供產生 `.xcarchive` 封存的機制。 話雖如此，[這裡描述](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5)用來在 Mac Xamarin Studio 中建立 `.xcarchive` 封存的邏輯，因此您可能會在需要時手動建立自己的 `.xcarchive`。

但值得注意的是，您不需要提交到 App Store 的 `.xcarchive`。 您可以提交 .IPA 檔案，只要它是以 App Store 散發設定檔（而非臨機操作散發設定檔）簽署即可。

事實上，您甚至可以壓縮 `.app` 配套（以 App Store 散發設定檔簽署），並將該 `.zip` 檔案提交至 app store。

不論是哪一種情況，您都可以使用應用程式載入器應用程式來提交應用程式（而不是 Xcode）。
