---
title: Visual Studio Android 模擬器
description: 本指南說明如何設定及使用「Visual Studio Android 模擬器」，以在 Visual Studio 2015 中開發 Xamarin.Android 應用程式。
ms.prod: xamarin
ms.assetid: CD128CB9-499F-4558-B49F-77248824EFDF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/30/2018
ms.openlocfilehash: 29e35d0dee614d28eed08fbe8799fc74c5ad1eba
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2018
---
# <a name="visual-studio-android-emulator"></a>Visual Studio Android 模擬器

_本指南說明如何設定及使用 Visual Studio Android Emulator，以在 Visual Studio 2015 中開發 Xamarin.Android 應用程式。_

## <a name="visual-studio-android-emulator-overview"></a>Visual Studio Android Emulator 概觀

Microsoft Visual Studio 2015 包含 Android 模擬器，此模擬器可用來作為進行 Xamarin.Android 應用程式偵錯時的目標：Visual Studio 的 Android 模擬器。 此模擬器使用您開發電腦的 Hyper-V 功能，因此啟動和執行速度都比 Android SDK 隨附的預設模擬器快。 開發 Xamarin.Android 應用程式時，可以使用「Visual Studio 的 Android 模擬器」來替代預設的 Android SDK 模擬器。

> [!NOTE]
> Visual Studio Android Emulator 僅與 Visual Studio 2015 相容 &ndash; 而無法搭配 Visual Studio 2017 使用。

本指南說明如何從 Visual Studio 啟動 Microsoft Android 模擬器來測試您的應用程式，並說明此模擬器中可用的各種功能。 您將了解如何選取「裝置設定檔」(類似於預設 Android SDK 模擬器中的裝置定義) 來模擬不同類型的 Android 裝置。 最後的疑難排解小節說明常見的錯誤和因應措施。

## <a name="requirements"></a>需求

