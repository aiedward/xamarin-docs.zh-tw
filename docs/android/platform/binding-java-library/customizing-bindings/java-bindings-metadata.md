---
title: Java 繫結中繼資料
description: 'Xamarin 中的 c # 程式碼會透過系結呼叫 JAVA 程式庫，這是一種機制，可抽象化 JAVA 原生介面（JNI）中指定的低層級詳細資料。 Xamarin 提供產生這些系結的工具。 這項工具可讓開發人員控制如何使用中繼資料來建立系結，這可讓您修改命名空間和重新命名成員等程式。 本檔討論中繼資料的運作方式、匯總中繼資料支援的屬性，以及說明如何藉由修改此中繼資料來解決系結問題。'
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 2a88888b2306589930ad6386fb69bbd3b48924b7
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571372"
---
# <a name="java-bindings-metadata"></a>Java 繫結中繼資料

_Xamarin 中的 c # 程式碼會透過系結呼叫 JAVA 程式庫，這是一種機制，可抽象化 JAVA 原生介面（JNI）中指定的低層級詳細資料。Xamarin 提供產生這些系結的工具。這項工具可讓開發人員控制如何使用中繼資料來建立系結，這可讓您修改命名空間和重新命名成員等程式。本檔討論中繼資料的運作方式、匯總中繼資料支援的屬性，以及說明如何藉由修改此中繼資料來解決系結問題。_

## <a name="overview"></a>概觀

Xamarin. Android **JAVA**系結程式庫會嘗試將系結現有 Android 程式庫所需的大部分工作，與有時稱為系結產生_器的工具協助自動化。_ 當系結 JAVA 程式庫時，Xamarin 會檢查 JAVA 類別，並產生所有要系結的封裝、類型和成員的清單。 此 Api 清單會儲存在 XML 檔案中，此檔案可在**發行**組建的** \{ project directory} \obj\Release\api.xml**中找到，而在**Debug**組建的** \{ 專案目錄} \obj\Debug\api.xml**中。

![在 obj/Debug 資料夾中，api .xml 檔案的位置](java-bindings-metadata-images/java-bindings-metadata-01.png)

系結產生器會使用**api .xml**檔案，作為建立必要 c # 包裝函式類別的指導方針。 這個 XML 檔案的內容是 Google 的_Android 開放原始碼專案_格式的變化。
下列程式碼片段是**api**內容的範例：

```xml
<api>
    <package name="android">
        <class abstract="false" deprecated="not deprecated" extends="java.lang.Object"
            extends-generic-aware="java.lang.Object" 
            final="true" 
            name="Manifest" 
            static="false" 
            visibility="public">
            <constructor deprecated="not deprecated" final="false"
                name="Manifest" static="false" type="android.Manifest"
                visibility="public">
            </constructor>
        </class>
...
</api>
```

在此範例中， **api**會在封裝中宣告 `android` 名為的類別，以 `Manifest` 擴充 `java.lang.Object` 。

在許多情況下，需要人工協助，讓 JAVA API 感覺更像「.NET」，或是更正導致系結元件無法編譯的問題。 例如，您可能需要將 JAVA 封裝名稱變更為 .NET 命名空間、重新命名類別，或變更方法的傳回型別。

您無法直接修改**api .xml**來完成這些變更。
相反地，變更會記錄在 JAVA 系結程式庫範本所提供的特殊 XML 檔案中。 在編譯 Xamarin. Android 系結元件時，系結產生器會在建立系結元件時受到這些對應檔的影響。

您可以在專案的 [**轉換**] 資料夾中找到這些 XML 對應檔案：

- **MetaData** &ndash; 允許對最終 API 進行變更，例如變更所產生系結的命名空間。 

- **EnumFields** &ndash; 包含 JAVA `int` 常數與 c # 之間的對應 `enums` 。 

- **EnumMethods** &ndash; 可讓您將方法參數和傳回類型從 JAVA `int` 常數變更為 c # `enums` 。 

**中繼資料 .xml**檔案最容易匯入這些檔案，因為它允許對系結進行一般用途的變更，例如：

- 重新命名命名空間、類別、方法或欄位，使其遵循 .NET 慣例。 

- 移除不需要的命名空間、類別、方法或欄位。 

- 將類別移至不同的命名空間。 

- 新增其他支援類別，讓系結的設計遵循 .NET framework 模式。 

讓我們繼續討論**中繼資料**的詳細資訊。

## <a name="metadataxml-transform-file"></a>中繼資料 .xml 轉換檔案

