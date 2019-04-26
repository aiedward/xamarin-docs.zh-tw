---
title: 使用 avaudioplayer 播放在 Xamarin 中 tvOS 中播放音效
description: 這篇文章會示範如何使用協助程式類別，以控制播放音效的 Xamarin.iOS 應用程式中使用 avaudioplayer 播放。
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: bc1e9febea9529c3fadbacd689404562952276fe
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61414096"
---
# <a name="playing-sound-in-tvos-with-avaudioplayer-in-xamarin"></a>使用 avaudioplayer 播放在 Xamarin 中 tvOS 中播放音效

## <a name="about-the-avaudioplayer"></a>關於 avaudioplayer 播放

`AVAudioPlayer`是用來播放音訊資料，從記憶體或檔案。 Apple 建議使用這個類別來播放音訊在您的應用程式中，除非您正在串流處理的網路，或需要低延遲音訊 I/O。

您可以使用`AVAudioPlayer`執行下列動作：

- 播放音效的選擇性迴圈的時間長度。
- 有選用的同步處理一次播放多個的音效。
- 控制磁碟區、 播放速率和立體聲定位的每個播放的音效。
- 支援向前快轉或倒帶等功能。
- 取得播放層級計量資料。

`AVAudioPlayer` 支援 iOS、 tvOS 和 OS X 所提供的這類任何音訊格式的音效`.aif`，`.wav`或`.mp3`。

## <a name="playing-sounds-in-tvos"></a>TvOS 中播放音效

由於 tvOS 支援相同的音訊的工具箱類別，為 iOS，請參閱我們的 iOS[使用 avaudioplayer 播放遊戲音效](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer)Xamarin.tvOS 應用程式中播放音訊的完整詳細資料的文件。



## <a name="related-links"></a>相關連結

- [Avaudioplayer 播放參考](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
