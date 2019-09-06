---
title: 在 iTunes Connect 中設定您的 tvOS 應用程式
description: 本文提供 iOS 在 iTunes Connect 中設定應用程式的補充指南，以取得 tvOS 特定設定。
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 92f028a632a32392a349c06c0b7ab8e15b308b9f
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279526"
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>在 iTunes Connect 中設定您的 tvOS 應用程式

_本文提供 iOS 在 iTunes Connect 中設定應用程式的補充指南，以取得 tvOS 特定設定。_


除了在[ITunes Connect 指南中設定您的應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)所需進行的設定和設定之外，本檔還涵蓋了在 Apple 電視應用程式中發行 tvOS 應用程式所需的特定設定。存放區.

<a name="Adding-a-tvOS-Release-Version" />

## <a name="adding-a-tvos-release-version"></a>新增 tvOS 發行版本

無論您是建立要在 Apple TV App Store 上發行的新應用程式，或是將 Apple TV 支援新增至現有的 iOS 應用程式，您都必須建立 iTunes Connect 記錄，並使用下列 iOS 特定指南加以設定：

- [建立 iTunes Connect Record](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [管理應用程式的影片和螢幕擷取畫面](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [管理名稱、描述、新增功能、關鍵和 URL](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [維護一般資訊](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

（選擇性）您可能也需要：

- [維護 Game Center 資訊](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [維護在應用程式內購買資訊](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

完成上述所有步驟後，開啟應用程式的 iTunes Connect 記錄，然後選取使用左側提要欄位新增 tvOS 支援：

[![](itunes-connect-images/connect01.png "使用左側提要欄位新增 tvOS 支援")](itunes-connect-images/connect01.png#lightbox)

然後，tvOS 特定資訊畫面就會提供給指定的 iTunes Connect 記錄：

[![](itunes-connect-images/connect02.png "[TvOS 特定資訊] 畫面")](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information" />

## <a name="tvos-version-information"></a>tvOS 版本資訊

從左側提要欄位中，選取 [ **1.0 準備提交**tvOS 應用程式] 區段底下：

[![](itunes-connect-images/connect03.png "tvOS 版本資訊")](itunes-connect-images/connect03.png#lightbox)

在此畫面上，提供下列資訊：

- 必要的螢幕擷取畫面、描述、關鍵字和 Url。
- 一般應用程式資訊，例如版本號碼、著作權和年齡評等。
- 選擇性的應用程式內購買。
- 選擇性 Game Center 支援排行榜和成就。
- 必要的應用程式審查資訊，例如連絡人、示範帳戶和注意事項。

輸入必要資訊之後，請按一下畫面右上角的 [**儲存**] 按鈕來儲存變更：

[![](itunes-connect-images/connect04.png "tvOS 已準備好提交的版本資訊")](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review" />

## <a name="preparing-to-submit-for-review"></a>準備提交以供審查

當您終於準備好要將 tvOS 應用程式提交至 Apple 電視 App Store 進行審查時，請回到應用程式的 iTunes Connect 記錄，然後按一下畫面右上角的 [**提交以供審查**] 按鈕：

[![](itunes-connect-images/connect05.png "提交以供審查")](itunes-connect-images/connect05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>總結

本文概述 iTunes Connect 將 tvOS 應用程式發行至 Apple TV App Store 所需的 tvOS 特定設定。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