如同我們已瞭解的，系結產生器會使用檔案**Metadata .xml**來影響系結元件的建立。
元資料格式使用[XPath](https://www.w3.org/TR/xpath/)語法，幾乎與[GAPI 中繼資料](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)指南中所述的*GAPI 中繼資料*完全相同。 這項實現幾乎是完整的 XPath 1.0 實作為，因此支援1.0 標準中的專案。 此檔案是功能強大的 XPath 型機制，可變更、新增、隱藏或移動 API 檔案中的任何元素或屬性。 中繼資料規格中的所有規則元素都包含 path 屬性，用以識別要套用規則的節點。 規則會依照下列順序套用：

- **新增節點** &ndash;將子節點附加至 path 屬性所指定的節點。
- **attr** &ndash;設定 path 屬性所指定之元素的屬性值。
- **移除-node** &ndash;移除符合指定 XPath 的節點。

以下是**中繼資料 .xml**檔案的範例：

```xml
<metadata>
    <!-- Normalize the namespace for .NET -->
    <attr path="/api/package[@name='com.evernote.android.job']" 
        name="managedName">Evernote.AndroidJob</attr>

    <!-- Don't  need these packages for the Xamarin binding/public API --> 
    <remove-node path="/api/package[@name='com.evernote.android.job.v14']" />
    <remove-node path="/api/package[@name='com.evernote.android.job.v21']" />

    <!-- Change a parameter name from the generic p0 to a more meaningful one. -->
    <attr path="/api/package[@name='com.evernote.android.job']/class[@name='JobManager']/method[@name='forceApi']/parameter[@name='p0']" 
        name="name">api</attr>
</metadata>
```

以下列出 JAVA API 的一些較常用的 XPath 元素：

- `interface`&ndash;用來尋找 JAVA 介面。 例如 `/interface[@name='AuthListener']`。

- `class`&ndash;用來尋找類別。 例如 `/class[@name='MapView']`。

- `method`&ndash;用來尋找 JAVA 類別或介面上的方法。 例如 `/class[@name='MapView']/method[@name='setTitleSource']`。

- `parameter`&ndash;識別方法的參數。 例如，`/parameter[@name='p0']`

### <a name="adding-types"></a>加入類型

`add-node`元素會告訴 Xamarin Android 系結專案，將新的包裝函式類別加入至**api .xml**。 例如，下列程式碼片段會指示系結產生器，以建立具有一個和一個單一欄位的類別：

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```

### <a name="removing-types"></a>移除類型

您可以指示 Xamarin 的系結產生器忽略 JAVA 類型，而不系結它。 這是藉由將 `remove-node` XML 元素新增至**中繼資料 .xml**檔案來完成：

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>重新命名成員

無法直接編輯**config.xml**檔案來重新命名成員，因為 Xamarin 需要原始 JAVA 原生介面（JNI）名稱。 因此， `//class/@name` 無法改變屬性; 如果是，則系結將無法使用。

請考慮我們想要重新命名類型的情況 `android.Manifest` 。
若要完成此動作，我們可能會嘗試直接編輯**config.xml** ，並將類別重新命名，如下所示：

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

這會導致系結產生器為包裝函式類別建立下列 c # 程式碼：

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

請注意，包裝函式類別已重新命名為 `NewName` ，而原始 JAVA 類型仍然是 `Manifest` 。 Xamarin. Android 系結類別無法再存取上的任何方法，包裝函式 `android.Manifest` 類別會系結至不存在的 JAVA 類型。

若要適當地變更已包裝型別（或方法）的 managed 名稱，必須設定屬性， `managedName` 如下列範例所示：

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes"></a>

#### <a name="renaming-eventarg-wrapper-classes"></a>重新命名包裝函式 `EventArg` 類別

當 Xamarin 的系結產生器識別接聽程式 `onXXX` _類型_的 setter 方法時，將會產生 c # 事件和子 `EventArgs` 類別，以支援以 JAVA 為基礎的接聽程式模式的 .net flavoured API。 例如，請考慮下列 JAVA 類別和方法：

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin 會 `on` 從 setter 方法中卸載前置詞，而改為使用 `2DSignNextManuever` 作為子類別名稱的基礎 `EventArgs` 。 子類別會命名為類似下列內容：

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

這不是合法的 c # 類別名稱。 若要修正這個問題，系結作者必須使用 `argsType` 屬性，並為子類別提供有效的 c # 名稱 `EventArgs` ：

```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

## <a name="supported-attributes"></a>支援的屬性

下列各節將說明一些用於轉換 JAVA Api 的屬性。

### <a name="argstype"></a>argsType

這個屬性會放在 setter 方法上，以命名 `EventArg` 將產生來支援 JAVA 接聽程式的子類別。 在本指南稍後的重新[命名 EventArg 包裝](#Renaming_EventArg_Wrapper_Classes)函式類別一節中，將會更詳細地說明這一點。

### <a name="eventname"></a>eventName

指定事件的名稱。 如果是空的，則會禁止產生事件。
這在標題重新[命名 EventArg 包裝](#Renaming_EventArg_Wrapper_Classes)函式類別一節中有更詳細的說明。

### <a name="managedname"></a>managedName

這是用來變更封裝、類別、方法或參數的名稱。 例如，若要將 JAVA 類別的名稱變更 `MyClass` 為 `NewClassName` ：

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

下一個範例說明用來將方法重新命名為的 XPath 運算式 `java.lang.object.toString` `Java.Lang.Object.NewManagedName` ：

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>managedType

`managedType`是用來變更方法的傳回型別。 在某些情況下，系結產生器會錯誤地推斷 JAVA 方法的傳回型別，這會導致編譯時期錯誤。 在此情況下，其中一個可行的解決方案是變更方法的傳回型別。

例如，系結產生器會認為 JAVA 方法應該傳回， `de.neom.neoreadersdk.resolution.compareTo()` `int` 並採用做 `Object` 為參數，這會導致錯誤訊息**錯誤 CS0535： ' DE。Neom. Neoreadersdk 解析 ' 不會執行介面成員 ' CompareTo （JAVA. Lang.ini. Object） '**。 下列程式碼片段示範如何將產生之 c # 方法的第一個參數類型從變更 `DE.Neom.Neoreadersdk.Resolution` 為 `Java.Lang.Object` ： 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]" name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

變更方法的傳回型別。 這不會變更傳回屬性（因為傳回屬性的變更可能會導致不相容的 JNI 簽章變更）。 在下列範例中，方法的傳回型別 `append` 從變更 `SpannableStringBuilder` 為（請 `IAppendable` 回想，c # 不支援協變數傳回類型）：

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>處理

模糊 JAVA 程式庫的工具可能會干擾 Xamarin. Android 系結產生器，以及它產生 c # 包裝函式類別的能力。 模糊類別的特性包括： 

- 類別名稱包含 **$** ，亦即 **$ 類別**。
- 類別名稱完全洩露小寫字元，亦即**類別**

此程式碼片段是如何產生「未混淆」 c # 類型的範例：

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

這個屬性可以用來變更 managed 屬性的名稱。

使用的特殊案例 `propertyName` 包含 JAVA 類別只有欄位的 getter 方法的情況。 在這種情況下，系結產生器會想要建立一個僅限寫入的屬性，這在 .NET 中不建議這樣做。 下列程式碼片段顯示如何藉由將設定為空字串，來「移除」 .NET 屬性 `propertyName` ：

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

請注意，setter 和 getter 方法仍會由系結產生器建立。

### <a name="sender"></a>傳送者

指定方法對應至事件時，方法的哪一個參數應為 `sender` 參數。 此值可以是 `true` 或 `false` 。 例如：

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>可見性

這個屬性是用來變更類別、方法或屬性的可見度。 例如，您可能需要升級 `protected` JAVA 方法，使其對應的 c # 包裝函式為 `public` ：

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields .xml 和 EnumMethods .xml

在某些情況下，Android 程式庫會使用整數常數來代表傳遞給程式庫屬性或方法的狀態。 在許多情況下，將這些整數常數系結至 c # 中的列舉會很有用。 若要加速此對應，請使用系結專案中的**EnumFields**和**EnumMethods。** 

### <a name="defining-an-enum-using-enumfieldsxml"></a>使用 EnumFields 定義列舉

**EnumFields**檔案包含 JAVA `int` 常數與 c # 之間的對應 `enums` 。 讓我們針對一組常數建立 c # 列舉的下列範例 `int` ： 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

在這裡，我們已採用 JAVA 類別 `SKRealReachSettings` ，並 `SKMeasurementUnit` 在命名空間中定義名為的 c # 列舉 `Skobbler.Ngx.Map.RealReach` 。 `field`專案會定義 JAVA 常數的名稱（範例 `UNIT_SECOND` ）、列舉專案的名稱（範例 `Second` ），以及這兩個實體所表示的整數值（範例 `0` ）。 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>使用 EnumMethods 定義 Getter/Setter 方法

**EnumMethods**可讓您將方法參數和傳回類型從 JAVA 常數變更 `int` 為 c # `enums` 。 換句話說，它會將 c # 列舉的讀取和寫入（定義于**EnumFields**中）對應到 JAVA `int` 常數 `get` 和 `set` 方法。

假設先前 `SKRealReachSettings` 定義的列舉，下列**EnumMethods**會定義此列舉的 getter/setter：

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

第一行會將 `method` JAVA 方法的傳回值對應 `getMeasurementUnit` 至 `SKMeasurementUnit` 列舉。 第二 `method` 行會將的第一個參數對應 `setMeasurementUnit` 至相同的列舉。

當所有這些變更都備妥之後，您就可以在 Xamarin 中使用下列程式碼來設定 `MeasurementUnit` ： 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```

## <a name="summary"></a>總結

本文討論了 Xamarin 如何使用中繼資料來轉換來自*Google* *AOSP 格式*的 API 定義。 在涵蓋可能使用*中繼資料*的變更之後，它會檢查重新命名成員時所遇到的限制，並提供支援的 xml 屬性清單，描述每個屬性的使用時機。

## <a name="related-links"></a>相關連結

- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [繫結 Java 程式庫](~/android/platform/binding-java-library/index.md)
- [GAPI 中繼資料](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
