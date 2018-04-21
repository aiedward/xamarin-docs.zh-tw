---
title: 我的 Mac 在哪裡?
description: 說明如何設定 Mac for Visual Studio 以組建 Xamarin.iOS 專案
ms.prod: xamarin
ms.assetid: 4f792690-b3b1-4d56-a5e2-363b4f246059
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: f3e2988c9700549db24ad69277df9e412c99caed
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="wheres-my-mac"></a>我的 Mac 在哪裡?

_說明如何設定 Mac for Visual Studio 以建置 Xamarin.iOS 專案_

目前的**穩定**版 Xamarin for Visual Studio 與 Mac 互動的方式不同於舊版[^](#earlier-versions)。

若要使用 Mac 作為 Xamarin Mac Agent，您必須在 Mac 上啟用 [遠端登入]。

1. 開啟 *Spotlight* (同時按 `Cmd-Space` 鍵) 並搜尋**遠端登入**，然後選取 [共享] 結果項目。 這會開啟 [系統偏好設定] 的 [共享] 面板。

  ![](visual-studio-ssh-images/spotlight.png "Spotlight 的遠端登入搜尋")

2. 勾選左側 [服務] 清單中的 [遠端登入] 選項，以允許 Xamarin for Visual Studio 連線到 Mac。

  ![](visual-studio-ssh-images/sharing.png "勾選 [服務] 清單中的 [遠端登入] 選項")

3. 確定 [遠端登入] 已設定為允許 [所有使用者] 存取，或是右側清單之允許的使用者清單中包含您的 Mac 使用者名稱或群組。

如果 Mac 與 Visual Studio 位於相同的網路上，Visual Studio 現在應該就能探索到它。
當 Visual Studio 嘗試連線到 Mac 時，系統會提示您使用 Mac 使用者認證從 Visual Studio 登入。

## <a name="where-can-i-find-more-information"></a>我可以在哪裡找到更多資訊？

以下列出一些可用的指南，可協助您設定和了解新的代理程式：

- [連線到 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- [疑難排解](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)
- [Xamarin University - Xamarin Mac Agent](https://university.xamarin.com/lightninglectures/xamarin-mac-agent)

<a name="earlier-versions" />

## <a name="migrating-from-previous-versions"></a>從舊版移轉

如果您使用舊版 Xamarin for Visual Studio，就會熟悉 **Xamarin.iOS Build Host** 應用程式，此應用程式需要在 Mac 上啟動，然後透過 PIN 碼與 Visual Studio 執行個體進行「配對」。

這一版的 Xamarin for Visual Studio 已不再需要組建主機應用程式。
