---
title: 針對不同螢幕建立資源
ms.prod: xamarin
ms.assetid: 3D17DE45-115C-7192-5685-44F8EEE07DCC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/28/2018
ms.openlocfilehash: cbd392dcae173eb3baf0fb8f0c3c4ec7c0da23a1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025121"
---
# <a name="creating-resources-for-varying-screens"></a>Creating resources for varying screens

Android itself runs on many different devices, each having a wide variety of resolutions, screen sizes, and screen densities. Android will perform scaling and resizing to make your application work on these devices, but this may result in a sub-optimal user experience. For example, images could appear blurry, or they may be positioned as expected on a view.

## <a name="concepts"></a>概念

A few terms and concepts are important to understand to support multiple screens.

- **Screen Size** &ndash; The amount of physical space for displaying your application

- **Screen Density** &ndash; The number of pixels in any given area on the screen. The typical unit of measure is dots per inch (dpi).

- **Resolution** &ndash; The total number of pixels on the screen. When developing applications, resolution is not as important as screen size and density.

- **Density-independent pixel (dp)** &ndash; A virtual unit of measure to allow layouts to be designed independent of density. This formula is used to convert dp into screen pixels:

    px &equals; dp &times; dpi &divide; 160

- **Orientation** &ndash; The screen's orientation is considered to be landscape when it is wider than it is tall. In contrast, portrait orientation is when the screen is taller than it is wide. The orientation can change during the lifetime of an application as the user rotates the device.

Notice that the first three of these concepts are inter-related &ndash; increasing the resolution without increasing the density will increase the screen size. However if both the density and resolution are increased, then the screen size can remain unchanged. This relationship between screen size, density, and resolution complicate screen support quickly.

To help deal with this complexity, the Android framework prefers to use *density-independent pixels (dp)* for screen layouts. By using density independent pixels, UI elements will appear to the user to have the same physical size on screens with different densities.

## <a name="supporting-various-screen-sizes-and-densities"></a>Supporting various screen sizes and densities

Android handles most of the work to render the layouts properly for each screen configuration. However, there are some actions that can be taken to help the system out.

The use of density-independent pixels instead of actual pixels in layouts is sufficient in most cases to ensure density independence.
Android will scale the drawables at runtime to the appropriate size.
However, it is possible that scaling will cause bitmaps to appear blurry. To work around this problem, supply alternate resources for the different densities. When designing devices for multiple resolutions and screen densities, it will prove easier to start with the higher resolution or density images and then scale down.

### <a name="declare-the-supported-screen-size"></a>Declare the supported screen size

