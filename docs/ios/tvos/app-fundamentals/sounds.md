---
title: 在 Xamarin 中使用 Avaudioplayer 播放音效在 tvOS 中播放音效
description: 本文說明如何使用 helper 類別來控制使用 Avaudioplayer 播放音效在 Xamarin iOS 應用程式中播放音效。
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: b34c769eaa3aef5bf47a9bfa891859289b195f03
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283777"
---
# <a name="playing-sound-in-tvos-with-avaudioplayer-in-xamarin"></a>在 Xamarin 中使用 Avaudioplayer 播放音效在 tvOS 中播放音效

## <a name="about-the-avaudioplayer"></a>關於 Avaudioplayer 播放音效

`AVAudioPlayer`是用來播放記憶體或檔案中的音訊資料。 Apple 建議在您的應用程式中使用此類別來播放音訊，除非您正在進行網路串流處理，或需要低延遲音訊 i/o。

您可以使用`AVAudioPlayer`來執行下列動作：

- 使用選擇性的迴圈播放任何持續時間的聲音。
- 使用選擇性的同步處理，同時播放多個聲音。
- 控制每個播放音效的音量、播放速率和身歷聲位置。
- 支援的功能，例如向前快轉或倒轉。
- 取得播放層級計量資料。

`AVAudioPlayer`支援 iOS、tvOS 和 OS X 所提供的任何音訊格式音效，例如`.aif`、 `.wav`或`.mp3`。

## <a name="playing-sounds-in-tvos"></a>在 tvOS 中播放聲音

因為 tvOS 支援與 iOS 相同的音訊工具箱類別，請參閱我們的 iOS[播放音效與 avaudioplayer 播放音效](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer)檔，以取得在 tvOS 應用程式中播放音訊的完整詳細資料。



## <a name="related-links"></a>相關連結

- [Avaudioplayer 播放音效參考](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
