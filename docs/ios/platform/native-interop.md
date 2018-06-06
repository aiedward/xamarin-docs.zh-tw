---
title: Xamarin.iOS 在參考原生程式庫
description: 本文將討論如何連結至 Xamarin.iOS 應用程式的原生 C 程式庫。 說明如何從 C# 建置原生的通用程式庫和存取 C 方法。
ms.prod: xamarin
ms.assetid: 1DA80280-E78A-EC4B-8673-C249C8425CF5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/28/2016
ms.openlocfilehash: bb27ba8b2d9c1b66448f22b7f80f17ba2e483544
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787721"
---
# <a name="referencing-native-libraries-in-xamarinios"></a>Xamarin.iOS 在參考原生程式庫

Xamarin.iOS 支援原生 C 程式庫和 Objective C 程式庫連結。 本文將討論如何連結 Xamarin.iOS 專案具有您原生 C 程式庫。 如需執行 Objective C 程式庫相同的動作，請參閱我們[繫結 Objective C 類型](~/ios/platform/binding-objective-c/index.md)文件。

<a name="building_native" />

## <a name="building-universal-native-libraries-i386-armv7-and-arm64"></a>建置通用原生程式庫 （i386、 ARMv7 和 ARM64）

它通常會針對每個適用於 iOS 開發的支援平台建置您的原生程式庫 (如裝置本身有 i386 模擬器和 ARMv7/ARM64)。 如果您已經有的 Xcode 專案程式庫，這是真的 trivial 執行。

若要建置您的原生程式庫的 i386 版本，請從終端機中執行下列命令：

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphonesimulator -arch i386 -configuration Release clean build
```

這會導致在原生靜態程式庫`MyProject.xcodeproj/build/Release-iphonesimulator/`。 複製 （或移動） 以供日後使用，並賦予它的唯一名稱的安全位置的程式庫封存檔案 (libMyLibrary.a) (例如**libMyLibrary i386.a**)，讓它不會使用相同的文件庫，您將 arm64 及 armv7 版相衝突接下來建置。

若要建置您的原生程式庫的 ARM64 版本，執行下列命令：

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch arm64 -configuration Release clean build
```

此時間內建的原生程式庫會位於`MyProject.xcodeproj/build/Release-iphoneos/`。 同樣地，複製 （或移動） 到安全的位置，重新命名為類似這個檔案**libMyLibrary arm64.a** ，讓它不會衝突。

現在建置 ARMv7 版本的程式庫：

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch armv7 -configuration Release clean build
```

複製 （或移動） 產生的程式庫檔案相同的位置移動其他 2 個版本的程式庫中，重新命名為類似**libMyLibrary armv7.a**。

若要讓通用二進位，您可以使用`lipo`工具就像這樣：

```bash
lipo -create -output libMyLibrary.a libMyLibrary-i386.a libMyLibrary-arm64.a libMyLibrary-armv7.a
```

這會建立`libMyLibrary.a`，將會是適用於 iOS 開發的所有目標的通用 (fat) 程式庫。


### <a name="missing-required-architecture-i386"></a>遺漏必要的架構 i386

如果您要取得`does not implement methodSignatureForSelector`或`does not implement doesNotRecognizeSelector`嘗試可能使用 Objective C 程式庫，在 iOS 模擬器中，您的程式庫中時，在執行階段輸出中的訊息並不是針對 i386 架構編譯 (請參閱[建置通用原生程式庫](#building_native)上一節)。

若要檢查指定的程式庫所支援的架構，請在終端機中使用下列命令：

```bash
lipo -info /full/path/to/libraryname.a
```

其中`/full/path/to/`是正在使用的程式庫的完整路徑和`libraryname.a`有問題的程式庫名稱。

如果您必須在來源文件庫，您將必須編譯並配套 i386 架構，如果您想要測試的 iOS 模擬器上的應用程式。

### <a name="linking-your-library"></a>連結程式庫

您使用任何協力廠商文件庫必須以靜態方式連結到您的應用程式。 

如果您想要以靜態方式連結程式庫 」 libMyLibrary.a 」，您有從網際網路或使用 Xcode 的組建，您需要做幾件事：

-  帶入您的專案程式庫
-  設定程式庫連結 Xamarin.iOS
-  從程式庫來存取的方法。


若要**帶入您的專案程式庫**，選取專案，從 [方案總管]，然後按**命令 + 選項 +**。 巡覽至 libMyLibrary.a，並將它加入至專案。 出現提示時，告知 Visual Studio for Mac 或 Visual Studio 將它複製到專案。 之後將它加入，尋找 libFoo.a 專案中，以滑鼠右鍵按一下它，並設定**建置動作**至**無**。

若要**將程式庫連結設定 Xamarin.iOS**，最終可執行檔 （不在程式庫本身，但最後的程式） 的專案選項上，您需要加入在**iOS 建置**的 （這些是額外的引數專案選項的一部分） 」-gcc_flags"選項後面加上引號的字串，包含所有額外的程式庫所需的程式，例如：

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load ${ProjectDir}/libMyLibrary.a"
```

