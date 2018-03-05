---
title: "共用的專案"
description: "共用的專案可讓您撰寫正由許多不同的應用程式專案的通用程式碼。 程式碼會編譯為組件的每個參考的專案，而且可包含編譯器指示詞，以協助特定平台功能納入共用程式碼基底。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 191c71fb-44a4-4e6c-af4b-7b1107dce6af
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 0ab1daa9ce76900067f374cda58040354688c7be
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="shared-projects"></a>共用的專案

_共用的專案可讓您撰寫正由許多不同的應用程式專案的通用程式碼。程式碼會編譯為組件的每個參考的專案，而且可包含編譯器指示詞，以協助特定平台功能納入共用程式碼基底。_

共用專案 （有時也稱為共用資產專案） 可讓您撰寫多個目標的專案，包括 Xamarin 應用程式之間共用的程式碼。

它們支援編譯器指示詞，因此您可以有條件地包含平台專屬的程式碼編譯成參考共用專案的專案的子集。 另外還有 IDE 支援，以協助管理編譯器指示詞，並以視覺化方式檢視程式碼中每個應用程式所呈現的樣子。

如果您已經使用過檔案連結專案之間共用程式碼，以類似的方式，但很多改良的 IDE 支援，適用於共用專案。


# <a name="requirements"></a>需求

共用的專案 Xamarin Studio 5 和 Visual Studio 2013 Update 2 （請參閱附註） 已加入支援。

