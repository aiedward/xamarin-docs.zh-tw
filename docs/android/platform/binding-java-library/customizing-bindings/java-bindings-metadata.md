---
title: "Java 繫結中繼資料"
description: "C# 程式碼中 Xamarin.Android 繫結，是一種機制，以區隔會指定在 Java 原生介面 (JNI) 的低層級詳細資料會透過呼叫 Java 文件庫。 Xamarin.Android 提供一種工具，會產生這些繫結。 此工具可讓開發人員控制項繫結使用的中繼資料，可讓程序，例如修改命名空間，以及重新命名成員的建立方式。 本文件討論中繼資料的運作方式，該中繼資料摘要的屬性支援，並說明如何藉由修改這個中繼資料來解決繫結的問題。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 91e27fcaef0ef1b262eceecd4d3c71bac34e328d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="java-bindings-metadata"></a>Java 繫結中繼資料

_C# 程式碼中 Xamarin.Android 繫結，是一種機制，以區隔會指定在 Java 原生介面 (JNI) 的低層級詳細資料會透過呼叫 Java 文件庫。Xamarin.Android 提供一種工具，會產生這些繫結。此工具可讓開發人員控制項繫結使用的中繼資料，可讓程序，例如修改命名空間，以及重新命名成員的建立方式。本文件討論中繼資料的運作方式，該中繼資料摘要的屬性支援，並說明如何藉由修改這個中繼資料來解決繫結的問題。_

<a name="Overview" />

## <a name="overview"></a>總覽

Xamarin.Android **Java 繫結的程式庫**來自動化許多現有的 Android 程式庫的協助工具，有時也稱為繫結所需的工作會嘗試_繫結產生器_。 Xamarin.Android 繫結時的 Java 程式庫，將會檢查 Java 類別，並產生一份所有封裝、 類型和成員的繫結。 此應用程式開發介面的清單儲存在 XML 檔案，請參閱**\{專案 directory}\obj\Release\api.xml**如**發行**建置和**\{專案directory}\obj\Debug\api.xml**如**偵錯**建置。

![Api.xml 檔案 obj/Debug 資料夾中的位置](java-bindings-metadata-images/java-bindings-metadata-01.png)

繫結產生器將使用**api.xml**檔案當做指導方針來產生必要的 C# 包裝函式類別。 這個 XML 檔案的內容是 Google 的變化_Android 開放原始碼專案_格式。
下列程式碼片段是內容的範例**api.xml**:

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

在此範例中， **api.xml**宣告中的類別`android`名為套件`Manifest`延伸`java.lang.Object`。

在許多情況下，人力協助無須覺得詳細 <.NET 像 Java api，或更正使編譯的組件繫結的問題。 例如，可能需要先將 Java 封裝名稱變更為.NET 命名空間、 類別、 重新命名或變更方法的傳回型別。

這些變更不會達成修改**api.xml**直接。
相反地，變更都會記錄在特殊的 XML 檔案所提供的 Java 繫結的程式庫範本。 當編譯 Xamarin.Android 繫結組件，繫結產生器會受到這些對應檔案時建立繫結組件

這些 XML 對應檔案，請參閱**轉換**專案的資料夾：

-   **MetaData.xml** &ndash;允許進行最後的 api，例如變更產生的繫結的命名空間變更。 

-   **EnumFields.xml** &ndash;包含 Java 之間的對應`int`常數和 C# `enums` 。 

-   **EnumMethods.xml** &ndash;允許變更的方法參數和傳回型別從 Java`int`常數至 C# `enums` 。 

**MetaData.xml**檔案是最匯入這些檔案，因為它可讓一般用途的變更，例如繫結：

-   請重新命名的命名空間、 類別、 方法或欄位，讓它們遵循下列.NET 慣例。 

-   命名空間、 類別、 方法或移除不必要的欄位。 

-   將類別移到不同的命名空間。 

-   加入的其他支援類別，讓繫結的設計會依照.NET framework 模式。 

可讓您移至討論**Metadata.xml**中更多詳細資料。

<a name="Metadata.xml_Transform_File" />

## <a name="metadataxml-transform-file"></a>Metadata.xml 轉換檔

