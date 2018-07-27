---
title: 使用共用專案共用程式碼
description: 共用的專案可讓您撰寫數個不同的應用程式專案所參考的通用程式碼。 程式碼編譯為一部分的每個參考的專案，並可包含編譯器指示詞，可幫助平台專屬功能納入共用的程式碼基底。
ms.prod: xamarin
ms.assetid: 191c71fb-44a4-4e6c-af4b-7b1107dce6af
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: 61096635cd94d0fdd0abe6fda59c4efa41eeceb1
ms.sourcegitcommit: 46bb04016d3c35d91ff434b38474e0cb8197961b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2018
ms.locfileid: "39270211"
---
# <a name="shared-projects-code-sharing"></a>共用專案共用程式碼

_共用的專案可讓您撰寫數個不同的應用程式專案所參考的通用程式碼。程式碼編譯為一部分的每個參考的專案，並可包含編譯器指示詞，可幫助平台專屬功能納入共用的程式碼基底。_

共用專案 （有時也稱為共用資產專案） 可讓您撰寫多個目標的專案，包括 Xamarin 應用程式之間共用的程式碼。

它們支援編譯器指示詞，您才能有條件地包含平台特定程式碼編譯到專案參考共用專案的子集。 另外還有 IDE 支援，協助管理的編譯器指示詞和視覺化程式碼在每個應用程式中所呈現的樣子。

如果您有專案之間共用程式碼中使用檔案連結在過去，共用專案的運作方式類似，但具有大幅改進的 IDE 支援。

## <a name="what-is-a-shared-project"></a>共用的專案是什麼？

不同於大部分其他專案類型共用的專案沒有任何輸出 （以 DLL 格式），而是程式碼會編譯到每個參考的專案。 下圖所示-共用專案的整個內容在概念上是 「 複製到 「 每個參考的專案和編譯一樣地的一部分。

![](shared-projects-images/sharedassetproject.png "共用的專案的架構")

共用的專案中的程式碼可以包含編譯器指示詞，以啟用或停用根據哪一個應用程式的專案使用的程式碼圖表中的彩色的平台方塊所建議的程式碼區段。

共用的專案不會不會編譯本身，純粹做為群組，可以包含其他專案中原始程式碼檔的存在。 當另一個專案所參考，程式碼實際上會編譯成*一部分*的專案。 共用專案無法參考任何其他專案類型 （包括其他共用專案）。

