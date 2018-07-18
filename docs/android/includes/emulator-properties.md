|屬性|描述|選項|
|--- |--- |--- |
|`abi.type`|**ABI 類型**&ndash;指定模擬裝置的 ABI （應用程式二進位介面） 型別。 **X86**選項適用於指令集通常稱為 「 x86 」 或 「 ia-32。 」 **X86_64**選項適用於 64 位元 x86 指令集。 **Armeabi v7a**選項適用於 ARM 指令 v7 的 ARM 延伸模組設定。 **Arm64 v8a**選項適用於 ARM 指令集支援 AArch64。|x86、x86_64、armeabi-v7a、arm64-v8a|
|`disk.cachePartition`|**快取資料分割**&ndash;決定是否要使用模擬的裝置 **/快取**在裝置上的磁碟分割。 **/快取**（這是一開始是空的） 資料分割是 Android 儲存經常存取的資料和應用程式元件的位置。 如果設定為**沒有**，模擬器不會使用 **/快取**磁碟分割，而另一個`disk.cache`設定將被忽略。|yes、no|
|`disk.cachePartition.path`|**快取資料分割路徑**&ndash;指定開發電腦上的快取的磁碟分割映像檔案。 模擬器會使用這個檔案進行 **/快取**磁碟分割。 輸入的絕對路徑或是相對於模擬器 的資料目錄路徑。 如果未設定，模擬器會建立空白的暫存檔案，稱為**cache.img**開發電腦上。 如果該檔案不存在，就會建立成空檔案。 如果這個選項就會忽略`disk.cachePartition`設**沒有**。||
|`disk.cachePartition.size`|**快取的磁碟分割大小**&ndash;快取分割區檔案 （以 mb 為單位） 的大小。 通常您不需要設定此選項，除非應用程式將下載大於 66 MB 預設快取大小的非常大型檔案，。 如果這個選項就會忽略`disk.cachePartition`設**沒有**。||
|`disk.dataPartition.initPath`|**資料分割的初始路徑**&ndash;指定的資料分割的初始內容。 在之後清除使用者資料，模擬器將指定檔案的內容複製到使用者資料 (根據預設， **userdata qemu.img**) 而不是使用**userdata.img**初始版本。||
|`disk.dataPartition.path`|**路徑的資料分割**&ndash;指定使用者資料的分割區檔案。 若要設定持續性使用者資料檔案，請輸入檔案名稱和您開發電腦上的路徑。 如果檔案不存在，模擬器會從預設的檔案建立映像**userdata.img**，將它儲存在指定的檔名`disk.dataPartition.path`，並在模擬器關閉時保存使用者資料。 如果您未指定路徑，預設檔名為**userdata qemu.img**。 特殊值 **<temp>** 會使模擬器來建立和使用的暫存檔案。 如果`disk.dataPartition.initPath`已經設定，其內容將複製到在開機時 disk.dataPartition.path 檔案。 請注意，這個選項不能留白。||
|`disk.dataPartition.size`|**資料分割大小**&ndash;指定的使用者資料分割的大小 （mb）。||
|`disk.ramdisk.path`|**Ramdisk 路徑**&ndash;開機磁碟分割 (ramdisk) 映像的路徑。 Ramdisk 映像是在掛接系統映像之前，核心會先載入的系統映像子集。 Ramdisk 映像通常包含開機時間二進位檔和初始化指令碼。 如果未指定此選項，預設值是**ramdisk.img**模擬器系統目錄中。||
|`disk.snapStorage.path`|**快照集儲存體路徑**&ndash;儲存所有快照集的快照集儲存區檔案的路徑。 在執行期間建立的所有快照都會儲存至這個檔案。 只有儲存至這個檔案的快照，才能在模擬器執行期間被還原。 如果未指定此選項，預設會為 snapshots.img 模擬器 data 目錄中。||
|`disk.systemPartition.initPath`|**系統磁碟分割 init 路徑**&ndash;唯讀複本的系統映像檔的路徑; 具體來說，資料分割包含的系統程式庫和對應的應用程式開發介面層級和任何變數的資料。 如果未指定這個路徑，則預設為 system.img 模擬器系統目錄中。||
|`disk.systemPartition.path`|**系統磁碟分割路徑**&ndash;讀取/寫入系統磁碟分割映像的路徑。 如果未設定此路徑，會建立並初始化所指定的檔案內容的暫存檔案`disk.systemPartition.initPath`。||
|`disk.systemPartition.size`|**系統磁碟分割大小**&ndash;理想大小 （以 mb 為單位） 的系統磁碟分割。 如果實際系統磁碟分割映像大於此設定，便會忽略此大小；否則，這會指定系統磁碟分割檔案可成長的大小上限。||
|`hw.accelerometer`|**加速計**&ndash;判斷模擬的裝置是否包含加速計感應器。 加速計可協助裝置判斷方向 (用於自動旋轉)。 加速計會回報裝置加速情況及三個感應器軸。|yes、no|
|`hw.audioInput`|**音訊錄製支援**&ndash;判斷模擬的裝置是否可以錄製音訊。|yes、no|
|`hw.audioOutput`|**音訊播放支援**&ndash;決定是否模擬的裝置可以播放音訊。|yes、no|
|`hw.battery`|**電池支援**&ndash;判斷模擬的裝置是否可以在使用電池執行。|yes、no|
|`hw.camera`|**攝影機支援**&ndash;判斷模擬的裝置是否有數位相機。|yes、no|
|`hw.camera.back`|**上一步相機**&ndash;設定回相機 （遠離使用者透鏡面）。 如果您使用網路攝影機開發電腦上模擬後端上的相機模擬裝置，此值必須設定網路攝影機以*n*，其中_n_ （如果您有只有一個網路攝影機，選取網路攝影機選擇**webcam0**)。 如果設定為模擬的模擬器會模擬攝影機中的軟體。 若要停用後相機，設定此值為 none。 如果您啟用後相機，請務必也啟用`hw.camera`。|emulated、none、webcam0|
|`hw.camera.front`|**前方相機**&ndash;設定前方相機 （透鏡面朝使用者）。 如果您使用網路攝影機開發電腦上模擬前方相機模擬的裝置上，這個值必須設定網路攝影機以*n*，其中_n_ （如果您有只有一個網路攝影機，請選取網路攝影機選擇**webcam0**)。 如果設定為模擬，模擬器會模擬在軟體中的相機。 若要停用前方相機，設定此值為 none。 如果您啟用前方相機，請務必也啟用`hw.camera`。|emulated、none、webcam0|
|`hw.camera.maxHorizontalPixels`|**最大的水平相機像素**&ndash;設定的最大的水平解析度，模擬的裝置的相機功能 （以像素為單位）。||
|`hw.camera.maxVerticalPixels`|**最大垂直相機像素**&ndash;設定的最大的垂直解析度，模擬的裝置的相機功能 （以像素為單位）。||
|`hw.cpu.arch`|**CPU 架構** &ndash; CPU 架構，用來模擬虛擬裝置。 如果您使用 Intel HAXM 硬體加速功能，選取**x86** 32 位元 cpu。 選取**x86_64** 64 位元 HAXM 加速裝置。 (請確定 SDK Manager 中安裝對應的 Intel x86 系統映像： 例如，Intel x86 Atom 或 Intel x86 Atom_64。)若要模擬 ARM CPU，請選取**arm**的 32 位元或選取**arm64** 64 位元 ARM cpu。 請記住，ARM 型虛擬裝置的執行速度比 x86 型慢很多，因為 ARM 無法使用硬體加速。|x86、x86_64、arm、arm64|
|`hw.cpu.model`|**CPU 模型**&ndash;這個值通常處於未設定 (將設為衍生自`hw.cpu.arch`若未明確設定)。 不過，可以將它設定為模擬器特定字串以供實驗使用。||
|`hw.dPad`|**了方向鍵金鑰**&ndash;判斷模擬的裝置是否支援方向鍵了 （方向鍵） 索引鍵。 DPad 通常有四個按鍵來指示方向控制。|yes、no|
|`hw.gps`|**GPS 支援**&ndash;判斷模擬的裝置是否有 GPS （全球定位系統） 接收端。|yes、no|
|`hw.gpu.enabled`|**GPU 模擬**&ndash;判斷模擬的裝置是否支援 GPU 模擬。 啟用時，GPU 模擬會使用 Open GL for Embedded Systems (OpenGL ES) 來轉譯畫面上的 2D 和 3D 圖形，而關聯的「GPU 模擬模式」設定則會決定 GPU 模擬的實作方式。|yes、no|
|`hw.gpu.mode`|**GPU 模擬模式**&ndash;判斷模擬器模擬 GPU 的實作方式。 如果您選取 [自動] 時，模擬器會選擇根據您的開發電腦設定的硬體和軟體加速。 如果您選取的主機，模擬器會使用開發電腦的圖形處理器來執行更快速呈現 GPU 模擬。 如果您的 GPU 不是與模擬器相容而且您在 Windows 上，您可以嘗試而不是主機的角度。 角度模式會使用 DirectX 提供類似於主機的效能。 如果您選取高地，模擬器會使用高地 3D 軟體程式庫來呈現圖形。 如果您有透過您的開發電腦的圖形處理器呈現的問題，請選取高地。 Swiftshader 模式可以用於呈現圖形以稍微少的效能比使用您的電腦 GPU 的軟體。 已被取代的選項，可能會造成某些不適當呈現項目，而因此不建議您關閉選項 （停用圖形硬體模擬）。|auto、host、mesa、angle、swiftshader、off|
|`hw.gsmModem`|**GSM 數據機支援**&ndash;決定是否模擬的裝置包括支援 GSM （通用行動通訊系統） 的電話語音選項系統的數據機。|yes、no|
|`hw.initialOrientation`|**初始螢幕方向** &ndash; 設定模擬裝置上的初始螢幕方向 (直向或橫向模式)。 在直向模式中，螢幕高度會大於寬度。 在橫向模式中，螢幕寬度會大於高度。 執行模擬裝置時，如果裝置設定檔中同時支援直向和橫向模式，您就可以變更方向。|portrait、landscape|
|`hw.keyboard`|**鍵盤支援**&ndash;判斷模擬的裝置是否支援 QWERTY 鍵盤。|yes、no|
|`hw.keyboard.charmap`|**鍵盤 charmap 名稱**&ndash;此裝置的硬體 charmap 名稱。 注意： 這應該一律是預設**qwerty2**除非您已經據此修改系統映像。 此名稱會在開機時傳送至核心。 使用的名稱如果不正確，將會造成虛擬裝置無法使用。||
|`hw.keyboard.lid`|**鍵盤筆記電腦螢幕支援**&ndash;如果已啟用鍵盤支援，此設定會決定是否 QWERTY 鍵盤可以關閉/隱藏或開啟/可見。 如果 hw.keyboard 設為 false，則會忽略此設定。 附註： 預設值為 false，如果以模擬的裝置為目標的應用程式開發介面層級 12 或更高版本。|yes、no|
|`hw.lcd.backlight`|**LCD 背光**&ndash;決定是否要將 LCD 背光模擬所模擬的裝置。|yes、no|
|`hw.lcd.density`|**LCD 密度**&ndash;模擬的 LCD 顯示器，以密度無關的像素或 dp 的密度 （dp 是虛擬的像素單位）。 當此設定為 160 dp 時，每個 dp 會對應一個實體像素。 在執行階段，Android 會使用此值來選取和調整適當的資源/資產，來進行正確的顯示器轉譯。|120、160、240、213、320|
|`hw.lcd.depth`|**LCD 色彩深度**&ndash;色彩位元深度模擬的畫面格緩衝區保存開車 LCD 顯示器的點陣圖。 此值可以是 16 位元 (65,536 個可能的色彩) 或 32 位元 (16,777,216 個色彩再加上透明度)。 32 位元設定可能讓模擬器執行速度稍微慢一些，但可提供較精確的色彩。|16, 32|
|`hw.lcd.height`|**LCD 像素高度**&ndash;模擬 LCD 顯示器垂直維度所組成的像素數目。||
|`hw.lcd.width`|**LCD 像素寬度**&ndash;模擬 LCD 顯示器的水平維度所組成的像素數目。||
|`hw.mainKeys`|**硬體後/Home 鍵**&ndash;判斷模擬的裝置是否支援硬體回和首頁瀏覽按鈕。 您可以將此值設定為**是**如果按鈕只在軟體中實作。 如果`hw.mainKeys`設**是**，模擬器不會在畫面上，顯示瀏覽按鈕，但是您可以使用模擬器側邊面板 「 按 」 這些按鈕。|yes、no|
|`hw.ramSize`|**裝置 RAM 大小**&ndash;模擬裝置，以 mb 為單位的實體 RAM 數量。 將會從螢幕大小或面板版本計算出預設值。 將大小增大可以加快模擬器作業速度，但代價是會從您的開發電腦要求更多資源。||
|`hw.screen`|**觸控式螢幕類型**&ndash;定義模擬的裝置上的螢幕類型。 多點觸控螢幕可以追蹤兩個或多個手指觸控介面上。 觸控式螢幕可以偵測只有單指觸控事件。 沒有觸控式螢幕不會偵測觸控事件。|touch、multi-touch、no-touch|
|`hw.sdCard`|**SDCard 支援**&ndash;判斷模擬的裝置支援插入和移除的虛擬 (Secure Digital) 的 sd 記憶卡。 模擬器會模擬實際的 sd 記憶卡裝置 （請參閱 hw.sdCard.path） 的資料分割使用成為可掛接的磁碟映像儲存在您的開發電腦上。|yes、no|
|`sdcard.size`|**SDCard 大小**&ndash;指定所指定的位置上虛擬的 sd 記憶卡檔案的大小`hw.sdCard.path`。 （以位元組為單位） 裝置上的可用。 如果此大小為簡單整數，就會以位元組為單位來指定大小。 您也可以使用 KB、MB 及 GB 為單位來指定此大小，方法是在大小附加 K、M 或 G。 大小下限為 9M，大小上限為 1023G。||
|`hw.sdCard.path`|**SDCard 映像路徑**&ndash;指定開發電腦上的 sd 記憶卡的磁碟分割映像檔的路徑與檔名。 例如，無法設定此路徑為**C:\sd\sdcard.img** Windows 上。||
|`hw.sensors.magnetic_field`|**磁場感應器**&ndash;判斷模擬的裝置是否支援磁場感應器。 磁場感應器 (也稱為磁力計) 會回報沿著三個感應器軸測量到的周遭地球磁場。 如果應用程式需要存取羅盤讀數，請啟用此設定。 例如，導航應用程式可能會使用此感應器來偵測使用者所正對的方向。|yes、no|
|`hw.sensors.orientation`|**方向的感應器**&ndash;決定是否模擬的裝置提供方向感應器值。 方向感應器會測量裝置圍繞三個實體軸 (x、y、z) 進行的旋轉角度。 請注意，方向感應器自 Android 2.2 (API 層級 8) 起即以被取代。|yes、no|
|`hw.sensors.proximity`|**鄰近感應器**&ndash;判斷模擬的裝置是否支援相近感應器。 此感應器會測量物件相對於裝置檢視螢幕的鄰近程度。 此感應器通常用來判斷話筒是否正舉到使用者的耳邊。|yes、no|
|`hw.sensors.temperature`|**溫度感應器**&ndash;判斷模擬的裝置是否支援溫度感應器。 此感應器可測量裝置攝氏溫度 (&deg;C)。|yes、no|
|`hw.touchScreen`|**觸控式螢幕支援**&ndash;判斷模擬的裝置是否支援觸控式螢幕。 觸控式螢幕是用來在螢幕上直接操控物件。|yes、no|
|`hw.trackBall`|**軌跡支援**&ndash;判斷模擬的裝置是否支援軌跡。|yes、no|
|`hw.useext4`|**EXT4 檔案系統支援**&ndash;判斷模擬的裝置是否使用 Linux EXT4 檔案系統磁碟分割。 由於檔案系統類型現在採用自動偵測，因此這個選項已被取代並忽略。|否|
|`kernel.newDeviceNaming`|**核心新的裝置命名**&ndash;用來指定核心是否需要新的裝置命名配置。 這通常與 Linux 3.10 核心或更新版本搭配使用。 如果設定為**自動偵測**，模擬器會自動偵測到核心是否需要新的裝置命名配置。|autodetect、yes、no|
|`kernel.parameters`|**核心參數**&ndash;指定 Linux 核心開機參數的字串。 此設定預設會留白。||
|`kernel.path`|**核心路徑**&ndash;指定 Linux 核心的路徑。 如果未指定這個路徑，模擬器會尋找核心 ranchu 模擬器系統目錄中。||
|`kernel.supportsYaffs2`|**YAFFS2 分割區支援**&ndash;決定核心是否支援 YAFFS2 (尚未另一個快閃檔案系統 2) 磁碟分割。 一般而言，這僅適用於 Linux 3.10 之前的核心。 如果設定為**自動偵測**模擬器會自動偵測是否在核心可以掛接 YAFFS2 檔案系統。|autodetect、yes、no|
|`skin.name`|**面板名稱** &ndash; Android 模擬器面板名稱。 面板是一個檔案集合，定義了模擬器顯示畫面的視覺和控制元素；它會描述 AVD 視窗在您開發電腦上看起來的樣子。 面板會描述螢幕大小、按鈕及整體設計，但不會影響您應用程式的操作。||
|`skin.path`|**面板路徑**&ndash;模擬器面板檔案所在的目錄路徑中指定 skin.name 此目錄包含 hardware.ini 配置檔案和顯示項目的面板的映像檔案。||
|`skin.dynamic`|**面板動態**&ndash;是否面板是動態的。 如果模擬器要根據指定的寬度和高度來建構指定大小的面板，則模擬器面板為動態面板。|否|

