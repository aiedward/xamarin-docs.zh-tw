---
title: 播放音效與 AVAudioPlayer
description: 本文示範如何控制播放的聲音使用 AVAudioPlayer 使用協助程式類別。
ms.prod: xamarin
ms.assetid: 4A683A94-F75D-4EAF-8497-E9443653250B
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/19/2016
ms.openlocfilehash: 25e3285a1da1b6a112629001d5412fdd0788c705
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="playing-sound-with-avaudioplayer"></a>播放音效與 AVAudioPlayer

_本文示範如何控制播放的聲音使用 AVAudioPlayer 使用協助程式類別。_

## <a name="about-the-avaudioplayer"></a>關於 AVAudioPlayer

`AVAudioPlayer`類別用來播放音訊資料從記憶體或檔案。 Apple 建議使用這個類別來播放音訊應用程式中，除非您正在資料流的網路，或需要低度延遲音訊 I/O。

您可以使用`AVAudioPlayer`類別來執行下列動作：

- 播放音效的選擇性迴圈的時間長度。
- 選擇性的同步處理，同時播放多重聲音。
- 控制磁碟區、 播放速率和立體聲的位置，針對每個播放的音效。
- 支援向前快轉或倒帶等功能。
- 取得計量資料的播放層級。

`AVAudioPlayer` 提供的 iOS、 tvOS 和 macOS 例如.aif、.wav 或.mp3 任何音訊格式支援音效。

## <a name="playing-sounds-in-macos"></a>MacOS 中播放音效

由於 macOS 支援相同的音訊的工具箱類別，為 iOS，請參閱我們的 iOS[遊戲音效與 AVAudioPlayer](https://developer.xamarin.com/recipes/ios/media/sound/avaudioplayer/) Xamarin.Mac 應用程式中播放音訊的完整詳細資料的文件。



## <a name="related-links"></a>相關連結

- [AVAudioPlayer 參考](https://developer.apple.com/documentation/avfoundation/avaudioplayer)