> [!IMPORTANT]
>  Microsoft 已發行的這個新的專案類型-**共用專案 ([下載 Visual Studio 擴充功能預覽](http://visualstudiogallery.msdn.microsoft.com/315c13a7-2787-4f57-bdf7-adae6ed54450))** ： 適用於 Visual Studio 2013 Update 2 (年 4 月 2014)。 請參閱 Microsoft [Windows Phone 8.1](http://blogs.msdn.com/b/visualstudio/archive/2014/04/08/building-windows-phone-8-1-apps-in-html.aspx)和[Microsoft Store](http://msdn.microsoft.com/en-us/library/windows/apps/dn609832.aspx#CrossPlatform)如需有關如何將它用於這些平台的文件。




 <a name="Walkthrough" />


# <a name="what-is-a-shared-project"></a>什麼是共用專案？

不像其他大部分的專案類型共用的專案沒有任何輸出 （以 DLL 的形式），而程式碼會編譯到每個參考的專案。 下圖說明這種-共用專案的整個內容在概念上是 「 複製到 「 每個參考的專案，並編譯如同它是的一部分。

 ![](shared-projects-images/sharedassetproject.png "共用的專案的架構")

共用的專案中的程式碼可以包含編譯器指示詞會啟用或停用根據哪一個應用程式的專案使用的程式碼會在圖表中的彩色的平台方塊所建議的程式碼區段。

共用的專案不會不會編譯本身，純粹做為來源的程式碼檔案可以包含其他專案中的群組存在。 當另一個專案所參考，程式碼實際上會編譯為*一部分*的該專案。 共用專案無法參考 （包括其他共用專案） 的任何其他專案類型。

請注意，Android 應用程式專案無法參考其他 Android 應用程式專案，例如 Android 的單元測試專案無法參考的 Android 應用程式專案。 如需有關這項限制的詳細資訊，請參閱此[論壇討論](http://forums.xamarin.com/discussion/comment/98092/)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)



<a name="Xamarin_Studio_Walkthrough" />

# <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio for Mac 逐步解說


本節逐步解說如何建立及使用共用專案，使用 Visual Studio for mac。 參考到[共用專案範例](#Shared_Project_Example)完成完整的範例 > 一節。


## <a name="creating-a-shared-project"></a>建立共用的專案


若要建立新的共用專案巡覽至**檔案 > 新的方案...**和選擇的名稱。


![](shared-projects-images/xs-newsolution.png "新增方案")


您也可以加入新的共用專案方案的方案檔上按一下滑鼠右鍵，然後選擇**新增 > 加入新的專案...**.新的共用專案看起來如下所示-請注意沒有參考或元件節點。這些不支援共用的專案。


![](shared-projects-images/xs-empty.png "空白共用的專案")


共用專案才能發揮作用，它必須至少一個 （例如 iOS 或 Android 應用程式或程式庫或 PCL 專案） 的可建置的專案所參考。 共用的專案不會不會編譯，它具有任何參考它，因此語法 （或任何其他） 時的錯誤將不會反白顯示直到它已被其他物件參照。



加入至共用專案參考是參考規則的程式庫專案與相同的方式。 這個螢幕擷取畫面顯示 Xamarin.iOS 專案參考共用的專案。


![](shared-projects-images/xs-reference.png "專案參考加入共用專案")


共用專案參考其他文件庫或應用程式之後您可以建置此方案，並檢視程式碼中的任何錯誤。 當共用專案參考_兩個或多個_其他專案中，功能表會出現在左上方的原始碼程式碼編輯器會顯示選擇哪些專案參考這個檔案。



## <a name="shared-project-options"></a>共用專案選項


當您共用的專案上按一下滑鼠右鍵並選擇**選項**那里較少的設定，比其他專案類型。 共用專案 （自行） 未編譯，因為您無法設定專案組態輸出或編譯器選項、 組件簽署或自訂命令。 有效率地共用的專案中的程式碼會從任何參考這些繼承這些值。



**選項**畫面如下所示-專案**名稱**和**預設命名空間**只有兩個設定，您通常會變更。


![](shared-projects-images/xs-sharedprojectoptions.png "共用專案選項")



# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)



<a name="Visual_Studio_Walkthrough" />

# <a name="visual-studio-walkthrough"></a>Visual Studio 逐步解說


本節逐步解說如何建立及使用共用專案，使用 Visual Studio。 參考到[共用專案範例](#Shared_Project_Example)> 一節的完整實作。


## <a name="creating-a-shared-project"></a>建立共用的專案


若要建立新的共用專案巡覽至**檔案 > 新的方案...** ，然後選擇 專案和方案的名稱。


![](shared-projects-images/vs-newsolution.png "新增方案")


您也可以加入新的共用專案方案的方案檔上按一下滑鼠右鍵，然後選擇**新增 > 新的專案...**.新的共用專案看起來如下所示 （已加入的類別檔案） 之後，請注意沒有參考或元件節點。這些不支援共用的專案。


![](shared-projects-images/vs-empty.png "空白共用的專案")


共用專案才能發揮作用，它必須至少一個 （例如 iOS 或 Android 應用程式或程式庫或 PCL 專案） 的可建置的專案所參考。 共用的專案不會不會編譯，它具有任何參考它，因此語法 （或任何其他） 時的錯誤將不會反白顯示直到它已被其他物件參照。



加入至共用專案參考是參考規則的程式庫專案與相同的方式。 這個螢幕擷取畫面顯示 Xamarin.iOS 專案參考共用的專案。


![](shared-projects-images/vs-reference.png "專案參考加入共用專案")


共用專案參考其他文件庫或應用程式之後您可以建置此方案，並檢視程式碼中的任何錯誤。 當共用專案參考_兩個或多個_其他專案中，功能表會出現在左上方的來源的程式碼編輯器，以查看哪些專案參考目前的程式碼檔案。


## <a name="shared-project-properties"></a>共用的專案屬性


當您選取共用專案那里較少的設定與其他專案類型中的 [屬性] 面板中。 共用專案 （自行） 未編譯，因為您無法設定專案組態輸出或編譯器選項、 組件簽署或自訂命令。 有效率地共用的專案中的程式碼會從任何參考這些繼承這些值。



**屬性**面板如下所示-**根命名空間**是您可以變更的唯一設定。


![](shared-projects-images/vs-sharedprojectproperties.png "共用的專案屬性")



-----

 <a name="Shared_Project_Example" />


# <a name="shared-project-example"></a>共用的專案範例

[Tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky)共用專案，以包含常見的程式碼就會使用這兩個 iOS、 Android 和 Windows Phone 應用程式所使用的範例。 同時`SQLite.cs`和`TaskRepository.cs`原始程式碼檔 utilise 編譯器指示詞 （例如。 `#if __ANDROID__`) 來產生參考它們的應用程式的每個不同的輸出。

完整的解決方案結構如下所示 (適用於 Mac 和 Visual Studio 的 Visual Studio 中分別):

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

 ![](shared-projects-images/xs-examplesolution.png "Visual Studio for Mac 方案")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

 ![](shared-projects-images/vs-examplesolution.png "Visual Studio 方案")

-----

Windows Phone 專案可以巡覽 Visual Studio 中適用於 Mac，即使該專案類型不支援 Visual Studio 中的編譯 for mac。

如下所示執行的應用程式。

 ![](shared-projects-images/example.png "iOS、 Android、 Windows Phone 範例")

 <a name="Summary" />


# <a name="summary"></a>總結

本文件描述共用專案的運作方式、 如何它們可以建立和使用 Visual Studio for Mac 和 Visual Studio，並導入示範共用中的專案動作的簡單範例應用程式。

## <a name="related-links"></a>相關連結

- [Tasky 範例應用程式](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [可攜式類別庫 （範例）](~/cross-platform/app-fundamentals/pcl.md)
- [共用程式碼選項 （範例）](~/cross-platform/app-fundamentals/code-sharing.md)