若要執行此模擬器，電腦必須符合執行 Hyper-V 的需求。 Hyper-V 需要 64 位元專業版的 Windows 8、Windows 8.1、Windows 10 或更高版本。 如需有關需求的詳細資訊，請參閱 [Visual Studio 的 Android 模擬器系統需求](https://msdn.microsoft.com/en-us/library/mt228280.aspx)。

> [!NOTE]
> 在已啟用 Hyper-V 的情況下，您無法使用 HAXM (Android SDK Emulator 所使用)。 如需有關 HAXM 的限制和可能問題，請參閱 [HAXM 虛擬化衝突](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#virt-conflicts)。


## <a name="running-the-emulator"></a>執行模擬器

Visual Studio 在 [偵錯目標] 下拉式功能表中提供數個預先設定的目標裝置設定檔 (如以下螢幕擷取畫面所示)。 Microsoft Android 模擬器目標會以 **VS 模擬器**作為開頭：

[![預先設定的目標裝置設定檔](visual-studio-android-emulator-images/01-vs-emulator-defaults-vs-sml.png)](visual-studio-android-emulator-images/01-vs-emulator-defaults-vs.png#lightbox)

當 Visual Studio 啟動 Xamarin.Android 應用程式時，會以所選裝置目標啟動模擬器，並將應用程式部署到該模擬器。 Visual Studio 左下角會顯示一則訊息，指出正在啟動模擬器：

[![正在啟動 VS 模擬器](visual-studio-android-emulator-images/02-emulator-starting-vs-sml.png)](visual-studio-android-emulator-images/02-emulator-starting-vs.png#lightbox)

在一段啟動延遲之後，就會出現如左下方所示的模擬器畫面。 請向上拖曳畫面上的鎖定圖示以將裝置解除鎖定。
接著，Xamarin.Android 應用程式應該就會在模擬器中執行，如右邊所示：

[![瀏覽器螢幕擷取畫面](visual-studio-android-emulator-images/03-first-screen-vs-sml.png)](visual-studio-android-emulator-images/03-first-screen-vs.png#lightbox)

與使用預設 Android SDK 模擬器時一樣，您可以在程式碼中設定中斷點、檢查變數，以及檢視呼叫堆疊。 模擬器右邊的垂直工具列可用來存取模擬器功能：

[![垂直工具列上的按鈕](visual-studio-android-emulator-images/04-vertical-toolbar-vs-sml.png)](visual-studio-android-emulator-images/04-vertical-toolbar-vs.png#lightbox)

以下清單摘要說明垂直工具列上每個按鈕的功能：

-   **關閉** &ndash; 關閉模擬器應用程式。 此按鈕不常使用 &ndash; 通常，模擬器在第一次啟動後即保持執行 (以避免模擬器因重新啟動而延遲)，並且只在不再需要時才關閉。

-   **最小化** &ndash; 讓模擬器保持執行，但將其最小化至工具列。

-   **電源** &ndash; 模擬開啟和關閉裝置電源。 (模擬器會繼續執行)。

-   **多點觸控** &ndash; 將裝置顯示器上作為觸控點來進行捏合和縮放的數個點重疊。
    拖曳一個點會造成另一個點朝反方移動，模擬兩指移動的情況。

-   **單點滑鼠輸入** &ndash; 讓裝置回到單點輸入模式 (在使用多點觸控輸入模式之後)。

-   **向左旋轉/向右旋轉** &ndash; 可協助測試應用程式如何回應方向變更。 例如，第一次按一下 [向左旋轉] 按鈕時，模擬器會切換成橫向模式。 按 [向右旋轉] 按鈕時，模擬器將會回到直向模式。

-   **全螢幕** &ndash; 將模擬器螢幕縮放成與桌面螢幕相符。

-   **縮放** &ndash; 依 33%、50%、66%、100% 或某個自訂的百分比來調整模擬器螢幕大小。


[其他工具] 按鈕會顯示一個開啟的對話方塊，當中顯示模擬器的額外功能：

[![[其他工具] 對話方塊](visual-studio-android-emulator-images/05-additional-tools-vs-sml.png)](visual-studio-android-emulator-images/05-additional-tools-vs.png#lightbox)


從該對話方塊頂端的索引標籤列，即可使用每項額外功能：

-   **加速計** &ndash; 模擬裝置在 3D 空間中的移動。

-   **位置** &ndash; 顯示一個可用來選取和模擬 GPS 位置的地圖。 在此地圖上，可以建立「地圖位置點」來模擬在位置間的移動。

-   **電池** &ndash; 提供一個滑桿來模擬電池中的剩餘電量。

-   **螢幕擷取畫面** &ndash; 在此索引標籤中，[擷取] 按鈕可建立螢幕擷取畫面並顯示快速預覽。 [儲存] 按鈕可儲存螢幕擷取畫面。

-   **相機** &ndash; 模擬透過固定的動畫影像拍照、從檔案拍照，或從您主機電腦上的網路攝影機拍照。 可以選取前端或後端相機。

-   **SD 記憶卡** &ndash; 模擬器可在您的主機電腦上建立可供裝置作為 SD 記憶卡的資料夾。
    當應用程式對模擬的 SD 記憶卡讀取和寫入檔案時，無須使用 `adb` 命令，即可從桌面直接存取這些檔案。

-   **網路** &ndash; 顯示模擬器網路設定的摘要 (模擬器會重複使用主機電腦的網路連線)。

如需有關如何使用這些功能的詳細資訊，請參閱 [Visual Studio 的 Android 模擬器簡介](https://blogs.msdn.microsoft.com/visualstudioalm/2014/11/12/introducing-visual-studios-emulator-for-android/) \(英文\)。



## <a name="configuring-device-profiles"></a>設定裝置設定檔

Microsoft Android 模擬器包含一組裝置設定檔，這些設定檔代表市場上 Android 裝置的最常用 Android 版本、螢幕大小及硬體屬性。 此外，這些裝置設定檔已經針對各種 Android 版本 (例如 KitKat、Lollipop 及 Marshmallow) 做好設定。

「模擬器管理員」可用來進行裝置設定檔安裝、解除安裝及啟動。 請從 [工具] 功能表中，選取 [Visual Studio 的 Android 模擬器]，如以下螢幕擷取畫面所示：

[![從 [工具] 功能表啟動模擬器](visual-studio-android-emulator-images/06-launch-emulator-manager-vs-sml.png)](visual-studio-android-emulator-images/06-launch-emulator-manager-vs.png#lightbox)

這會開啟 [裝置設定檔] 對話方塊。 已安裝的設定檔會在裝置設定檔清單的頂端以醒目方式標示。 未安裝 (但可供安裝) 的設定檔會顯示成灰色：

[![裝置設定檔圖示](visual-studio-android-emulator-images/07-device-profiles-vs-sml.png)](visual-studio-android-emulator-images/07-device-profiles-vs.png#lightbox)

若要安裝新的設定檔，請按一下設定檔安裝圖示 (箭頭向下的箭號，如以上螢幕擷取畫面所示)。 例如，當您按一下 [5.7" Marshmallow (6.0.0) XHDPI Phone] 的設定檔安裝圖示時，「模擬器管理員」會下載該設定檔，如下所示：

[![下載設定檔的範例](visual-studio-android-emulator-images/08-downloading-profile-vs-sml.png)](visual-studio-android-emulator-images/08-downloading-profile-vs.png#lightbox)

下載裝置設定檔之後，會以醒目方式標示它，以表示已成功安裝該設定檔。 按一下 [顯示詳細資料] 圖示將會顯示平台類型、CPU 架構、螢幕大小/解析度，以及裝置可用的記憶體：

[![顯示裝置設定檔詳細資料](visual-studio-android-emulator-images/09-show-details-vs-sml.png)](visual-studio-android-emulator-images/09-show-details-vs.png#lightbox)

當 Visual Studio [偵錯目標] 下拉式功能表開啟時，新安裝的裝置設定檔現已可供作為目標使用：

[![目標下拉式功能表中的新設定檔](visual-studio-android-emulator-images/10-debug-target-vs-sml.png)](visual-studio-android-emulator-images/10-debug-target-vs.png#lightbox)

若要縮短此清單，可以在「模擬器管理員」中按一下 [將此模擬器解除安裝] 來移除不使用的裝置設定檔。 請注意，目前無法在此模擬器中建立自訂的裝置模擬器。


## <a name="troubleshooting"></a>疑難排解

本節說明搭配 Xamarin.Android 使用「Visual Studio 的 Android 模擬器」時的一些常見錯誤和因應措施。

<a name="cant_connect" />

### <a name="emulator-will-not-start"></a>模擬器無法啟動

在某些情況下，如果主機處理器與 Hyper-V 虛擬機器之間不相容，模擬器將無法啟動。 若要解決此問題，請設定 Hyper-V 以限制虛擬機器所能擁有的處理器功能 &ndash; 這可改善虛擬機器與不同主機處理器版本的相容性。
請使用下列步驟來進行此變更：

1.  按一下 [啟動] 按鈕，輸入 **MMC**，然後按 **Enter**鍵。 按一下 [Hyper-V 管理員]，如下所示：

    [![Hyper-V 管理員](visual-studio-android-emulator-images/15-launch-hyperv-manager.png)](visual-studio-android-emulator-images/15-launch-hyperv-manager.png#lightbox)

2.  在 [Hyper-V 管理員] 的 [虛擬機器] 窗格中，於要編輯的模擬器上按一下滑鼠右鍵，然後按一下 [設定]：

    [![虛擬機器的 [設定] 功能表項目](visual-studio-android-emulator-images/16-vm-settings.png)](visual-studio-android-emulator-images/16-vm-settings.png#lightbox)

3.  在設定視窗中，找出 [相容性] 區段 (位於 [硬體] > [處理器] 底下)，然後啟用 [移轉至使用不同處理器版本的實體電腦]：

    [![已選取 [移轉] 選項](visual-studio-android-emulator-images/17-set-compatibility-vs-sml.png)](visual-studio-android-emulator-images/17-set-compatibility-vs.png#lightbox)

4.  按一下 [確定] 並關閉 [Hyper-V 管理員] 視窗。



### <a name="app-deploys-and-starts-but-fails-immediately"></a>應用程式部署並啟動但立即發生失敗

在此情況下，模擬器啟動，應用程式成功部署至模擬器，並且應用程式啟動。 不過，應用程式立即發生失敗。
在許多情況下，這也是主機處理器與 Hyper-V 虛擬機器之間不相容所造成。 若要解決此錯誤，請依照[模擬器無法啟動](#cant_connect) (參見上方) 中的指示進行操作。


### <a name="emulator-stops-with-the-diagnostic-message-libaot-mscorlibdllso-not-found"></a>模擬器停止並傳回下列診斷訊息：**找不到 libaot-mscorlib.dll.so**

若要解決此錯誤，請使用下列步驟來停用快速部署：

1.  依照[模擬器無法啟動](#cant_connect) (參見上方) 中的指示進行操作。

2.  按兩下專案 [屬性]。

3.  按一下 [Android 選項]，然後取消選取 [使用 Fast Deployment (僅限偵錯模式)]：

    [![已取消選取 [使用 Fast Deployment (僅限偵錯模式)]](visual-studio-android-emulator-images/18-fast-deployment-vs-sml.png)](visual-studio-android-emulator-images/18-fast-deployment-vs.png#lightbox)



### <a name="drag-and-drop-does-not-work"></a>無法拖放

如果以系統管理員身分啟動「Visual Studio 的 Android 模擬器」(或是在 Visual Studio 以系統管理員權限執行的情況下，從 Visual Studio 啟動該模擬器)，可能無法拖放 .APK 或 .ZIP 檔案。 若要解決此問題，請在未提高權限的情況下 (亦即不是以系統管理員身分) 執行「Visual Studio 的 Android 模擬器」。


### <a name="other-errors"></a>其他錯誤

上述疑難排解秘訣涵蓋搭配 Xamarin.Android 使用「Visual Studio Android 模擬器」時的最常見問題。 如需更完整的「Visual Studio Android 模擬器」疑難排解指南，請參閱[針對 Visual Studio 的 Android 模擬器進行疑難排解](https://msdn.microsoft.com/en-us/library/mt228282.aspx)。



## <a name="summary"></a>總結

本文介紹了「Visual Studio 的 Android 模擬器」；不僅說明如何在 Visual Studio 中使用模擬器來針對 Xamarin.Android 應用程式進行疑難排解，還描述了垂直工作列上按鈕的功能，並提供 [其他工具] 對話方塊中可用功能的簡要概觀。 它說明了如何使用「模擬器管理員」來進行裝置設定檔安裝、解除安裝及啟動。 ＜疑難排解＞一節則說明了使用模擬器時常見的問題和因應措施。


## <a name="related-links"></a>相關連結

- [Android 版 Visual Studio 模擬器](https://www.visualstudio.com/vs/msft-android-emulator/)
- [Visual Studio Android Emulator 簡介](https://blogs.msdn.microsoft.com/visualstudioalm/2014/11/12/introducing-visual-studios-emulator-for-android/)