因為我們已經學會，檔案**Metadata.xml**可由繫結產生器來建立繫結組件的影響。
中繼資料格式會使用[XPath](https://www.w3.org/TR/xpath/)語法，且幾乎完全相同*GAPI 中繼資料*述[GAPI 中繼資料](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)指南。 這個實作是幾乎完整實作的 XPath 1.0，因此在標準 1.0 支援項目。 這個檔案是強大的 XPath 基礎機制，以變更、 加入、 隱藏或移動 API 檔案中的任何元素或屬性。 所有規則項目中繼資料規格中包含的路徑屬性來識別所要套用規則的節點。 規則會套用順序如下：

* **加入節點**&ndash;將子節點附加至 path 屬性所指定的節點。
* **attr** &ndash;設定路徑屬性所指定的項目屬性的值。
* **移除節點**&ndash;移除符合指定的 XPath 節點。

以下是範例**Metadata.xml**檔案：

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

以下列出的某些較常用的 XPath 項目 Java api:

-   `interface` &ndash; 用來尋找 Java 介面。 例如`/interface[@name='AuthListener']`。

-   `class` &ndash; 用來尋找類別。 例如`/class[@name='MapView']`。

-   `method` &ndash; 用於找出的 Java 類別或介面上的方法。 例如`/class[@name='MapView']/method[@name='setTitleSource']`。

-   `parameter` &ndash; 找出方法的參數。 例如： `/parameter[@name='p0']`


<a name="ADDING_TYPES" />

### <a name="adding-types"></a>加入型別

`add-node`項目會告訴 Xamarin.Android 繫結專案，以便將新的包裝函式類別來**api.xml**。 例如，下列程式碼片段會直接繫結產生器建立的類別建構函式與單一欄位：

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```

<a name="REMOVING_TYPES" />

### <a name="removing-types"></a>移除型別

很可能指示 Xamarin.Android 繫結產生器，以忽略 Java 類型並不繫結。 這是藉由新增`remove-node`XML 項目來**metadata.xml**檔案：

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

<a name="Renaming_Members" />

### <a name="renaming-members"></a>重新命名成員

重新命名成員，無法執行直接編輯**api.xml**檔案，因為 Xamarin.Android 需要原始 Java 原生介面 (JNI) 名稱。 因此，`//class/@name`無法更改屬性; 如果是，繫結將無法運作。

假設我們要重新命名類型， `android.Manifest`。
若要達成此目的，我們可能會嘗試直接編輯**api.xml**和重新命名類別就像這樣：

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

這會導致建立下列 C# 類別的程式碼包裝函式繫結產生器：

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

請注意，包裝函式類別已重新命名為`NewName`，而原始的 Java 類型仍然是`Manifest`。 就無法再存取任何方法的 Xamarin.Android 繫結類別`android.Manifest`; 包裝函式類別繫結至不存在的 Java 類型。

若要正確地變更受管理的已包裝的 「 類型 」 （或稱 「 方法 」） 名稱，就必須設定`managedName`屬性在此範例所示：

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

#### <a name="renaming-eventarg-wrapper-classes"></a>重新命名`EventArg`包裝函式類別

Xamarin.Android 繫結產生器時識別`onXXX`setter 方法_接聽項型別_，C# 事件和`EventArgs`子類別將會產生以支援.NET flavoured API 的 Java 為基礎的接聽程式模式。 例如，請考慮下列的 Java 類別和方法：

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin.Android 將卸除前置詞`on`從 setter 方法，並改用`2DSignNextManuever`做為名稱的基礎`EventArgs`子類別。 子類別的名稱會是類似的項目：

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

這不是合法的 C# 類別名稱。 若要修正這個問題，必須使用繫結作者`argsType`屬性，並提供是 C# 的有效名稱`EventArgs`子類別：
 
```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

 
<a name="Supported_Attributes" />

## <a name="supported-attributes"></a>支援的屬性

下列各節說明一些轉換 Java 應用程式開發介面的屬性。

### <a name="argstype"></a>argsType

這個屬性位於 setter 方法，來命名`EventArg`支援 Java 接聽程式將產生的子類別。 這所述的區段中會更詳細探討[重新命名 EventArg 包裝函式類別](#Renaming_EventArg_Wrapper_Classes)以便稍後在本指南。

### <a name="eventname"></a>eventName

指定事件的名稱。 如果是空的它會禁止事件產生。
此說明章節標題中的更詳細地[重新命名 EventArg 包裝函式類別](#Renaming_EventArg_Wrapper_Classes)。

### <a name="managedname"></a>managedName

這用來變更封裝、 類別、 方法或參數的名稱。 例如若要變更的 Java 類別名稱`MyClass`至`NewClassName`:

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

下一個範例說明如何重新命名方法的 XPath 運算式`java.lang.object.toString`至`Java.Lang.Object.NewManagedName`:

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>managedType

`managedType` 用來變更方法的傳回型別。 在某些情況下繫結產生器將不正確推斷 Java 方法，而導致編譯時期錯誤的傳回型別。 在此情況下一個可能的解決方案時，變更方法的傳回型別。

比方說，繫結產生器認為 Java 方法`de.neom.neoreadersdk.resolution.compareTo()`應該傳回`int`，這會導致錯誤訊息**錯誤 CS0535: ' DE。Neom.Neoreadersdk.Resolution' 未實作介面成員 'Java.Lang.IComparable.CompareTo(Java.Lang.Object)'**。 下列程式碼片段示範如何變更產生 C# 中的方法的傳回型別`int`至`Java.Lang.Object`: 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]"name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

變更方法的傳回型別。 這不會變更傳回的屬性 （做為傳回的屬性會導致不相容變更 JNI 簽章的變更）。 在下列範例中，傳回型別`append`方法已從`SpannableStringBuilder`至`IAppendable`（請記得，C# 不支援 covariant 傳回型別）：

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>模糊化

模糊化 Java 文件庫的工具可能會干擾 Xamarin.Android 繫結產生器，以及其能力產生 C# 包裝函式類別。 模糊化類別的特性包括: * 類別名稱包含 **$** ，也就是**$.class** * 類別名稱完全受到個小寫字元，也就是**a.class**

這個程式碼片段是如何產生 「 未模糊化"C# 類型的範例：

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

這個屬性可以用來變更一種 managed 屬性的名稱。

使用的特殊的案例`propertyName`牽涉到其中的 Java 類別有只有一個欄位 getter 方法的情況。 在此情況下繫結產生器會想要建立唯寫屬性，建議您在.NET 中的項目。 下列程式碼片段示範如何藉由設定 [移除].NET 屬性`propertyName`為空字串：

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

請注意，將仍繫結器所建立的 setter 與 getter 方法。

### <a name="sender"></a>sender

指定方法的參數應該是`sender`參數對應到事件的方法。 這個值可以是`true`或`false`。 例如: 

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>可視性

這個屬性用於變更類別、 方法或屬性的可見性。 例如，可能需要升級`protected`Java 方法，讓它對應 C# 包裝函式是`public`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields.xml 和 EnumMethods.xml

一些情況下，Android 程式庫會使用整數常數來表示要傳遞給屬性或方法的程式庫的狀態。 在許多情況下，最好將這些整數常數繫結至 C# 中的列舉。 若要簡化此對應，使用**EnumFields.xml**和**EnumMethods.xml**繫結專案中的檔案。 

### <a name="defining-an-enum-using-enumfieldsxml"></a>定義使用 EnumFields.xml 列舉

**EnumFields.xml**檔案包含 Java 之間的對應`int`常數和 C# `enums`。 讓我們來看下列範例會建立一組 C# 列舉`int`常數： 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

這裡我們已經建立 Java 類別`SKRealReachSettings`和定義呼叫的 C# 列舉`SKRealReachSettings`命名空間中`Skobbler.Ngx.Map.RealReach`。 `field`項目定義之 Java 常數的名稱 (範例`UNIT\_SECOND`)，列舉項目的名稱 (範例`Second`)，以及這兩個實體所代表的整數值 (範例`0`)。 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>定義使用 EnumMethods.xml Getter/Setter 方法

**EnumMethods.xml**檔可讓 Java 從變更的方法參數和傳回型別`int`常數至 C# `enums`。 換句話說，它對應的讀取和寫入的 C# 列舉 (定義於**EnumFields.xml**檔案) java`int`常數`get`和`set`方法。

指定`SKRealReachSettings`列舉會定義下列上方**EnumMethods.xml**檔案會定義這個列舉 getter/setter:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

第一個`method`列對應之傳回值的 Java`getMeasurementUnit`方法`SKRealReachSettings`列舉。 第二個`method`列對應的第一個參數`setMeasurementUnit`至相同的列舉。

所有這些變更之後，您可以使用下列程式碼中 Xamarin.Android 設定`MeasurementUnit`: 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章討論過 Xamarin.Android 如何使用中繼資料轉換從一個 API 定義*Google* *AOSP 格式*。 涵蓋可能會變更之後使用*Metadata.xml*它會檢查重新命名成員時所遇到的限制，它會顯示支援的 XML 屬性清單，描述何時使用每個屬性。



## <a name="related-links"></a>相關連結

- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [繫結 Java 程式庫](~/android/platform/binding-java-library/index.md)
- [GAPI 中繼資料](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
