---
title: Java 繫結中繼資料
description: C#在 Xamarin.Android 中的程式碼會呼叫的 Java 程式庫，透過繫結，這是一種機制，用來擷取指定在 Java Native Interface (JNI) 的低階詳細資料。 Xamarin.Android 提供一種工具，會產生這些繫結。 此工具可讓開發人員控制項如何使用中繼資料，可讓程序，例如修改命名空間和重新命名成員，建立繫結。 本文件討論的中繼資料的運作方式，該中繼資料摘要的屬性支援，並說明如何藉由修改這個中繼資料解析繫結的問題。
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 06a7a3b00934b7a2f3eeb4fcfa6fc90071901ba0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60955653"
---
# <a name="java-bindings-metadata"></a>Java 繫結中繼資料

_C#在 Xamarin.Android 中的程式碼會呼叫的 Java 程式庫，透過繫結，這是一種機制，用來擷取指定在 Java Native Interface (JNI) 的低階詳細資料。Xamarin.Android 提供一種工具，會產生這些繫結。此工具可讓開發人員控制項如何使用中繼資料，可讓程序，例如修改命名空間和重新命名成員，建立繫結。本文件討論的中繼資料的運作方式，該中繼資料摘要的屬性支援，並說明如何藉由修改這個中繼資料解析繫結的問題。_


## <a name="overview"></a>總覽

Xamarin.Android **Java 繫結程式庫**嘗試自動執行許多工作所需的繫結現有的 Android 程式庫的工具，有時也稱為協助_繫結產生器_。 Xamarin.Android 繫結時的 Java 程式庫，將會檢查 Java 類別，並產生一份所有封裝、 類型和成員的繫結。 這份 Api 會儲存在 XML 檔案，請參閱 **\{專案 directory}\obj\Release\api.xml** 如 **發行** 建置和 **\{專案directory}\obj\Debug\api.xml** for **偵錯** 建置。

![Api.xml 檔案 obj/Debug 資料夾中的位置](java-bindings-metadata-images/java-bindings-metadata-01.png)

繫結產生器會使用**api.xml**做為指導方針來產生必要的檔案C#包裝函式類別。 此 XML 檔案的內容是 Google 的變化_Android Open Source Project_格式。
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

在許多情況下，人類的協助或會需要認為多個 「.NET 等 」 的 Java api 來更正問題，使繫結的組件無法編譯。 例如，可能需要變更.NET 命名空間的 Java 套件名稱、 重新命名類別，或變更方法的傳回型別。

這些變更不會藉由修改**api.xml**直接。
相反地，變更會記錄在特殊 Java 繫結程式庫範本所提供的 XML 檔案。 當編譯 Xamarin.Android 繫結組件，繫結產生器會受到這些對應檔案時建立的繫結組件

這些 XML 對應檔案中可能可以找到**轉換**專案資料夾：

-   **MetaData.xml** &ndash;允許同時連接到最終的 API，例如變更產生的繫結的命名空間的變更。 

-   **EnumFields.xml** &ndash;包含 Java 之間的對應`int`常數和C# `enums` 。 

-   **EnumMethods.xml** &ndash;允許變更方法參數和傳回型別，從 Java`int`常數C# `enums` 。 

**MetaData.xml**檔案是最匯入這些檔案，因為它可讓一般用途的變更，例如繫結：

-   請重新命名的命名空間、 類別、 方法或欄位，因此它們會遵循.NET 慣例。 

-   正在移除命名空間、 類別、 方法或不必要的欄位。 

-   移至不同的命名空間的類別。 

-   新增的其他支援類別，讓繫結的設計遵循.NET framework 模式。 

可讓 移至討論**Metadata.xml**中更多詳細資料。


## <a name="metadataxml-transform-file"></a>Metadata.xml 轉換檔

