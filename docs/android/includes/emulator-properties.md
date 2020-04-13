---
ms.openlocfilehash: b505f4a4f7a8f50a6044d01a327eef76a7b1ce3b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70758035"
---
|屬性|描述|選項。|
|--- |--- |--- |
|`abi.type`|**ABI 類型** &ndash; 指定模擬裝置的 ABI (應用程式二進位介面) 類型。 **x86** 選項是用於指令集，通常稱為 "x86" 或 "IA-32"。 **x86_64** 選項是用於 64 位元 x86 指令集。 **armeabi-v7a** 選項是用於具有 v7-a ARM 擴充功能的 ARM 指令集。 **arm64-v8a** 選項是用於支援 AArch64 的 ARM 指令集。|x86、x86_64、armeabi-v7a、arm64-v8a|
|`disk.cachePartition`|**快取磁碟分割** &ndash; 決定模擬裝置是否將使用裝置上的 **/cache** 磁碟分割。 **/cache** 磁碟分割 (一開始是空的) 是 Android 儲存經常存取之資料和應用程式元件的位置。 如果設定為 **no**，模擬器將不會使用 **/cache** 磁碟分割，而且會忽略其他 `disk.cache` 設定。|yes、no|
|`disk.cachePartition.path`|**快取磁碟分割路徑** &ndash; 指定您開發電腦上的快取磁碟分割映像檔。 模擬器將會使用此檔案作為 **/cache** 磁碟分割。 輸入絕對路徑或模擬器 [data] 目錄的相對路徑。 如果未設定，模擬器會在您的開發電腦上建立一個名為 **cache.img** 的暫存檔。 如果該檔案不存在，就會建立成空檔案。 若 `disk.cachePartition` 是設定為 **no**，將會忽略此選項。||
|`disk.cachePartition.size`|**快取磁碟分割大小** &ndash; 快取磁碟分割檔案的大小 (單位為位元組)。 通常您不需要設定此選項，除非應用程式將下載大於 66 MB 預設快取大小的非常大型檔案，。 若 `disk.cachePartition` 是設定為 **no**，將會忽略此選項。 如果此值是整數，就會以位元組為單位來指定大小。 您也可以使用 KB、MB 與 GB 為單位來指定此大小，方法是在值附加 **K**、**M** 或 **G**。 最小尺寸為**9M,** 最大尺寸為**1023G。**||
|`disk.dataPartition.initPath`|**資料磁碟分割的初始路徑** &ndash; 指定資料磁碟分割的初始內容。 抹除使用者資料之後，模擬器會將所指定檔案的內容複製到使用者資料 (預設為 **userdata-qemu.img**) 中，而不會使用 **userdata.img** 作為初始版本。||
|`disk.dataPartition.path`|**資料磁碟分割的路徑** &ndash; 指定使用者資料磁碟分割檔案。 若要設定持續性使用者資料檔案，請輸入檔案名稱和您開發電腦上的路徑。 如果該檔案不存在，模擬器就會從預設檔案 **userdata.img** 建立映像、將它儲存在 `disk.dataPartition.path` 所指定的檔案名稱中，然後在模擬器關閉時，將使用者資料保存至該檔案。 如果您未指定路徑，預設檔案會命名為 **userdata-qemu.img**。 特殊值**\<臨時>** 導致模擬器創建和使用暫存檔。 若已設定 `disk.dataPartition.initPath`，其內容在開機階段將會被複製到 `disk.dataPartition.path` 檔案。 請注意，這個選項不能留白。||
|`disk.dataPartition.size`|**資料磁碟分割大小** &ndash; 指定使用者資料磁碟分割的大小 (單位為位元組)。 如果此值是整數，就會以位元組為單位來指定大小。 您也可以使用 KB、MB 與 GB 為單位來指定此大小，方法是在值附加 **K**、**M** 或 **G**。 最小尺寸為**9M,** 最大尺寸為**1023G。**||
|`disk.ramdisk.path`|**Ramdisk 路徑** &ndash; 開機磁碟分割 (Ramdisk) 映像的路徑。 Ramdisk 映像是在掛接系統映像之前，核心會先載入的系統映像子集。 Ramdisk 映像通常包含開機時間二進位檔和初始化指令碼。 如果未指定此選項，則預設會是模擬器系統目錄中的 **ramdisk.img**。||
|`disk.snapStorage.path`|**快照儲存體路徑** &ndash; 儲存所有快照的「快照儲存體檔案」路徑。 在執行期間建立的所有快照都會儲存至這個檔案。 只有儲存至這個檔案的快照，才能在模擬器執行期間被還原。 如果未指定此選項，則預設會是模擬器資料目錄中的 snapshots.img。||
|`disk.systemPartition.initPath`|**系統磁碟分割初始路徑** &ndash; 系統映像檔唯讀複本的路徑；具體而言，就是包含系統程式庫及與 API 層級和任何變體對應之資料的磁碟分割。 如果未指定此路徑，則預設會是模擬器系統目錄中的 system.img。||
|`disk.systemPartition.path`|**系統磁碟分割路徑** &ndash; 讀取/寫入系統磁碟分割映像的路徑。 如果未設定此路徑，就會從 `disk.systemPartition.initPath` 所指定檔案的內容建立暫存檔並初始化。||
|`disk.systemPartition.size`|**系統磁碟分割大小** &ndash; 系統磁碟分割的理想大小 (單位為位元組)。 如果實際系統磁碟分割映像大於此設定，便會忽略此大小；否則，這會指定系統磁碟分割檔案可成長的大小上限。 如果此值是整數，就會以位元組為單位來指定大小。 您也可以使用 KB、MB 與 GB 為單位來指定此大小，方法是在值附加 **K**、**M** 或 **G**。 最小尺寸為**9M,** 最大尺寸為**1023G。**||
|`hw.accelerometer`|**加速計** &ndash; 決定模擬裝置是否包含加速計感應器。 加速計可協助裝置判斷方向 (用於自動旋轉)。 加速計會回報裝置加速情況及三個感應器軸。|yes、no|
|`hw.audioInput`|**音訊錄製支援** &ndash; 決定模擬裝置是否可以錄製音訊。|yes、no|
|`hw.audioOutput`|**音訊播放支援** &ndash; 決定模擬裝置是否可以播放音訊。|yes、no|
|`hw.battery`|**電池支援** &ndash; 決定模擬裝置是否可以靠電池電力運行。|yes、no|
|`hw.camera`|**相機支援** &ndash; 決定模擬裝置是否具有相機。|yes、no|
|`hw.camera.back`|**後置相機** &ndash; 設定後置相機 (鏡頭朝使用者的反方向)。 如果您使用開發電腦上的網路攝影機來模擬模擬裝置上的後置相機，就必須將此值設定為 webcam*n*，其中 _n_ 會選取網路攝影機 (如果您只有一個網路攝影機，請選擇 **webcam0**)。 如果設定為 emulated，模擬器就會以軟體模擬相機。 若要停用後置相機，請將此值設定為 none。 如果您啟用後置相機，請務必一併啟用 `hw.camera`。|emulated、none、webcam0|
|`hw.camera.front`|**前置相機** &ndash; 設定前置相機 (鏡頭朝向使用者的方向)。 如果您使用開發電腦上的網路攝影機來模擬模擬裝置上的前置相機，就必須將此值設定為 webcam*n*，其中 _n_ 會選取網路攝影機 (如果您只有一個網路攝影機，請選擇 **webcam0**)。 如果設定為 emulated，模擬器就會以軟體模擬相機。 若要停用前置相機，請將此值設定為 none。 如果您啟用前置相機，請務必一併啟用 `hw.camera`。|emulated、none、webcam0|
|`hw.camera.maxHorizontalPixels`|**最大水平相機像素** &ndash; 設定模擬裝置的相機最大水平解析度 (單位為像素)。||
|`hw.camera.maxVerticalPixels`|**最大垂直相機像素** &ndash; 設定模擬裝置的相機最大垂直解析度 (單位為像素)。||
|`hw.cpu.arch`|**CPU 架構** &ndash; 虛擬裝置所要模擬的 CPU 架構。 如果您使用 Intel HAXM 來進行硬體加速，請針對 32 位元 CPU 選取 **x86**。 針對 64 位元 HAXM 加速裝置，則選取 **x86_64**。 (請務必在 SDK 管理器中安裝相應的英特爾 x86 系統映射:例如,英特爾 x86 Atom 或英特爾 x86 Atom_64)要類比 ARM CPU,請為 32 位元選擇**臂,** 或為 64 位 ARM CPU 選擇**臂 64。** 請記住，ARM 型虛擬裝置的執行速度比 x86 型慢很多，因為 ARM 無法使用硬體加速。|x86、x86_64、arm、arm64|
|`hw.cpu.model`|**CPU 型號** &ndash; 此值通常保留不設定 (在未明確設定的情況下，它會設定為衍生自 `hw.cpu.arch` 的值)。 不過，可以將它設定為模擬器特定字串以供實驗使用。||
|`hw.dPad`|**DPad 按鍵** &ndash; 決定模擬裝置是否支援方向鍵 (DPad) 按鍵。 DPad 通常有四個按鍵來指示方向控制。|yes、no|
|`hw.gps`|**GPS 支援** &ndash; 決定模擬裝置是否具有 GPS (全球定位系統) 接收器。|yes、no|
|`hw.gpu.enabled`|**GPU 模擬** &ndash; 決定模擬裝置是否支援 GPU 模擬。 啟用時，GPU 模擬會使用 Open GL for Embedded Systems (OpenGL ES) 來轉譯畫面上的 2D 和 3D 圖形，而關聯的「GPU 模擬模式」設定則會決定 GPU 模擬的實作方式。|yes、no|
|`hw.gpu.mode`|**GPU 模擬模式** &ndash; 決定模擬器實作 GPU 模擬的方式。 如果您選取 auto，模擬器將會根據您的開發電腦設定來選擇硬體和軟體加速。 如果您選取 host，模擬器將會使用您開發電腦的圖形處理器來執行 GPU 模擬以加快轉譯速度。 如果您的 GPU 與模擬器不相容且您使用 Windows，則您可以嘗試使用 angle，而不使用 host。 angle 模式會使用 DirectX 來提供與 host 類似的效能。 如果您選取 mesa，模擬器將會使用 Mesa 3D 軟體程式庫來轉譯圖形。 當您無法透過開發電腦的圖形處理器進行轉譯時，請選取 mesa。 在效能比使用電腦 GPU 稍差的軟體中，可以使用 swiftshader 模式來轉譯圖形。 off 選項 (停用圖形硬體模擬) 已過時，此選項可能導致某些項目轉譯不正確，因此不建議使用。|auto、host、mesa、angle、swiftshader、off|
|`hw.gsmModem`|**GSM 數據機支援** &ndash; 決定模擬裝置是否包含支援 GSM (全球行動通訊系統) 電話無線電系統的數據機。|yes、no|
|`hw.initialOrientation`|**初始螢幕方向** &ndash; 設定模擬裝置上的初始螢幕方向 (直向或橫向模式)。 在直向模式中，螢幕高度會大於寬度。 在橫向模式中，螢幕寬度會大於高度。 執行模擬裝置時，如果裝置設定檔中同時支援直向和橫向模式，您就可以變更方向。|portrait、landscape|
|`hw.keyboard`|**鍵盤支援** &ndash; 決定模擬裝置是否支援 QWERTY 鍵盤。|yes、no|
|`hw.keyboard.charmap`|**鍵盤字元對應名稱** &ndash; 此裝置的硬體字元對應名稱。 注意：這應該一律是預設的 **qwerty2**，除非您已相應地修改系統映像。 此名稱會在開機時傳送至核心。 使用的名稱如果不正確，將會造成虛擬裝置無法使用。||
|`hw.keyboard.lid`|**鍵盤蓋支援** &ndash; 如果已啟用鍵盤支援，此設定可決定是否可以將 QWERTY 鍵盤關閉/隱藏或開啟/顯示。 如果將 hw.keyboard 設定為 false，就會忽略此設定。 注意：如果模擬裝置的目標是 API 層級 12 或更高層級，則預設值為 false。|yes、no|
|`hw.lcd.backlight`|**LCD 背光** &ndash; 決定模擬裝置是否要模擬 LCD 背光。|yes、no|
|`hw.lcd.density`|**LCD 密度** &ndash; 所模擬 LCD 顯示器的密度，測量單位為密度獨立像素 (或 dp，dp 是虛擬像素單位)。 當此設定為 160 dp 時，每個 dp 會對應一個實體像素。 在執行階段，Android 會使用此值來選取和調整適當的資源/資產，來進行正確的顯示器轉譯。|120、160、240、213、320|
|`hw.lcd.depth`|**LCD 色彩深度** &ndash; 所模擬畫面緩衝區的色彩位元深度，畫面緩衝區會保存用於驅動 LCD 顯示的點陣圖。 此值可以是 16 位元 (65,536 個可能的色彩) 或 32 位元 (16,777,216 個色彩再加上透明度)。 32 位元設定可能讓模擬器執行速度稍微慢一些，但可提供較精確的色彩。|16, 32|
|`hw.lcd.height`|**LCD 像素高度** &ndash; 構成所模擬 LCD 顯示器之垂直維度的像素數目。||
|`hw.lcd.width`|**LCD 像素寬度** &ndash; 構成所模擬 LCD 顯示器之水平維度的像素數目。||
|`hw.mainKeys`|**硬體返回/主畫面按鍵** &ndash; 決定模擬裝置是否支援硬體 [返回] 與 [主畫面] 導覽按鈕。 如果只在軟體中實作按鈕，您可以將此值設定為 **yes**。 如果將 `hw.mainKeys` 設定為 **yes**，模擬器將不會在螢幕上顯示導覽按鈕，但您可以使用模擬器側邊面板來「按」這些按鈕。|yes、no|
|`hw.ramSize`|**裝置 RAM 大小** &ndash; 模擬裝置上的實體 RAM 數量 (單位為 MB)。 將會從螢幕大小或面板版本計算出預設值。 將大小增大可以加快模擬器作業速度，但代價是會從您的開發電腦要求更多資源。||
|`hw.screen`|**觸控式螢幕類型** &ndash; 定義模擬裝置上螢幕的類型。 multi-touch 螢幕可追蹤觸控式介面上兩根以上的手指。 touch 螢幕只能偵測單一手指觸控事件。 no-touch 螢幕不會偵測觸控事件。|touch、multi-touch、no-touch|
|`hw.sdCard`|**SDCard 支援** &ndash; 決定模擬裝置是否支援插入和移除虛擬 SD (Secure Digital) 記憶卡。 模擬器會使用儲存在您開發電腦上的可掛接磁碟映像，來模擬實際 SD 記憶卡裝置的磁碟分割 (請參閱 hw.sdCard.path)。|yes、no|
|`sdcard.size`|**SDCard 大小** &ndash; 指定位於 `hw.sdCard.path` 所指定裝置上可用位置之虛擬 SD 記憶卡檔案的 大小 (以位元組為單位)。 如果此值是整數，就會以位元組為單位來指定大小。 您也可以使用 KB、MB 與 GB 為單位來指定此大小，方法是在值附加 **K**、**M** 或 **G**。 最小尺寸為**9M,** 最大尺寸為**1023G。**||
|`hw.sdCard.path`|**SDCard 映像路徑** &ndash; 指定您開發電腦上 SD 記憶卡磁碟分割映像檔的檔案名稱和路徑。 例如，可以將此路徑設定為 Windows 上的 **C:\sd\sdcard.img**。||
|`hw.sensors.magnetic_field`|**磁場感應器** &ndash; 決定模擬裝置是否支援磁場感應器。 磁場感應器 (也稱為磁力計) 會回報沿著三個感應器軸測量到的周遭地球磁場。 如果應用程式需要存取羅盤讀數，請啟用此設定。 例如，導航應用程式可能會使用此感應器來偵測使用者所正對的方向。|yes、no|
|`hw.sensors.orientation`|**方向感應器** &ndash; 決定模擬裝置是否提供方向感應器值。 方向感應器會測量裝置圍繞三個實體軸 (x、y、z) 進行的旋轉角度。 請注意，方向感應器自 Android 2.2 (API 層級 8) 起即以被取代。|yes、no|
|`hw.sensors.proximity`|**近接感應器** &ndash; 決定模擬裝置是否支援近接感應器。 此感應器會測量物件相對於裝置檢視螢幕的鄰近程度。 此感應器通常用來判斷話筒是否正舉到使用者的耳邊。|yes、no|
|`hw.sensors.temperature`|**溫度感應器** &ndash; 決定模擬裝置是否支援溫度感應器。 此感應器會測量裝置的攝氏溫度 (&deg;C)。|yes、no|
|`hw.touchScreen`|**觸控式螢幕支援** &ndash; 決定模擬裝置是否支援觸控式螢幕。 觸控式螢幕是用來在螢幕上直接操控物件。|yes、no|
|`hw.trackBall`|**軌跡球支援** &ndash; 決定模擬裝置是否支援軌跡球。|yes、no|
|`hw.useext4`|**EXT4 檔案系統支援** &ndash; 決定模擬裝置是否使用 Linux EXT4 檔案系統作為磁碟分割。 由於檔案系統類型現在採用自動偵測，因此這個選項已被取代並忽略。|否|
|`kernel.newDeviceNaming`|**核心新裝置命名** &ndash; 用來指定核心是否需要新的裝置命名配置。 這通常與 Linux 3.10 核心或更新版本搭配使用。 如果設定為 **autodetect**，模擬器將會自動偵測核心是否需要新的命名配置。|autodetect、yes、no|
|`kernel.parameters`|**核心參數** &ndash; 指定 Linux 核心開機參數的字串。 此設定預設會留白。||
|`kernel.path`|**核心路徑** &ndash; 指定 Linux 核心的路徑。 如果未指定此路徑，模擬器就會查看模擬器系統目錄中是否有 kernel-ranchu。||
|`kernel.supportsYaffs2`|**YAFFS2 磁碟分割支援** &ndash; 決定核心是否支援 YAFFS2 (Yet Another Flash File System 2) 磁碟分割。 一般而言，這僅適用於 Linux 3.10 之前的核心。 如果設定為 **autodetect**，模擬器將會自動偵測核心是否可以掛接 YAFFS2 檔案系統。|autodetect、yes、no|
|`skin.name`|**面板名稱** &ndash; Android 模擬器面板的名稱。 面板是一個檔案集合，定義了模擬器顯示畫面的視覺和控制元素；它會描述 AVD 視窗在您開發電腦上看起來的樣子。 面板會描述螢幕大小、按鈕及整體設計，但不會影響您應用程式的操作。||
|`skin.path`|**面板路徑** &ndash; 包含 skin.name 中所指定模擬器面板檔案的目錄路徑。此目錄包含 hardware.ini 版面配置檔案，以及面板顯示元素的影像檔。||
|`skin.dynamic`|**面板動態** &ndash; 面板是否為動態面板。 如果模擬器要根據指定的寬度和高度來建構指定大小的面板，則模擬器面板為動態面板。|否|
