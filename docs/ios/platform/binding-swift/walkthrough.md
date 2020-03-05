---
title: 逐步解說：系結 iOS Swift 程式庫
description: 本文提供針對現有 Swift 架構（Gigya）建立 Xamarin iOS 系結的實際操作逐步解說。 其中涵蓋的主題包括編譯 Swift 架構、將其系結，以及在 Xamarin iOS 應用程式中使用系結。
ms.prod: xamarin
ms.assetid: B563ADE9-D0E3-4BC3-A288-66D2296BE706
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: b650f86a1bba62d5db7463875de3398db9c33842
ms.sourcegitcommit: b751605179bef8eee2df92cb484011a7dceb6fda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "78292555"
---
# <a name="walkthrough-bind-an-ios-swift-library"></a>逐步解說：系結 iOS Swift 程式庫

Xamarin 可讓行動開發人員使用 Visual Studio 和， C#建立跨平臺的原生行動體驗。 您可以使用現成的 iOS 平臺 SDK 元件。 但在許多情況下，您也會想要使用針對該平臺開發的協力廠商 Sdk，Xamarin 可讓您透過系結執行此動作。 若要將協力廠商的目標-C 架構併入您的 Xamarin iOS 應用程式，您必須先為它建立 Xamarin. iOS 系結，才能在應用程式中使用它。

IOS 平臺以及其原生語言和工具會不斷演進，而 Swift 則是目前 iOS 開發環境中最動態的其中一個區域。 有一些協力廠商 Sdk 已從目標-C 遷移至 Swift，而這會帶來新的挑戰。 雖然 Swift 系結程式類似于目標-C，但它需要額外的步驟和設定設定，才能成功建立並執行 AppStore 可接受的 Xamarin iOS 應用程式。

本檔的目的是要說明解決此案例的高階方法，並提供使用簡單範例的詳細逐步指南。

## <a name="background"></a>背景

Swift 一開始是由 Apple 在2014中引進，而且現在已在協力廠商架構的採用版本5.1 上快速成長。 您有幾個選項可以系結 Swift 架構，而本檔概述使用以目標 C 產生的介面標頭的方法。 建立架構時，Xcode 工具會自動建立標頭，並使用它作為從受管理世界到 Swift 世界的通訊方式。

## <a name="prerequisites"></a>Prerequisites

為了完成此逐步解說，您需要：

