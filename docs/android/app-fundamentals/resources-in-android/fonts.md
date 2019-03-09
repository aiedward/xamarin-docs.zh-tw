---
title: 字型
ms.prod: xamarin
ms.assetid: 3F543FC5-FDED-47F8-8D2C-481FCC98BFDA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/09/2018
ms.openlocfilehash: c7953748e79bd43bc14601c1f0ea05d1a36adf08
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668124"
---
# <a name="fonts"></a>字型

## <a name="overview"></a>總覽

從 API 層級 26，Android SDK 可讓被視為資源，就像版面配置字型或可繪製資源來。 [Android 支援程式庫 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/26.1.0.1)新字型 API 的目標 API 層級 14 或更高版本的這些應用程式會向下移植。

瞄準的 API 26 或之後安裝 Android 支援程式庫 v26，有兩種方式可使用 Android 應用程式中的字型：

1. **封裝做為 Android 資源字型**&ndash;這可確保字型一律可供應用程式，但會增加 APK 的大小。
2. **下載字型** &ndash; Android 也支援下載從字型_字型提供者_。 字型提供者會檢查字型是否已在裝置上。 如有必要，就會下載並快取在裝置上的字型。 這種字型可以在多個應用程式之間共用。

類似的字型 （或可能有數個不同的樣式字型） 可能會分為_字型家族_。 這可讓開發人員指定的字型，例如它的權數，某些屬性和 Android 將會自動選取適當的字型的字型系列。

