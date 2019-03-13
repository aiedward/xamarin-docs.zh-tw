---
title: Xamarin.Mac 中播放使用 avaudioplayer 播放音效
description: 本文件說明如何使用 avaudioplayer 播放音效的 Xamarin.Mac 應用程式中播放。 它討論 avaudioplayer 播放高的層級和更全面地探索它的其他文件的連結。
ms.prod: xamarin
ms.assetid: 4A683A94-F75D-4EAF-8497-E9443653250B
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 10/19/2016
ms.openlocfilehash: 9aeb7bbfc2fddef1f690b5299ec060c475ea1ce7
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120212"
---
# <a name="playing-sound-with-avaudioplayer-in-xamarinmac"></a>Xamarin.Mac 中播放使用 avaudioplayer 播放音效

## <a name="about-the-avaudioplayer"></a>關於 avaudioplayer 播放

`AVAudioPlayer`類別用於從記憶體或檔案播放音訊資料。 Apple 建議使用這個類別來播放音訊在您的應用程式中，除非您正在串流處理的網路，或需要低延遲音訊 I/O。

您可以使用`AVAudioPlayer`類別來執行下列動作：

- 播放音效的選擇性迴圈的時間長度。
- 有選用的同步處理一次播放多個的音效。
- 控制磁碟區、 播放速率和立體聲定位的每個播放的音效。
- 支援向前快轉或倒帶等功能。
- 取得播放層級計量資料。

`AVAudioPlayer` 支援任何提供的 iOS、 tvOS 和 macOS 例如.aif、.wav 或.mp3 的音訊格式的音效。

## <a name="playing-sounds-in-macos"></a>在 macOS 中播放音效

因為 macOS 支援相同的音訊的工具箱類別，為 iOS，請參閱我們的 iOS[使用 avaudioplayer 播放遊戲音效](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer)Xamarin.Mac 應用程式中播放音訊的完整詳細資料的文件。

## <a name="related-links"></a>相關連結

- [Avaudioplayer 播放參考](https://developer.apple.com/documentation/avfoundation/avaudioplayer)
