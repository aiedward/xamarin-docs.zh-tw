---
title: 控制項
description: Xamarin.iOS 公開 Apple 提供的所有原生使用者介面物件。 輕鬆地加入 Xamarin.iOS 應用程式使用 iOS 設計工具中，Xcode 的介面產生器或以程式設計的方式。 不論您選擇的方法，Xamarin.iOS 會公開所有的使用者介面物件的屬性和 C# 中的方法。
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 82b2998319d4e78ee4f58a6d024032a509724537
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="controls"></a>控制項

_Xamarin.iOS 公開 Apple 提供的所有原生使用者介面物件。輕鬆地加入 Xamarin.iOS 應用程式使用 iOS 設計工具中，Xcode 的介面產生器或以程式設計的方式。不論您選擇的方法，Xamarin.iOS 會公開所有的使用者介面物件的屬性和 C# 中的方法。_

本文件將介紹一些最常見的 iOS 使用者介面控制項，以及如何使用它們。

## <a name="alertsalertsmd"></a>[警示](alerts.md)

從 iOS 8 開始，已完成取代的 UIActionSheet UIAlertController 和其中 UIAlertView 現在已被取代。

## <a name="buttonsbuttonsmd"></a>[按鈕](buttons.md)

UIButton 類別用於表示按鈕 iOS 螢幕中的各種不同的樣式。 本節將介紹 iOS 中的按鈕所使用的不同選項。

## <a name="collection-viewsuicollectionviewmd"></a>[集合檢視](uicollectionview.md)

中可用的集合檢視`UICollectionView`類別中，會使用配置在螢幕上呈現多個項目會導入的 iOS 6 中的新概念。 提供資料至模式`UICollectionView`建立項目，並與其互動的相同委派和資料來源模式常用於 iOS 開發這些項目遵循。

## <a name="imagesimagemd"></a>[影像](image.md)

將影像加入至您的應用程式需要兩個步驟： 首先，將影像加入至您的專案。然後，加入控制項和它們在螢幕上顯示的程式碼。 請參閱[處理映像](~/ios/app-fundamentals/images-icons/index.md)文件如需詳細的映像處理 Xamarin.iOS 涵蓋範圍。

## <a name="manual-camera-controlsintro-to-manual-camera-controlsmd"></a>[手動相機控制項](intro-to-manual-camera-controls.md)

提供的手冊攝影機控制項`AVFoundation Framework`中 iOS 8，允許行動裝置的應用程式，以便對於 iOS 裝置的相機的完整控制權。 這個更細緻的控制層級可以用於建立專業的層級相機應用程式，並提供演出者組合所佔用的靜態影像或視訊調整相機的參數。

## <a name="mapsios-mapsindexmd"></a>[地圖](ios-maps/index.md)

地圖是所有現代的行動裝置作業系統中的常見功能。 iOS 提供原生透過對應組件架構的對應支援。 使用對應的套件，應用程式可以輕鬆加入豐富、 互動式的對應。 您可以自訂這些對應中有許多種，例如加上註解標記地圖上的位置，以及覆疊圖形的任意形狀。 對應組件即使具有用於顯示裝置的目前位置的內建支援。

## <a name="labelslabelsmd"></a>[標籤](labels.md)

`UILabel`控制項用來顯示單一和多行，唯讀文字。

## <a name="pickers-and-date-pickerspickermd"></a>[選擇器和日期選擇器](picker.md)

選擇器控制項顯示 '滾輪類似' 控制項包含可捲動的值，選取要反白顯示的值清單。 使用者旋轉滾輪以選取他們想要的選項。

一個特定的使用者案例的選擇器設定的日期和/或時間。 若要提供此 apple 已建立稱為 UIDatePicker UIPickerView 類別的自訂子類別。

## <a name="progress-and-activity-indicatorsprogress-activity-indicatormd"></a>[進度和活動的指標](progress-activity-indicator.md)

iOS 提供兩種主要的方式，以指出應用程式中的進度： 標記活動 (包括特定_網路_活動指示器) 和進度列。

## <a name="search-barssearchbarmd"></a>[搜尋列](searchbar.md)

UISearchBar 用來搜尋值的清單。 

## <a name="sliders-steppers-and-segmented-controlsslider-switch-segmented-controlsmd"></a>[滑桿、 Stepper 和分割的控制項](slider-switch-segmented-controls.md)

滑桿控制項允許的範圍內的數字值的簡單的選取項目。 使用 iOS`UISwitch`布林值的輸入，可能會由其他平台上使用選項按鈕。 分割控制項是組織的方式，可讓使用者互動少數的選項。

## <a name="stack-viewuistackviewmd"></a>[堆疊檢視](uistackview.md)

堆疊檢視控制項 (`UIStackView`) 會利用自動配置和大小類別來管理一堆 subviews，水平或垂直方向，iOS 裝置的方向和螢幕大小會動態地回應。

## <a name="tables-and-cellstablesindexmd"></a>[資料表和儲存格](tables/index.md)

他 > 一節介紹用來建立及顯示資料表的類別，然後提供如何使用這些 Xamarin.iOS 中的範例。 會涵蓋自訂版面配置，實作編輯和使用 Xamarin iOS 設計工具以視覺化方式設計資料表的資料表，使用的預設外觀。 有時候顯示顯然是資料列 （例如音樂應用程式） 和有時候很難辨識表格控制項 （例如編輯連絡人應用程式中或交談中訊息 」 應用程式中） 的清單。

## <a name="text-inputtext-inputmd"></a>[文字輸入](text-input.md)

利用接受使用者文字輸入來完成`UITextField`單行輸入與 UITextView 為多行編輯的文字。 您可以任何這些控制項拖曳到螢幕，並按兩下，設定初始文字。

## <a name="tab-bars-and-tab-bar-controllerscreating-tabbed-applicationsmd"></a>[索引標籤列和索引標籤列控制項](creating-tabbed-applications.md)

使用 tab 導覽 UI 的 iOS 應用程式會使用 UITabBarController 類別建立。 本文章中我們將逐步解說如何設定包含數個控制器和檢視的索引標籤式應用程式。 然後，我們將檢驗如何載入 UITabBarController 時，就會為根控制站，例如登入畫面之後。

## <a name="web-viewsuiwebviewmd"></a>[Web 檢視](uiwebview.md)

在本文中，我們將探討每個由 Apple 提供的三個 Web 檢視： `UIWebView`， `WKWebview`，和`SFSafariViewController`、 其相似性和差異，以及如何使用。

## <a name="related-links"></a>相關連結

- [控制項 （範例）](https://developer.xamarin.com/samples/Controls/)
