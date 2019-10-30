---
title: 在 Xamarin. Mac 中使用 Avaudioplayer 播放音效播放音效
description: 本檔說明如何在 Xamarin. Mac 應用程式中使用 Avaudioplayer 播放音效播放音效。 它會討論高階的 Avaudioplayer 播放音效，並連結至其他探索更完整的檔。
ms.prod: xamarin
ms.assetid: 4A683A94-F75D-4EAF-8497-E9443653250B
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 10/19/2016
ms.openlocfilehash: 18043a88a129d48a1cad3b9ee15b6989d50ad126
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030049"
---
# <a name="playing-sound-with-avaudioplayer-in-xamarinmac"></a>在 Xamarin. Mac 中使用 Avaudioplayer 播放音效播放音效

## <a name="about-the-avaudioplayer"></a>關於 Avaudioplayer 播放音效

`AVAudioPlayer` 類別是用來播放記憶體或檔案中的音訊資料。 Apple 建議在您的應用程式中使用此類別來播放音訊，除非您正在進行網路串流處理，或需要低延遲音訊 i/o。

您可以使用 `AVAudioPlayer` 類別來執行下列動作：

- 使用選擇性的迴圈播放任何持續時間的聲音。
- 使用選擇性的同步處理，同時播放多個聲音。
- 控制每個播放音效的音量、播放速率和身歷聲位置。
- 支援的功能，例如向前快轉或倒轉。
- 取得播放層級計量資料。

`AVAudioPlayer` 支援 iOS、tvOS 和 macOS 所提供的任何音訊格式音效，例如. aif、.wav 或 mp3。

## <a name="playing-sounds-in-macos"></a>在 macOS 中播放聲音

因為 macOS 支援與 iOS 相同的音訊工具箱類別，請參閱我們的 iOS[播放音效與 avaudioplayer 播放音效](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer)檔，以取得在 Xamarin. Mac 應用程式中播放音訊的完整詳細資料。

## <a name="related-links"></a>相關連結

- [Avaudioplayer 播放音效參考](https://developer.apple.com/documentation/avfoundation/avaudioplayer)