Android 支援程式庫 v26 會向下移植支援 API 層級 26 的字型。 當目標舊版的 API 層級，就必須宣告`app`XML 命名空間，並使用不同的字型屬性`android:`命名空間和`app:`命名空間。 如果只有`android:`命名空間，則字型將不會顯示執行 API 層級 25 或更少的裝置。 例如，此 XML 程式碼片段會宣告新[_字型家族_](#font_families)會在 API 層級 14 和更新版本中運作的資源：

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

     <font  android:font="@font/sourcesanspro_regular"
            android:fontStyle="normal"
            android:fontWeight="400"
            app:font="@font/sourcesanspro_regular"
            app:fontStyle="normal"
            app:fontWeight="400" />

</font-family>
```

只要字型可供 Android 應用程式，以正確方式，可將它們套用至 UI 的小工具設定[`fontFamily`屬性](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)。 例如，下列程式碼片段會示範如何顯示字型 TextView 中：

```xml
<TextView
    android:text="The quick brown fox jumped over the lazy dog."
    android:fontFamily="@font/sourcesanspro_regular"
    app:fontFamily="@font/sourcesanspro_regular"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

本指南會先討論如何使用字型做為 Android 的資源，並移至討論如何在執行階段的字型下載。

## <a name="fonts-as-a-resource"></a>為資源的字型

封裝的 Android apk 的字型，可確保它會一律可供應用程式。 字型檔案 (其中一個。TTF 或。OTF 檔案） 新增至 Xamarin.Android 應用程式，就像任何其他資源，藉由將檔案複製到中的子目錄**資源**Xamarin.Android 專案的資料夾。 字型資源保留在**字型**子目錄**資源**專案的資料夾。

> [!NOTE]
> 字型應有**建置動作**的**AndroidResource**或者不封裝成最終 APK。 建置動作應該由 IDE 自動設定。

有許多類似的字型檔案 （例如，使用不同的加權或樣式的相同字型） 時，可以將它們組成的字型家族。

<a name="font_families" />

### <a name="font-families"></a>字型家族

字型家族是一組有不同的加權和樣式的字型。 例如，可能有不同的字型檔案為粗體或斜體的字型。 定義字型家族`font`會保留在 XML 檔案中的項目**字型資源/** 目錄。 每個字型家族應該有其自己的 XML 檔案。

若要建立的字型家族，第一次新增的所有字型**字型資源/** 資料夾。 中的字型系列的 [字型] 資料夾，然後建立新的 XML 檔案。 XML 檔案的名稱具有任何親和性或關聯性所參考; 的字型資源檔可以是任何合法的 Android 資源的檔案名稱。 此 XML 檔案會有根`font-family`包含一或多個項目`font`項目。 每個`font`項目宣告的字型屬性。

下列 XML 程式碼是範例的字型家族_來源 San Pro_字型，可定義許多不同的字型粗細。 這會儲存為檔案中**資源/字型**名為資料夾**sourcesanspro.xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto">
    <font android:font="@font/sourcesanspro_regular"
          android:fontStyle="normal"
          android:fontWeight="400"
          app:font="@font/sourcesanspro_regular"
          app:fontStyle="normal"
          app:fontWeight="400" />
    <font android:font="@font/sourcesanspro_bold"
          android:fontStyle="normal"
          android:fontWeight="800"
          app:font="@font/sourcesanspro_bold"
          app:fontStyle="normal"
          app:fontWeight="800" />
    <font android:font="@font/sourcesanspro_italic"
          android:fontStyle="italic"
          android:fontWeight="400"
          app:font="@font/sourcesanspro_italic"
          app:fontStyle="italic"
          app:fontWeight="400" />
</font-family>
```

`fontStyle`屬性有兩個可能的值：

* **正常**&ndash;一般字型
* **斜體**&ndash;斜體字

`fontWeight`屬性會對應至 CSS`font-weight`屬性，並參考之字型的粗細。 這是 100-900 的範圍內的值。 下列清單描述常見的字型粗細值和其名稱：

* **Thin** &ndash; 100
* **Extra Light** &ndash; 200
* **Light** &ndash; 300
* **Normal** &ndash; 400
* **中型** &ndash; 500
* **半粗體** &ndash; 600
* **粗體** &ndash; 700
* **非常粗** &ndash; 800
* **黑色** &ndash; 900

一旦已定義字型家族，它可供以宣告方式設定`fontFamily`， `textStyle`，和`fontWeight`版面配置檔案中的屬性。  例如 600 權數字型 （一般） 和斜體文字樣式設定下列 XML 程式碼片段：

```xml
<TextView
    android:text="Sans Source Pro semi-bold italic, 600 weight, italic"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:fontFamily="@font/sourcesanspro"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:gravity="center_horizontal"
    android:fontWeight="600"
    android:textStyle="italic"
    />
```

### <a name="programmatically-assigning-fonts"></a>以程式設計的方式指派字型

字型可以透過程式設計方式設定[ `Resources.GetFont` ](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int))方法來擷取[ `Typeface` ](https://developer.android.com/reference/android/graphics/Typeface.html)物件。 有許多檢視`TypeFace`屬性，可用來將小工具中的字型。 此程式碼片段示範如何以程式設計方式將字型設定 TextView 上：

```csharp
Android.Graphics.Typeface typeface = this.Resources.GetFont(Resource.Font.caveat_regular);
textView1.Typeface = typeface;
textView1.Text = "Changed the font";
```

`GetFont`方法會自動載入第一種字型的字型家族內。  若要載入符合特定樣式的字型，請使用`Typeface.Create`方法。 這個方法會嘗試載入的字型，符合指定的樣式。 例如，此程式碼片段會嘗試載入粗體`Typeface`物件中定義字型家族**資源] / [字型**:

```csharp
var typeface = Typeface.Create("<FONT FAMILY NAME>", Android.Graphics.TypefaceStyle.Bold);
textView1.Typeface = typeface;
```

## <a name="downloading-fonts"></a>下載字型

而不是將字型封裝為應用程式資源，Android 可以從遠端來源下載字型。 這會有需要的縮小 APK 的大小影響。

字型下載的協助_字型提供者_。 這是特製化的內容提供者所管理的下載和快取的裝置上的所有應用程式的字型。 Android 8.0 包含字型提供者若要下載從字型[Google 字型 Repository](http://fonts.google.com)。 此預設字型提供者已向前移植到與 Android 支援程式庫 v26 API 層級 14。

當應用程式提出要求時的字型時，字型提供者會先檢查有字型已經在裝置上。 否則，便會嘗試下載字型。 如果字型無法下載、 然後 Android 會使用預設系統字型。 一旦下載字型後，就可在裝置上，不只是要求初始的應用程式的所有應用程式。

若要下載字型提出要求時應用程式不會直接查詢字型提供者。 相反地，應用程式會使用的執行個體[ `FontsContract` ](https://developer.android.com/reference/android/provider/FontsContract.html) API (或[ `FontsContractCompat` ](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html)如果正在使用支援程式庫 26)。  

Android 8.0 的支援方式有兩種下載字型：

1. **宣告為資源的可下載字型**&ndash;應用程式可以宣告至 Android XML 資源檔透過可下載的字型。 這些檔案會包含所有 Android 需要以非同步方式下載字型，當應用程式會啟動，並在裝置上時進行快取的中繼資料。
2. **以程式設計方式**&ndash;中 Android API 層級 26 Api 允許應用程式的應用程式執行時，以程式設計的方式，下載字型。 應用程式會建立`FontRequest`物件指定的字型，並傳遞此物件與`FontsContract`類別。 `FontsContract`會採用`FontRequest`，並擷取從字型_字型提供者_。 Android 會以同步方式下載字型。 建立的範例`FontRequest`會稍後在本指南中所示。

不論使用哪種方法時，您可以下載必須新增至 Xamarin.Android 應用程式之前字型的資源檔。 首先中的 XML 檔案中必須宣告所字型**字型資源/** 目錄的字型家族的一部分。 此程式碼片段是如何下載從字型的範例[Google 字型的開放原始碼集合](https://fonts.google.com)使用 Android 8.0 （或支援程式庫 v26） 隨附的預設字型提供者：

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto"
             android:fontProviderAuthority="com.google.android.gms.fonts"
             android:fontProviderPackage="com.google.android.gms"
             android:fontProviderQuery="VT323"
             android:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
             app:fontProviderAuthority="com.google.android.gms.fonts"
             app:fontProviderPackage="com.google.android.gms"
             app:fontProviderQuery="VT323"
             app:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
>
</font-family>
```

`font-family`元素包含下列屬性： 宣告 Android 需要下載字型的資訊：

1. **fontProviderAuthority** &ndash;字型提供者来用於要求的授權單位。
2. **fontPackage** &ndash;字型提供者來要求封裝。 這用來驗證提供者的身分識別。
3. **fontQuery** &ndash;這是可協助找出要求的字型的字型提供者的字串。 字型查詢的詳細資訊專屬於字型提供者。 [ `QueryBuilder` ](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs)類別[可下載的字型](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/)範例應用程式提供一些資訊的查詢格式字型來自 Google 字型開啟來源集合。
4. **fontProviderCerts** &ndash;的提供者應該使用簽署憑證的雜湊集清單的資源陣列。

字型定義之後，您可能必須提供相關資訊_字型憑證_與下載。

### <a name="font-certificates"></a>字型的憑證

如果字型提供者未預先安裝在裝置上，或應用程式使用`Xamarin.Android.Support.Compat`程式庫，Android 需要字型提供者的安全性憑證。 這些憑證將會列在 保留在陣列資源檔**資源/值**目錄。

例如，下列 XML 會命名為**Resources/values/fonts_cert.xml** ，並將憑證儲存為 Google 字型提供者：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="com_google_android_gms_fonts_certs">
        <item>@array/com_google_android_gms_fonts_certs_dev</item>
        <item>@array/com_google_android_gms_fonts_certs_prod</item>
    </array>
    <string-array name="com_google_android_gms_fonts_certs_dev">
        <item>
            MIIEqDCCA5CgAwIBAgIJANWFuGx90071MA0GCSqGSIb3DQEBBAUAMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTAeFw0wODA0MTUyMzM2NTZaFw0zNTA5MDEyMzM2NTZaMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBANbOLggKv+IxTdGNs8/TGFy0PTP6DHThvbbR24kT9ixcOd9W+EaBPWW+wPPKQmsHxajtWjmQwWfna8mZuSeJS48LIgAZlKkpFeVyxW0qMBujb8X8ETrWy550NaFtI6t9+u7hZeTfHwqNvacKhp1RbE6dBRGWynwMVX8XW8N1+UjFaq6GCJukT4qmpN2afb8sCjUigq0GuMwYXrFVee74bQgLHWGJwPmvmLHC69EH6kWr22ijx4OKXlSIx2xT1AsSHee70w5iDBiK4aph27yH3TxkXy9V89TDdexAcKk/cVHYNnDBapcavl7y0RiQ4biu8ymM8Ga/nmzhRKya6G0cGw8CAQOjgfwwgfkwHQYDVR0OBBYEFI0cxb6VTEM8YYY6FbBMvAPyT+CyMIHJBgNVHSMEgcEwgb6AFI0cxb6VTEM8YYY6FbBMvAPyT+CyoYGapIGXMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbYIJANWFuGx90071MAwGA1UdEwQFMAMBAf8wDQYJKoZIhvcNAQEEBQADggEBABnTDPEF+3iSP0wNfdIjIz1AlnrPzgAIHVvXxunW7SBrDhEglQZBbKJEk5kT0mtKoOD1JMrSu1xuTKEBahWRbqHsXclaXjoBADb0kkjVEJu/Lh5hgYZnOjvlba8Ld7HCKePCVePoTJBdI4fvugnL8TsgK05aIskyY0hKI9L8KfqfGTl1lzOv2KoWD0KWwtAWPoGChZxmQ+nBli+gwYMzM1vAkP+aayLe0a1EQimlOalO762r0GXO0ks+UeXde2Z4e+8S/pf7pITEI/tP+MxJTALw9QUWEv9lKTk+jkbqxbsh8nfBUapfKqYn0eidpwq2AzVp3juYl7//fKnaPhJD9gs=
        </item>
    </string-array>
    <string-array name="com_google_android_gms_fonts_certs_prod">
        <item>
            MIIEQzCCAyugAwIBAgIJAMLgh0ZkSjCNMA0GCSqGSIb3DQEBBAUAMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDAeFw0wODA4MjEyMzEzMzRaFw0zNjAxMDcyMzEzMzRaMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBAKtWLgDYO6IIrgqWbxJOKdoR8qtW0I9Y4sypEwPpt1TTcvZApxsdyxMJZ2JORland2qSGT2y5b+3JKkedxiLDmpHpDsz2WCbdxgxRczfey5YZnTJ4VZbH0xqWVW/8lGmPav5xVwnIiJS6HXk+BVKZF+JcWjAsb/GEuq/eFdpuzSqeYTcfi6idkyugwfYwXFU1+5fZKUaRKYCwkkFQVfcAs1fXA5V+++FGfvjJ/CxURaSxaBvGdGDhfXE28LWuT9ozCl5xw4Yq5OGazvV24mZVSoOO0yZ31j7kYvtwYK6NeADwbSxDdJEqO4k//0zOHKrUiGYXtqw/A0LFFtqoZKFjnkCAQOjgdkwgdYwHQYDVR0OBBYEFMd9jMIhF1Ylmn/Tgt9r45jk14alMIGmBgNVHSMEgZ4wgZuAFMd9jMIhF1Ylmn/Tgt9r45jk14aloXikdjB0MQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEUMBIGA1UEChMLR29vZ2xlIEluYy4xEDAOBgNVBAsTB0FuZHJvaWQxEDAOBgNVBAMTB0FuZHJvaWSCCQDC4IdGZEowjTAMBgNVHRMEBTADAQH/MA0GCSqGSIb3DQEBBAUAA4IBAQBt0lLO74UwLDYKqs6Tm8/yzKkEu116FmH4rkaymUIE0P9KaMftGlMexFlaYjzmB2OxZyl6euNXEsQH8gjwyxCUKRJNexBiGcCEyj6z+a1fuHHvkiaai+KL8W1EyNmgjmyy8AW7P+LLlkR+ho5zEHatRbM/YAnqGcFh5iZBqpknHf1SKMXFh4dd239FJ1jWYfbMDMy3NS5CTMQ2XFI1MvcyUTdZPErjQfTbQe3aDQsQcafEQPD+nqActifKZ0Np0IS9L9kR/wbNvyz6ENwPiTrjV2KRkEjH78ZMcUQXg0L3BYHJ3lc69Vs5Ddf9uUGGMYldX3WfMBEmh/9iFBDAaTCK
        </item>
    </string-array>
</resources>
```

這些資源中的檔案位置，應用程式都能下載字型。

### <a name="declaring-downloadable-fonts-as-resources"></a>宣告為資源的可下載的字型

藉由列出在可下載的字型**AndroidManifest.XML**，應用程式初次啟動時，Android 會以非同步方式下載字型。 字型的本身為陣列資源檔，類似如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="downloadable_fonts" translatable="false">
        <item>@font/vt323</item>
    </array>
</resources>
```

若要下載這些字型，他們必須在宣告**AndroidManifest.XML**加上`meta-data`做為子系的`application`項目。 例如，如果宣告上的資源檔中的可下載的字型**Resources/values/downloadable_fonts.xml**，則此程式碼片段必須新增至資訊清單：

```xml
<meta-data android:name="downloadable_fonts" android:resource="@array/downloadable_fonts" />
```

### <a name="downloading-a-font-with-the-font-apis"></a>下載字型的字型 api

可具現化，以程式設計方式下載字型[ `FontRequest` ](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html)物件，然後傳遞至`FontContractCompat.RequestFont`方法。 `FontContractCompat.RequestFont`方法會先檢查裝置上是否有字型，然後必要時將會以非同步方式查詢字型提供者和嘗試下載應用程式的字型。 如果`FontRequest`Android 將會使用預設系統字型是無法下載字型。

A`FontRequest`物件包含的資訊，將字型提供者用來尋找及下載字型。 A`FontRequest`需要四項資訊：

1. **字型提供者授權單位**&ndash;字型提供者来用於要求的授權單位。
2. **字型套件**&ndash;字型提供者來要求封裝。 這用來驗證提供者的身分識別。
3. **字型查詢**&ndash;這是可協助找出要求的字型的字型提供者的字串。 字型查詢的詳細資訊專屬於字型提供者。 字串的詳細資料特有的字型提供者。 [ `QueryBuilder` ](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs)類別[可下載的字型](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/)範例應用程式提供一些資訊的查詢格式字型來自 Google 字型開啟來源集合。
4. **字型提供者憑證**&ndash;的提供者應該使用簽署憑證的雜湊集清單的資源陣列。

此程式碼片段是具現化新範例`FontRequest`物件：

```csharp
FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms", <FontToDownload>, Resource.Array.com_google_android_gms_fonts_certs);
```

在先前的程式碼片段`FontToDownload`這項查詢會從 Google 字型的開放原始碼集合有助於字型。

然後再傳遞`FontRequest`至`FontContractCompat.RequestFont`方法中，有兩個必須建立的物件：

* **`FontsContractCompat.FontRequestCallback`** &ndash; 這是您必須擴充的抽象類別。 它是會回呼時叫用`RequestFont`完成。 Xamarin.Android 應用程式必須子類別`FontsContractCompat.FontRequestCallback`，並覆寫`OnTypefaceRequestFailed`和`OnTypefaceRetrieved`，提供下載失敗，或分別成功時所要採取的動作。
* **`Handler`** &ndash; 這是`Handler`這會由`RequestFont`如有必要，請下載在執行緒上的字型。 字型應該**不**UI 執行緒上下載。

此程式碼片段是範例C#類別，會以非同步方式下載從 Google 字型的開放原始碼集合的字型。 它會實作`FontRequestCallback`介面，並引發C#事件時`FontRequest`已完成。

```csharp
public class FontDownloadHelper : FontsContractCompat.FontRequestCallback
{
    // A very simple font query; replace as necessary
    public static readonly String FontToDownload = "Courgette";

    Android.OS.Handler Handler = null;

    public event EventHandler<FontDownloadEventArg> FontDownloaded = delegate
    {
        // just an empty delegate to avoid null reference exceptions.  
    };


    public void DownloadFonts(Context context)
    {
        FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms",FontToDownload , Resource.Array.com_google_android_gms_fonts_certs);
        FontsContractCompat.RequestFont(context, request, this, GetHandlerThreadHandler());
    }

    public override void OnTypefaceRequestFailed(int reason)
    {
        base.OnTypefaceRequestFailed(reason);
        FontDownloaded(this, new FontDownloadEventArg(null));
    }

    public override void OnTypefaceRetrieved(Android.Graphics.Typeface typeface)
    {
        base.OnTypefaceRetrieved(typeface);
        FontDownloaded(this, new FontDownloadEventArg(typeface));
    }

    Handler GetHandlerThreadHandler()
    {
        if (Handler == null)
        {
            HandlerThread handlerThread = new HandlerThread("fonts");
            handlerThread.Start();
            Handler = new Handler(handlerThread.Looper);
        }
        return Handler;
    }
}

public class FontDownloadEventArg : EventArgs
{
    public FontDownloadEventArg(Android.Graphics.Typeface typeface)
    {
        Typeface = typeface;
    }
    public Android.Graphics.Typeface Typeface { get; private set; }
    public bool RequestFailed
    {
        get
        {
            return Typeface != null;
        }
    }
}
```

若要使用此協助程式中，新`FontDownloadHelper`建立時，並指派的事件處理常式：  

```csharp
var fontHelper = new FontDownloadHelper();

fontHelper.FontDownloaded += (object sender, FontDownloadEventArg e) =>
{
    //React to the request
};
fontHelper.DownloadFonts(this); // this is an Android Context instance.
```

## <a name="summary"></a>總結

本指南會討論中支援可下載的字型和字型做為資源的 Android 8.0 的新 Api。 它討論如何將現有的字型內嵌在 APK，並在版面配置中使用它們。 它也會討論如何 Android 8.0 支援下載字型字型提供者，以程式設計方式或宣告字型中繼資料，在資源檔。

## <a name="related-links"></a>相關連結

- [fontFamily](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)
- [FontConfig](https://developer.android.com/reference/android/text/FontConfig.html)
- [FontRequest](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html)
- [FontsContractCompat](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html)
- [Resources.GetFont](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int))
- [Typeface](https://developer.android.com/reference/android/graphics/Typeface.html)
- [Android 支援程式庫 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/)
- [在 Android 中使用字型](https://www.youtube.com/watch?v=TfB-TsLFJdM)
- [CSS 字型粗細規格](https://www.w3.org/TR/css-fonts-3/#font-weight-numeric-values)
- [Google 字型的開放原始碼集合](https://fonts.google.com/)
- [Pro San 的來源](https://fonts.google.com/specimen/Source+Sans+Pro)
