---
title: 在 Xamarin.iOS 中的應用程式圖示
description: 本文件說明如何使用在 Xamarin.iOS 中的各種應用程式圖示： 本身的應用程式圖示、 焦點圖示、 設定圖示和 iTunes 插圖。
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/22/2017
ms.openlocfilehash: 1eb3ec77dd478744906625dcfcd9fb843e74577c
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827468"
---
# <a name="application-icons-in-xamarinios"></a>在 Xamarin.iOS 中的應用程式圖示

將會詳細介紹下列主題：

* [應用程式、 焦點和設定圖示](#icon-types)-不同類型的所需的 iOS 應用程式的圖示。
* [管理圖示使用資產目錄](#managing)-管理的應用程式圖示使用資產目錄。
* [iTunes 插圖](#itunes)-提供所需的 iTunes 插圖，提供您的應用程式的特定方法。

<a name="icon-types" />

## <a name="application-spotlight-and-settings-icons"></a>應用程式、 焦點和設定圖示

UI 控制項，以及文件圖示的 Xamarin.iOS 應用程式可以使用影像資產相同的方式，影像資產可用來提供應用程式圖示。 從 iPad 的下列螢幕擷取畫面說明圖示，在 iOS 中的三個用途：

- **應用程式圖示**-每個 iOS 應用程式必須定義應用程式圖示。 這是使用者將點選圖示以啟動應用程式的 iOS 主畫面。 此外，此圖示會使用 Game Center，如果適用的話。 範例： 

    [![](app-icons-images/000.png "應用程式圖示")](app-icons-images/000-full.png#lightbox)
- **焦點圖示**-每當使用者在 Spotlight 搜尋中，輸入應用程式的名稱會顯示這個圖示。 範例： 

    [![](app-icons-images/000a.png "焦點圖示")](app-icons-images/000a-full.png#lightbox)
- **設定圖示**-如果使用者輸入**設定**其 iOS 裝置，此圖示上的應用程式將會顯示在結尾**設定**應用程式的清單。 範例： 

    [![](app-icons-images/000b.png "設定圖示")](app-icons-images/000b-full.png#lightbox)

支援所有所需的目標 iOS 9 （或更新版本） 透過 iOS 5 的 Xamarin.iOS 應用程式的圖示類型將需要映像資產大小與解析度如下：

### <a name="iphone-icon-sizes"></a>iPhone 圖示大小

- **iPhone: iOS 9 和 10 (iPhone 6 & 7 Plus)**

    ||3 倍|
    |---|---|
    |應用程式圖示|180x180|
    |焦點|120x120|
    |設定|87x87|

- **iPhone: iOS 7 和 8**

    ||1x|2x|
    |---|---|---|
    |應用程式圖示|60x60<sup>1</sup>|120x120|
    |焦點|40x40<sup>2</sup>|80x80|
    |設定|-|-|

- **iPhone: iOS 5 和 6**

    ||1x|2x|
    |---|---|---|
    |應用程式圖示|57x57|114x114|
    |焦點|29x29|58x58|
    |設定|29 x 29<sup>3、 4</sup>|58x58<sup>3、 4</sup>|

### <a name="ipad-icon-sizes"></a>iPad 圖示大小

- **iPad: iOS 9 和 10**

    ||2 x (iPad Pro)|
    |---|---|
    |應用程式圖示|167x167<sup>6</sup>|
    |焦點|120x120<sup>6</sup>|
    |設定|58x58<sup>5</sup>|

- **iPad: iOS 7 和 8**

    ||1x|2x|
    |---|---|---|
    |應用程式圖示|76x76|152x152|
    |焦點|40x40|80x80|
    |設定|-|-|

- **iPad: iOS 5 和 6**

    ||1x|2x|
    |---|---|---|
    |應用程式圖示|72x72|144x144|
    |焦點|50x50|100x100|
    |設定|29x29<sup>3, 5</sup>|58x58<sup>3、 5</sup>|

 1. 這兩個適用於 Mac 和 Xcode 的 Visual Studio 不再支援設定適用於 iOS 7 的 1 x 映像。
 2. 不支援設定適用於 iOS 7 的 1x 映像，當使用資產目錄。
 3. iOS 7 和 8 使用相同的映像大小，因為 iOS 5 和 6。
 4. 若要使用相同的映像和大小，請為精選圖示。
 5. 為 iPhone 中使用相同的大小圖示。
 6. 只支援使用資產目錄的影像集。
 
 如需有關圖示的詳細資訊，請參閱 Apple[圖示和影像大小](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1)文件。

<a name="managing" />

## <a name="managing-icons-with-asset-catalogs"></a>管理圖示使用資產目錄

圖示，特殊`AppIcon`影像集可以新增至`Assets.xcassets`應用程式的專案中的檔案。 中包含所有支援所有的解決方案所需的映像版本_xcasset_和群組在一起。 Visual Studio for Mac 中的特殊編輯器可讓開發人員包含，並以圖形方式設定這些映像。

若要使用資產目錄，執行下列作業：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 按兩下`Info.plist`檔案中**方案總管 中**以開啟它進行編輯。
2. 向下捲動至**應用程式圖示**一節。
3. 從**來源**下拉式清單中，確認**Assets.xcassets**選取： 

    ![](app-icons-images/migrate01.png "確定已選取 Assets.xcassets")
4. 從**方案總管**，連按兩下`Assets.xcassets`檔案，以開啟它進行編輯： 

    ![](app-icons-images/asset01.png "在 [方案總管] 中 Assets.xcassets 檔案")
5. 選取 `AppIcon`從清單中顯示的資產`Icon Editor`:

    ![](app-icons-images/asset02.png "Assets.xcassets 編輯器")
6. 按一下指定的圖示類型和選取所需的型別/大小的影像檔或在映像將從資料夾拖曳並放在所需的大小。
7. 按一下 **開啟**納入專案中的映像，並將它設 xcasset 中的按鈕。
8. 重複的所需的所有映像。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 按兩下**Info.plist**中的檔案**方案總管 中**:

    ![](app-icons-images/icon01w.png "選取 Info.plist")
2. 按一下**視覺效果資產**索引標籤，然後按一下**使用資產目錄**按鈕下方**應用程式圖示**: 

    ![](app-icons-images/icon02w.png "選取 [視覺資產] 索引標籤")
4. 從**方案總管**，展開**資產目錄**資料夾： 

    ![](app-icons-images/image009.png "展開 [資產目錄] 資料夾")
5. 按兩下**媒體**檔案以在編輯器中開啟它： 

    ![](app-icons-images/image010.png "在編輯器中開啟媒體檔案")
6. 底下**屬性總管**開發人員可以選取不同的類型和所需的圖示大小。
7. 按一下指定的圖示類型，然後選取所需的型別/大小的影像檔。
8. 按一下 **開啟**納入專案中的映像，並將它設 xcasset 中的按鈕。
9. 重複的所需的所有映像。

-----

這是慣用的方法包括及管理將用來提供應用程式的應用程式、 焦點和設定圖示的影像資產。

### <a name="migrating-from-infoplist-to-asset-catalogs"></a>從 Info.plist 移轉至資產目錄

現有的 Xamarin.iOS 應用程式使用`Info.plist`管理它的圖示檔案中，強烈推薦，開發人員它切換成使用`AppIcons`內的影像資產`Assets.xcassets`。

請執行下列動作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 按兩下`Info.plist`檔案中**方案總管 中**以開啟它進行編輯。
2. 向下捲動至**應用程式圖示**一節。
3. 從**來源**下拉式清單中選取**移轉至資產目錄**: 

    ![](app-icons-images/migrate02.png "選取移轉至資產目錄")
4. 中所定義的任何現有的圖示`Info.plist`檔案將會移轉至`AppIcons`影像集新增至`Assets.xcassets`: 

     ![](app-icons-images/migrate03.png "Assets.xcassets AppIcons 映像集合")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 按兩下`Info.plist`檔案中**方案總管 中**以開啟它進行編輯。
2. 在 iPhone 上按一下圖示 > 一節： 

    ![](app-icons-images/image007.png "以 iPhone 圖示編輯器")
3. 向下捲動至**圖示**一節。
4. 從**資產目錄**下拉式清單中選取**使用資產目錄**。
5. 中所定義的任何現有的圖示`Info.plist`檔案將會移轉至`Images`集新增至`Assets.xcassets`。
6. 將變更儲存至 `Info.plist` 檔案。

-----

<a name="itunes" />

## <a name="itunes-artwork"></a>iTunes 插圖

如果使用提供的應用程式 （無論是公司使用者或 beta 版測試實際裝置上） 的特定方法，開發人員也需要包括 512x512 與 1024x1024 的影像，將用來代表在 iTunes 中的應用程式。

若要指定 iTunes 插圖，請執行下列動作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 按兩下`Info.plist`檔案中**方案總管 中**以開啟它進行編輯。
2. 若要捲動**iTunes 插圖**編輯器區段： 

    ![](app-icons-images/itunes01.png "捲動到 iTunes 插圖編輯器區段")
3. 針對任何缺少的影像，按一下 在編輯器中縮圖上選取所需的 iTunes 插圖的影像檔案，從 開啟檔案 對話方塊中，按一下 **確定** 按鈕。
4. 重複此步驟直到所有所需的映像已指定應用程式。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 按兩下`Info.plist`檔案中**方案總管 中**以開啟它進行編輯。

2. 按一下 **視覺效果資產**索引標籤，展開**iTunes 插圖**: 

    ![](app-icons-images/itunes01w.png "編輯 iTunes 插圖，在 Visual Studio 中")
4. 針對任何缺少的影像，按一下 在編輯器中縮圖上選取所需的 iTunes 插圖的影像檔案，從 開啟檔案 對話方塊中，按一下 **開啟** 按鈕。
5. 重複此步驟直到所有所需的映像已指定應用程式。

-----

## <a name="related-links"></a>相關連結

- [使用映像 （範例）](https://developer.xamarin.com/samples/monotouch/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [自訂圖示和影像建立指導方針](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html))
