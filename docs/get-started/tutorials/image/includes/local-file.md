---
ms.openlocfilehash: 93ee0681adcc63fe05b4be88ff67f0aeee3e03ca
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "61384409"
---
影像檔案可以新增至平台專案，也可以從 Xamarin.Forms 的共用程式碼來參考。 當影像是平台專用的 (例如，在不同平台上使用不同的解析度) 或屬於略微不同的設計時，就需要這種影像散發方法。

在本練習中，您會修改 **ImageTutorial** 解決方案，使其顯示本機影像而非下載自 URI 的影像。 這個本機影像是 Xamarin 的標誌，請藉由按一下下方按鈕來下載。

> [!div class="nextstepaction"]
> [下載 XamarinLogo.png](https://raw.githubusercontent.com/xamarin/xamarin-forms-samples/master/UserInterface/PlatformSpecifics/Droid/Resources/drawable/XamarinLogo.png)

> [!IMPORTANT]
> 若要跨所有平台使用單一影像，「在每個平台上都必須使用相同的檔案名稱」  ，且應該是有效的 Android 資源名稱 (也就是只允許小寫字母、數字、底線和句點)。

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在 [方案總管]  中的 [ImageTutorial.iOS]  專案內，展開 [資產目錄]  ，然後按兩下 [資產]  來加以開啟。 然後，在 [Assets.xcassets]  索引標籤中，按一下 [加號]  按鈕，然後選取 [新增影像集]  ：

    ![螢幕擷取畫面：在 Visual Studio 中的資產目錄內建立新影像集](../images/vs/new-image-set.png "新資產目錄影像集")

1. 在 [Assets.xcassets]  索引標籤中，選取新的影像集，隨即便會顯示編輯器：

    ![螢幕擷取畫面：Visual Studio 資產目錄中的新影像集](../images/vs/new-image-set-editor.png "資產目錄影像集編輯器")

1. 將 [XamarinLogo.png] 從檔案系統拖曳至 [通用] 類別的 [1x] 方塊：

    ![螢幕擷取畫面：Visual Studio 中包含影像的影像集](../images/vs/image-set-with-image.png "包含影像的影像集")

1. 在 [Assets.xcassets]  索引標籤中，以滑鼠右鍵按一下新影像集的名稱，然後將其重新命名為 **XamarinLogo**：

    ![螢幕擷取畫面：Visual Studio 中已重新命名的影像集](../images/vs/rename-image-set.png "已重新命名的影像集")

    儲存並關閉 [Assets.xcassets]  索引標籤。

1. 在 [方案總管]  的 [ImageTutorial.Android]  專案中，展開 [資源]  資料夾。 然後，將 [XamarinLogo.png]  從檔案系統拖曳至 [可繪製資源]  資料夾：

    ![螢幕擷取畫面：Visual Studio 中作為 Android 資源的影像檔](../images/vs/android-resource.png "Android 資源資料夾中的本機影像檔")

    > [!NOTE]
    > Visual Studio 會自動將影像的建置動作設定為 [AndroidResource]  。

1. 在 [ImageTutorial]  專案中的 MainPage.xaml  內，將 [`Image`](xref:Xamarin.Forms.Editor) 宣告修改為顯示本機的 **XamarinLogo** 檔案：

    ```xaml
    <Image Source="XamarinLogo"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    此程式碼會將 [`Source`](xref:Xamarin.Forms.Image.Source) 屬性設定為要顯示的本機檔案。 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 屬性會設定為 300 個裝置獨立單位 (在 iOS 上) 和 250 個裝置獨立單位 (在 Android 上)。 此外，[`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 屬性可指定將影像水平置中。

    > [!NOTE]
    > 對於 iOS 上的 PNG 影像，在 [`Source`](xref:Xamarin.Forms.Image.Source) 屬性中指定的檔案名稱內可省略 **.png** 副檔名。 若為其他影像格式，則必須有副檔名。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![螢幕擷取畫面：在 iOS 和 Android 上，顯示本機影像的 Image 檢視](../images/local-file.png "顯示本機影像的 Image 檢視")](../images/local-file-large.png#lightbox "顯示本機影像的 Image 檢視")

    如需本機影像的詳細資訊，請參閱 [Xamarin.Forms 中的影像](~/xamarin-forms/user-interface/images.md)指南中的[本機影像](~/xamarin-forms/user-interface/images.md#local-images)。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 [Solution Pad]  中的 [ImageTutorial.iOS]  專案內，按兩下 [Assets.xcassets]  來加以開啟。 然後，在 [資產清單]  中，以滑鼠右鍵按一下並選取 [新增影像集]  ：

    ![螢幕擷取畫面：在 Visual Studio for Mac 中的資產目錄內建立新影像集](../images/vsmac/new-image-set.png "新資產目錄影像集")

1. 在 [資產清單]  中，選取新的影像集，隨即便會顯示編輯器：

    ![螢幕擷取畫面：Visual Studio for Mac 資產目錄中的新影像集](../images/vsmac/new-image-set-editor.png "資產目錄影像集編輯器")

1. 將 [XamarinLogo.png] 從檔案系統拖曳至 [通用] 類別的 [1x] 方塊：

    ![螢幕擷取畫面：Visual Studio for Mac 中包含影像的影像集](../images/vsmac/image-set-with-image.png "包含影像的影像集")

1. 在 [資產清單]  中，按兩下新影像集的名稱，然後將其重新命名為 **XamarinLogo**：

    ![螢幕擷取畫面：Visual Studio for Mac 中已重新命名的影像集](../images/vsmac/rename-image-set.png "已重新命名的影像集")

1. 在 [Solution Pad]  的 [ImageTutorial.Android]  專案中，展開 [資源]  資料夾。 然後，將 [XamarinLogo.png]  從檔案系統拖曳至 [可繪製資源]  資料夾。

1. 在 [將檔案新增至資料夾]  對話方塊中，選取 [確定]  。

    ![螢幕擷取畫面：Visual Studio for Mac 中作為 Android 資源的影像檔](../images/vsmac/android-resource.png "Android 資源資料夾中的本機影像檔")

    > [!NOTE]
    > Visual Studio for Mac 會自動將影像的建置動作設定為 [AndroidResource]  。

1. 在 [ImageTutorial]  專案中的 MainPage.xaml  內，將 [`Image`](xref:Xamarin.Forms.Editor) 宣告修改為顯示本機的 **XamarinLogo** 檔案：

    ```xaml
    <Image Source="XamarinLogo"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    此程式碼會將 [`Source`](xref:Xamarin.Forms.Image.Source) 屬性設定為要顯示的本機檔案。 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 屬性會設定為 300 個裝置獨立單位 (在 iOS 上) 和 250 個裝置獨立單位 (在 Android 上)。 此外，[`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 屬性可指定將影像水平置中。

    > [!NOTE]
    > 對於 iOS 上的 PNG 影像，在 [`Source`](xref:Xamarin.Forms.Image.Source) 屬性中指定的檔案名稱內可省略 **.png** 副檔名。 若為其他影像格式，則必須有副檔名。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![螢幕擷取畫面：在 iOS 和 Android 上，顯示本機影像的 Image 檢視](../images/local-file.png "顯示本機影像的 Image 檢視")](../images/local-file-large.png#lightbox "顯示本機影像的 Image 檢視")

    如需本機影像的詳細資訊，請參閱 [Xamarin.Forms 中的影像](~/xamarin-forms/user-interface/images.md)指南中的[本機影像](~/xamarin-forms/user-interface/images.md#local-images)。
