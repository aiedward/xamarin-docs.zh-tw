---
title: 適用于 Xamarin iOS 應用程式的啟動畫面
description: 本文說明如何使用單一的整合分鏡腳本，針對所有 iOS 裝置建立應用程式啟動畫面。
ms.prod: xamarin
ms.assetid: 31A489CA-756B-4B9B-B386-4BADF18EDD33
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2018
ms.openlocfilehash: ef1a97df5648d0f120b58f6206f18f74a6f50f92
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "78292047"
---
# <a name="launch-screens-for-xamarinios-apps"></a>適用于 Xamarin iOS 應用程式的啟動畫面

_本文說明如何使用單一的整合分鏡腳本，針對所有 iOS 裝置建立應用程式啟動畫面。_

在 iOS 8 之前，建立 iOS 應用程式的啟動畫面需要開發人員提供應用程式可執行之各種裝置外型規格和解決方案的影像資產。 不過，自 iOS 8 發行後，您就可以使用單一的整合腳本，建立在所有情況下都正確的啟動畫面。

這個簡短的逐步解說將說明如何建立啟動畫面，其中包含新專案中預設提供的分鏡腳本，或以手動方式新增至現有專案的分鏡腳本。 接著，它會示範如何使用 iOS 設計工具將影像視圖和標籤新增至分鏡腳本、設定這些視圖的條件約束，以及確認分鏡腳本在各種裝置和方向上看起來是否正確。

<a name="storyboard" />

## <a name="managing-launch-screens-with-storyboards"></a>使用分鏡腳本管理啟動畫面

在 iOS 8 （和更新版本）中，開發人員可以建立特殊的整合分鏡腳本來提供啟動畫面，而不是使用一或多個靜態啟動映射。 在 iOS 設計工具中建立啟動分鏡腳本時，請使用大小類別和自動設定來定義不同顯示環境的不同版面配置。 藉由使用大小類別和自動設定，開發人員可以建立在所有裝置和顯示環境中都看起來良好的單一啟動畫面。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中，選取 [檔案] **> [新增方案**]，然後選擇 [**單一視圖應用程式**] 來建立新的專案： 

    ![已選取單一 View 應用程式的 [新增專案] 視窗](launch-screens-images/launch01.png)

    - 根據預設，新的專案會包含定義啟動畫面介面的**LaunchScreen 檔案。** 
    - 若要改為將啟動畫面分鏡腳本新增至現有的專案，請以滑鼠右鍵按一下  **Solution Pad**中的專案名稱，然後選擇 **加入 > 新增檔案 ...** ，然後選取 **啟動畫面**：

    ![已選取 iOS 啟動畫面的 [新增檔案] 視窗](launch-screens-images/launch01b.png)

    - 將檔案命名為**LaunchScreen**或您選擇的其他名稱。

2. 設定專案，以針對其啟動畫面使用適當的分鏡腳本：

    - 按兩下**Solution Pad**中的**plist**檔案，將它開啟以供編輯。
    - 在 [**啟動映射**] 區段中，確認 [**啟動畫面**] 已設定為適當分鏡腳本的名稱：

    ![[Info. plist] 中的啟動畫面選取器](launch-screens-images/launch02.png)

    - 根據預設，新的專案會設定為使用**LaunchScreen**做為啟動畫面。

3. 將影像新增至**Assets.xcassets**資產目錄，讓它可在啟動畫面上使用。 如需詳細資訊，請參閱[顯示影像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)指南中的將[影像新增至資產目錄影像集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)一節。

4. 在  **Solution Pad**中按兩下以開啟**LaunchScreen**進行編輯。

5. 在 iOS 設計工具中，選擇要在其上預覽啟動畫面分鏡腳本的裝置和方向。 開啟底部工具列上的 [裝置選擇] 面板，然後選取 [ **IPhone 4S** ] 和 [**縱向**]。

    ![裝置選擇工具列](launch-screens-images/launch05.png)

    - 請注意，選取裝置和方向只會變更 iOS 設計工具預覽設計的方式。 無論在此處進行的選擇為何，除非已使用 [**編輯特性**] 按鈕來指定，否則新加入的條件約束會套用到所有裝置和方向。 

6. 設定視圖控制器主要視圖的**背景**色彩。 按一下 View Controller 中間的以選取 [View]，然後使用**Properties Pad**來調整背景色彩：

    ![具有紫色背景色彩的單一視圖](launch-screens-images/launch06.png)

7. 將**影像視圖**新增至啟動畫面，並設定其來源**影像**：

    - 從 [**工具箱] 面板**中，將**影像視圖**拖曳至視圖的中央。
    - 選取**影像視圖**後，在**Properties Pad**的**Widget**區段中，將**image**屬性設定為已新增至**assets.xcassets**資產目錄的映射集。 視需要重新調整**影像視圖**的位置並調整其大小：
    
    ![具有影像屬性集的影像視圖](launch-screens-images/launch07.png)

8. 在**影像視圖**下方新增**標籤**，並使用**Properties Pad**來設定其屬性： 

    ![具有文字和色彩設定的標籤](launch-screens-images/launch08.png)

