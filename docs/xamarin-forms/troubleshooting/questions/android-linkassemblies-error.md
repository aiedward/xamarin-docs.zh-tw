---
標題：「Android 組建錯誤– LinkAssemblies 工作意外失敗」 ms。主題：疑難排解 ms-chap： xamarin assetid： EB3BE685-CB72-48E3-89D7-C845E76B9FA2。技術： xamarin-表單作者： davidbritch ms。作者： dabritch ms。日期：03/07/2019 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Android 組建錯誤– LinkAssemblies 工作意外失敗

`The "LinkAssemblies" task failed unexpectedly`當您建立使用表單的 Xamarin. Android 專案時，可能會看到錯誤訊息。 當連結器作用中（通常是在*發行*組建以縮減應用程式套件的大小時）時，就會發生這種情況;而且會發生這種情況，是因為 Android 目標並未更新為最新的架構。 （詳細資訊： [ Xamarin.Forms 支援的平臺](~/get-started/supported-platforms.md#android-platform-support)）

此問題的解決方法是確定您有最新的受支援 Android SDK 版本，並將**目標 Framework**設定為最新安裝的平臺。 此外，也建議您將 [**目標 Android 版本**] 設定為 [最新安裝的平臺]，並將 [**最低 android 版本**] 設為 API 19 或更高版本。 這會被視為支援的設定。

## <a name="setting-in-visual-studio-for-mac"></a>Visual Studio for Mac 中的設定

1. 以滑鼠右鍵按一下 Android 專案，然後選取功能表中的 [**選項**]。
2. 在 [**專案選項**] 對話方塊中，移至 **[組建 >] [一般**]。
3. 將 [**使用 Android 版本進行編譯：（目標 Framework）** ] 設定為最新安裝的平臺。
4. 在 [**專案選項**] 對話方塊中，移至 [**組建 > Android 應用程式**]。
5. 將 [**最低 android 版本**] 設定為 [API 層級19或更高]，並將 [**目標 android 版本**] 設為您在中選擇的最新已安裝平臺（3）。

## <a name="setting-in-visual-studio"></a>Visual Studio 中的設定

1. 以滑鼠右鍵按一下 Android 專案，然後選取功能表中的 [**屬性**]。
2. 在專案屬性中，移至 [**應用程式**]。
3. 將 [**使用 Android 版本進行編譯：（目標 Framework）** ] 設定為最新安裝的平臺。
4. 在 [專案屬性] 中，移至 [ **Android 資訊清單**]。
5. 將 [**最低 android 版本**] 設定為 [API 層級19或更高]，並將 [**目標 android 版本**] 設為您在中選擇的最新已安裝平臺（3）。

當您更新這些設定之後，請清除並重建您的專案，以確保您的變更會被挑選。
