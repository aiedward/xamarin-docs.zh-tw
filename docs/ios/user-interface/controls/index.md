---
title: Xamarin 中的使用者介面控制項
description: 本檔連結的指南會描述適用于 Xamarin iOS 開發人員的各種 iOS 使用者介面控制項。 連結的內容會討論警示、按鈕、集合視圖、影像、手動相機控制項、地圖、標籤、選擇器、日期選擇器等等。
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 2979b51c954ab891da5e452e579a650a013a7572
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289541"
---
# <a name="user-interface-controls-in-xamarinios"></a>Xamarin 中的使用者介面控制項

本檔介紹一些最常見的 iOS 使用者介面控制項，以及如何使用它們。

## <a name="alertsalertsmd"></a>[警示](alerts.md)

從 iOS 8 開始，UIAlertController 已完成取代的 UIActionSheet 和 UIAlertView，兩者現在已被取代。

## <a name="buttonsbuttonsmd"></a>[按鈕](buttons.md)

UIButton 類別是用來代表 iOS 畫面中各種不同的按鈕樣式。 本節介紹在 iOS 中使用按鈕的不同選項。

## <a name="collection-viewsuicollectionviewmd"></a>[集合檢視](uicollectionview.md)

類別中提供的`UICollectionView`集合 Views 是 iOS 6 中的新概念，引進使用版面配置在螢幕上呈現多個專案。 將資料`UICollectionView`提供給來建立專案並與這些專案互動的模式，會遵循常用於 iOS 開發的相同委派和資料來源模式。

## <a name="imagesimagemd"></a>[影像](image.md)

將影像新增至您的應用程式需要兩個步驟：首先，將影像新增至您的專案;然後，新增控制項和程式碼，以將它們顯示在螢幕上。 請參閱[使用影像](~/ios/app-fundamentals/images-icons/index.md)一文，以取得在 Xamarin 中更詳細的影像處理涵蓋範圍。

## <a name="manual-camera-controlsintro-to-manual-camera-controlsmd"></a>[手動相機控制項](intro-to-manual-camera-controls.md)

Ios 8 `AVFoundation Framework`中提供的手動相機控制項可讓行動應用程式完全控制 ios 裝置的相機。 這個精細的控制層級可用來建立專業層級的相機應用程式，並在拍攝靜止影像或影片時，藉由調整相機的參數來提供演出者組合。

## <a name="mapsios-mapsindexmd"></a>[地圖](ios-maps/index.md)

Maps 是所有新式行動作業系統中的一項常見功能。 iOS 透過「地圖套件架構」提供原本的對應支援。 透過地圖套件，應用程式可以輕鬆地加入豐富的互動式地圖。 這些對應可以透過各種方式進行自訂，例如新增注釋以標記地圖上的位置，以及覆迭任意圖形的圖形。 地圖套件甚至具有內建支援，可顯示裝置的目前位置。

## <a name="labelslabelsmd"></a>[標籤](labels.md)

`UILabel`控制項用於顯示單一和多行的唯讀文字。

## <a name="pickers-and-date-pickerspickermd"></a>[選擇器和日期選擇器](picker.md)

選擇器控制項會顯示「滾輪」控制項，其中包含具有反白顯示之所選值的可滾動值清單。 使用者會旋轉滾輪以選取他們想要的選項。

其中一個特定的使用者案例，可供選擇器來設定日期和/或時間。 為了提供此 Apple，已建立名為 UIDatePicker 的 UIPickerView 類別的自訂子類別。

## <a name="progress-and-activity-indicatorsprogress-activity-indicatormd"></a>[進度和活動的指標](progress-activity-indicator.md)

iOS 提供兩種主要方式來表示應用程式中的進度：活動指示器（包括特定的_網路_活動指標）和進度列。

## <a name="search-barssearchbarmd"></a>[搜尋列](searchbar.md)

UISearchBar 是用來搜尋值清單。 

## <a name="sliders-switches-and-segmented-controlsslider-switch-segmented-controlsmd"></a>[滑桿、開關與分段控制項](slider-switch-segmented-controls.md)

滑杆控制項可讓您簡單選擇範圍內的數值。 iOS 會使用`UISwitch`做為布林值輸入，在其他平臺上可以用選項按鈕表示。 分段的控制項是一種可讓使用者與少數選項互動的組織方式。

## <a name="stack-viewuistackviewmd"></a>[堆疊檢視](uistackview.md)

Stack 視圖控制項（`UIStackView`）會利用自動設定和大小類別的功能來管理子檢視堆疊（不論是水準或垂直），這會動態回應 iOS 裝置的方向和螢幕大小。

## <a name="tables-and-cellstablesindexmd"></a>[資料表和儲存格](tables/index.md)

他的章節介紹用來建立和顯示資料表的類別，然後提供如何在 Xamarin 中使用它們的範例。 它將涵蓋使用資料表的預設面板、自訂配置、執行編輯，以及使用 Xamarin iOS 設計工具以視覺化方式設計資料表。 有時顯示的是資料列清單（例如音樂應用程式），以及其他難以辨識資料表控制項的時間（例如，在連絡人應用程式中編輯，或是在 [訊息] 應用程式中的交談）。

## <a name="text-inputtext-inputmd"></a>[文字輸入](text-input.md)

接受使用者文字輸入是透過`UITextField`適用于多行可編輯文字的單行輸入和 UITextView 來完成。 您可以將其中一個控制項拖曳到螢幕上，然後按兩下以設定初始文字。

## <a name="tab-bars-and-tab-bar-controllerscreating-tabbed-applicationsmd"></a>[索引標籤列和索引標籤列控制項](creating-tabbed-applications.md)

使用索引標籤導覽 UI 的 iOS 應用程式會使用 UITabBarController 類別來建立。 在本文中，我們將逐步解說如何設定包含數個控制器和 views 的索引標籤式應用程式。 然後，我們將檢查當 UITabBarController 不是根控制器（例如登入畫面之後）時，如何載入它。

## <a name="web-viewsuiwebviewmd"></a>[Web 檢視](uiwebview.md)

在本文中，我們將探討 Apple 所提供的三個 Web 瀏覽器： `UIWebView`、 `WKWebview`和`SFSafariViewController`、其相似和差異，以及其使用方式。

## <a name="related-links"></a>相關連結

- [控制項（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
