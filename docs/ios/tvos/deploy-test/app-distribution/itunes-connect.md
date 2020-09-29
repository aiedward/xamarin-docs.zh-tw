---
title: 在 iTunes Connect 中設定您的 tvOS 應用程式
description: 本文提供 iOS 補充指南，說明如何在 iTunes Connect 中設定您的應用程式以進行 tvOS 特定設定。
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 43692bf2180887e7983cf35fb1812a91222dbc7a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435157"
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>在 iTunes Connect 中設定您的 tvOS 應用程式

_本文提供 iOS 補充指南，說明如何在 iTunes Connect 中設定您的應用程式以進行 tvOS 特定設定。_

除了您在 [ITunes Connect 指南中設定應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) 所需進行的設定和設定之外，本檔也涵蓋在 Apple TV App Store 中發行 Xamarin. tvOS 應用程式所需的特定設定。

<a name="Adding-a-tvOS-Release-Version"></a>

## <a name="adding-a-tvos-release-version"></a>新增 tvOS 發行版本

無論您要建立要在 Apple TV App Store 上發行的新應用程式，或是將 Apple TV 支援新增至現有的 iOS 應用程式，您都必須建立 iTunes Connect 記錄，並使用下列 iOS 特定指南進行設定：

- [建立 iTunes Connect Record](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [管理應用程式的影片和螢幕擷取畫面](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [管理名稱、描述、新增功能、關鍵字和URL](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [維護一般資訊](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

或者，您可能也需要：

- [維護 Game Center 資訊](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [維護在應用程式內購買資訊](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

完成上述所有步驟後，開啟您應用程式的 iTunes Connect 記錄，然後選取使用左側提要欄位新增 tvOS 支援：

[![使用左邊的提要欄位新增 tvOS 支援](itunes-connect-images/connect01.png)](itunes-connect-images/connect01.png#lightbox)

然後，tvOS 特定的資訊畫面將可用於指定的 iTunes Connect 記錄：

[![TvOS 特定資訊畫面](itunes-connect-images/connect02.png)](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information"></a>

## <a name="tvos-version-information"></a>tvOS 版本資訊

從左側邊欄中，選取 [tvOS 應用程式] 區段下的 [ **1.0 準備提交** ]：

[![tvOS 版本資訊](itunes-connect-images/connect03.png)](itunes-connect-images/connect03.png#lightbox)

在此畫面上，提供下列資訊：

- 必要的螢幕擷取畫面、描述、關鍵字和 Url。
- 一般的應用程式資訊，例如版本號碼、著作權和年齡評等。
- 選擇性的應用程式內購買。
- 選用 Game Center 支援排行榜和成就。
- 必要的應用程式評論資訊，例如 Contact、Demo Accounts 和 Notes。

輸入必要資訊之後，請按一下畫面右上角的 [ **儲存** ] 按鈕來儲存變更：

[![tvOS 可供提交的版本資訊](itunes-connect-images/connect04.png)](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review"></a>

## <a name="preparing-to-submit-for-review"></a>準備提交以供審查

當您最後準備好要將 tvOS 應用程式提交至 Apple TV App Store 以供審查時，請返回應用程式的 iTunes Connect 記錄，然後按一下畫面右上角的 [ **提交進行審核** ] 按鈕：

[![提交以供審查](itunes-connect-images/connect05.png)](itunes-connect-images/connect05.png#lightbox)

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文概述 iTunes Connect 將 tvOS 應用程式發行至 Apple TV App Store 所需的 tvOS 特定設定。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)