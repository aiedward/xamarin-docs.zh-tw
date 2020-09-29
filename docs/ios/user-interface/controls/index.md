---
title: 在 Xamarin 中消費者介面控制項
description: 本檔連結的指南會說明適用于 Xamarin iOS 開發人員的各種 iOS 使用者介面控制項。 連結的內容會討論警示、按鈕、集合視圖、影像、手動相機控制項、地圖、標籤、選擇器、日期選擇器等等。
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 336e2468a3532b300697ed7fe596864e1bcf9622
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433971"
---
# <a name="user-interface-controls-in-xamarinios"></a>在 Xamarin 中消費者介面控制項

本檔介紹一些最常見的 iOS 使用者介面控制項，以及如何使用它們。

## <a name="alerts"></a>[警示](alerts.md)

從 iOS 8 開始，UIAlertController 已完全取代 UIActionSheet 和 UIAlertView，兩者現在已被取代。

## <a name="buttons"></a>[按鈕](buttons.md)

UIButton 類別是用來表示 iOS 畫面中各種不同的按鈕樣式。 本節介紹在 iOS 中使用按鈕的不同選項。

## <a name="collection-views"></a>[集合檢視](uicollectionview.md)

類別中提供的集合視圖 `UICollectionView` 是 iOS 6 中的新概念，可在螢幕上使用版面配置來呈現多個專案。 將資料提供給 `UICollectionView` 來建立專案並與這些專案互動的模式，會遵循 iOS 開發中常用的相同委派和資料來源模式。

## <a name="images"></a>[影像](image.md)

將影像新增至您的應用程式需要兩個步驟：首先，將影像新增至您的專案;然後，新增控制項和程式碼，以將它們顯示在畫面上。 請參閱「使用 [影像](~/ios/app-fundamentals/images-icons/index.md) 」一文，以取得更詳細的在 Xamarin 中處理影像的涵蓋範圍。

## <a name="manual-camera-controls"></a>[手動相機控制項](intro-to-manual-camera-controls.md)

IOS 8 提供的手動攝影機控制項， `AVFoundation Framework` 可讓行動應用程式完全掌控 ios 裝置的相機。 這種精細的控制層級可用來建立專業級的相機應用程式，並在拍攝靜止影像或影片的同時，藉由調整相機的參數來提供演出者組合。

## <a name="maps"></a>[地圖](ios-maps/index.md)

Maps 是所有新式行動作業系統中的常見功能。 iOS 透過地圖套件架構以原生方式提供地圖支援。 使用地圖套件，應用程式可以輕鬆地加入豐富的互動式地圖。 您可以透過各種方式自訂這些對應，例如新增批註以標示地圖上的位置，以及覆迭任意圖形的圖形。 地圖套件甚至有內建的支援，可顯示裝置目前的位置。

## <a name="labels"></a>[標籤](labels.md)

`UILabel`控制項可用來顯示單一和多行的唯讀文字。

## <a name="pickers-and-date-pickers"></a>[選擇器和日期選擇器](picker.md)

選擇器控制項會顯示「後端」控制項，其中包含要反白顯示所選取值的值的可滾動清單。 使用者會旋轉滾輪以選取他們想要的選項。

一個特定的使用者案例，可供選擇器用來設定日期和/或時間。 為了提供此 Apple，已建立名為 UIDatePicker 的 UIPickerView 類別的自訂子類別。

## <a name="progress-and-activity-indicators"></a>[進度和活動的指標](progress-activity-indicator.md)

iOS 提供兩種主要方式來表示應用程式中的進度：活動指標 (包括特定的 _網路_ 活動指標) 和進度列。

## <a name="search-bars"></a>[搜尋列](searchbar.md)

UISearchBar 用來搜尋值的清單。 

## <a name="sliders-switches-and-segmented-controls"></a>[滑桿、開關與區段控制項](slider-switch-segmented-controls.md)

滑杆控制項可讓您在範圍內簡單選取數值。 iOS 會使用 `UISwitch` 做為布林輸入，可在其他平臺上以選項按鈕表示。 分割的控制項是一種有條理的方式，可讓使用者與少量的選項互動。

## <a name="stack-view"></a>[堆疊檢視](uistackview.md)

Stack View 控制項 (`UIStackView`) 利用自動設定和大小類別的功能，以水準或垂直方式管理子檢視堆疊，其會動態回應 iOS 裝置的方向和螢幕大小。

## <a name="tables-and-cells"></a>[表格和儲存格](tables/index.md)

本節介紹用來建立和顯示資料表的類別，然後提供如何在 Xamarin 中使用這些類別的範例。 它將涵蓋如何使用資料表的預設面板、自訂配置、執行編輯，以及使用 Xamarin iOS 設計工具以視覺化方式設計資料表。 有時顯示是資料列的清單， (例如「音樂」應用程式) 和其他時候很難辨識表格控制項 (例如在「連絡人」應用程式中編輯，或是在「訊息」應用程式) 中的對話。

## <a name="text-input"></a>[文字輸入](text-input.md)

接受使用者文字輸入的作業是使用 `UITextField` 適用于多行可編輯文字的單一行輸入和 UITextView 來完成。 您可以將其中一個控制項拖曳到螢幕上，然後按兩下以設定初始文字。

## <a name="tab-bars-and-tab-bar-controllers"></a>[索引標籤列和索引標籤列控制項](creating-tabbed-applications.md)

使用 tab 導覽 UI 的 iOS 應用程式是使用 UITabBarController 類別所建立。 在本文中，我們將逐步解說如何設定包含數個控制器和 views 的索引標籤式應用程式。 接著，我們將探討如何在非根控制器（例如登入畫面之後）載入 UITabBarController。

## <a name="web-views"></a>[Web 檢視](webview.md)

在本文中，我們將探討 Apple 所提供的網頁瀏覽器，以及它們的 `WKWebview` `SFSafariViewController` 相似性和差異，以及其使用方式。

## <a name="related-links"></a>相關連結

- [控制項 (範例) ](/samples/xamarin/ios-samples/controls)