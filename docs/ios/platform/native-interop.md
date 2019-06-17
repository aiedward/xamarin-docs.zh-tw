---
title: 參考在 Xamarin.iOS 中的原生程式庫
description: 本文討論如何將原生 C 程式庫連結至 Xamarin.iOS 應用程式。 它說明如何建置通用的原生程式庫和存取 C 方法，從C#。
ms.prod: xamarin
ms.assetid: 1DA80280-E78A-EC4B-8673-C249C8425CF5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/28/2016
ms.openlocfilehash: 7ed8fc18624f46abd4a9fc293d8c33a1722da7dd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424617"
---
# <a name="referencing-native-libraries-in-xamarinios"></a>參考在 Xamarin.iOS 中的原生程式庫

Xamarin.iOS 可支援使用原生 C 程式庫和 OBJECTIVE-C 程式庫連結。 本文件討論如何連結您原生的 C 程式庫與您的 Xamarin.iOS 專案。 如需有關執行相同的 Objective C 程式庫，請參閱我們[繫結 Objective C 類型](~/ios/platform/binding-objective-c/index.md)文件。

<a name="building_native" />

## <a name="building-universal-native-libraries-i386-armv7-and-arm64"></a>建置通用原生程式庫 （i386、 ARMv7 和 ARM64）

通常最好針對每個支援的平台，適用於 iOS 開發建置您的原生程式庫 (如裝置本身有 i386 模擬器和 ARMv7/ARM64)。 如果您已經有 Xcode 專案程式庫，這是真的易事。

若要建置您的原生程式庫的 i386 版本，請從終端機執行下列命令：

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphonesimulator -arch i386 -configuration Release clean build
```

這會導致在原生靜態程式庫`MyProject.xcodeproj/build/Release-iphonesimulator/`。 複製 （或移動） 程式庫要封存檔 (libMyLibrary.a) 至安全供日後使用，並賦予它一個唯一的名稱 (例如**libMyLibrary i386.a**)，讓它不衝突的 arm64 和 armv7 版本中，您將相同程式庫使用建置下一步。

若要建置您的原生程式庫的 ARM64 版本，執行下列命令：

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch arm64 -configuration Release clean build
```

建置的原生程式庫將位於的目前`MyProject.xcodeproj/build/Release-iphoneos/`。 同樣地，複製 （或移動） 到安全的位置，重新命名為類似此檔案**libMyLibrary arm64.a** ，讓它不會衝突。

現在建置程式庫的 ARMv7 版本：

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch armv7 -configuration Release clean build
```

產生的程式庫檔案複製 （或移動） 到相同的位置移動其他 2 個版本的程式庫，其重新命名為類似**libMyLibrary armv7.a**。

若要讓通用二進位，您可以使用`lipo`工具就像這樣：

```bash
lipo -create -output libMyLibrary.a libMyLibrary-i386.a libMyLibrary-arm64.a libMyLibrary-armv7.a
```

這會建立`libMyLibrary.a`，將會是適用於所有的 iOS 開發目標的通用 (fat) 程式庫。


### <a name="missing-required-architecture-i386"></a>遺漏必要的架構 i386

如果您收到`does not implement methodSignatureForSelector`或是`does not implement doesNotRecognizeSelector`i386 架構未編譯時嘗試要在 iOS 模擬器中，您的程式庫中的 Objective C 程式庫可能在執行階段輸出中的訊息 (請參閱[建置到通用原生程式庫](#building_native)上一節)。

若要檢查指定的程式庫所支援的架構，請在終端機中使用下列命令：

```bash
lipo -info /full/path/to/libraryname.a
```

何處`/full/path/to/`是正在使用的程式庫的完整路徑和`libraryname.a`有問題的程式庫名稱。

如果您有來源至程式庫時，您必須編譯並組合 i386 架構，如果您想要測試您的應用程式，在 iOS 模擬器上。

### <a name="linking-your-library"></a>連結您的程式庫

您使用任何協力廠商程式庫必須與您的應用程式以靜態方式連結。 

如果您想要以靜態方式連結程式庫 」 libMyLibrary.a"你從網際網路或具有 Xcode 組建的您需要執行一些作業：

-  帶入您的專案中的程式庫
-  設定 Xamarin.iOS 程式庫連結
-  從程式庫來存取方法。


若要**帶入您的專案中的媒體櫃**，選取專案，從 [方案總管]，然後按**命令 + 選項 + a**。 瀏覽至 libMyLibrary.a，並將它新增至專案。 出現提示時，會告訴 Visual Studio for Mac 或 Visual Studio，將它複製到專案。 新增它之後, 尋找 libFoo.a 專案中、 以滑鼠右鍵按一下它，並設定**建置動作**要**無**。

若要**設定 Xamarin.iOS 程式庫連結**，在最終可執行檔 （不在程式庫本身，但最終程式） 的專案選項，您需要在新增**iOS 組建**的額外引數 （這些是在專案選項的一部分）"-gcc_flags 」 選項後面加上引號的字串，包含所有額外的程式庫所需的程式，例如：

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load ${ProjectDir}/libMyLibrary.a"
```

