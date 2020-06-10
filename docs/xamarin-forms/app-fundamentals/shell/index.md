---
title： " Xamarin.Forms shell" 描述： "本指南說明如何使用 Xamarin.Forms shell，藉 Xamarin.Forms 由提供大部分應用程式所需的基本功能，降低應用程式的複雜性。
assetid： 85B322AA-808F-41B6-953A-5877264AE643 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：05/28/2019 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-shell"></a>Xamarin.Forms外層

## <a name="introduction"></a>[簡介](introduction.md)

Xamarin.FormsShell 提供大部分行動應用程式所需的基本功能，藉此降低行動應用程式開發的複雜度。 這包括一般導覽使用者體驗、URI 型導覽配置，以及整合式搜尋處理常式。

## <a name="create-a-xamarinforms-shell-applicationcreatemd"></a>[建立 Xamarin.Forms Shell 應用程式](create.md)

建立 Xamarin.Forms Shell 應用程式的流程，是建立將類別子類別化的 XAML 檔案 `Shell` ，將應用程式類別的屬性設定為子類別化的 `MainPage` `App` `Shell` 物件，然後在子類別中描述應用程式的視覺階層 `Shell` 。

## <a name="flyout"></a>[Flyout](flyout.md)

飛出視窗為 Shell 應用程式的根功能表，且可透過圖示或從螢幕側邊撥動來存取。 飛出視窗會由選用標題、飛出視窗項目及選用功能表項目所組成。

## <a name="tabs"></a>[定位點](tabs.md)

飛出視窗之後，Shell 應用程式中的下一個導覽層級是底部的索引標籤列。 或者，應用程式的導覽模式可以從底部索引標籤開始，而不使用飛出視窗。 在這兩種情況下，當底部索引標籤包含多個頁面時，頁面將可透過頂端索引標籤來導覽。

## <a name="page-configuration"></a>[頁面設定](configuration.md)

`Shell`類別會定義附加屬性，以用來設定 Shell 應用程式中頁面的外觀 Xamarin.Forms 。 這包括設定頁面色彩、停用導覽列、停用索引標籤列，以及在導覽列中顯示檢視。

## <a name="navigation"></a>[瀏覽](navigation.md)

Shell 應用程式可以利用 URI 型導覽配置，使用路由導覽至應用程式中的任何頁面，而不需要遵循設定的導覽階層。

## <a name="search"></a>[搜尋](search.md)

Shell 應用程式可以使用能夠新增至每個頁面頂端的搜尋方塊所提供的整合式搜尋功能。

## <a name="lifecycle"></a>[生命週期](lifecycle.md)

Shell 應用程式會遵守 Xamarin.Forms 生命週期，而 `Appearing` 當頁面即將出現在螢幕上時，就會引發事件，而 `Disappearing` 當頁面即將從螢幕中消失時，就會引發事件。

## <a name="custom-renderers"></a>[自訂轉譯器](customrenderers.md)

Shell 應用程式可透過各種不同 Shell 類別公開的屬性與方法進行高度自訂。 不過，它也能夠在需要更複雜的平台專用自訂時，建立 Shell 自訂轉譯器。
