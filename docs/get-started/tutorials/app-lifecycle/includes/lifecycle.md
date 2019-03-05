# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 啟動 Visual Studio，然後建立名為 **AppLifecycleTutorial** 的全新空白 Xamarin.Forms 應用程式。 確定該應用程式使用 .NET Standard 作為共用程式碼機制。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段，要求將解決方案命名為 **AppLifecycleTutorial**。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [方案總管] 中的 [AppLifecycleTutorial] 專案中，展開 **App.xaml**，然後按兩下 **App.xaml.cs** 將其開啟。 然後在 **App.xaml.cs** 中，更新 `OnStart`、`OnSleep` 和 `OnResume` 覆寫，如下所示：

    ```csharp
    protected override void OnStart()
    {
        Console.WriteLine("OnStart");
    }

    protected override void OnSleep()
    {
        Console.WriteLine("OnSleep");
    }

    protected override void OnResume()
    {
        Console.WriteLine("OnResume");
    }
    ```

    此程式碼會使用可指出何時叫用每個方法的 `Console.WriteLine` 陳述式來更新應用程式生命週期方法覆寫：

    - `OnStart` 方法會在應用程式啟動時叫用。
    - `OnSleep` 方法會在應用程式進入背景狀態時叫用。
    - `OnResume` 方法會在應用程式從背景繼續時叫用。

    > [!NOTE]
    > 沒有任何終止應用程式的方法。 在正常情況下，會從 `OnSleep` 方法進行應用程式終止。

1. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式。 啟動應用程式時會叫用 `OnStart` 方法，且 **OnStart** 會輸出到 Visual Studio 的 [輸出] 視窗：

    ```
    [Mono] Found as 'java_interop_jnienv_get_object_array_element'.
    OnStart
    [OpenGLRenderer] HWUI GL Pipeline
    ```

    當應用程式在背景執行時 (藉由點選 iOS 或 Android 上的 [首頁] 按鈕)，會叫用 `OnSleep` 方法：

    ```
    [EGL_emulation] eglMakeCurrent: 0x83ee2920: ver 3 0 (tinfo 0x8357eff0)
    OnSleep
    [Mono] Image addref System.Runtime.Serialization[0x83ee19c0] -> System.Runtime.Serialization.dll[0x83f57b00]: 2
    ```

    然後，當應用程式從背景繼續時 (在 iOS 上點選應用程式圖示，在 Android 上點選 [概觀] 按鈕，然後選取 AppLifecycleTutorial 應用程式)，會叫用 `OnResume` 方法：

    ```
    Thread finished: <Thread Pool> #5
    OnResume
    [EGL_emulation] eglMakeCurrent: 0x83ee2920: ver 3 0 (tinfo 0x8357eff0)
    ```

    > [!NOTE]
    > 這些程式碼區塊會顯示在 Android 上執行應用程式時的範例輸出。

    如需有關 Xamarin.Forms 應用程式生命週期的詳細資訊，請參閱 [Xamarin.Forms 應用程式生命週期](~/xamarin-forms/app-fundamentals/app-lifecycle.md)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 啟動 Visual Studio for Mac，然後建立名為 **AppLifecycleTutorial** 的全新空白 Xamarin.Forms 應用程式。 確定該應用程式使用 .NET Standard 作為共用程式碼機制。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段，要求將解決方案命名為 **AppLifecycleTutorial**。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [Solution Pad] 中的 [AppLifecycleTutorial] 專案中，展開 **App.xaml**，然後按兩下 **App.xaml.cs** 將其開啟。 然後在 **App.xaml.cs** 中，更新 `OnStart`、`OnSleep` 和 `OnResume` 覆寫，如下所示：

    ```csharp
    protected override void OnStart()
    {
        Console.WriteLine("OnStart");
    }

    protected override void OnSleep()
    {
        Console.WriteLine("OnSleep");
    }

    protected override void OnResume()
    {
        Console.WriteLine("OnResume");
    }
    ```

    此程式碼會使用可指出何時叫用每個方法的 `Console.WriteLine` 陳述式來更新應用程式生命週期方法覆寫：

    - `OnStart` 方法會在應用程式啟動時叫用。
    - `OnSleep` 方法會在應用程式進入背景狀態時叫用。
    - `OnResume` 方法會在應用程式從背景繼續時叫用。

    > [!NOTE]
    > 沒有任何終止應用程式的方法。 在正常情況下，會從 `OnSleep` 方法進行應用程式終止。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式。 啟動應用程式時會叫用 `OnStart` 方法，且 **OnStart** 會輸出到 Visual Studio for Mac 的 [應用程式輸出] 視窗：

    ```
    2019-02-11 12:05:23.164761+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskLoadEntitlements failed error=22 cs_flags=200, pid=4089
    2019-02-11 12:05:23.165297+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskCopyDebugDescription: AppLifecycleTuto[4089]/0#-1 LF=0
    2019-02-11 12:05:23.685430+0000 AppLifecycleTutorial.iOS[4089:361037] OnStart
    ```

    當應用程式在背景執行時 (藉由點選 iOS 或 Android 上的 [首頁] 按鈕)，會叫用 `OnSleep` 方法：

    ```
    2019-02-11 12:06:12.394301+0000 AppLifecycleTutorial.iOS[4089:361037] OnSleep
    Thread started: <Thread Pool> #3
    Thread started: <Thread Pool> #4
    Thread started: <Thread Pool> #5
    Thread started: <Thread Pool> #6
    2019-02-11 12:06:13.056968+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskLoadEntitlements failed error=22 cs_flags=200, pid=4089
    2019-02-11 12:06:13.057264+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskCopyDebugDescription: AppLifecycleTuto[4089]/0#-1 LF=0
    ```

    然後，當應用程式從背景繼續時 (在 iOS 上點選應用程式圖示，在 Android 上點選 [概觀] 按鈕，然後選取 AppLifecycleTutorial 應用程式)，會叫用 `OnResume` 方法：

    ```
    2019-02-11 12:07:10.321905+0000 AppLifecycleTutorial.iOS[4130:365695] SecTaskLoadEntitlements failed error=22 cs_flags=200, pid=4130
    2019-02-11 12:07:10.322557+0000 AppLifecycleTutorial.iOS[4130:365695] SecTaskCopyDebugDescription: AppLifecycleTuto[4130]/0#-1 LF=0
    2019-02-11 12:07:17.110938+0000 AppLifecycleTutorial.iOS[4130:365695] OnResume
    ```

    > [!NOTE]
    > 這些程式碼區塊會顯示在 iOS 上執行應用程式時的範例輸出。

    如需有關 Xamarin.Forms 應用程式生命週期的詳細資訊，請參閱 [Xamarin.Forms 應用程式生命週期](~/xamarin-forms/app-fundamentals/app-lifecycle.md)。