上述範例中將會連結**libMyLibrary.a**

您可以使用`-gcc_flags`来指定命令列引數傳遞至 GCC 編譯器用來執行可執行檔的最後一個連結的任何集合。 例如，此命令列，也會參考 CFNetwork 架構：

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

如果您的原生程式庫包含 c + + 程式碼，您必須也將-cxx 旗標您 「 額外引數 」 以便 Xamarin.iOS 就會使用正確的編譯器。 C + + 的先前選項應該像這樣：

```bash
-cxx -gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

<a name="Accessing_C_Methods_from_C#" />

## <a name="accessing-c-methods-from-c35"></a>從 C 存取 C 方法&#35;

在 iOS 上有兩種類型的原生程式庫可用：

-  共用是作業系統的一部分的文件庫。

-  您寄送您的應用程式的靜態程式庫。


若要存取定義在這些其中一個方法，您使用[單聲道的 P/Invoke 功能](http://www.mono-project.com/docs/advanced/pinvoke/)這是您會使用在.NET 中，也就是大約的相同技術：

-  決定您想要叫用的 C 函式
-  判斷它的簽章
-  決定於其所存在的程式庫
-  撰寫適當的 P/Invoke 宣告


當您使用 P/Invoke 時您需要指定您要連結的程式庫的路徑。 當使用 iOS 共用程式庫，您可以硬式編碼路徑，或您可以使用我們已在中定義的便利性常數我們[常數類別](https://developer.xamarin.com/api/type/Constants/)，這些常數應涵蓋的 iOS 共用程式庫。

例如，如果您想要叫用從 Apple UIKit 程式庫在 c： 具有此簽章 UIRectFrameUsingBlendMode 方法

```csharp
void UIRectFrameUsingBlendMode (CGRect rect, CGBlendMode mode);
```

P/Invoke 宣告看起來會像這樣：

```csharp
[DllImport (Constants.UIKitLibrary,EntryPoint="UIRectFrameUsingBlendMode")]
public extern static void RectFrameUsingBlendMode (RectangleF rect, CGBlendMode blendMode);
```

Constants.UIKitLibrary 是只是定義為常數 」 / System/Library/Frameworks/UIKit.framework/UIKit"，進入點可讓我們選擇性地指定外部名稱 (UIRectFramUsingBlendMode) 公開不同的名稱，在 C# 中，較短者時RectFrameUsingBlendMode。

<a name="Accessing_C_Dylibs" />

### <a name="accessing-c-dylibs"></a>存取 C dylib 檔案

如果您需要使用 C Dylib Xamarin.iOS 應用程式中，沒有額外的安裝程式之前呼叫，需要進行一些`DllImport`屬性。

例如，如果我們有`Animal.dylib`與`Animal_Version`我們將在我們的應用程式中呼叫的方法，我們需要通知 Xamarin.iOS 的程式庫位置然後再嘗試使用它。

若要這樣做，請編輯`Main.CS`檔案，並讓它看起來如下所示：

```csharp
static void Main (string[] args)
{
    // Load Dylib
    MonoTouch.ObjCRuntime.Dlfcn.dlopen ("/full/path/to/Animal.dylib", 0);

    // Start application
    UIApplication.Main (args, null, "AppDelegate");
}
```

其中`/full/path/to/`是被取用 Dylib 的完整路徑。 此位置的程式碼，我們可以再連結到`Animal_Version`方法，如下所示：

```csharp
[DllImport("Animal.dylib", EntryPoint="Animal_Version")]
public static extern double AnimalLibraryVersion();
```

<a name="Static_Libraries" />

### <a name="static-libraries"></a>靜態程式庫

您只可以在 iOS 上使用靜態程式庫，因為沒有外部共用程式庫連結，所以 path 參數中 DllImport 屬性需要使用特殊名稱`__Internal`（請注意雙底線字元開頭的名稱） 與路徑名稱。

這會強制 DllImport 來查閱您目前的程式，而不是嘗試載入從共用文件庫中所參考之方法的符號。

