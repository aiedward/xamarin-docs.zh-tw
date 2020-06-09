---
title: 字型
ms.prod: xamarin
ms.assetid: 3F543FC5-FDED-47F8-8D2C-481FCC98BFDA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/09/2018
ms.openlocfilehash: daeebc4d1531e340b305b810096b72094ab9230d
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84566366"
---
# <a name="fonts"></a>字型

## <a name="overview"></a>概觀

從 API 層級26開始，Android SDK 允許將字型視為資源，就像版面配置或可繪製資源一樣。 [Android 支援程式庫 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/26.1.0.1)會將新的字型 API 將至以 API 層級14或更高版本為目標的應用程式。

以 API 26 為目標或安裝 Android 支援程式庫 v26 之後，有兩種方式可在 Android 應用程式中使用字型：

1. **將字型封裝為 Android 資源** &ndash;這可確保此字型一律可供應用程式使用，但會增加 APK 的大小。
2. **下載字型** &ndash;Android 也支援從_字型提供者_下載字型。 字型提供者會檢查字型是否已存在於裝置上。 如有必要，字型將會下載並快取在裝置上。 此字型可以在多個應用程式之間共用。

類似的字型（或可能有數個不同樣式的字型）可能會分組成_字型家族_。 這可讓開發人員指定字型的某些屬性（例如權數），而 Android 會自動從字型系列選取適當的字型。