Declaring the screen size ensures that only supported devices can download the application. This is accomplished by setting the [supports-screens](https://developer.android.com/guide/topics/manifest/supports-screens-element.html) element in the **AndroidManifest.xml** file. This element is used to specify what screen sizes are supported by the application. A given screen is considered to be supported if the application can properly place its layouts to fill screen. By using this manifest element, the application will not show up in [*Google Play*](https://play.google.com/) for devices that do not meet the screen specifications. However, the application will still run on devices with unsupported screens, but the layouts may appear blurry and pixelated.

Supported screen sixes are declared in the **Properites/AndroidManifest.xml** file of the solution:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Android Manifest](resources-for-varying-screens-images/01-android-manifest-sml.w1581.png)](resources-for-varying-screens-images/01-android-manifest.w1581.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![Android Manifest](resources-for-varying-screens-images/01-android-manifest-sml.m761.png)](resources-for-varying-screens-images/01-android-manifest.m761.png#lightbox)

-----

Edit **AndroidManifest.xml** to include [supports-screens](https://developer.android.com/guide/topics/manifest/supports-screens-element.html):

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="HelloWorld.HelloWorld">
      <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="27" />
      <supports-screens android:resizable="true"
                        android:smallScreens="true"
                        android:normalScreens="true"
                        android:largeScreens="true" />
      <application android:allowBackup="true"
                   android:icon="@mipmap/ic_launcher"
                   android:label="@string/app_name"
                   android:roundIcon="@mipmap/ic_launcher_round"
                   android:supportsRtl="true" android:theme="@style/AppTheme">
  </application>
</manifest>
```

### <a name="provide-alternate-layouts-for-different-screen-sizes"></a>Provide alternate layouts for different screen sizes

Alternate layouts make it possible to customize a view for a specifc screen size, changing the positioning or size of the component UI elements.

Starting with API Level 13 (Android 3.2), the screen sizes are deprecated in favor of using the sw*N*dp qualifier. This new qualifier declares the amount of space a given layout needs. It is recommended that applications that are meant to run on Android 3.2 or higher should be using these newer qualifiers.

For example, if a layout required a minimum 700 dp of screen width, the alternate layout would go in a folder **layout-sw700dp**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Layout folder for 700 dp screen width](resources-for-varying-screens-images/03-layout-sw700dp-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![Layout folder for 700 dp screen width](resources-for-varying-screens-images/03-layout-sw700dp-xs.png)

-----

As a guideline, here are some numbers for various devices:

- **Typical phone** &ndash; 320 dp: a typical phone

- **A 5" tablet / "tweener" device** &ndash; 480 dp: such as the Samsung Note

- **A 7" tablet** &ndash; 600 dp: such as the Barnes &amp; Noble Nook

- **A 10" tablet** &ndash; 720 dp: such as the Motorola Xoom

For applications that target API levels up to 12 (Android 3.1), the layouts should go in directories that use the qualifiers **small**/**normal**/**large**/**xlarge** as generalizations of the various screen sizes that are available in most devices. For example, in the image below, there are alternate resources for the four different screen sizes:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Alternate resources for four screen sizes](resources-for-varying-screens-images/04-layout-large-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![Alternate resources for four screen sizes](resources-for-varying-screens-images/04-layout-large-xs.png)

-----

The following is a comparison of how the older pre-API Level 13 screen size qualifiers compare to density-independent pixels:

- 426 dp x 320 dp is **small**

- 470 dp x 320 dp is **normal**

- 640 dp x 480 dp is **large**

- 960 dp x 720 dp is **xlarge**

The newer screen size qualifiers in API level 13 and up have a higher precedence than the older screen qualifiers of API levels 12 and lower.
For applications that will span the old and the new API levels, it may be necessary to create alternate resources using both sets of qualifiers as shown in the following screenshot:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Alternate resources with both qualifiers](resources-for-varying-screens-images/05-both-qualifiers-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![Alternate resources with both qualifiers](resources-for-varying-screens-images/05-both-qualifiers-xs.png)

-----

### <a name="provide-different-bitmaps-for-different-screen-densities"></a>Provide different bitmaps for different screen densities

Although Android will scale bitmaps as necessary for a device, the bitmaps themselves may not elegantly scale up or down: they may become fuzzy or blurry. Providing bitmaps appropriate for the screen density will mitigate this problem.

For example, the image below is an example of layout and appearance problems that may occur when density-specify resources are not provided.

![Screenshots without density resources](resources-for-varying-screens-images/06-density-not-provided.png)

Compare this to a layout that is designed with density-specific resources:

![Screenshots with density-specific resources](resources-for-varying-screens-images/07-density-specific-resources.png)

### <a name="create-varying-density-resources-with-android-asset-studio"></a>Create varying density resources with Android Asset Studio

The creation of these bitmaps of various densities can be a bit tedious. As such, Google has created an online utility that can reduce some of the tedium involved with the creation of these bitmaps called the [**Android Asset Studio**](https://romannurik.github.io/AndroidAssetStudio/).

[![Android Asset Studio](resources-for-varying-screens-images/08-android-asset-studio-sml.png)](resources-for-varying-screens-images/08-android-asset-studio.png#lightbox)

This website will help with creation of bitmaps that target the four common screen densities by providing one image. Android Asset Studio will then create the bitmaps with some customizations and then allow them to be downloaded as a zip file.

## <a name="tips-for-multiple-screens"></a>Tips for multiple screens

Android runs on a bewildering number of devices, and the combination of screen sizes and screen densities can seem overwhelming. The following tips can help minimize the effort necessary to support various devices:

- **僅針對您所需的設計和開發** &ndash;其中有許多不同的裝置，但有些則有極少的外型規格，可能需要大量的設計和開發。 [**螢幕大小 和 密度**](https://developer.android.com/resources/dashboard/screens.html) 儀表板是 Google 提供的頁面，可提供螢幕大小/螢幕密度矩陣的詳細資料。 This breakdown provides insight on how to development effort on supporting screens.

- **Use DPs rather than Pixels** - Pixels become troublesome as screen density changes. Do not hardcode pixel values. Avoid pixels in favor of dp (density-independent pixels).

- **Avoid** [AbsoluteLayout](xref:Android.Widget.AbsoluteLayout)
  **Wherever Possible** &ndash; it is deprecated in API level 3 (Android 1.5) and will result in brittle layouts. It should not be used. 相反地，請嘗試使用更有彈性的版面配置 widget，例如[**LinearLayout**](xref:Android.Widget.LinearLayout)、 [**RelativeLayout**](xref:Android.Widget.RelativeLayout)或新的[**GridLayout**](xref:Android.Widget.GridLayout)。

- **挑選一個版面配置方向做為預設**&ndash; 例如，而不是提供替代資源配置 **-land**和**版面配置-埠**，將資源置於**版面**配置中，並將資源用於直向**版面配置-埠**。

- **使用 LayoutParams 的高度和寬度**-在 XML 配置檔案中定義 UI 元素時，使用**wrap_content**和**fill_parent**值的 Android 應用程式將會更成功地確保在不同裝置之間進行適當的查看，而不是使用圖元或密度獨立單位。 這些維度值會使 Android 適當地縮放點陣圖資源。 基於此相同原因，當指定 UI 元素的邊界和填補時，高密度獨立單位是最適合的保留。

## <a name="testing-multiple-screens"></a>測試多個畫面

Android 應用程式必須針對將支援的所有設定進行測試。 理想的裝置應該在實際的裝置上進行測試，但在許多情況下，這是不可能的，也不可行。
在此情況下，針對每個裝置設定使用模擬器和 Android 虛擬裝置安裝將會很有用。

Android SDK 提供一些模擬器的外觀，可以用來建立 Avd，將會複寫許多裝置的大小、密度和解析度。
許多硬體廠商同樣都會提供裝置的外觀。

另一個選項是使用協力廠商測試服務的服務。
這些服務將會 APK，在許多不同的裝置上執行，然後提供應用程式運作方式的意見反應。
