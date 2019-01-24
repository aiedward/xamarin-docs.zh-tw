---
ms.assetid: EA2D979E-9151-4CE9-9289-13B6A979838B
title: 透過 Xamarin 使用 C/c + + 程式庫
description: 'Visual Studio for Mac 可用來建置和整合行動裝置應用程式中的跨平台 C/c + + 程式碼，適用於 Android 和 iOS，使用 Xamarin 和C#。 這篇文章說明如何設定和偵錯 c + + 專案中的 Xamarin 應用程式。'
author: mikeparker104
ms.author: miparker
ms.date: 12/17/20178
---
# <a name="use-cc-libraries-with-xamarin"></a>透過 Xamarin 使用 C/c + + 程式庫

## <a name="overview"></a>總覽

Xamarin 可讓開發人員使用 Visual Studio 中建立跨平台的原生行動應用程式。 一般而言，C#繫結會用來將現有的平台元件，開發人員公開。 不過，有些時才能使用現有的 Xamarin 應用程式需要程式碼基底的時候。 有時候小組沒有程式碼基底時間、 預算或大型、 通過完善測試，以及高度最佳化的連接埠的資源C#。

[Visual c + + 跨平台行動開發](https://docs.microsoft.com/visualstudio/cross-platform/visual-cpp-for-cross-platform-mobile-development) 可讓 C/c + + 和C#程式碼，以建立成相同的方案中，提供許多優點，包括統一的偵錯體驗的一部分。 Microsoft 已在這種方式，例如傳遞應用程式中使用 C/c + + 和 Xamarin [Hyperlapse Mobile](https://www.microsoft.com/p/hyperlapse-mobile/9wzdncrd1prw) 並 [Pix 相機](https://www.microsoft.com/microsoftpix)。

不過，在某些情況下沒有想 （或需求） 保留現有的 C/c + + 工具和程序中的位置，並保留分離的應用程式中，然後再將媒體櫃，如同它是類似於第三方元件程式庫程式碼。 在這些情況下，所面臨的挑戰不只會公開相關的成員，至C#，但管理程式庫做為相依性。 和當然，盡可能此程序自動化。  

這篇文章概述此案例的高階方法，並逐步解說一個簡單的範例。

## <a name="background"></a>背景

C/c + + 會視為跨平台的語言，但絕佳必須小心以確保來源的程式碼確實跨平台，使用只有 C/c + + 支援的所有目標的編譯器，並包含幾乎沒有任何的有條件地包含平台或編譯器特有的程式碼。

最後的程式碼必須編譯並順利執行，因此這全然共通性目標平台 （與編譯器） 上的所有目標平台上。 從編譯器稍有差異可能仍會發生問題，並徹底測試 （最好是自動） 上的每個目標平台變得越來越重要。  

## <a name="high-level-approach"></a>高階方法

下圖代表用來將 C/c + + 原始程式碼轉換為跨平台 Xamarin 程式庫共用透過 NuGet，然後在 Xamarin.Forms 應用程式中使用的四個階段方法。
 

![搭配 Xamarin 使用 C/c + + 的高階方法](images/cpp-steps.jpg)

4 個階段是：

1.  C/c + + 原始程式碼編譯成特定平台原生程式庫。
2.  包裝原生程式庫，與 Visual Studio 方案。
3.  封裝並推送 NuGet 套件，.NET 包裝函式。
4.  取用 NuGet 套件從 Xamarin 應用程式。

### <a name="stage-1-compiling-the-cc-source-code-into-platform-specific-native-libraries"></a>第 1 階段：C/c + + 原始程式碼編譯成特定平台原生程式庫

此階段的目標是要建立可呼叫的原生程式庫C#包裝函式。 這可能會或可能不是根據您的情況相關。 許多工具和程序，可以前往應謹記在這個常見的案例是超出本文的範圍。 重要考量是的維持 C/c + + 程式碼基底的任何原生包裝函式程式碼，足夠的單元測試、 與同步及建置自動化。 

逐步解說中的程式庫所隨附的殼層指令碼中使用 Visual Studio Code 來建立。 此逐步解說中的擴充的版本可在 [Mobile CAT GitHub 存放庫](https://github.com/xamarin/mobcat/blob/dev/samples/cppwithxamarin/README.md)的討論中深入探討範例的這個部分。 原生程式庫會被視為第三方相依性在此情況下不過這個階段說明內容。


為了簡單起見，本逐步解說的目標架構的子集。 適用於 iOS，它會使用 lipo 公用程式從個別的架構特定二進位檔，建立單一的 fat 二進位。 Android 會使用動態的二進位檔副檔名為.so 而 iOS 會使用靜態的 fat 二進位.a 副檔名為。 

### <a name="stage-2-wrapping-the-native-libraries-with-a-visual-studio-solution"></a>第 2 階段：包裝原生程式庫，與 Visual Studio 方案

下一個階段是包裝原生程式庫，以便輕鬆地從.NET 使用。 做法是使用 Visual Studio 方案包含四個專案。 共用的專案包含常見的程式碼。 每個 Xamarin.Android、 Xamarin.iOS，以及.NET Standard 為目標的專案可讓跨平台的方式參考的程式庫。

包裝函式會使用[誘導轉向技巧](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/)，' Paul Betts 所描述。 這不是唯一的方式，但它可以輕鬆參考程式庫，它就不需要明確地管理在使用應用程式本身中的平台特定實作。 這一輪基本上確保目標 (.NET Standard 的 Android、 iOS) 共用相同的命名空間、 組件名稱和類別結構。 因為 NuGet 一律會偏好的平台專屬程式庫，.NET Standard 版本永遠不會在執行階段使用。

大部分的工作，在此步驟中將著重在使用 P/Invoke 呼叫原生程式庫方法，和管理的基礎物件的參考。 目標是要公開給取用者的程式庫的功能，同時減少了解任何複雜程度。 Xamarin.Forms 開發人員不需要對 unmanaged 程式庫的內部運作方式的實用知識。 它應該覺得他們使用其他受管理C#程式庫。

最後，此階段的輸出是一組.NET 程式庫，其中每個目標，以及 nuspec 文件，其中包含以建置下一個步驟中的封裝所需的資訊。

**第 3 階段：封裝和推送 NuGet 套件，.NET 包裝函式**

第三個階段建立使用上一個步驟的組建成品的 NuGet 套件。 從這個步驟的結果是 NuGet 套件，可從 Xamarin 應用程式取用。 逐步解說會使用本機目錄，做為 NuGet 摘要。 在生產環境中，此步驟應該將套件發行至公用或私用 NuGet 摘要，並應該完全自動化。

**第 4 階段：使用 Xamarin.Forms 應用程式 NuGet 封裝**

最後一個步驟是參考，並使用 Xamarin.Forms 應用程式 NuGet 封裝。 這需要使用在上一個步驟中定義該摘要的 Visual Studio 中設定 NuGet 摘要。

摘要設定之後，封裝就需要參考每個專案中的跨平台 Xamarin.Forms 應用程式。 'Bait 交換器技巧' 會提供相同的介面，因此可以使用單一位置中所定義的程式碼呼叫原生程式庫功能。

包含原始程式碼儲存機制[的進一步閱讀清單](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin#wrapping-up)，其中包含有關如何設定私用 NuGet 摘要上 Azure DevOps，以及如何將套件推送至該摘要文章。 但需要較多的安裝時間，比本機目錄，這種類型是摘要的更好的小組開發環境。

## <a name="walk-through"></a>逐步解說

提供的步驟僅適用於 **Visual Studio for Mac**，但結構的運作方式 **Visual Studio 2017** 以及。

### <a name="prerequisites"></a>必要條件

若要跟著做，請將需要開發人員：

-   [NuGet 命令列 (CLI)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)

-   [*Visual Studio* *for Mac*](https://visualstudio.microsoft.com/downloads)

> [!NOTE]
> 作用 [**Apple 開發人員帳戶**](https://developer.apple.com/) ，才能將應用程式部署至 iPhone。

## <a name="creating-the-native-libraries-stage-1"></a>建立原生程式庫 (第 1 階段)

原生程式庫功能為基礎的範例 [逐步解說：建立和使用靜態程式庫 （c + +）](https://docs.microsoft.com/cpp/windows/walkthrough-creating-and-using-a-static-library-cpp?view=vs-2017)。

本逐步解說會略過第一個階段，建置原生程式庫，因為程式庫依現狀在此案例中的第三方相依性。 先行編譯的原生程式庫會隨附 [程式碼範例](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin)可以是[下載](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin/Sample/Artefacts) 直接。

### <a name="working-with-the-native-library"></a>使用原生程式庫

原始 *MathFuncsLib* 範例包含具有下列定義中稱為 MyMathFuncs 的單一類別： 

```cpp
namespace MathFuncs
{
    class MyMathFuncs
    {
    public:
        double Add(double a, double b);
        double Subtract(double a, double b);
        double Multiply(double a, double b);
        double Divide(double a, double b);
    };
}
```

其他的類別會定義包裝函式可讓.NET 取用者建立、 處置，並與其互動的基礎原生 MyMathFuncs 類別。

```cpp
#include "MyMathFuncs.h"
using namespace MathFuncs;

extern "C" {
    MyMathFuncs* CreateMyMathFuncsClass();
    void DisposeMyMathFuncsClass(MyMathFuncs* ptr);
    double MyMathFuncsAdd(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsSubtract(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsMultiply(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsDivide(MyMathFuncs *ptr, double a, double b);
}
```

它會使用這些包裝函式 [Xamarin](https://visualstudio.microsoft.com/xamarin/) 側邊。

## <a name="wrapping-the-native-library-stage-2"></a>包裝原生程式庫 (第 2 階段)

這個階段需要[先行編譯程式庫](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin/Sample/Artefacts)中所述 [前一節](https://docs.microsoft.com/xamarin/cross-platform/cpp/index)。

### <a name="creating-the-visual-studio-solution"></a>建立 Visual Studio 方案

1. 在  **Visual Studio for Mac**，按一下**新增專案**(從*歡迎使用 頁面*) 或**新方案**(從*檔案*  功能表)。
2. 從**新的專案** 視窗中，選擇**共用專案**(從*多平台 > 程式庫*)，然後按一下**下一步**。
3. 更新下列欄位，然後按一下 **建立**:

    - **專案名稱：** MathFuncs.Shared  
    - **解決方案名稱：** MathFuncs  
    - **位置：** 使用預設的儲存位置 （或挑選另一個方法）   
    - **建立方案目錄中的專案：** 將此設為已核取
4. 從**方案總管**，按兩下**MathFuncs.Shared**專案，然後瀏覽至**主要設定**。
5. 移除 **。共用**從**預設命名空間**因此設為**MathFuncs** ，然後按一下**確定**。
6. 開啟**MyClass.cs** （範本所建立），然後重新命名的類別和檔名**MyMathFuncsWrapper**並將變更的命名空間**MathFuncs**。
7. **CONTROL + 按一下**方案**MathFuncs**，然後選擇 **加入新的專案...** 從**新增**功能表。
8. 從**新的專案** 視窗中，選擇 **.NET 標準程式庫**(從*多平台 > 程式庫*)，然後按一下**下一步**。
9. 選擇 **.NET Standard 2.0** ，然後按一下**下一步**。
10. 更新下列欄位，然後按一下 **建立**:

    - **專案名稱：** MathFuncs.Standard  
    - **位置：** 使用相同儲存為共用專案的位置   

11. 從**方案總管**，按兩下**MathFuncs.Standard**專案。
12. 瀏覽至**主要設定**，然後更新**預設命名空間**來**MathFuncs**。
13. 瀏覽至**輸出**設定，然後更新**組件名稱**來**MathFuncs**然後按一下**確定**。
14. 瀏覽至**編譯器**設定，變更**組態**來**版本**，設定**偵錯資訊**到**符號只**。
15. 刪除**Class1.cs/Getting 啟動**從專案 （如果其中一個已成為範本的一部份）。
16. **CONTROL + 按一下**專案**相依性/References**資料夾，然後選擇**編輯參考**。
17. 選取  **MathFuncs.Shared**從**專案**索引標籤，然後按一下**確定**。
18. 重複步驟 7-17 （略過步驟 9） 使用下列設定：

    | **專案名稱**  | **TEMPLATE NAME**   | **新的 [專案] 功能表**   |
    |-------------------| --------------------| -----------------------|
    | MathFuncs.Android | 類別庫       | Android > 程式庫      |
    | MathFuncs.iOS     | 繫結程式庫     | iOS > 程式庫          |

19. 從**方案總管**，按兩下**MathFuncs.Android**專案，然後瀏覽至**編譯器**設定。

20. 具有**組態**設為**偵錯**，編輯**定義符號**包含**Android;**。

21. 變更**組態**要**Release**，然後編輯**定義符號**成也包括**Android;**。

22. 重複步驟 19-20 **MathFuncs.iOS**、 編輯**定義的符號**包含**iOS;** 而不是**Android;** 這兩種情況。

23. 在 建置方案**發行**組態 (**控制 + COMMAND + B**) 並驗證所有三個輸出組件 (Android、 iOS、.NET Standard) （在個別專案的 bin 資料夾） 共用相同名稱**MathFuncs.dll**。

在這個階段中，方案應該有三個目標，因此適用於 Android、 iOS 和.NET Standard 和共用的專案參考的每個三個目標的其中一個。 這些應該設定為使用相同的預設命名空間和輸出組件具有相同名稱。 這是必要的先前所述的 '誘導轉向' 方法。

### <a name="adding-the-native-libraries"></a>新增原生程式庫

Android 與 iOS 之間，將原生程式庫新增至包裝函式解決方案的程序略有不同。

#### <a name="native-references-for-mathfuncsandroid"></a>MathFuncs.Android 原生參考

1. **CONTROL + 按一下**上**MathFuncs.Android**專案，然後選擇**新資料夾**從**新增**功能表將它命名為**libs**.

2. 每個 **ABI** （應用程式二進位介面）， **CONTROL + 按一下** 上 **libs** 資料夾，然後選擇  **新的資料夾** 從 **新增** 功能表上，將它命名為之後個別 **ABI**。 在此情況下：

    - arm64-v8a
    - armeabi-v7a
    - x86
    - x86_64  

    > [!NOTE]
    > 如需更詳細的概觀，請參閱 [架構和 Cpu](https://developer.android.com/ndk/guides/arch) 相關的主題 [NDK 開發人員指南](https://developer.android.com/ndk/guides/)，特別是處理區段 [應用程式套件中的原生程式碼](https://developer.android.com/ndk/guides/abis#native-code-in-app-packages).

3. 確認資料夾結構：  

    ```
    - lib
        - arm64-v8a
        - armeabi-v7a
        - x86
        - x86_64
    ```

4. 新增對應 **.so**程式庫的每個**ABI**資料夾為基礎的下列對應：

    **arm64-v8a:** libs/Android/arm64

    **armeabi-v7a:** libs/Android/arm  

    **x86:** libs/Android/x86

    **x86_64:** libs/Android/x86_64

    > [!NOTE]
    > 若要加入檔案， **CONTROL + 按一下**在代表個別的資料夾**ABI**，然後選擇 **新增檔案...** 從**新增**功能表。 選擇適當的程式庫 (從**PrecompiledLibs** directory) 然後按一下**開放**，然後按一下  **確定**保留預設選項*複製檔案的目錄*。

5. 針對每個 **.so**檔案**CONTROL + 按一下**然後選擇**EmbeddedNativeLibrary**選項**建置動作**功能表。

現在 **libs** 資料夾應該會出現，如下所示：

```bash
- lib
    - arm64-v8a
        - libMathFuncs.so
    - armeabi-v7a
        - libMathFuncs.so
    - x86 
        - libMathFuncs.so
    - x86_64 
        - libMathFuncs.so
```

#### <a name="native-references-for-mathfuncsios"></a>MathFuncs.iOS 原生參考

1. **CONTROL + 按一下**上**MathFuncs.iOS**專案，然後選擇**加入原生參考**從**新增**功能表。 
2. 選擇**libMathFuncs.a**程式庫 (從程式庫/ios 底下**PrecompiledLibs**目錄) 然後按一下 **開啟** 
3. **CONTROL + 按一下**上**libMathFuncs**檔案 (內**原生參考**資料夾，然後選擇**屬性**功能表選項  
4. 設定**原生參考**屬性，如此就會檢查 （顯示勾號圖示） 中**屬性**板：
        
    - 強制載入
    - 是 c + +
    - 智慧連結 

    > [!NOTE]
    > 使用繫結程式庫專案類型，連同[原生參考](https://docs.microsoft.com/xamarin/cross-platform/macios/native-references)內嵌靜態程式庫，並讓它能夠自動使用 Xamarin.iOS 應用程式參考它 （即使它是透過 NuGet 套件包含在內） 的連結。 

5. 開啟**ApiDefinition.cs**，刪除樣板化標記為註解的程式碼 (並且只留下**MathFuncs**命名空間)，然後執行相同的步驟，如**Structs.cs** 

    > [!NOTE]
    > 繫結程式庫專案需要這些檔案 (使用*ObjCBindingApiDefinition*並*ObjCBindingCoreSource*建置動作) 才能建置。 不過，我們會撰寫程式碼，呼叫我們的原生程式庫，這些檔案，您可以使用標準的 P/Invoke 的 Android 和 iOS 的程式庫目標之間共用的方式之外。

### <a name="writing-the-managed-library-code"></a>撰寫 managed 程式庫程式碼

現在，撰寫C#程式碼呼叫原生程式庫。 目標是要隱藏任何基礎的複雜程度。 取用者應該不需要任何原生程式庫內部項目或 P/Invoke 概念的知識。  

#### <a name="creating-a-safehandle"></a>建立的 SafeHandle

1. **CONTROL + 按一下**上**MathFuncs.Shared**專案，然後選擇**新增檔案...** 從**新增**功能表。 
2. 選擇**空類別**從**新的檔案**視窗中，其命名為**MyMathFuncsSafeHandle** ，然後按一下 **新增**
3. 實作**MyMathFuncsSafeHandle**類別：

    ```csharp
    using System;
    using Microsoft.Win32.SafeHandles;

    namespace MathFuncs
    {
        internal class MyMathFuncsSafeHandle : SafeHandleZeroOrMinusOneIsInvalid
        {
            public MyMathFuncsSafeHandle() : base(true) { }

            public IntPtr Ptr => this.handle;

            protected override bool ReleaseHandle()
            {
                // TODO: Release the handle here
                return true;
            }
        }
    }
    ```

    > [!NOTE]
    > A [SafeHandle](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.safehandle?view=netframework-4.7.2)是慣用的方法來使用 managed 程式碼中的 unmanaged 資源。 抽象化許多重要的最終處理和物件生命週期相關的未定案程式碼。 這個控制代碼的擁有者接著可以將它像任何其他 managed 資源，並不會實作完整[可處置的模式](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose)。 

#### <a name="creating-the-internal-wrapper-class"></a>建立內部包裝函式類別

1. 開啟**MyMathFuncsWrapper.cs**，將它變更為內部的靜態類別

    ```csharp
    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
        }
    }
    ```

2. 在相同的檔案中，請將下列的條件陳述式加入類別：

    ```csharp
    #if Android
        const string DllName = "libMathFuncs.so";
    #else
        const string DllName = "__Internal";
    #endif
    ```

    > [!NOTE]
    > 這會設定**DllName**常數值會根據是否要針對建置程式庫**Android**或是**iOS**。 這是為了解決不同的命名慣例，由每個個別的平台，但在此情況下使用的程式庫的類型。 Android 會使用動態程式庫，因此必須包括副檔名的檔案名稱。 適用於 iOS、 '*__Internal*' 是必要的因為我們要使用的靜態程式庫。

3. 將參考加入**System.Runtime.InteropServices**頂端**MyMathFuncsWrapper.cs**檔案

    ```csharp
    using System.Runtime.InteropServices;
    ```

4. 新增包裝函式方法來處理建立和處置**MyMathFuncs**類別：

    ```csharp
    [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
    internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

    [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
    internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);
    ```

    > [!NOTE]
    > 我們會傳入我們常數**DllName**要**DllImport**屬性連同**EntryPoint**明確告訴.NET 執行階段呼叫的函式的名稱在該程式庫。 技術上來說，我們不需要提供**EntryPoint**值，如果我們的受管理的方法名稱與未受管理的密碼完全相同。 如果未提供其中一個項目，則受管理的方法名稱會用作**EntryPoint**改。 不過，最好是明確的。  

5. 新增包裝函式方法，讓我們將使用**MyMathFuncs**類別使用下列程式碼：

    ```csharp
    [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
    internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
    internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
    internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
    internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
    ```

    > [!NOTE]
    > 我們在此範例中的參數使用簡單的型別。 因為封送處理是位元複製在此情況下它需要我們採取任何額外的工作。 也請注意，使用**MyMathFuncsSafeHandle**類別，而不是標準**IntPtr**。 **IntPtr**會自動對應到**SafeHandle**封送處理程序的一部分。

6. 確認已完成**MyMathFuncsWrapper**類別會顯示為如下：

    ```csharp
    using System.Runtime.InteropServices;

    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
            #if Android
                const string DllName = "libMathFuncs.so";
            #else
                const string DllName = "__Internal";
            #endif

            [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
            internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

            [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
            internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);

            [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
            internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
            internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
            internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
            internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
        }
    }
    ```

#### <a name="completing-the-mymathfuncssafehandle-class"></a>完成 MyMathFuncsSafeHandle 類別
1. 開啟**MyMathFuncsSafeHandle**類別中，瀏覽至預留位置**TODO**註解內**ReleaseHandle**方法：
    ```csharp
    // TODO: Release the handle here
    ```
2. 取代**TODO**列：

    ```csharp
    MyMathFuncsWrapper.DisposeMyMathFuncs(this);
    ```

#### <a name="writing-the-mymathfuncs-class"></a>撰寫 MyMathFuncs 類別

現在，包裝函式已完成，建立 MyMathFuncs 類別會管理 unmanaged c + + MyMathFuncs 物件的參考。  

1. **CONTROL + 按一下**上**MathFuncs.Shared**專案，然後選擇**新增檔案...** 從**新增**功能表。 
2. 選擇**空類別**從**新的檔案**視窗中，其命名為**MyMathFuncs** ，然後按一下 **新增**
3. 新增下列成員來**MyMathFuncs**類別：

    ```csharp
    readonly MyMathFuncsSafeHandle handle;
    ```

4. 實作類別的建構函式，因此它會建立並儲存於原生控制代碼**MyMathFuncs**物件具現化類別時：

    ```csharp
    public MyMathFuncs()
    {
        handle = MyMathFuncsWrapper.CreateMyMathFuncs();
    }
    ```

5. 實作**IDisposable**介面使用下列程式碼：

    ```csharp
    public class MyMathFuncs : IDisposable
    {
        ...

        protected virtual void Dispose(bool disposing)
        {
            if (handle != null && !handle.IsInvalid)
                handle.Dispose();
        }

        public void Dispose()
        {
            Dispose(true);
            GC.SuppressFinalize(this);
        }

        // ...
    }
    ```

6. 實作**MyMathFuncs**方法使用**MyMathFuncsWrapper**類別來執行幕後實際的工作基礎 unmanaged 物件傳入我們儲存的指標。 程式碼應該如下所示：

    ```csharp
    public double Add(double a, double b)
    {
        return MyMathFuncsWrapper.Add(handle, a, b);
    }

    public double Subtract(double a, double b)
    {
        return MyMathFuncsWrapper.Subtract(handle, a, b);
    }

    public double Multiply(double a, double b)
    {
        return MyMathFuncsWrapper.Multiply(handle, a, b);
    }

    public double Divide(double a, double b)
    {
        return MyMathFuncsWrapper.Divide(handle, a, b);
    }
    ```

#### <a name="creating-the-nuspec"></a>建立 nuspec

解決方案需要有封裝，並透過 NuGet 散發程式庫，才能 **nuspec**檔案。 這會識別其中一個產生的組件會包含每個支援平台。

1.  **CONTROL + 按一下** 方案 **MathFuncs**，然後選擇 **新增方案資料夾**從 **新增** 功能表將它命名為 **SolutionItems**。
2.  **CONTROL + 按一下** 上 **SolutionItems** 資料夾，然後選擇  **新檔...** 從 **新增** 功能表。
3.  選擇 **空白的 XML 檔案** 從 **新檔案** 視窗中，其命名為 **MathFuncs.nuspec** ，然後按一下 **新**。
4.  更新 **MathFuncs.nuspec** 使用基本封裝的中繼資料顯示給**NuGet**取用者。 例如: 


    ```xml
    <?xml version="1.0"?>
    <package>
        <metadata>
            <id>MathFuncs</id>
            <version>$version$</version>
            <authors>Microsoft Mobile Customer Advisory Team</authors>
            <description>Sample C++ Wrapper Library</description>
            <requireLicenseAcceptance>false</requireLicenseAcceptance>
            <copyright>Copyright 2018</copyright>
        </metadata>
    </package>
    ```

    > [!NOTE]
    >  請參閱[nuspec 參考](https://docs.microsoft.com/nuget/reference/nuspec)文件，如需使用此資訊清單結構描述的詳細資料。

5. 新增`<files>`項目做為子系`<package>`項目 (正下方`<metadata>`)，識別每個檔案有不同`<file>`項目：

    ```xml
    <files>

        <!-- Android -->

        <!-- iOS -->        

        <!-- netstandard2.0 -->

    </files>
    ```

    > [!NOTE]
    > 當套件安裝至專案，並有多個相同的名稱所指定的組件，NuGet 會有效地選擇最特定到指定的平台的組件。

6. 新增`<file>`項目**Android**組件：

    ```xml
    <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
    <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
    ```

7. 新增`<file>`項目**iOS**組件：

    ```xml
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
    ```

8. 新增`<file>`項目**netstandard2.0**組件：

    ```xml
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />
    ```

9. 請確認**nuspec**資訊清單：

    ```xml
    <?xml version="1.0"?>
    <package>
    <metadata>
        <id>MathFuncs</id>
        <version>$version$</version>
        <authors>Microsoft Mobile Customer Advisory Team</authors>
        <description>Sample C++ Wrapper Library</description>
        <requireLicenseAcceptance>false</requireLicenseAcceptance>
        <copyright>Copyright 2018</copyright>
    </metadata>
    <files>
    
        <!-- Android -->
        <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
        <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
        
        <!-- iOS -->
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
        
        <!-- netstandard2.0 -->
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />

    </files>
    </package>
    ```

    > [!NOTE]
    > 此檔案會指定組件的輸出路徑，從 **Release** 建置，因此請務必建立使用該設定的解決方案。

此時，解決方案包含 3 個.NET 組件和支援 **nuspec** 資訊清單。

## <a name="distributing-the-net-wrapper-with-nuget"></a>散發與 NuGet 的.NET 包裝函式

下一個步驟是封裝及發佈 NuGet 套件，因此它可能可輕鬆取用應用程式和管理為相依性。 包裝和耗用量可能所有內無法完成的單一解決方案，但散發類別庫 NuGet 協助分離，而且可讓您透過我們管理這些程式碼基底獨立。

### <a name="preparing-a-local-packages-directory"></a>準備本機封裝的目錄

NuGet 摘要的最簡單形式是本機目錄：

1.  在  **Finder**，瀏覽至方便存取的目錄。 例如， **/使用者**。
2.  選擇 **新的資料夾** 從 **檔案** 功能表上，提供有意義的名稱，例如 **本機 nuget 摘要**。

### <a name="creating-the-package"></a>建立封裝

1.  設定 **組建組態** 要 **發行**，並執行組建，使用 **COMMAND + B**。
2.  開啟 **終端機**並將目錄變更為包含的資料夾 **nuspec** 檔案。
3.  在  **終端機**，執行 **nuget 套件** 命令，並指定 **nuspec** 檔案， **版本**  (例如，1.0.0)，而 **OutputDirectory** 使用中建立的資料夾 [上一個步驟](https://docs.microsoft.com/xamarin/cross-platform/cpp/index#creating-a-local-nuget-feed)，也就是 **本機 nuget 摘要**。 例如: 

    ```bash
    nuget pack MathFuncs.nuspec -Version 1.0.0 -OutputDirectory ~/local-nuget-feed
    ```

4. **確認**所 **MathFuncs.1.0.0.nupkg** 內未曾建立 **本機 nuget 摘要**目錄。

### <a name="optional-using-a-private-nuget-feed-with-azure-devops"></a>[選用]使用私用 NuGet 摘要，含有 Azure DevOps

更穩固的技術所述 [開始使用 Azure DevOps 中的 NuGet 套件](https://docs.microsoft.com/azure/devops/artifacts/get-started-nuget?view=vsts&tabs=new-nav#publish-a-package)，但會示範如何建立私用摘要和推送套件 （在上一個步驟中產生） 到該摘要。

它非常適合用來完全自動化，並不會使用此工作流程很 [Azure 管線](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts)。 如需詳細資訊，請參閱 <<c0> [ 開始使用 Azure 管線](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=vsts)。

## <a name="consuming-the-net-wrapper-from-a-xamarinforms-app"></a>使用 Xamarin.Forms 應用程式從.NET 包裝函式
若要完成本逐步解說，建立 **Xamarin.Forms** 只取用該封裝的應用程式發行至本機 **NuGet** 摘要。

### <a name="creating-the-xamarinforms-project"></a>建立**Xamarin.Forms**專案

1. 開啟的新執行個體 **Visual Studio for Mac**。 這可以從 **終端機**:

    ```bash
    open -n -a "Visual Studio"
    ```

2. 在  **Visual Studio for Mac**，按一下**新增專案**(從*歡迎使用 頁面*) 或**新方案**(從*檔案*  功能表)。
3. 從**新的專案** 視窗中，選擇**空白的 Forms App** (從*多平台 > 應用程式*)，然後按一下**下一步**。
4. 更新下列欄位，然後按一下 [**下一步]**:

    - **應用程式名稱：** MathFuncsApp.
    - **組織識別碼：** 例如，使用了反向的命名空間_com {your_org}_。
    - **目標平台：** 使用預設值 （Android 和 iOS 的目標）。
    - **共用的程式碼：** 設定為.NET Standard （「 媒體櫃共用 」 方案是可行的但本逐步解說的範圍之外）。

5. 更新下列欄位，然後按一下 **建立**:

    - **專案名稱：** MathFuncsApp.
    - **解決方案名稱：** MathFuncsApp.  
    - **位置：** 使用預設的儲存位置 （或挑選另一個方法）。

6. 在 [**方案總管] 中**， **CONTROL + 按一下**目標上 (**MathFuncsApp.Android**或**MathFuncs.iOS**) 進行初始測試，然後選擇**設定為啟始專案**。
7. 選擇慣用**裝置**或是**模擬器**/**模擬器**。 
8. 執行此方案 (**命令 + RETURN**) 來驗證樣板化**Xamarin.Forms**專案隨即建置並執行沒問題。 

    > [!NOTE]
    > **iOS** （特別是模擬器） 通常會有最快的建置/部署時間。

### <a name="adding-the-local-nuget-feed-to-the-nuget-configuration"></a>新增本機 NuGet 摘要，NuGet 組態

1. 在  **Visual Studio**，選擇**喜好設定**(從**Visual Studio**功能表)。
2. 選擇**來源**下**NuGet**區段，然後按一下**新增**。
3. 更新下列欄位，然後按一下 **加入來源**:

    - **名稱：** 提供有意義的名稱，例如，本機套件。  
    - **位置：** 指定**本機 nuget 摘要**資料夾中建立[前一步驟](#preparing-a-local-packages-directory)。

    > [!NOTE]
    > 在此情況下則不需要指定 **使用者名稱** 並 **密碼**。 

4. 按一下 [確定 **Deploying Office Solutions**]。

### <a name="referencing-the-package"></a>參考套件

針對每個專案重複下列步驟 (**MathFuncsApp**， **MathFuncsApp.Android**，並**MathFuncsApp.iOS**)。

1. **CONTROL + 按一下**專案，然後選擇 **新增 NuGet 套件...** 從**新增**功能表。
2. 搜尋**MathFuncs**。 
3. 確認**版本**的封裝**1.0.0**和其他詳細資料會出現如預期般這類**標題**並**描述**，也就是*MathFuncs*並*範例 c + + 包裝函式程式庫*。 
4. 選取  **MathFuncs**套件，然後按一下 **加入封裝**。

### <a name="using-the-library-functions"></a>使用程式庫函式

現在，使用的參考 **MathFuncs** 封裝中的每個專案，而函式可用於C#程式碼。

1.  開啟 **MainPage.xaml.cs** 內在 **MathFuncsApp** 常見 **Xamarin.Forms**專案 (參考這兩個 **MathFuncsApp.Android** 並 **MathFuncsApp.iOS**)。
2.  新增 **使用** 陳述式 **System.Diagnostics** 並 **MathFuncs** 在檔案頂端：

    ```csharp
    using System.Diagnostics;
    using MathFuncs;
    ```

3. 宣告的執行個體`MyMathFuncs`類別的頂端`MainPage`類別：

    ```csharp
    MyMathFuncs myMathFuncs;
    ```

4. 覆寫`OnAppearing`並`OnDisappearing`方法從`ContentPage`基底類別：

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
    }
    ```

5. 更新`OnAppearing`方法來初始化`myMathFuncs`先前宣告的變數：

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
        myMathFuncs = new MyMathFuncs();
    }
    ```

6. 更新`OnDisappearing`方法來呼叫`Dispose`方法`myMathFuncs`:

    ```csharp
    protected override void OnDisappearing()
    {
        base.OnAppearing();
        myMathFuncs.Dispose();
    }
    ```

7. 實作私用方法，稱為**TestMathFuncs** ，如下所示：

    ```csharp
    private void TestMathFuncs()
    {
        var numberA = 1;
        var numberB = 2;

        // Test Add function
        var addResult = myMathFuncs.Add(numberA, numberB);

        // Test Subtract function
        var subtractResult = myMathFuncs.Subtract(numberA, numberB);

        // Test Multiply function
        var multiplyResult = myMathFuncs.Multiply(numberA, numberB);

        // Test Divide function
        var divideResult = myMathFuncs.Divide(numberA, numberB);

        // Output results
        Debug.WriteLine($"{numberA} + {numberB} = {addResult}");
        Debug.WriteLine($"{numberA} - {numberB} = {subtractResult}");
        Debug.WriteLine($"{numberA} * {numberB} = {multiplyResult}");
        Debug.WriteLine($"{numberA} / {numberB} = {divideResult}");
    }
    ```

8. 最後，呼叫`TestMathFuncs`結尾的`OnAppearing`方法：

    ```csharp
    TestMathFuncs();
    ```

9. 每個目標平台上執行應用程式，並驗證中的輸出**應用程式輸出**板看起來像這樣：

    ```csharp
    1 + 2 = 3
    1 - 2 = -1
    1 * 2 = 2
    1 / 2 = 0.5
    ```

    > [!NOTE]
    > 如果您遇到 '*DLLNotFoundException*' 時測試以 Android 或建置錯誤，在 iOS 上，請務必檢查裝置/模擬器/模擬器所使用的 CPU 架構是否與我們選擇子集相容支援。 

## <a name="summary"></a>總結

這篇文章說明如何建立 Xamarin.Forms 應用程式會透過一般.NET 包裝函式透過 NuGet 套件散發的原生程式庫。 在本逐步解說所提供的範例是刻意要更輕鬆地示範的方法非常簡單。 實際的應用程式必須處理的例外狀況處理等的複雜性、 回呼、 封送處理的更複雜的型別，和其他相依程式庫連結。 一項重要考量是用 c + + 程式碼的發展是協調，並與包裝函式和用戶端應用程式進行同步處理的程序。 此程序根據一個或多個這些疑慮是單一小組的責任而有所不同。 無論如何，自動化是實質的好處。 以下是一些資源，提供進一步閱讀有關的重要概念，以及相關的下載項目。 

### <a name="downloads"></a>下載

- [NuGet 命令列 (CLI) 工具](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)
- [Visual Studio](https://visualstudio.microsoft.com/vs)

### <a name="examples"></a>範例

- [Hyperlapse 使用 c + + 跨平台行動開發](https://blogs.msdn.microsoft.com/vcblog/2015/06/26/hyperlapse-cross-platform-mobile-development-with-visual-c-and-xamarin/)
- [Microsoft Pix （c + + 和 Xamarin）](https://blog.xamarin.com/microsoft-research-ships-intelligent-apps-with-the-power-of-c-and-ai/)
- [Mono San Angeles 範例的連接埠](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)

### <a name="further-reading"></a>進一步閱讀

[這篇文章的內容與相關的文章](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin#wrapping-up)