9. 使用 [**條件約束] 工具列**中的右手按鈕，切換到條件約束編輯模式：
    
    ![條件約束編輯模式按鈕](launch-screens-images/launch09.png)

10. 將條件約束新增至**影像視圖**、設定其高度和寬度，並水準和垂直置中：

    ![具有版面配置條件約束的影像視圖](launch-screens-images/launch10.png)

    - 如需如何加入條件約束的詳細資訊，請參閱[使用 Xamarin Designer for iOS 的自動](~/ios/user-interface/designer/designer-auto-layout.md)配置。

11. 將條件約束新增至**標籤**、水準置中、為其提供高度和寬度，並從**影像視圖**垂直定位固定距離：

    ![具有版面配置條件約束的標籤](launch-screens-images/launch11.png)

12. 測試其他裝置和方向，以確認設計在所有案例中看起來如預期。 在需要針對特定裝置或方向進行調整的情況下，請使用 [**編輯特性**] 按鈕來新增特定大小類別的條件約束：

    ![使用橫向方向轉譯為 iPhone X 的啟動畫面](launch-screens-images/launch12.png)

13. 將變更儲存至分鏡腳本。 在模擬器或裝置上執行應用程式，啟動畫面會在應用程式啟動時顯示。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 建立新專案。 在 Visual Studio 中，選取 檔案 **> 新增 > C#專案 > Visual > iPhone & IPad > iOS 應用程式（Xamarin）** ：

    ![已選取 iOS 應用程式（Xamarin）的 [新增專案] 視窗](launch-screens-images/launch01.w157.png)

    選取 [**單一視圖] 應用程式**範本，然後按一下 **[確定]** ：

    ![單一視圖應用程式範本](launch-screens-images/launch01-2.w157.png)

2. 如果**方案總管**中存在**資源 > LaunchScreen** ，請以滑鼠右鍵按一下該檔案，然後選擇 [**刪除**]，將其刪除。 在下一個步驟中，會將此檔案取代為分鏡腳本。

3. 建立用來做為啟動畫面的分鏡腳本。 在 **方案總管**中，以滑鼠右鍵按一下專案，然後選擇 **加入 > 新專案**，然後按 **空白**分鏡腳本。 將此腳本命名為**LaunchScreen** ，然後按一下 [**新增**]：

    ![已選取空白分鏡腳本的 [加入新專案] 視窗](launch-screens-images/launch03.w157.png)

4. 將專案設定為使用**LaunchScreen**作為其啟動畫面分鏡腳本：

    - 在 [方案總管] 中，按兩下 [Info.plist] 檔案以開啟它進行編輯。 
    - 在 [**視覺資產**] 索引標籤上，將 [**啟動畫面**] 設定為**LaunchScreen**。

    ![[Info. plist] 中的啟動畫面選取器](launch-screens-images/launch04-vs.png)

5. 將影像新增至專案中的資產目錄，讓它可在啟動畫面上使用：

    - 在 **方案總管**中，以滑鼠右鍵按一下 **資產目錄**，然後選取 **新增資產目錄**。 命名此新資產目錄**資產**：

    ![已選取資產目錄的 [加入新專案] 視窗](launch-screens-images/launch05.w157.png)

    - 將新的映射集新增至**資產**資產目錄，如[顯示影像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)指南的將[影像新增至資產目錄影像集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)一節中所述。

6. 在 **方案總管**中按兩下以開啟**LaunchScreen**進行編輯。

    - 若要編輯分鏡腳本檔案，Visual Studio 需要 Mac 組建主機的作用中連接。 如需詳細資訊，請參閱連線[到 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)指南。

7. 在 iOS 設計工具中，選擇要在其上預覽啟動畫面分鏡腳本的裝置和方向。 開啟底部工具列上的 [裝置選取] 面板，然後選取 [ **IPhone 4S** ] 和 [**縱向**]： 

    ![裝置選擇工具列](launch-screens-images/launch07-vs.png)

    - 請注意，選取裝置和方向只會變更 iOS 設計工具預覽設計的方式。 無論在此處進行的選擇為何，除非已使用 [**編輯特性**] 按鈕來指定，否則新加入的條件約束會套用到所有裝置和方向。 

8. 從 [**工具箱**] 將 [**視圖控制器**] 拖曳至設計介面，將其加入至分鏡腳本： 

    ![已新增至設計介面的空白視圖控制器](launch-screens-images/launch08-vs.png)

9. 設定視圖控制器主要視圖的**背景**色彩。 按一下 View Controller 中間的以選取 [View]，然後使用 [**屬性] 視窗**來調整背景色彩：
    
    ![具有紫色背景色彩的單一視圖](launch-screens-images/launch09-vs.png)

10. 將**影像視圖**新增至啟動畫面，並設定其來源**影像**：

    - 將 [**影像] 視圖**從 [**工具箱**] 拖曳至視圖的中央。
    - 在仍選取 [**影像**] 的情況下，在 [**屬性] 視窗**的 [ **Widget** ] 區段中，將 [**影像**] 屬性設定為已新增至**資產**資產目錄的影像集。 視需要重新調整**影像視圖**的位置並調整其大小：
    
    ![具有影像屬性集的影像視圖](launch-screens-images/launch10-vs.png)