因為我們已經學，檔案**Metadata.xml**繫結產生器用來影響繫結組件的建立。
中繼資料格式會使用[XPath](https://www.w3.org/TR/xpath/)語法和幾乎等同*GAPI 中繼資料*中所述[GAPI 中繼資料](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)指南。 此實作是幾乎 XPath 1.0 的完整實作，並因此支援標準 1.0 中的 項目。 這個檔案是強大的 XPath 型機制，來變更、 加入、 隱藏或移動 API 檔案中的任何項目或屬性。 所有中繼資料規格中的規則項目都包含路徑屬性，以識別所要套用規則的節點。 依下列順序套用規則：

* **新增節點**&ndash;將子節點附加至 path 屬性所指定的節點。
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

以下列出一些較常用的 XPath 項目適用於 Java API:

-   `interface` &ndash; 用來找出 Java 介面。 例如`/interface[@name='AuthListener']`。

-   `class` &ndash; 用來尋找類別。 例如`/class[@name='MapView']`。

-   `method` &ndash; 用來找出上的 Java 類別或介面的方法。 例如`/class[@name='MapView']/method[@name='setTitleSource']`。

-   `parameter` &ndash; 找出方法的參數。 例如： `/parameter[@name='p0']`



### <a name="adding-types"></a>加入類型

`add-node`項目會告訴 Xamarin.Android 繫結專案，以便將新的包裝函式類別，來**api.xml**。 例如，下列程式碼片段會直接繫結產生器，以建立類別的建構函式和單一欄位：

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```


### <a name="removing-types"></a>移除類型

它可能會指示 Xamarin.Android 繫結產生器以略過 Java 類型並不將它繫結。 這是藉由新增`remove-node`XML 項目**metadata.xml**檔案：

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>重新命名成員

重新命名成員，無法執行直接編輯**api.xml**檔案，因為 Xamarin.Android 需要原始 Java Native Interface (JNI) 名稱。 因此，`//class/@name`無法改變屬性; 如果是，繫結將無法運作。

假設我們想要用來重新命名為型別， `android.Manifest`。
若要達成此目的，我們可能會嘗試直接編輯**api.xml**和重新命名類別就像這樣：

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

這會導致繫結產生器建立下列C#包裝函式類別的程式碼：

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

請注意，已重新命名為的包裝函式類別`NewName`，而原始 Java 類型仍然是`Manifest`。 就無法再存取任何方法上之 Xamarin.Android 繫結類別可能`android.Manifest`; 包裝函式類別繫結至不存在的 Java 類型。

若要正確地變更受管理的已包裝的 「 類型 」 （或稱 「 方法 」） 名稱，就必須設定`managedName`屬性在此範例中所示：

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>重新命名`EventArg`包裝函式類別

Xamarin.Android 繫結產生器的識別時`onXXX`setter 方法，如_接聽項型別_、C#事件並`EventArgs`子類別將會產生以支援.NET flavoured API 的 Java 為基礎接聽程式模式。 例如，請考慮下列的 Java 類別和方法：

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin.Android 會卸除前置詞`on`setter 方法，並改用`2DSignNextManuever`做為名稱的基礎`EventArgs`子類別。 子類別會命名為類似：

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

這不是合法C#類別的名稱。 若要修正這個問題，必須使用的繫結作者`argsType`屬性，並提供有效C#的名稱`EventArgs`子類別：
 
```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

 

## <a name="supported-attributes"></a>支援的屬性

下列各節描述一些轉換 Java Api 的屬性。

### <a name="argstype"></a>argsType

此屬性位於命名的 setter 方法`EventArg`系統將產生支援 Java 接聽程式的子類別。 這中更多的詳細資料如下一節中所述[重新命名 EventArg 包裝函式類別](#Renaming_EventArg_Wrapper_Classes)以便稍後在本指南。

### <a name="eventname"></a>eventName

指定事件的名稱。 如果是空的它會禁止事件產生。
此說明章節標題中的更詳細[重新命名 EventArg 包裝函式類別](#Renaming_EventArg_Wrapper_Classes)。

### <a name="managedname"></a>managedName

這用來變更封裝、 類別、 方法或參數的名稱。 若要變更之 Java 類別名稱的範例`MyClass`至`NewClassName`:

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

下一個範例說明 XPath 運算式來重新命名方法`java.lang.object.toString`至`Java.Lang.Object.NewManagedName`:

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>managedType

`managedType` 用來變更方法的傳回型別。 在某些情況下，繫結產生器會不正確地推斷 Java 方法，這會導致編譯時期錯誤的傳回型別。 一個可行的解決方案，在此情況下，就是變更方法的傳回型別。

比方說，繫結產生器會認為，Java 方法`de.neom.neoreadersdk.resolution.compareTo()`應該會傳回`int`，這會導致錯誤訊息**錯誤 CS0535:' DE。Neom.Neoreadersdk.Resolution' 未實作介面成員 'Java.Lang.IComparable.CompareTo(Java.Lang.Object)'**。 下列程式碼片段示範如何變更所產生的傳回類型C#方法從`int`要`Java.Lang.Object`: 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]"name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

變更方法的傳回型別。 這不會變更傳回的屬性 （如變更以傳回屬性的 JNI 簽章也可能造成不相容的變更）。 在下列範例中，傳回類型`append`方法已從`SpannableStringBuilder`要`IAppendable`(請記得，C#不支援 covariant 傳回型別):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>模糊處理

混淆 Java 程式庫的工具可能會干擾 Xamarin.Android 繫結產生器，而且它能夠產生C#包裝函式類別。 模糊化類別的特性包括： 

* 類別名稱會包含 **$** ，也就是 **$.class** 
* 類別名稱完全洩露的小寫字元，也就是**a.class**

此程式碼片段是如何產生的範例為 「 未經過混淆處理 」C#型別：

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

這個屬性可用來變更受管理的屬性名稱。

使用的特殊的案例`propertyName`牽涉到這種情況的其中一個 Java 類別有只有欄位的 getter 方法。 在此情況下，繫結產生器會想要建立唯寫屬性，不建議在.NET 中的項目。 下列程式碼片段示範如何藉由設定 [移除].NET 屬性`propertyName`設為空字串：

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

請注意，setter 與 getter 方法仍會繫結產生器所建立。

### <a name="sender"></a>sender

指定方法的參數應為`sender`當方法對應到事件的參數。 此值可以是`true`或`false`。 例如：

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>可視性

這個屬性用來變更可見性的類別、 方法或屬性。 例如，可能需要升級`protected`Java 方法，讓它的對應C#包裝函式會`public`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields.xml 和 EnumMethods.xml

有 Android 程式庫使用整數常數來表示狀態傳遞至屬性或方法的程式庫的情況。 在許多情況下，最好將這些整數常數繫結中的列舉C#。 若要簡化這項對應，使用**EnumFields.xml**並**EnumMethods.xml**繫結專案中的檔案。 

### <a name="defining-an-enum-using-enumfieldsxml"></a>定義使用 EnumFields.xml 列舉

**EnumFields.xml**檔案包含 Java 之間的對應`int`常數和C# `enums`。 讓我們來看下列範例的C#建立的一組列舉`int`常數： 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

這裡我們採用 Java 類別`SKRealReachSettings`定義C#呼叫列舉`SKMeasurementUnit`命名空間中`Skobbler.Ngx.Map.RealReach`。 `field`項目定義 Java 常數的名稱 (範例`UNIT_SECOND`)，列舉項目的名稱 (範例`Second`)，與這兩個實體將代表整數值 (範例`0`)。 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>定義使用 EnumMethods.xml 的 Getter/Setter 方法

**EnumMethods.xml**檔可讓 Java 的變更方法參數和傳回型別`int`常數C# `enums`。 換句話說，它會對應的讀取和寫入的C#列舉 (中定義**EnumFields.xml**檔案) 到 Java`int`常數`get`並`set`方法。

給定`SKRealReachSettings`列舉會定義下列上方**EnumMethods.xml**檔案會定義這個列舉 getter/setter:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

第一個`method`線條對應使用 Java 的傳回值`getMeasurementUnit`方法，以`SKMeasurementUnit`列舉。 第二個`method`列會將對應的第一個參數`setMeasurementUnit`至相同的列舉。

所有這些變更之後，您可以使用下列程式碼在 Xamarin.Android 中設定`MeasurementUnit`: 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```


## <a name="summary"></a>總結

這篇文章討論如何 Xamarin.Android 會使用中繼資料來轉換從 API 定義*Google* *AOSP 格式*。 涵蓋所進行的變更後使用*Metadata.xml*，它會檢查重新命名成員時所遇到的限制，並它支援的 XML 屬性清單，描述何時使用每個屬性。



## <a name="related-links"></a>相關連結

- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [繫結 Java 程式庫](~/android/platform/binding-java-library/index.md)
- [GAPI 中繼資料](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