Android 支援程式庫 v26 會將對 API 層級26的字型支援。 以較舊的 API 層級為目標時，必須宣告 `app` XML 命名空間，並使用 `android:` 命名空間和命名空間來命名各種字型屬性 `app:` 。 如果只 `android:` 使用命名空間，則字型不會顯示執行 API 層級25或更少的裝置。 例如，此 XML 程式碼片段會宣告可在 API 層級14和更高版本中使用的新[_字型系列_](#font_families)資源：

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

只要以適當方式將字型提供給 Android 應用程式，就可以藉由設定[ `fontFamily` 屬性](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)來套用至 UI widget。 例如，下列程式碼片段示範如何在 TextView 中顯示字型：

```xml
<TextView
    android:text="The quick brown fox jumped over the lazy dog."
    android:fontFamily="@font/sourcesanspro_regular"
    app:fontFamily="@font/sourcesanspro_regular"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

本指南會先討論如何使用字型作為 Android 資源，然後繼續討論如何在執行時間下載字型。

## <a name="fonts-as-a-resource"></a>作為資源的字型

將字型封裝成 Android APK，可確保應用程式一律可供使用。 字型檔案（或）。.TTF 或。OTF 檔案）會新增至 Xamarin Android 應用程式，就像任何其他資源一樣，只要將檔案複製到 Xamarin 專案之**Resources**資料夾中的子目錄即可。 字型資源會保留在專案的 [**資源**] 資料夾的**字型**子目錄中。

> [!NOTE]
> 字型應具有 [ **AndroidResource** ] 的 [**組建] 動作**，否則不會封裝到最終的 APK 中。 組建動作應由 IDE 自動設定。

當有許多類似的字型檔案（例如，具有不同權數或樣式的相同字型）時，可以將它們分組為字型系列。

<a name="font_families"></a>

### <a name="font-families"></a>字型系列

字型家族是一組具有不同權數和樣式的字型。 例如，可能會有不同的字型檔案用於粗體或斜體字體。 字型系列是由 XML 檔案 `font` 中保留在**Resources/font-size**目錄中的元素所定義。 每個字型系列都應該有自己的 XML 檔案。

若要建立字型家族，請先將所有字型新增至**Resources/font-size**資料夾。 然後在字型系列的字型資料夾中建立新的 XML 檔案。 XML 檔案的名稱與所參考的字型沒有相似性或關聯性;資源檔可以是任何合法的 Android 資源檔名稱。 這個 XML 檔案會有一個根 `font-family` 元素，其中包含一或多個 `font` 元素。 每個元素都會宣告 `font` 字型的屬性。

下列 XML 是_來源 San Pro_字型的字型家族範例，其定義了許多不同的字型粗細。 這會儲存為 [資源] **/[字型**] 資料夾中名為**sourcesanspro**的檔案：

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

- **正常** &ndash;一般字型
- **斜體** &ndash;斜體字型

`fontWeight`屬性會對應至 CSS `font-weight` 屬性，並參考字型的粗細。 這是 100-900 範圍內的值。 下列清單描述常見的字型粗細值和其名稱：

- **精簡** &ndash;100
- **格外輕** &ndash;200
- **光線** &ndash;300
- **正常** &ndash;400
- **中** &ndash;500
- **半粗體** &ndash;600
- **粗體** &ndash;700
- **額外粗體** &ndash;800
- **黑色** &ndash;900

一旦定義了字型系列，就可以在配置檔案 `fontFamily` 中設定、 `textStyle` 和屬性，以宣告方式使用它 `fontWeight` 。  例如，下列 XML 程式碼片段會設定600權數位型（一般）和斜體文字樣式：

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

### <a name="programmatically-assigning-fonts"></a>以程式設計方式指派字型

您可以使用方法，以程式設計方式設定字型 [`Resources.GetFont`](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int)) ，以取得 [`Typeface`](https://developer.android.com/reference/android/graphics/Typeface.html) 物件。 許多視圖都有 `TypeFace` 可用來將字型指派給 widget 的屬性。 此程式碼片段示範如何以程式設計方式在 TextView 上設定字型：

```csharp
Android.Graphics.Typeface typeface = this.Resources.GetFont(Resource.Font.caveat_regular);
textView1.Typeface = typeface;
textView1.Text = "Changed the font";
```

`GetFont`方法會自動載入字型系列內的第一個字型。  若要載入符合特定樣式的字型，請使用 `Typeface.Create` 方法。 這個方法會嘗試載入符合指定樣式的字型。 例如，此程式碼片段會嘗試 `Typeface` 從 [**資源/** 字型] 中定義的字型家族載入粗體物件：

```csharp
var typeface = Typeface.Create("<FONT FAMILY NAME>", Android.Graphics.TypefaceStyle.Bold);
textView1.Typeface = typeface;
```

## <a name="downloading-fonts"></a>下載字型

Android 可以從遠端來源下載字型，而不是將字型封裝為應用程式資源。 這會產生減少 APK 大小的理想效果。

_字型提供者_可協助您下載字體。 這是專門的內容提供者，可管理將字型下載和快取到裝置上的所有應用程式。 Android 8.0 包含字型提供者，可從[Google 字型存放庫](https://fonts.google.com)下載字體。 此預設字型提供者會使用 Android 支援程式庫 v26 backport 至 API 層級14。

當應用程式對字型提出要求時，字型提供者會先檢查字型是否已在裝置上。 如果不是，則會嘗試下載字型。 如果無法下載字型，則 Android 會使用預設系統字型。 下載字型之後，裝置上的所有應用程式都可以使用它，而不只是提出初始要求的應用程式。

提出要求以下載字型時，應用程式不會直接查詢字型提供者。 相反地，應用程式會使用 API 的實例 [`FontsContract`](https://developer.android.com/reference/android/provider/FontsContract.html) （或者， [`FontsContractCompat`](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html) 如果使用支援程式庫26，則為）。  

Android 8.0 支援以兩種不同的方式下載字型：

1. **將可下載的字型宣告為資源** &ndash;應用程式可以透過 XML 資源檔，將可下載的字型宣告為 Android。 這些檔案會包含 Android 在應用程式啟動時需要以非同步方式下載字型，並將其快取在裝置上的所有中繼資料。
2. **以** &ndash; 程式設計Android API 層級26中的 Api 可讓應用程式在執行應用程式時，以程式設計方式下載字型。 應用程式會 `FontRequest` 為指定的字型建立物件，並將此物件傳遞給 `FontsContract` 類別。 `FontsContract`會採用 `FontRequest` ，並從_字型提供者_抓取字型。 Android 會以同步方式下載字型。 `FontRequest`本指南稍後會顯示建立的範例。

不論使用哪種方法，都必須先新增到 Xamarin Android 應用程式的資源檔，才能下載字型。 首先，必須在**Resources/font-size**目錄中的 XML 檔案中宣告字型，做為字型系列的一部分。 此程式碼片段是一個範例，說明如何使用 Android 8.0 隨附的預設字型提供者（或支援程式庫 v26），從[Google Fonts 開放原始碼集合](https://fonts.google.com)下載字型：

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

`font-family`元素包含下列屬性，宣告 Android 下載字型所需的資訊：

1. **fontProviderAuthority** &ndash;要用於要求的字型提供者的授權單位。
2. **fontPackage** &ndash;要用於要求的字型提供者封裝。 這是用來驗證提供者的身分識別。
3. **fontQuery** &ndash;這是一個字串，可協助字型提供者尋找所要求的字型。 字型查詢的詳細資料是字型提供者特有的。 可下載的字型 [`QueryBuilder`](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) 範例[Downloadable Fonts](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/)應用程式中的類別會針對來自 Google 字型開放原始碼集合的字型，提供一些查詢格式的相關資訊。
4. **fontProviderCerts** &ndash; 資源陣列，其中包含應用來簽署提供者之憑證的雜湊集清單。

一旦定義了字型，就可能需要提供有關下載所涉及之_字型憑證_的資訊。

### <a name="font-certificates"></a>字型憑證

如果裝置上未預先安裝字型提供者，或是應用程式使用連結 `Xamarin.Android.Support.Compat` 庫，則 Android 會要求字型提供者的安全性憑證。 這些憑證會列在保留在**Resources/values**目錄中的陣列資源檔中。

例如，下列 XML 會命名為**Resources/values/fonts_cert .xml** ，並儲存 Google 字型提供者的憑證：

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

這些資源檔備妥之後，應用程式就能夠下載字型。

### <a name="declaring-downloadable-fonts-as-resources"></a>將可下載的字型宣告為資源

藉由在**androidmanifest.xml**中列出可下載的字型，Android 會在應用程式第一次啟動時，以非同步方式下載字型。 字型本身會列在陣列資源檔中，如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="downloadable_fonts" translatable="false">
        <item>@font/vt323</item>
    </array>
</resources>
```

若要下載這些字型，必須藉由新增做為專案的子系，在**androidmanifest.xml**中宣告這些字型 `meta-data` `application` 。 例如，如果可下載的字型是在資源 **/值/downloadable_fonts .xml**的資源檔中宣告，則必須將此程式碼片段新增至資訊清單：

```xml
<meta-data android:name="downloadable_fonts" android:resource="@array/downloadable_fonts" />
```

### <a name="downloading-a-font-with-the-font-apis"></a>使用字型 Api 下載字型

您可以藉由具現化物件並將其 [`FontRequest`](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html) 傳遞給方法，以程式設計方式下載字型 `FontContractCompat.RequestFont` 。 `FontContractCompat.RequestFont`方法會先檢查裝置上是否有字型，然後視需要以非同步方式查詢字型提供者，並嘗試下載該應用程式的字型。 如果 `FontRequest` 無法下載字型，則 Android 會使用預設系統字型。

`FontRequest`物件包含字型提供者所使用的資訊，以找出並下載字型。 `FontRequest`需要四個資訊片段：

1. **字型提供者授權** &ndash; 單位要用於要求的字型提供者的授權單位。
2. **字型套件** &ndash;要用於要求的字型提供者封裝。 這是用來驗證提供者的身分識別。
3. **字型查詢** &ndash;這是一個字串，可協助字型提供者尋找所要求的字型。 字型查詢的詳細資料是字型提供者特有的。 此字串的詳細資料是字型提供者特有的。 可下載的字型 [`QueryBuilder`](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) 範例[Downloadable Fonts](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/)應用程式中的類別會針對來自 Google 字型開放原始碼集合的字型，提供一些查詢格式的相關資訊。
4. **字型提供者憑證** &ndash; 資源陣列，其中包含要用來簽署提供者之憑證的雜湊集清單。

此程式碼片段是具現化新物件的範例 `FontRequest` ：

```csharp
FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms", <FontToDownload>, Resource.Array.com_google_android_gms_fonts_certs);
```

在上一個程式碼片段中，有 `FontToDownload` 一個查詢會協助來自 Google Fonts 開放原始碼集合的字型。

將傳遞 `FontRequest` 至方法之前 `FontContractCompat.RequestFont` ，必須先建立兩個物件：

- **`FontsContractCompat.FontRequestCallback`**&ndash;這是必須擴充的抽象類別。 它是一種回呼，會在完成時叫用 `RequestFont` 。 Xamarin Android 應用程式必須子類別化 `FontsContractCompat.FontRequestCallback` 並覆寫 `OnTypefaceRequestFailed` 和 `OnTypefaceRetrieved` ，以提供在下載失敗或分別成功時所要採取的動作。
- **`Handler`**&ndash;這是 `Handler` 將用 `RequestFont` 來線上程上下載字型的（如有必要）。 字型**不**應在 UI 執行緒上下載。

此程式碼片段是 c # 類別的範例，它會以非同步方式從 Google Fonts 的開放原始碼集合下載字型。 它會執行 `FontRequestCallback` 介面，並在完成時引發 c # 事件 `FontRequest` 。

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

若要使用此 helper， `FontDownloadHelper` 會建立新的，並指派事件處理常式：  

```csharp
var fontHelper = new FontDownloadHelper();

fontHelper.FontDownloaded += (object sender, FontDownloadEventArg e) =>
{
    //React to the request
};
fontHelper.DownloadFonts(this); // this is an Android Context instance.
```

## <a name="summary"></a>總結

本指南討論 Android 8.0 中的新 Api，以支援可下載的字型和字型作為資源。 它討論了如何將現有的字型內嵌在 APK 中，並在版面配置中使用它們。 同時也會討論 Android 8.0 如何以程式設計方式或在資源檔中宣告字型中繼資料，來支援從字型提供者下載字體。

## <a name="related-links"></a>相關連結

- [fontFamily](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)
- [FontConfig](https://developer.android.com/reference/android/text/FontConfig.html)
- [FontRequest](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html)
- [FontsContractCompat](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html)
- [Resources. Ivsfontandcolorstorage.getfont](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int))
- [字樣](https://developer.android.com/reference/android/graphics/Typeface.html)
- [Android 支援程式庫 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/)
- [在 Android 中使用字型](https://www.youtube.com/watch?v=TfB-TsLFJdM)
- [CSS 字型粗細規格](https://www.w3.org/TR/css-fonts-3/#font-weight-numeric-values)
- [Google 字型開放原始碼集合](https://fonts.google.com/)
- [來源 San Pro](https://fonts.google.com/specimen/Source+Sans+Pro)