11. 在**影像視圖**下方新增**標籤**：

    - 將**標籤**從 [**工具箱**] 拖曳至設計介面，放在**影像視圖**底下。
    - 使用 [**屬性] 視窗**來設定**標籤**的屬性：

    ![具有文字和色彩設定的標籤](launch-screens-images/launch11-vs.png) 

12. 使用 [**條件約束] 工具列**中的右手按鈕，切換到條件約束編輯模式：
    
    ![條件約束編輯模式按鈕](launch-screens-images/launch12-vs.png) 

13. 將條件約束新增至**影像視圖**、設定其高度和寬度，並水準和垂直置中：

    ![具有版面配置條件約束的影像視圖](launch-screens-images/launch13-vs.png) 

    - 如需加入條件約束的詳細資訊，請參閱[使用 Xamarin Designer for iOS 的自動](~/ios/user-interface/designer/designer-auto-layout.md)配置。

14. 將條件約束新增至**標籤**、水準置中、為其提供高度和寬度，並從**影像視圖**垂直定位固定距離：
    
    ![具有版面配置條件約束的標籤](launch-screens-images/launch14-vs.png) 

15. 測試其他裝置和方向，以確認設計在所有案例中看起來如預期。 在需要針對特定裝置或方向進行調整的情況下，請使用 [**編輯特性**] 按鈕來新增特定大小類別的條件約束：

    ![使用橫向方向轉譯為 iPhone X 的啟動畫面](launch-screens-images/launch15-vs.png) 

16. 將變更儲存至分鏡腳本。 在模擬器或裝置上執行應用程式，啟動畫面會在應用程式啟動時顯示。

-----

> [!NOTE]
> 當做啟動畫面使用的分鏡腳本_必須_只包含簡單的內建 UI 元素，而且**不能**進行任何計算或從自訂類別衍生。

如需有關使用整合的分鏡腳本建立啟動畫面的詳細資訊，請參閱[整合](~/ios/user-interface/storyboards/unified-storyboards.md)分鏡腳本指南的[動態啟動畫面](~/ios/user-interface/storyboards/unified-storyboards.md#dynamic-launch-screens)一節。

## <a name="migrating-to-launch-screen-storyboards"></a>遷移以啟動畫面分鏡腳本

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

當更新現有的應用程式以針對其啟動畫面使用分鏡腳本時，請以滑鼠右鍵按一下 **方案總管**中的**專案名稱**，**然後選取** 新增 > **新**檔案 ...。選取  **iOS**  > **啟動畫面**，然後按一下 **新增** 按鈕：

![](launch-screens-images/storyboard02.png "Select an iOS Launch Screen")

接下來，按兩下**方案總管**中的 `Info.plist` 檔案，將它開啟以供編輯。 在 [**啟動畫面**] 底下，選取上方建立的新分鏡腳本檔案。

![](launch-screens-images/storyboard09.png "Select the new Storyboard file created above")

若要使用新的腳本做為啟動畫面，請執行下列動作：

1. 按兩下**方案總管**中的 `Info.plist` 檔案，將它開啟以供編輯。
2. 在編輯器的 [**通用啟動映射**] 區段中，開啟 [**啟動畫面**] 下拉式清單，然後選取上方所建立之分鏡腳本的名稱： 

    ![](launch-screens-images/storyboard08.png "Setting the launch screen to the storyboard")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 以滑鼠右鍵按一下 **方案總管**中的專案名稱，然後**選取** **新增 > 新檔案 ...** ： 

    ![](launch-screens-images/image012.png "Add new file")
2. 輸入啟動畫面的名稱，然後按一下 [**新增**] 按鈕： 

    ![](launch-screens-images/image013.png "Enter a name for the launch screen")
3. 在 **方案總管**中，按兩下新建立的分鏡腳本檔案，將其開啟進行編輯。
4. 確定 [**大小] 類別**設定為 [**任何： any** ] 和 **[** **一般**]： 

    ![](launch-screens-images/image016.png "Ensure that the Size Class is set to any:any and the View As is Generic")
5. 從大小類別、簡單 UI 專案（例如 `UIImageView`）和您已包含在應用程式套件組合中的影像，組裝啟動畫面： 

    ![](launch-screens-images/image017.png "Assembly the launch screen in the iOS Designer")
6. 將變更儲存至分鏡腳本。

-----

## <a name="related-links"></a>相關連結

- [動態啟動畫面（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen)
- [統一的分鏡腳本](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 設計工具基本概念](~/ios/user-interface/designer/index.md)
- [將影像新增至資產目錄映射集](~/ios/app-fundamentals/images-icons/displaying-an-image.md#adding-images-to-an-asset-catalog-image-set)
- [使用 Xamarin Designer for iOS 的自動版面配置](~/ios/user-interface/designer/designer-auto-layout.md)
- [人力介面指導方針：啟動畫面](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/launch-screen/)
