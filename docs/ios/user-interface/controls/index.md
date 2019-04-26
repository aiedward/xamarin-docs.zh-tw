---
title: 在 Xamarin.iOS 中的使用者介面控制項
description: 指南說明 Xamarin.iOS 開發人員可使用的各種不同的 iOS 使用者介面控制項，此文件連結。 連結的內容討論警示、 按鈕、 集合檢視、 映像、 手動相機控制項、 對應、 標籤、 選擇器、 日期選擇器，以及更多。
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: d5b7d4a372704079343a357c1d341a5260fcf583
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61157412"
---
# <a name="user-interface-controls-in-xamarinios"></a>在 Xamarin.iOS 中的使用者介面控制項

本文件介紹一些最常見的 iOS 使用者介面控制項，以及如何使用它們。

## <a name="alertsalertsmd"></a>[警示](alerts.md)

從 iOS 8 開始，已完成的已取代的 UIActionSheet UIAlertController 和其中 UIAlertView 現在已被取代。

## <a name="buttonsbuttonsmd"></a>[按鈕](buttons.md)

標記的 UIButton 類別用來代表各種不同的 iOS 畫面的按鈕樣式。 本節將介紹使用按鈕在 iOS 中的不同選項。

## <a name="collection-viewsuicollectionviewmd"></a>[集合檢視](uicollectionview.md)

中可用的集合檢視`UICollectionView`類別中，是在 iOS 6 中的新概念介紹使用版面配置在螢幕上呈現多個項目。 提供資料至模式`UICollectionView`建立項目，以及與這些相同的委派和資料來源模式的 iOS 開發中常用的項目後續互動。

## <a name="imagesimagemd"></a>[影像](image.md)

將映像新增至您的應用程式需要兩個步驟： 首先，將影像加入至您的專案;然後，新增控制項以及它們在螢幕上顯示的程式碼。 請參閱[處理映像](~/ios/app-fundamentals/images-icons/index.md)一文，以更深入探討在 Xamarin.iOS 中處理的映像。

## <a name="manual-camera-controlsintro-to-manual-camera-controlsmd"></a>[手動相機控制項](intro-to-manual-camera-controls.md)

所提供之手動相機控制項`AVFoundation Framework`在 iOS 8 中，允許行動裝置的應用程式，以便完全控制 iOS 裝置的相機。 這個更細緻的控制層級可以用於建立專業的層級相機應用程式，並提供調整觀景窗的參數，同時仍的映像或影片的演出者的組合。

## <a name="mapsios-mapsindexmd"></a>[地圖](ios-maps/index.md)

對應是在所有現代的行動作業系統中的常見功能。 iOS 提供原生透過 Map Kit 架構的對應支援。 Map Kit 與應用程式可以輕鬆地加入豐富的互動式地圖。 在各種不同的方式，例如加入註解標記在地圖上的位置，以及在覆疊的任意形狀的圖形中，您可以自訂這些對應。 Map Kit 甚至還有用於顯示裝置的目前位置的內建支援。

## <a name="labelslabelsmd"></a>[標籤](labels.md)

`UILabel`控制項用來顯示單一和多行，唯讀文字。

## <a name="pickers-and-date-pickerspickermd"></a>[選擇器和日期選擇器](picker.md)

選擇器控制項會顯示 '滾輪類似' 控制項，其中包含可捲動的值，且選取要反白顯示的值清單。 使用者會旋轉滾輪以選取他們想要的選項。

一個特定的使用者情況下，選擇器針對它設定的日期和/或時間。 為了提供此 apple 已建立稱為 UIDatePicker UIPickerView 類別的自訂子類別。

## <a name="progress-and-activity-indicatorsprogress-activity-indicatormd"></a>[進度和活動的指標](progress-activity-indicator.md)

iOS 會提供兩種主要的方式來表示應用程式中的進度：活動的指標 (包括特定_網路_活動指示器) 和進度列。

## <a name="search-barssearchbarmd"></a>[搜尋列](searchbar.md)

UISearchBar 用來搜尋值的清單。 

## <a name="sliders-switches-and-segmented-controlsslider-switch-segmented-controlsmd"></a>[滑桿、開關與分段控制項](slider-switch-segmented-controls.md)

滑桿控制項允許的範圍內的數字值的簡單的選取項目。 iOS 使用`UISwitch`布林值的輸入，可能會由其他平台上使用選項按鈕。 分段控制項是組織的方式，以允許使用者與少數選項的互動。

## <a name="stack-viewuistackviewmd"></a>[堆疊檢視](uistackview.md)

堆疊檢視控制項 (`UIStackView`) 利用自動版面配置和大小類別來管理子檢視的堆疊水平或垂直方向和螢幕大小的 iOS 裝置會以動態方式回應。

## <a name="tables-and-cellstablesindexmd"></a>[資料表和儲存格](tables/index.md)

他一節介紹用來建立並顯示資料表的類別，然後提供範例，示範如何在 Xamarin.iOS 中使用它們。 它將涵蓋用於自訂配置，實作編輯和使用 Xamarin iOS 設計工具以視覺化方式設計資料表的資料表中的預設外觀。 有時候顯示顯然是 （例如音樂應用程式） 的資料列，而有時候很難辨識表格控制項 （例如編輯連絡人 」 應用程式或交談中訊息 」 應用程式中） 的清單。

## <a name="text-inputtext-inputmd"></a>[文字輸入](text-input.md)

接受使用者文字輸入以完成`UITextField`單行輸入和多行編輯文字的 UITextView。 您可以拖曳任一個控制項到螢幕，並按兩下，設定初始文字。

## <a name="tab-bars-and-tab-bar-controllerscreating-tabbed-applicationsmd"></a>[索引標籤列和索引標籤列控制項](creating-tabbed-applications.md)

使用索引標籤巡覽 UI 的 iOS 應用程式會使用 UITabBarController 類別建置的。 這篇文章中我們將逐步解說如何設定包含數個控制器和檢視的索引標籤式應用程式。 然後，我們將檢驗如何載入 UITabBarController，當它之後，不到根控制站，例如登入畫面。

## <a name="web-viewsuiwebviewmd"></a>[Web 檢視](uiwebview.md)

在本文中，我們將探討每個 Apple 提供的三個 Web 檢視： `UIWebView`， `WKWebview`，和`SFSafariViewController`、 其相似之處和差異，以及如何使用。

## <a name="related-links"></a>相關連結

- [控制項 （範例）](https://developer.xamarin.com/samples/Controls/)
