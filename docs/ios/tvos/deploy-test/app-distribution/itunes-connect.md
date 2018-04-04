---
title: 在 iTunes Connect 中設定您 tvOS 應用程式
description: 本文章提供 iOS 應用程式，在 iTunes Connect tvOS 特定組態中設定的補充指南。
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: d082a980572349da1b7e6155b3aa4de41512796f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>在 iTunes Connect 中設定您 tvOS 應用程式

_本文章提供 iOS 應用程式，在 iTunes Connect tvOS 特定組態中設定的補充指南。_


除了組態和設定，您必須對遵循 iOS[在 iTunes Connect 中設定您的應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)指南中，本文件涵蓋的特定設定，才能釋放 Xamarin.tvOSApple TV App Store 中的應用程式。

<a name="Adding-a-tvOS-Release-Version" />

## <a name="adding-a-tvos-release-version"></a>加入 tvOS 發行版本

您要建立新的應用程式釋出 Apple TV App Store 上的還是將 Apple TV 支援加入至現有的 iOS 應用程式，您必須建立 iTunes Connect 記錄並設定它使用下列 iOS 特定引導：

- [建立 iTunes Connect Record](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [管理應用程式的影片和螢幕擷取畫面](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [管理名稱、描述、新增功能、關鍵和 URL](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [維護的一般資訊](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

（選擇性） 您可能也需要：

- [維護 Game Center 資訊](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [維護在應用程式內購買資訊](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

所有已完成上述步驟中，開啟您的應用程式 iTunes Connect 記錄並選取来加入 tvOS 支援使用左提要欄位：

[![](itunes-connect-images/connect01.png "加入 tvOS 支援使用資訊看板左手")](itunes-connect-images/connect01.png#lightbox)

TvOS 特定資訊畫面然後可供指定的連入 iTunes Connect 記錄：

[![](itunes-connect-images/connect02.png "[TvOS 特定資訊] 畫面")](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information" />

## <a name="tvos-version-information"></a>tvOS 版本資訊

在左提要欄位中，選取 [ **1.0 準備送出**tvOS 應用程式] 區段下方：

[![](itunes-connect-images/connect03.png "tvOS 版本資訊")](itunes-connect-images/connect03.png#lightbox)

在這個畫面上提供下列資訊：

- 必要的螢幕擷取畫面、 描述、 關鍵字和 Url。
- 一般應用程式資訊，例如版本號碼、 版權和年齡分級。
- 選擇性的應用程式內購買。
- 選擇性 Game Center 支援的排行榜和成果。
- 必要的應用程式檢閱資訊，例如連絡人、 示範帳戶和附註。

當您輸入必要的資訊之後時，按一下**儲存**以儲存變更螢幕的右上角的按鈕：

[![](itunes-connect-images/connect04.png "tvOS 準備送出的版本資訊")](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review" />

## <a name="preparing-to-submit-for-review"></a>準備要提交進行檢閱

最後送出您的 Apple TV App Store 審核 Xamarin.tvOS 應用程式準備好時，返回 應用程式的 iTunes Connect 記錄，然後按一下**提交檢閱**螢幕右上角的按鈕：

[![](itunes-connect-images/connect05.png "提交的檢閱")](itunes-connect-images/connect05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>總結

本文提供版本 Apple TV App Store 的 tvOS 應用程式需要在 iTunes Connect 中的 tvOS 特定設定的概觀。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