- [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [Objective Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/get-started#installing-objective-sharpie)
- [APPCENTER CLI](https://docs.microsoft.com/appcenter/test-cloud/) （選擇性）

## <a name="build-a-native-library"></a>建立原生程式庫

第一個步驟是建立原生 Swift 架構，並啟用目標-C 標頭。 架構通常是由協力廠商開發人員提供，並將標頭內嵌在下列目錄的封裝中： **\<FrameworkName >. framework/header/\<FrameworkName >-Swift. h**。

此標頭會公開公用介面，其將用來建立 Xamarin. iOS 系結中繼資料C# ，並產生公開 Swift 架構成員的類別。 如果標頭不存在或具有不完整的公用介面（例如，您看不到類別/成員），您有兩個選項：

- 更新 Swift 原始程式碼以產生標頭，並以 `@objc` 屬性標記必要的成員
- 建立 proxy 架構，您可以在其中控制公用介面，並將所有對基礎架構的呼叫進行 proxy

在本教學課程中，會說明第二種方法，因為它的相依性較不一定是協力廠商原始程式碼的相依性。 另一個避免第一種方法的原因是，需要額外的工作才能支援未來的架構變更。 當您開始將變更新增至協力廠商原始程式碼之後，您必須負責支援這些變更，並可能會在每次更新時合併它們。

例如，在本教學課程中，會建立[Gigya SWIFT SDK](https://developers.gigya.com/display/GD/Swift+SDK)的系結：

1. 開啟 Xcode 並建立新的 Swift 架構，這會是在 Xamarin iOS 程式碼和協力廠商 Swift 架構之間的 proxy。 按一下 [檔案] **> [新增 > 專案**]，然後依照 wizard 步驟執行：

    ![xcode 建立架構專案](walkthrough-images/xcode-create-framework-project.png)

    ![xcode 名稱架構專案](walkthrough-images/xcode-name-framework-project.png)

1. 從開發人員網站下載[Gigya 架構](https://developers.gigya.com/display/GD/Swift+SDK)，並將它解壓縮。 在撰寫本文時，最新版本是[Gigya SWIFT SDK 1.0.9](https://downloads.gigya.com/predownload?fileName=Swift-Core-framework-1.0.9.zip)

1. 從 [專案檔] explorer 中選取**SwiftFrameworkProxy** ，然後選取 [一般] 索引標籤

1. 將**Gigya**套件拖放到 [一般] 索引標籤下的 [Xcode 架構和程式庫] 清單中，在新增架構時，勾選 [**需要時複製專案**] 選項：

    ![xcode 複製架構](walkthrough-images/xcode-copy-framework.png)

    確認 Swift 架構已新增至專案，否則將無法使用下列選項。

1. 請確定已選取 [**不要內嵌**] 選項，稍後會以手動方式進行控制：

    [![xcode donotembed 選項](walkthrough-images/xcode-donotembed-option.png)](walkthrough-images/xcode-donotembed-option.png#lightbox)

1. 確定 [組建設定] 選項**一律內嵌 Swift 標準程式庫**，其中包含架構為的 swift 程式庫設定為 [否]。 稍後會以手動方式控制它，而 Swift dylib 來處理會包含在最終的套件中：

    [![xcode 一律內嵌 false 選項](walkthrough-images/xcode-alwaysembedfalse-option.png)](walkthrough-images/xcode-alwaysembedfalse-option.png#lightbox)

1. 確定 [**啟用 Bitcode** ] 選項設定為 [**否**]。 從現在起，Xamarin 不會包含 Bitcode，而 Apple 會要求所有程式庫都支援相同的架構：

    [![xcode enable bitcode false 選項](walkthrough-images/xcode-enablebitcodefalse-option.png)](walkthrough-images/xcode-enablebitcodefalse-option.png#lightbox)

    您可以對架構執行下列終端機命令，以確認產生的架構已停用 Bitcode 選項：

    ```bash
    otool -l SwiftFrameworkProxy.framework/SwiftFrameworkProxy | grep __LLVM
    ```

    如果您想要檢查特定設定的專案設定，則輸出應該是空的。

1. 請確定已啟用 [**目標-C 產生的介面標頭名稱**] 選項，並指定標頭名稱。 預設名稱為 **\<FrameworkName >-Swift. h**：

    [![xcode objectice-c 標頭已啟用選項](walkthrough-images/xcode-objcheaderenabled-option.png)](walkthrough-images/xcode-objcheaderenabled-option.png#lightbox)

1. 公開所需的方法，並使用 `@objc` 屬性將其標示為，並套用下列定義的其他規則。 如果您在不使用此步驟的情況下建立架構，則產生的目標 C 標頭將會是空的，而 Xamarin。 iOS 將無法存取 Swift 架構成員。 藉由建立新的 Swift 檔案**SwiftFrameworkProxy**並定義下列程式碼，來公開基礎 GIGYA swift SDK 的初始化邏輯：

    ```swift
    import Foundation
    import UIKit
    import Gigya

    @objc(SwiftFrameworkProxy)
    public class SwiftFrameworkProxy : NSObject {

        @objc
        public func initFor(apiKey: String) -> String {
            Gigya.sharedInstance().initFor(apiKey: apiKey)
            let gigyaDomain = Gigya.sharedInstance().config.apiDomain
            let result = "Gigya initialized with domain: \(gigyaDomain)"
            return result
        }
    }
    ```

    上述程式碼有幾個重要的注意事項：

    - 從原始的協力廠商 Gigya SDK 匯入 Gigya 模組，現在可以存取架構的任何成員。
    - 使用指定名稱的 `@objc` 屬性來標記 SwiftFrameworkProxy 類別，否則會產生唯一的無法讀取名稱，例如 `_TtC19SwiftFrameworkProxy19SwiftFrameworkProxy`。 型別名稱應該清楚地定義，因為它會在稍後用它的名稱來使用。
    - 從 `NSObject`繼承 proxy 類別，否則不會在目標-C 標頭檔中產生。
    - 將所有要公開為 `public`的成員標示為。

1. 將 [配置] 組建設定從 [**調試**] 變更為 [**發行**]。 若要這麼做，請開啟**Xcode > 目標 > 編輯配置** 對話方塊，然後將**組建**設定選項設為 **發行**：

    ![xcode 編輯配置](walkthrough-images/xcode-edit-scheme.png)

    ![xcode 編輯配置版本](walkthrough-images/xcode-edit-scheme-release.png)

1. 此時，已準備好建立架構。 建立模擬器和裝置架構的架構，然後將輸出合併為單一架構封裝。 識別已安裝的 SDK 版本，以便使用**xcodebuild**工具建立原始程式碼：

    ```bash
    xcodebuild -showsdks
    ```

    輸出將類似於：

    ```bash
    iOS SDKs:
            iOS 13.2                        -sdk iphoneos13.2
    iOS Simulator SDKs:
            Simulator - iOS 13.2            -sdk iphonesimulator13.2
    macOS SDKs:
            DriverKit 19.0                  -sdk driverkit.macosx19.0
            macOS 10.15                     -sdk macosx10.15
    tvOS SDKs:
            tvOS 13.2                       -sdk appletvos13.2
    tvOS Simulator SDKs:
            Simulator - tvOS 13.2           -sdk appletvsimulator13.2
    watchOS SDKs:
            watchOS 6.1                     -sdk watchos6.1
    watchOS Simulator SDKs:
            Simulator - watchOS 6.1         -sdk watchsimulator6.1
    ```

    選擇所需的 iOS SDK 和 iOS 模擬器 SDK 版本，在此案例中為13.2 版，並使用下列命令執行組建

    ```bash
    xcodebuild -sdk iphonesimulator13.2 -project "Swift/SwiftFrameworkProxy/SwiftFrameworkProxy.xcodeproj" -configuration Release
    xcodebuild -sdk iphoneos13.2 -project "Swift/SwiftFrameworkProxy/SwiftFrameworkProxy.xcodeproj" -configuration Release
    ```

    > [!TIP]
    > 如果您有工作區，而不是 project，請建立工作區，並將目標指定為必要參數。 您也想要指定輸出目錄，因為針對工作區，此目錄將會與專案組建不同。

    > [!TIP]
    > 您也可以使用協助程式[腳本](https://github.com/alexeystrakh/xamarin-binding-swift-framework/blob/master/Swift/Scripts/build.fat.sh#L3-L14)，針對所有適用的架構建立架構，或只從目標選取器中的 Xcode 切換模擬器和裝置建立它。

1. 有兩個 Swift 架構，每個平臺各有一個，將它們合併成單一套件，以供稍後內嵌到 Xamarin iOS 系結專案。 若要建立結合這兩個架構的 fat 架構，您需要執行下列步驟。 架構套件只是一個資料夾，因此您可以執行所有類型的作業，例如新增、移除和取代檔案：

    - 流覽至具有**發行 iphoneos**和**發行 iphonesimulator;** 子資料夾的組建輸出檔案夾，並複製其中一個架構做為最終輸出（fat framework）的初始版本。

        ```bash
        cp -R "Release-iphoneos" "Release-fat"
        ```

    - 使用 fat framework 模組結合另一個組建的模組

        ```bash
        cp -R "Release-iphonesimulator/SwiftFrameworkProxy.framework/Modules/SwiftFrameworkProxy.swiftmodule/" "Release-fat/SwiftFrameworkProxy.framework/Modules/SwiftFrameworkProxy.swiftmodule/"
        ```

    - 將 iphoneos + iphonesimulator; 設定合併為 fat 架構

        ```bash
        lipo -create -output "Release-fat/SwiftFrameworkProxy.framework/SwiftFrameworkProxy" "Release-iphoneos/SwiftFrameworkProxy.framework/SwiftFrameworkProxy" "Release-iphonesimulator/SwiftFrameworkProxy.framework/SwiftFrameworkProxy"
        ```

    - 驗證結果

        ```bash
        lipo -info "Release-fat/SwiftFrameworkProxy.framework/SwiftFrameworkProxy"
        ```

        輸出應該會轉譯下列內容，以反映架構的名稱和包含的架構：

        ```bash
        Architectures in the fat file: Release-fat/SwiftFrameworkProxy.framework/SwiftFrameworkProxy are: x86_64 arm64
        ```

    > [!TIP]
    > 如果您只想要支援單一平臺（例如，您要建立只能在裝置上執行的應用程式），可以略過建立 fat 程式庫的步驟，並從先前的裝置組建使用輸出架構。

    > [!TIP]
    > 您也可以使用[helper 腳本](https://github.com/alexeystrakh/xamarin-binding-swift-framework/blob/master/Swift/Scripts/build.fat.sh#L16-L24)來建立 fat 架構，這會自動化上述所有步驟。

## <a name="prepare-metadata"></a>準備中繼資料

此時，您應該已有已準備好要供 Xamarin iOS 系結使用之目標 C 的架構。  下一個步驟是準備 API 定義介面，以供系結專案用來產生C#類別。 [目標 Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/)工具和產生的標頭檔可以手動或自動建立這些定義。 使用 Sharpie 來產生中繼資料：

1. 從官方下載網站下載最新的[目標 Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/)工具，並依照嚮導進行安裝。 安裝完成後，您可以執行 sharpie 命令來進行驗證：

    ```bash
    sharpie -v
    ```

1. 使用 sharpie 和自動產生的目標 C 標頭檔來產生中繼資料：

    ```bash
    sharpie bind --sdk=iphoneos13.2 --output="XamarinApiDef" --namespace="Binding" --scope="Release-fat/SwiftFrameworkProxy.framework/Headers/" "Release-fat/SwiftFrameworkProxy.framework/Headers/SwiftFrameworkProxy-Swift.h"
    ```

    輸出會反映產生的中繼資料檔案： **ApiDefinitions.cs**和**StructsAndEnums.cs**。 儲存這些檔案以進行下一個步驟，將它們包含在 Xamarin iOS 系結專案中，以及原生參考：

    ```bash
    Parsing 1 header files...
    Binding...
        [write] ApiDefinitions.cs
        [write] StructsAndEnums.cs
    ```

    此工具會為C#每個公開的目標-C 成員產生中繼資料，看起來會類似下列程式碼。 如您所見，它可以手動定義，因為它具有人類看得懂的格式和直接的成員對應：

    ```csharp
    [Export ("initForApiKey:")]
    string InitForApiKey (string apiKey);
    ```

    > [!TIP]
    > 如果您變更了標頭名稱的預設 Xcode 設定，標頭檔案名稱可能會不同。 根據預設，它具有具有 **-Swift**尾碼的專案名稱。 您一律可以流覽至架構封裝的 [標頭] 資料夾，以檢查檔案及其名稱。

    > [!TIP]
    > 做為自動化程式的一部分，您可以使用[helper 腳本](https://github.com/alexeystrakh/xamarin-binding-swift-framework/blob/master/Swift/Scripts/build.fat.sh#L35)，在建立 fat framework 之後自動產生中繼資料。

## <a name="build-a-binding-library"></a>建立系結程式庫

下一個步驟是使用 Visual Studio 系結範本來建立 Xamarin iOS 系結專案、新增必要的中繼資料、原生參考，然後建立專案來產生可耗用的程式庫：

1. 開啟 Visual Studio for Mac，並建立新的 [Xamarin] 系結程式庫專案，並為其命名，在此案例中為 SwiftFrameworkProxy，並完成 wizard。 [Xamarin] 系結範本位於下列路徑： **ios >** 程式庫 > 系結程式庫：

    ![visual studio 建立系結程式庫](walkthrough-images/visualstudio-create-binding-library.png)

1. 刪除現有的中繼資料檔案**ApiDefinition.cs**和**Structs.cs** ，因為它們將會完全取代為目標 Sharpie 工具所產生的中繼資料。
1. 複製 Sharpie 在上述其中一個步驟產生的中繼資料，然後在 [屬性] 視窗中選取下列組建動作： **ObjBindingApiDefinition** （針對**ApiDefinitions.cs**檔案）和**ObjBindingCoreSource** （適用于**StructsAndEnums.cs**檔案）：

    ![visual studio 專案結構中繼資料](walkthrough-images/visualstudio-project-structure-metadata.png)

    中繼資料本身會使用 language 來C#描述每個公開的目標-C 類別和成員。 您可以同時看到原始的目標 C 標頭定義和C#宣告：

    ```csharp
    // @interface SwiftFrameworkProxy : NSObject
    [BaseType (typeof(NSObject))]
    interface SwiftFrameworkProxy
    {
        // -(NSString * _Nonnull)initForApiKey:(NSString * _Nonnull)apiKey __attribute__((objc_method_family("none"))) __attribute__((warn_unused_result));
        [Export ("initForApiKey:")]
        string InitForApiKey (string apiKey);
    }
    ```

    雖然它是有效C#的程式碼，但並不會使用它，而是由 Xamarin iOS 工具用來根據此C#元資料定義來產生類別。 因此，您會取得具有相同名稱的C#類別，而不是介面 SwiftFrameworkProxy，而這可以由您的 Xamarin. iOS 程式碼具現化。 這個類別會取得您的中繼資料所定義的方法、屬性和其他成員，您將以C#某種方式呼叫。

1. 將原生參考新增至所產生的舊版 fat framework，以及該架構的每個相依性。 在此情況下，請將 SwiftFrameworkProxy 和 Gigya 架構原生參考新增至系結專案：

    - 若要加入原生架構參考，請開啟搜尋工具並流覽至具有架構的資料夾。 將架構拖放到 方案總管中的 原生參考位置 底下。 或者，您可以使用 [原生參考] 資料夾上的內容功能表選項，然後按一下 [**加入原生參考**] 來查詢架構並加以加入：

    ![visual studio 專案結構原生參考](walkthrough-images/visualstudio-project-structure-nativerefs.png)

    - 更新每個原生參考的屬性，並檢查三個重要選項：

        - 設定智慧連結 = true
        - Set Force Load = false
        - 設定用來建立原始架構的架構清單。 在此情況下，每個架構只有兩個相依性： Foundation 和 UIKit。 將它設定為 [架構] 欄位：

        ![visual studio nativeref proxy 選項](walkthrough-images/visualstudio-nativeref-proxy-options.png)

        如果您有任何其他要指定的連結器旗標，請在 [連結器旗標] 欄位中設定它們。 在此情況下，請將它保留為空白。

    - 視需要指定其他連結器旗標。 如果您系結的程式庫只會公開目標-C Api，但在內部使用 Swift，則您可能會看到類似以下的問題：

        ```Console
        error MT5209 : Native linking error : warning: Auto-Linking library not found for -lswiftCore
        error MT5209 : Native linking error : warning: Auto-Linking library not found for -lswiftQuartzCore
        error MT5209 : Native linking error : warning: Auto-Linking library not found for -lswiftCoreImage
        ```

        在原生程式庫的系結專案屬性中，必須將下列值新增至連結器旗標：

        ```Linker
        L/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift/iphonesimulator/ -L/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift/iphoneos -Wl,-rpath -Wl,@executable_path/Frameworks
        ```

        前兩個選項（ `-L ...` 一個）會告訴原生編譯器哪裡可以找到 swift 程式庫。 原生編譯器會忽略沒有正確架構的程式庫，這表示您可以同時傳遞模擬器程式庫和裝置程式庫的位置，以便它適用于模擬器和裝置組建（這些只有 iOS 的路徑是正確的;針對 tvOS 和 watchOS，他們必須更新）。 其中一個缺點是，如果系結程式庫的取用者在不同的位置中有 Xcode，則此方法會要求正確的 Xcode 在/Application/Xcode.app 中，而不會有作用。 替代方案是在可執行檔專案的 iOS 組建選項（`--gcc_flags -L... -L...`）中，將這些選項新增至其他 mtouch 引數中。 第三個選項會使原生連結器將 swift 程式庫的位置儲存在可執行檔中，讓 OS 可以找到它們。

1. 最後的動作是建立程式庫，並確定您沒有任何編譯錯誤。 您通常會發現，目標 Sharpie 所產生的系結中繼資料會以 `[Verify]` 屬性來標注。 這些屬性工作表示您應該藉由比較系結與原始的目標-C 宣告（將會在系結宣告上方的批註中提供），來驗證目標 Sharpie 是否執行正確的動作。 您可以透過[下列連結](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/platform/verify)，深入瞭解以屬性標示的成員。 建立專案之後，就可以由 Xamarin iOS 應用程式使用。

## <a name="consume-the-binding-library"></a>使用系結程式庫

最後一個步驟是在 Xamarin iOS 應用程式中使用 Xamarin. iOS 系結程式庫。 建立新的 Xamarin iOS 專案、新增對系結程式庫的參考，以及啟用 Gigya Swift SDK：

1. 建立 Xamarin iOS 專案。 您可以使用**iOS > 應用程式 > 單一視圖應用程式**作為起點：

    ![visual studio 應用程式新增](walkthrough-images/visualstudio-app-new.png)

1. 將系結專案參考加入至先前建立的目標專案或 .dll。 將系結程式庫視為一般的 Xamarin. iOS 程式庫：

    ![visual studio 應用程式 refs](walkthrough-images/visualstudio-app-refs.png)

1. 更新應用程式的原始程式碼，並將初始化邏輯新增至主要 ViewController，以啟用 Gigya SDK

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
        var proxy = new SwiftFrameworkProxy();
        var result = proxy.InitForApiKey("APIKey");
        System.Diagnostics.Debug.WriteLine(result);
    }
    ```

1. 建立名為**btnLogin**的按鈕，並新增下列按鈕的 click 處理常式，以啟動驗證流程：

    ```csharp
    private void btnLogin_Tap(object sender, EventArgs e)
    {
        _proxy.LoginWithProvider(GigyaSocialProvidersProxy.Instagram, this, (result, error) =>
        {
            // process your login result here
        });
    }
    ```

1. 執行應用程式，在 debug 輸出中，您應該會看到下列這一行： `Gigya initialized with domain: us1.gigya.com`。 按一下按鈕以啟動驗證流程：

    [![swift proxy 結果](walkthrough-images/swiftproxy-result.png)](walkthrough-images/swiftproxy-result.png#lightbox)

恭喜！ 您已成功建立 Xamarin iOS 應用程式和系結程式庫，它會使用 Swift 架構。 上述應用程式會在 iOS 12.2 + 上成功執行，因為從這個 iOS 版本開始， [Apple 引進 ABI 穩定性](https://swift.org/blog/swift-5-1-released/)，而每個 ios 開始 12.2 + 包含 Swift 執行時間程式庫，可用來執行以 swift 5.1 + 編譯的應用程式。 如果您需要新增先前 iOS 版本的支援，還有幾個步驟可以完成：

1. 為了新增 iOS 12.1 和更早版本的支援，您想要提供用來編譯架構的特定 Swift dylib 來處理。 使用[SwiftRuntimeSupport](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/) NuGet 套件，透過您的 .ipa 處理及複製所需的程式庫。 將 NuGet 參考新增至您的目標專案，並重建應用程式。 不需要進一步的步驟，NuGet 套件將會安裝特定的工作，這些工作是使用組建程式來執行、識別所需的 Swift dylib 來處理，並使用最終的 .IPA 進行封裝。

1. 為了將應用程式提交到您想要使用 Xcode 和散發選項的 app store，這會更新 .IPA 檔案和**SwiftSupport**資料夾 dylib 來處理，讓 AppStore 可以接受它：

    ○封存應用程式。 從 [Visual Studio for Mac] 功能表中，選取 [**組建 > 封存以供發佈**]：

    ![發行的 visual studio 封存](walkthrough-images/visualstudio-archiveforpublishing.png)

    此動作會建立專案，並將其提供給召集人，讓 Xcode 可供散發。

    ○透過 Xcode 散發。 開啟 Xcode，然後流覽至 [ **Window > 召集人]** 功能表選項：

    ![visual studio 封存](walkthrough-images/visualstudio-archives.png)

    選取在上一個步驟建立的封存，然後按一下 [發佈應用程式] 按鈕。 依照嚮導將應用程式上傳至 AppStore。

1. 此步驟是選擇性的，但請務必確認您的應用程式可以在 iOS 12.1 和更早版本上執行，以及12.2。 您可以透過測試雲端和 UITest 架構來完成這項工作。 建立 UITest 專案和基本 UI 測試，以執行應用程式：

    - 建立 UITest 專案，並針對您的 Xamarin iOS 應用程式加以設定：

        ![visual studio uitest 新增](walkthrough-images/visualstudio-uitest-new.png)

        > [!TIP]
        > 您可以透過[下列連結](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)，找到有關如何建立 UITest 專案並為您的應用程式設定它的詳細資訊。

    - 建立基本測試來執行應用程式，並使用一些 Swift SDK 功能。 此測試會啟動應用程式、嘗試登入，然後按下 [取消] 按鈕：

        ```csharp
        [Test]
        public void HappyPath()
        {
            app.WaitForElement(StatusLabel);
            app.WaitForElement(LoginButton);
            app.Screenshot("App loaded.");
            Assert.AreEqual(app.Query(StatusLabel).FirstOrDefault().Text, "Gigya initialized with domain: us1.gigya.com");

            app.Tap(LoginButton);
            app.WaitForElement(GigyaWebView);
            app.Screenshot("Login activated.");

            app.Tap(CancelButton);
            app.WaitForElement(LoginButton);
            app.Screenshot("Login cancelled.");
        }
        ```

        > [!TIP]
        > 透過[下列連結](https://docs.microsoft.com/appcenter/test-cloud/uitest/)深入瞭解 uitest 架構和 UI 自動化。

    - 在 app center 中建立 iOS 應用程式，使用新的裝置集來建立新的測試回合，以執行測試：

        ![visual studio app center 新增](walkthrough-images/visualstudio-appcenter-new.png)

        > [!TIP]
        > 透過[下列連結](https://docs.microsoft.com/appcenter/test-cloud/)深入瞭解 AppCenter Test Cloud。

    - 安裝 appcenter CLI

        ```bash
        npm install -g appcenter-cli
        ```

        > [!IMPORTANT]
        > 請確定您已安裝 node 6.3 或更新版本

    - 使用下列命令來執行測試。 也請確定您的 appcenter 命令列目前已登入。

        ```bash
        appcenter test run uitest --app "Mobile-Customer-Advisory-Team/SwiftBinding.iOS" --devices a7e7cb50 --app-path "Xamarin.SingleView.ipa" --test-series "master" --locale "en_US" --build-dir "Xamarin/Xamarin.SingleView.UITests/bin/Debug/"
        ```

    - 驗證結果。 在 AppCenter 入口網站中，流覽至**應用程式 > 測試 > 測試**回合：

        ![visual studio appcenter uitest 結果](walkthrough-images/visualstudio-appcenter-uitest-result.png)

        並選取所需的測試回合，並驗證結果：

        ![visual studio appcenter uitest 執行](walkthrough-images/visualstudio-appcenter-uitest-runs.png)

您已開發使用原生 Swift 架構的基本 Xamarin iOS 應用程式，透過 Xamarin 程式庫。 此範例提供簡單的方式來使用選取的架構，而在實際的應用程式中，您將需要公開更多 Api 並準備這些 Api 的中繼資料。 產生中繼資料的腳本會簡化架構 Api 未來的變更。

## <a name="related-links"></a>相關連結

- [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [Objective Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/)
- [Sharpie 中繼資料驗證](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/platform/verify)
- [系結目標-C 架構](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough)
- [Gigya iOS SDK （Swift 架構）](https://developers.gigya.com/display/GD/Swift+SDK)
- [Swift 5.1 ABI 穩定性](https://swift.org/blog/swift-5-1-released/)
- [SwiftRuntimeSupport NuGet](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/)
- [Xamarin UITest 自動化](https://docs.microsoft.com/appcenter/test-cloud/uitest/)
- [UITest 設定](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [AppCenter 測試雲端](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [範例專案存放庫](https://github.com/alexeystrakh/xamarin-binding-swift-framework)