請注意，Android 應用程式專案無法參考其他 Android 應用程式專案-例如，Android 單元測試專案無法參考的 Android 應用程式專案。 如需有關這項限制的詳細資訊，請參閱此[論壇討論](http://forums.xamarin.com/discussion/comment/98092/)。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio for Mac 逐步解說

本節逐步解說如何建立和使用共用的專案，使用 Visual Studio for mac。 參考要[共用的專案範例](#Shared_Project_Example)一節以取得完整的範例。

## <a name="creating-a-shared-project"></a>建立共用的專案

若要建立新的共用專案巡覽至**檔案 > 新增解決方案...** (或以滑鼠右鍵按一下現有的方案，然後選擇**新增 > 新增專案...**):

[![新的共用的專案](shared-projects-images/xs-newsolution-sml.png "新方案")](shared-projects-images/xs-newsolution.png#lightbox)

在下一個畫面上，選擇專案名稱，然後按一下**建立**。

如下所示的新共用的專案-請注意，有任何參考或元件節點;這些不支援共用的專案。

![空白共用專案](shared-projects-images/xs-empty.png "空白共用專案")

以供共用專案，它需要至少一個可以建置的專案 （例如 iOS 或 Android 應用程式或程式庫或將 PCL 專案） 的參考。 共用的專案不會不會編譯，具有任何參考它，因此語法 （或任何其他） 時的錯誤將不會反白顯示之前已參考其他項目。

加入共用專案的參考是參考規則的程式庫專案相同的方式。 此螢幕擷取畫面顯示參考共用專案的 Xamarin.iOS 專案。

![](shared-projects-images/xs-reference.png "共用專案的專案參考")

共用專案參考另一個程式庫或應用程式之後您可以建置方案，並檢視程式碼中的任何錯誤。 當所參考共用專案_兩個或多個_其他專案中，功能表會出現在左上方的原始碼程式碼編輯器顯示選擇哪些專案參考此檔案。

## <a name="shared-project-options"></a>共用專案選項

當您共用的專案上按一下滑鼠右鍵並選擇**選項**那里較少的設定，比其他專案類型。 共用專案不會經過編譯 （靠自己），因為您無法設定輸出或編譯器選項、 專案組態、 組件簽署或自訂的命令。 共用的專案中的程式碼實際上會繼承自任何參考它們的這些值。



**選項**畫面如下所示-專案**名稱**並**預設命名空間**只有兩項設定，您通常會變更。


![](shared-projects-images/xs-sharedprojectoptions.png "共用專案選項")



# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)



## <a name="visual-studio-walkthrough"></a>Visual Studio 逐步解說


本節逐步解說如何建立和使用共用的專案，使用 Visual Studio。 參考要[共用的專案範例](#Shared_Project_Example)區段的完整實作。

### <a name="creating-a-shared-project"></a>建立共用的專案

若要建立新的共用專案巡覽至**檔案 > 新增解決方案...** ，然後選擇 專案和方案的名稱。

![](shared-projects-images/vs-newsolution.png "新增方案")

您也可以加入新的共用專案方案的方案檔上按一下滑鼠右鍵，然後選擇**新增 > 新增專案...**.新增共用的專案看起來如下所示 （在已加入的類別檔案） 之後，請注意沒有參考或元件節點;這些不支援共用的專案。

![](shared-projects-images/vs-empty.png "空白的共用的專案")

以供共用專案，它需要至少一個可以建置的專案 （例如 iOS 或 Android 應用程式或程式庫或將 PCL 專案） 的參考。 共用的專案不會不會編譯，具有任何參考它，因此語法 （或任何其他） 時的錯誤將不會反白顯示之前已參考其他項目。

加入共用專案的參考是參考規則的程式庫專案相同的方式。 此螢幕擷取畫面顯示參考共用專案的 Xamarin.iOS 專案。

![](shared-projects-images/vs-reference.png "共用專案的專案參考")

共用專案參考另一個程式庫或應用程式之後您可以建置方案，並檢視程式碼中的任何錯誤。 當所參考共用專案_兩個或多個_其他專案中，功能表會出現在原始檔程式碼編輯器，以查看哪些專案參考目前的程式碼檔案左上方。


### <a name="shared-project-properties"></a>共用的專案屬性


當您選取共用專案那里較少的設定與其他專案類型的 [屬性] 面板中。 共用專案不會經過編譯 （靠自己），因為您無法設定輸出或編譯器選項、 專案組態、 組件簽署或自訂的命令。 共用的專案中的程式碼實際上會繼承自任何參考它們的這些值。

**屬性**-顯示面板**根命名空間**是唯一的設定，您可以變更。

![](shared-projects-images/vs-sharedprojectproperties.png "共用的專案屬性")

-----

<a name="Shared_Project_Example"/>

## <a name="shared-project-example"></a>共用的專案範例

[Tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky)共用的專案，以包含通用的程式碼就會使用這兩個 iOS、 Android 和 Windows Phone 應用程式所使用的範例。 同時`SQLite.cs`和`TaskRepository.cs`原始程式碼檔利用編譯器指示詞 （例如。 `#if __ANDROID__`) 來參考它們的應用程式的每個產生不同的輸出。

完整的解決方案結構如下所示 (在 Visual Studio for Mac 和 Visual Studio 分別):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](shared-projects-images/xs-examplesolution.png "Visual Studio for Mac 解決方案")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](shared-projects-images/vs-examplesolution.png "Visual Studio 方案")

-----

Windows Phone 專案可以巡覽從 Visual studio for Mac，即使該專案類型不支援在 Visual Studio for mac 的編譯

正在執行的應用程式如下所示：

![](shared-projects-images/example.png "iOS、 Android、 Windows Phone 範例")

## <a name="summary"></a>總結

本文件所述共用專案的運作方式，如何它們可以建立及使用 Visual Studio for Mac 和 Visual Studio 中，並導入簡單的範例應用程式，示範共用專案中的動作。

## <a name="related-links"></a>相關連結

- [Tasky 範例應用程式](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [可攜式類別庫 （範例）](~/cross-platform/app-fundamentals/pcl.md)
- [共用程式碼選項 （範例）](~/cross-platform/app-fundamentals/code-sharing.md)
