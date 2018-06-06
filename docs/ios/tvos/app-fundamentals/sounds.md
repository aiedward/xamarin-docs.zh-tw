---
title: 與在 Xamarin AVAudioPlayer tvOS 中播放音效
description: 本文示範如何控制播放的聲音使用 AVAudioPlayer Xamarin.iOS 應用程式中使用的協助程式類別。
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 7d95a8ea6c22c0d897d8ccfe0c2ca401f6523783
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788626"
---
# <a name="playing-sound-in-tvos-with-avaudioplayer-in-xamarin"></a>與在 Xamarin AVAudioPlayer tvOS 中播放音效

## <a name="about-the-avaudioplayer"></a>關於 AVAudioPlayer

`AVAudioPlayer`用於播放的音訊資料從記憶體或檔案。 Apple 建議使用這個類別來播放音訊應用程式中，除非您正在資料流的網路，或需要低度延遲音訊 I/O。

您可以使用`AVAudioPlayer`執行下列動作：

- 播放音效的選擇性迴圈的時間長度。
- 選擇性的同步處理，同時播放多重聲音。
- 控制磁碟區、 播放速率和立體聲的位置，針對每個播放的音效。
- 支援向前快轉或倒帶等功能。
- 取得計量資料的播放層級。

`AVAudioPlayer` 提供的 iOS、 tvOS 和 OS X 例如任何音訊格式支援音效`.aif`，`.wav`或`.mp3`。

## <a name="playing-sounds-in-tvos"></a>TvOS 中播放音效

由於 tvOS 支援相同的音訊的工具箱類別，為 iOS，請參閱我們的 iOS[遊戲音效與 AVAudioPlayer](http://developer.xamarin.com/recipes/ios/media/sound/avaudioplayer/) Xamarin.tvOS 應用程式中播放音訊的完整詳細資料的文件。



## <a name="related-links"></a>相關連結

- [AVAudioPlayer 參考](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
