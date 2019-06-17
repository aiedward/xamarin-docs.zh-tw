---
title: 在 iTunes Connect 中設定您的 tvOS 應用程式
description: 這篇文章提供 iOS 應用程式在 tvOS 的特定設定的 iTunes Connect 中設定的補充指南。
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3f4ef00cfe990de2d5afd461d7a110d32bc4a236
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61413193"
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>在 iTunes Connect 中設定您的 tvOS 應用程式

_這篇文章提供 iOS 應用程式在 tvOS 的特定設定的 iTunes Connect 中設定的補充指南。_


除了組態和設定，您必須先遵循 iOS[在 iTunes Connect 中設定您的應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)指南中，本文件涵蓋特定的設定，才能發行 Xamarin.tvOSApple TV App Store 中的應用程式。

<a name="Adding-a-tvOS-Release-Version" />

## <a name="adding-a-tvos-release-version"></a>新增 tvOS 版本

不論您要建立新的應用程式，正式發行日為 Apple TV App Store，或將 Apple TV 支援新增至現有的 iOS 應用程式中，您必須建立 iTunes Connect 記錄並設定它使用下列 iOS 特定指導：

- [建立 iTunes Connect Record](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [管理應用程式的影片和螢幕擷取畫面](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [管理名稱、描述、新增功能、關鍵和 URL](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [維護的一般資訊](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

（選擇性） 您可能也需要：

- [維護 Game Center 資訊](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [維護在應用程式內購買資訊](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

與所有已完成上述步驟中，開啟您的應用程式 iTunes Connect 記錄，然後選取来加入 tvOS 支援使用左側資訊看板：

[![](itunes-connect-images/connect01.png "新增 tvOS 的支援，使用左側資訊看板")](itunes-connect-images/connect01.png#lightbox)

TvOS 的特定資訊畫面則可用的指定 iTunes Connect 記錄：

[![](itunes-connect-images/connect02.png "TvOS 的特定資訊畫面")](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information" />

## <a name="tvos-version-information"></a>tvOS 版本資訊

從左側資訊看板中，選取**1.0 準備提交**tvOS 應用程式 區段下：

[![](itunes-connect-images/connect03.png "tvOS 版本資訊")](itunes-connect-images/connect03.png#lightbox)

在此畫面上提供下列資訊：

- 需要螢幕擷取畫面、 描述、 關鍵字和 Url。
- 應用程式一般資訊，例如版本號碼、 著作權以及年齡分級。
- 選擇性的應用程式內購買項目。
- 選擇性 Game Center 排行榜和成就的支援。
- 需要應用程式檢閱資訊，例如連絡人、 示範帳戶和附註。

一旦您輸入所需的資訊，請按一下**儲存**以儲存變更螢幕的右上角的按鈕：

[![](itunes-connect-images/connect04.png "tvOS 準備好提交的版本資訊")](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review" />

## <a name="preparing-to-submit-for-review"></a>準備提交以供審查

終於可以開始提交至 Apple TV App Store 檢閱 Xamarin.tvOS 應用程式時，請返回 應用程式的 iTunes Connect 記錄，然後按一下**提交以供審查**畫面右上角的按鈕：

[![](itunes-connect-images/connect05.png "提交以供審查")](itunes-connect-images/connect05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章提供在 iTunes Connect 中，才能發行至 Apple TV App Store 的 tvOS 應用程式的 tvOS 特定設定的概觀。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