上述範例會將連結**libMyLibrary.a**

您可以使用`-gcc_flags`來指定要傳遞至用來執行可執行檔的最後一個連結的 GCC 編譯器的命令列引任何的數設定。 比方說，這個命令列中，也會參考 CFNetwork framework:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

如果您的原生程式庫包含C++您也必須傳遞-cxx 旗標在您 「 額外引數 」，讓 Xamarin.iOS 知道若要使用正確的編譯器的程式碼。 針對C++先前的選項如下：

```bash
-cxx -gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

<a name="Accessing_C_Methods_from_C#" />

## <a name="accessing-c-methods-from-c35"></a>從 C 中存取 C 方法&#35;

在 iOS 上有兩種類型的原生程式庫可用：

-  共用程式庫的作業系統的一部分。

-  您寄送您的應用程式的靜態程式庫。


若要存取定義其中一種方法，您使用[Mono 的 P/Invoke 功能](https://www.mono-project.com/docs/advanced/pinvoke/)這是您在.NET 中，大約會使用的相同技術：

-  決定您想要叫用的 C 函式
-  判斷其簽章
-  判斷以其所在的程式庫
-  撰寫適當的 P/Invoke 宣告

當您使用 P/Invoke 時您需要指定您要連結的程式庫的路徑。 使用 iOS 共用程式庫、 可以硬式編碼路徑，或您可以使用我們在定義了方便常數時我們`Constants`，這些常數應涵蓋的共用的 iOS 程式庫。

例如，如果您想要叫用 Apple UIKit 程式庫，這在 c： 具有此簽章 UIRectFrameUsingBlendMode 方法

```csharp
void UIRectFrameUsingBlendMode (CGRect rect, CGBlendMode mode);
```

P/Invoke 宣告如下所示：

```csharp
[DllImport (Constants.UIKitLibrary,EntryPoint="UIRectFrameUsingBlendMode")]
public extern static void RectFrameUsingBlendMode (RectangleF rect, CGBlendMode blendMode);
```

Constants.UIKitLibrary 是只是定義為常數"/ System/Library/Frameworks/UIKit.framework/UIKit"，進入點可讓我們以選擇性地指定外部名稱 (UIRectFramUsingBlendMode) 同時公開不同的名稱，在C#，較短的 RectFrameUsingBlendMode。

<a name="Accessing_C_Dylibs" />

### <a name="accessing-c-dylibs"></a>存取 C dylib 來處理

如果您有需要使用 C Dylib Xamarin.iOS 應用程式中，有一點額外的安裝程式，都必須先呼叫`DllImport`屬性。

例如，如果我們有`Animal.dylib`與`Animal_Version`我們將在我們的應用程式中呼叫的方法，我們需要通知 Xamarin.iOS 程式庫的位置，然後再嘗試使用它。

若要這樣做，請編輯`Main.CS`檔案，並使它看起來如下：

```csharp
static void Main (string[] args)
{
    // Load Dylib
    MonoTouch.ObjCRuntime.Dlfcn.dlopen ("/full/path/to/Animal.dylib", 0);

    // Start application
    UIApplication.Main (args, null, "AppDelegate");
}
```

其中`/full/path/to/`是 Dylib 所耗用的完整路徑。 使用此程式碼就緒之後，我們可以接著連結至`Animal_Version`方法，如下所示：

```csharp
[DllImport("Animal.dylib", EntryPoint="Animal_Version")]
public static extern double AnimalLibraryVersion();
```

<a name="Static_Libraries" />

### <a name="static-libraries"></a>靜態程式庫

因為您只可以在 iOS 上使用靜態程式庫，沒有任何外部的共用程式庫連結，所以 path 參數中 DllImport 屬性必須使用特殊名稱`__Internal`（請注意在名稱開頭的雙底線字元） 相對於路徑名稱。

這會強制 DllImport 查閱您目前的程式，而不是嘗試將其從共用的程式庫中所參考之方法的符號。

