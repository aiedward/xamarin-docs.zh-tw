---
title: Xamarin.iOS 應用程式圖示
description: 本文件說明如何使用各種應用程式圖示中 Xamarin.iOS： 本身的應用程式圖示，精選圖示、 設定圖示和 iTunes 圖檔。
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/22/2017
ms.openlocfilehash: 3c07f2573aa8ac6e28b2cd6bff56a773e6206aea
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783993"
---
# <a name="application-icons-in-xamarinios"></a>Xamarin.iOS 應用程式圖示

將會詳細介紹下列主題：

* [應用程式、 焦點和設定圖示](#icon-types)-不同類型的所需的 iOS 應用程式的圖示。
* [管理資產目錄圖示](#managing)-使用資產目錄管理的應用程式圖示。
* [iTunes 作品](#itunes)-提供特定方法，傳遞您的應用程式需要的 iTunes 圖檔。

<a name="icon-types" />

## <a name="application-spotlight-and-settings-icons"></a>應用程式、 焦點，以及設定圖示

UI 控制項，以及文件圖示 Xamarin.iOS 應用程式可以使用影像資產的相同方式，在影像資產可用來提供應用程式的圖示。 從 iPad 的下列螢幕擷取畫面說明圖示，在 iOS 中三個用途：

- **應用程式圖示**-每個 iOS 應用程式必須定義應用程式圖示。 這是使用者會點選圖示從 [iOS] 主畫面啟動應用程式。 此外，使用這個圖示是由遊戲中心的話。 範例： 

    [![](app-icons-images/000.png "應用程式圖示")](app-icons-images/000-full.png#lightbox)
- **醒目圖示**-每當使用者在 Spotlight 搜尋中，輸入應用程式的名稱會顯示這個圖示。 範例： 

    [![](app-icons-images/000a.png "焦點圖示")](app-icons-images/000a-full.png#lightbox)
- **設定圖示**-如果使用者輸入**設定**其 iOS 裝置，此圖示上的應用程式將會顯示在結尾**設定**應用程式的清單。 範例： 

    [![](app-icons-images/000b.png "設定圖示")](app-icons-images/000b-full.png#lightbox)

下列影像資產的大小和解析度需要支援所有目標 iOS 9 （或更高） 到 iOS 5 Xamarin.iOS 應用程式所需的圖示類型：

### <a name="iphone-icon-sizes"></a>iPhone 圖示大小

- **iPhone: iOS 9 和 10 (iPhone 6 及 7 加上)**

    ||3 倍|
    |---|---|
    |應用程式圖示|180x180|
    |精選|120x120|
    |設定|87x87|

- **iPhone: iOS 7 和 8**

    ||1 x|2x|
    |---|---|---|
    |應用程式圖示|60x60<sup>1</sup>|120x120|
    |精選|40x40<sup>2</sup>|80x80|
    |設定|-|-|

- **iPhone: iOS 5 和 6**

    ||1 x|2x|
    |---|---|---|
    |應用程式圖示|57x57|114x114|
    |精選|29x29|58x58|
    |設定|29 x 29<sup>3、 4</sup>|58x58<sup>3、 4</sup>|

### <a name="ipad-icon-sizes"></a>iPad 圖示大小

- **iPad: iOS 9 和 10**

    ||2 x (iPad Pro)|
    |---|---|
    |應用程式圖示|167x167<sup>6</sup>|
    |精選|120x120<sup>6</sup>|
    |設定|58x58<sup>5</sup>|

- **iPad: iOS 7 和 8**

    ||1 x|2x|
    |---|---|---|
    |應用程式圖示|76x76|152x152|
    |精選|40x40|80x80|
    |設定|-|-|

- **iPad: iOS 5 和 6**

    ||1 x|2x|
    |---|---|---|
    |應用程式圖示|72x72|144x144|
    |精選|50x50|100x100|
    |設定|29 x 29<sup>3，5</sup>|58x58<sup>3，5</sup>|

 1. 針對 Mac 和 Xcode 這兩個 Visual Studio 不再支援設定適用於 iOS 7 1 x 影像。
 2. 不支援設定適用於 iOS 7 1 x 映像，當使用資產目錄。
 3. iOS 7 和 8 使用相同的映像大小為 iOS 5 和 6。
 4. 以焦點圖示，會使用相同的映像和大小。
 5. IPhone 為使用相同的大小圖示。
 6. 僅支援資產目錄映像集。
 
 如需有關圖示的詳細資訊，請參閱 Apple[圖示和映像大小](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1)文件。

<a name="managing" />

## <a name="managing-icons-with-asset-catalogs"></a>管理資產目錄圖示

針對特殊圖示`AppIcons`映像集便可加入至`Assets.xcassets`應用程式的專案中的檔案。 所有版本支援所有解析度所需的映像中都包含_xcasset_和分組在一起。 適用於 Mac 的 Visual Studio 中的特殊編輯器可讓開發人員包括，並以圖形方式設定這些映像。

若要使用資產目錄，執行下列作業：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 按兩下`Info.plist`檔案**方案總管 中**開啟進行編輯。
2. 向下捲動至**應用程式圖示**> 一節。
3. 從**來源**下拉式清單中，確認**AppIcon**選取： 

    ![](app-icons-images/migrate01.png "確定已選取 AppIcons")
4. 從**方案總管 中**，連按兩下`Assets.xcassets`檔案，以開啟它進行編輯： 

    ![](app-icons-images/asset01.png "在 [方案總管] 中 Assets.xcassets 檔案")
5. 選取`AppIcons`從顯示的資產清單`Icon Editor`: 

    ![](app-icons-images/asset02.png "AppIcons 編輯器")
6. 按一下指定的圖示類型和選取所需的型別/大小的影像檔或在映像將從資料夾拖曳並放置在所需的大小。
7. 按一下**開啟**專案中包含影像，並將它設 xcasset 中的按鈕。
8. 重複所需的所有映像。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 按兩下**Info.plist**檔案**方案總管 中**:

    ![](app-icons-images/icon01w.png "選取 Info.plist")
2. 按一下**視覺資產**索引標籤，然後按一下 **使用資產目錄**按鈕底下**應用程式圖示**: 

    ![](app-icons-images/icon02w.png "選取 [視覺資產] 索引標籤")
4. 從**方案總管 中**，依序展開**資產目錄**資料夾： 

    ![](app-icons-images/image009.png "展開資產目錄資料夾")
5. 按兩下**媒體**檔案在編輯器中開啟它： 

    ![](app-icons-images/image010.png "在編輯器中開啟媒體檔案")
6. 在下**屬性總管**開發人員可以選擇的不同類型和所需的圖示大小。
7. 按一下指定的圖示類型，然後選取所需的型別/大小的影像檔。
8. 按一下**開啟**專案中包含影像，並將它設 xcasset 中的按鈕。
9. 重複所需的所有映像。

-----

這是慣用的方法包括和管理將用來提供應用程式的應用程式、 焦點和設定圖示的影像資產。

### <a name="migrating-from-infoplist-to-asset-catalogs"></a>從 Info.plist 移轉至資產目錄

現有的 Xamarin.iOS 應用程式使用`Info.plist`檔案來管理它的圖示時，強烈建議，開發人員將它切換到使用`AppIcons`內的影像資產`Assets.xcassets`。

請執行下列動作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 按兩下`Info.plist`檔案**方案總管 中**開啟進行編輯。
2. 向下捲動至**應用程式圖示**> 一節。
3. 從**來源**下拉式清單中選取**移轉到 Asset 目錄**: 

    ![](app-icons-images/migrate02.png "選取移轉到 Asset 類別目錄")
4. 中所定義的任何現有的圖示`Info.plist`檔案將會移轉至`AppIcons`映像設定加入至`Assets.xcassets`: 

     ![](app-icons-images/migrate03.png "在 Assets.xcassets 設定 AppIcons 映像")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 按兩下`Info.plist`檔案**方案總管 中**開啟進行編輯。
2. 在 iPhone 上按一下圖示 」 一節： 

    ![](app-icons-images/image007.png "以 iPhone 圖示編輯器")
3. 向下捲動至**圖示**> 一節。
4. 從**資產目錄**下拉式清單中選取**使用資產目錄**。
5. 中所定義的任何現有的圖示`Info.plist`檔案將會移轉至`Images`集加入至`Assets.xcassets`。
6. 將變更儲存至 `Info.plist` 檔案。

-----

<a name="itunes" />

## <a name="itunes-artwork"></a>iTunes 插圖

如果使用傳送應用程式 （無論是公司的使用者或是在實際裝置上測試） 的特定方法時，開發人員也必須包含 512 x 512，1024 x 1024 映像，將用來表示應用程式，在 iTunes。

若要指定 iTunes 插圖，請執行下列動作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 按兩下`Info.plist`檔案**方案總管 中**開啟進行編輯。
2. 捲動到**iTunes 作品**編輯器區段： 

    ![](app-icons-images/itunes01.png "捲動至 iTunes 編輯器圖檔區段")
3. 任何遺失的影像中，按一下 [在編輯器中縮圖上，從 [開啟檔案] 對話方塊中選取所需的 iTunes 作品的映像檔按**確定**] 按鈕。
4. 重複此步驟直到所有需要映像已指定的應用程式。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 按兩下`Info.plist`檔案**方案總管 中**開啟進行編輯。

2. 按一下**視覺資產**索引標籤上，展開  **iTunes 作品**: 

    ![](app-icons-images/itunes01w.png "編輯 iTunes Visual Studio 中的圖檔")
4. 任何遺失的影像中，按一下 [在編輯器中縮圖上，從 [開啟檔案] 對話方塊中選取所需的 iTunes 作品的映像檔按**開啟**] 按鈕。
5. 重複此步驟直到所有需要映像已指定的應用程式。

-----

## <a name="related-links"></a>相關連結

- [使用映像 （範例）](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [自訂圖示和映像建立指導方針](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html))
