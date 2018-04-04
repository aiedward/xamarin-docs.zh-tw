---
title: 檔案
description: 使用內嵌的資源，或針對原生檔案系統應用程式開發介面撰寫，可以完成檔案處理 xamarin.forms。
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/22/2017
ms.openlocfilehash: 8315f1a0056c6a6f084ebfe2c29f0c0c2bb30330
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="files"></a>檔案

_使用內嵌的資源，或針對原生檔案系統應用程式開發介面撰寫，可以完成檔案處理 xamarin.forms。_

## <a name="overview"></a>總覽

Xamarin.Forms 程式碼會在多個平台上執行 - 每一個都有自己的檔案系統。 這表示讀取和寫入檔案是最容易完成的每個平台上使用原生檔案 Api。 或者，內嵌的資源是簡單的解決方案，可將資料檔案與應用程式。

本文件包含下列一般檔案處理案例：

-  [ **檔案內嵌為資源**](#Loading_Files_Embedded_as_Resources) -檔案可做為應用程式，並載入使用反映 API 的一部分傳送。
-  [ **儲存及載入檔案**](#Loading_and_Saving_Files) -可以是使用者可寫入存放原生實作，然後使用存取`DependencyService`。


第三方元件呼叫**PCLStorage**也可用來讀取和寫入使用者可存取存放從 PCL 程式碼檔案。

處理影像檔的資訊，請參閱[處理映像](~/xamarin-forms/user-interface/images.md)頁面。

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>載入內嵌為資源的檔案

若要內嵌至檔案**PCL**組件，建立或加入檔案，並確定**建置動作： EmbeddedResource**。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![設定內嵌資源建置動作](files-images/vs-embeddedresource-sml.png "設定 EmbeddedResource BuildAction")](files-images/vs-embeddedresource.png#lightbox "設定 EmbeddedResource 建置動作")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![文字檔案內嵌在 PCL 中，設定內嵌的資源建置動作](files-images/xs-embeddedresource-sml.png "設定 EmbeddedResource BuildAction")](files-images/xs-embeddedresource.png#lightbox "設定 EmbeddedResource 建置動作")

-----

`GetManifestResourceStream` 用來存取內嵌的檔案使用其**資源識別碼**。 根據預設，資源識別碼是內嵌在專案的預設命名空間的前置詞的檔案名稱在此情況下組件是**WorkingWithFiles**和檔名**PCLTextResource.txt**，資源識別碼是讓`WorkingWithFiles.PCLTextResource.txt`。

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream)) {
    text = reader.ReadToEnd ();
}
```

`text`然後顯示文字，或使用它在程式碼中使用變數。 下列螢幕擷取畫面[範例應用程式](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)中呈現的文字會顯示`Label`控制項。

 [![文字檔案內嵌在 PCL](files-images/pcltext-sml.png "內嵌的文字檔案，在應用程式中顯示的 PCL")](files-images/pcltext.png#lightbox "PCL 顯示在應用程式中內嵌的文字檔案")

載入及還原序列化 XML 也一樣簡單。 下列程式碼會示範 XML 檔案被載入並還原序列化的資源，然後繫結至`ListView`供顯示。 XML 檔案包含的陣列`Monkey`物件 （已定義類別的範例程式碼）。

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLXmlResource.xml");
List<Monkey> monkeys;
using (var reader = new System.IO.StreamReader (stream)) {
    var serializer = new XmlSerializer(typeof(List<Monkey>));
    monkeys = (List<Monkey>)serializer.Deserialize(reader);
}
var listView = new ListView ();
listView.ItemsSource = monkeys;
```

 [![Xml 檔案內嵌在 PCL 中，顯示在 ListView 中](files-images/pclxml-sml.png "內嵌的 XML 檔案，在清單檢視中顯示的 PCL")](files-images/pclxml.png#lightbox "PCL 顯示在清單檢視中的內嵌 XML 檔案")

<a name="Embedding_in_Shared_Projects" />

### <a name="embedding-in-shared-projects"></a>將內嵌在共用專案

但前置詞用於共用專案的內容會編譯成參考的專案，因為內嵌檔案的資源識別碼可以變更共用專案也可以做為內嵌資源，包含檔案。 這表示每個內嵌檔案的資源識別碼可能會不同，每個平台。

有兩個共用專案的這個問題的解決方案：

-  **同步處理專案**-編輯專案屬性，每個平台使用**相同**組件名稱和預設命名空間。 然後，這個值可以是 「 硬式編碼 」，做為內嵌的資源 Id 共用專案中的前置詞。
-  **#if 編譯器指示詞**-使用編譯器指示詞，將正確的資源識別碼前置詞，並使用該值，以動態方式建構正確的資源識別碼。


程式碼說明第二個選項如下所示。 編譯器指示詞用來選取 （這通常是相同的參考專案的預設命名空間） 的硬式編碼資源前置詞。 `resourcePrefix`變數會用來建立有效的資源識別碼藉由串連內嵌的資源檔名。

```csharp
#if __IOS__
var resourcePrefix = "WorkingWithFiles.iOS.";
#endif
#if __ANDROID__
var resourcePrefix = "WorkingWithFiles.Droid.";
#endif
#if WINDOWS_PHONE
var resourcePrefix = "WorkingWithFiles.WinPhone.";
#endif

Debug.WriteLine("Using this resource prefix: " + resourcePrefix);
// note that the prefix includes the trailing period '.' that is required
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
Stream stream = assembly.GetManifestResourceStream
    (resourcePrefix + "SharedTextResource.txt");
```

<a name="Organizing_Resources" />

### <a name="organizing-resources"></a>組織資源

上述範例假設檔案內嵌的 PCL 專案，則這個資源識別碼是表單的根目錄中**Namespace.Filename.Extension**，例如`WorkingWithFiles.PCLTextResource.txt`和`WorkingWithFiles.iOS.SharedTextResource.txt`。

您可將組織資料夾中的內嵌的資源。 當內嵌的資源放置於某個資料夾時，資料夾名稱會成為組件的資源識別碼 （以句號分隔），讓資源 ID 格式會變成**Namespace.Folder.Filename.Extension**。 將放入資料夾使用範例應用程式中的檔案**MyFolder**會使對應的資源識別碼`WorkingWithFiles.MyFolder.PCLTextResource.txt`和`WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`。

<a name="Debugging_Embedded_Resources" />

### <a name="debugging-embedded-resources"></a>偵錯內嵌的資源

因為有時候很難了解為什麼未載入特定的資源，所以下列偵錯程式碼可以暫時加入應用程式以協助確認已正確設定的資源。 它會輸出指定的組件中內嵌的所有已知的資源**錯誤**板協助偵錯載入問題的資源。

```csharp
using System.Reflection;
// ...
// use for debugging, not in released app code!
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
foreach (var res in assembly.GetManifestResourceNames()) {
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>儲存及載入檔案

Xamarin.Forms 會執行多個平台上，每個都有它自己的檔案系統，因為載入及儲存使用者所建立的檔案沒有單一方法。 若要示範如何儲存和載入文字檔案範例應用程式包含一個畫面，將儲存並載入某些使用者輸入的 [完成] 畫面如下所示：

 [![儲存及載入文字](files-images/saveandload-sml.png "儲存和載入應用程式中的檔案")](files-images/saveandload.png#lightbox "儲存和載入應用程式中的檔案")

每個平台都有稍微不同的目錄結構和其他檔案系統功能-例如 Xamarin.iOS 和 Xamarin.Android 支援大部分`System.IO`功能，但 Windows Phone 只支援`IsolatedStorage`和[`Windows.Storage` ](http://msdn.microsoft.com/library/windowsphone/develop/jj681698(v=vs.105).aspx)應用程式開發介面。

若要解決這個問題，範例應用程式定義的介面，以載入和儲存檔案 Xamarin.Forms PCL 中。 它提供一個簡單的 API，以載入和儲存文字檔案將儲存在裝置上。

```csharp
public interface ISaveAndLoad {
    void SaveText (string filename, string text);
    string LoadText (string filename);
}
```

PCL 程式碼再使用[DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)來取得介面的原生實作的參考。 這可讓可攜式程式碼，將委派的載入和儲存檔案，以在每個平台專屬專案中撰寫類別。 在範例中，**儲存**和**負載**按鈕會寫入所示：

```csharp
var saveButton = new Button {Text = "Save"};
saveButton.Clicked += (sender, e) => {
    DependencyService.Get<ISaveAndLoad>().SaveText("temp.txt", input.Text);
};
var loadButton = new Button {Text = "Load"};
loadButton.Clicked += (sender, e) => {
    output.Text = DependencyService.Get<ISaveAndLoad>().LoadText("temp.txt");
};
```

然後實作必須先新增至每個平台專屬專案檔案可以實際儲存和載入。

### <a name="ios-and-android"></a>iOS 和 Android

Xamarin.iOS 和 Xamarin.Android 專案介面的實作可能會相同。 程式碼如下所示，包括`[assembly: Dependency (typeof (SaveAndLoad))]`屬性所需的`DependencyService`運作。

```csharp
[assembly: Dependency (typeof (SaveAndLoad))]
namespace WorkingWithFiles {
    public class SaveAndLoad : ISaveAndLoad {
        public void SaveText (string filename, string text) {
            var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var filePath = Path.Combine (documentsPath, filename);
            System.IO.File.WriteAllText (filePath, text);
        }
        public string LoadText (string filename) {
            var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var filePath = Path.Combine (documentsPath, filename);
            return System.IO.File.ReadAllText (filePath);
        }
    }
}
```

### <a name="universal-windows-platform-uwp-windows-81-and-windows-phone-81"></a>通用 Windows 平台 (UWP)、 Windows 8.1 和 Windows Phone 8.1

這些平台都有不同的檔案系統 API – [ `Windows.Storage` ](/windows/uwp/files/quickstart-reading-and-writing-files/) – 也就是用來儲存及載入的檔案。
`ISaveAndLoad`可以實作介面，如下所示：

```csharp
using System;
using System.Threading.Tasks;
using Windows.Storage;
using WinApp;
using WorkingWithFiles;
using Xamarin.Forms;

[assembly: Dependency(typeof(SaveAndLoad_WinApp))]

namespace WindowsApp
{
    // https://msdn.microsoft.com/library/windows/apps/xaml/hh758325.aspx
    public class SaveAndLoad_WinApp : ISaveAndLoad
    {
        public async Task SaveTextAsync(string filename, string text)
        {
            StorageFolder localFolder = ApplicationData.Current.LocalFolder;
            StorageFile sampleFile = await localFolder.CreateFileAsync(filename, CreationCollisionOption.ReplaceExisting);
            await FileIO.WriteTextAsync(sampleFile, text);
        }
        public async Task<string> LoadTextAsync(string filename)
        {
            StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
            StorageFile sampleFile = await storageFolder.GetFileAsync(filename);
            string text = await Windows.Storage.FileIO.ReadTextAsync(sampleFile);
            return text;
        }
    }
}
```


<a name="Saving_and_Loading_in_Shared_Projects" />

### <a name="saving-and-loading-in-shared-projects"></a>儲存及載入共用的專案中

因為共用專案支援編譯器指示詞是可以在共用專案內的單一類別檔案中納入所有平台專屬程式碼 (而不使用`DependencyService`)。
單一`SaveAndLoad`無法撰寫類別，包含這兩種實作上述項目，選擇性地編譯成參考的專案，使用類似的編譯器指示詞`#if WINDOWS_PHONE`， `#if __IOS__`，和`#if __ANDROID__`。

## <a name="additional-information"></a>其他資訊

PCL 架構 Xamarin.Forms 專案也可以利用的[PCLStorage NuGet](http://www.nuget.org/packages/pclstorage) ([程式碼&amp;文件](https://pclstorage.codeplex.com/)) 以協助實作檔案作業以跨平台的方式。


## <a name="summary"></a>總結

本文件示範了一些簡單的檔案作業載入內嵌的資源和儲存及載入裝置上的文字。 開發人員可以實作自己原生檔案 Api 使用`DependencyService`，讓您視需要處理其在檔案操作需求的複雜。


## <a name="related-links"></a>相關連結

- [FilesSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)
- [建立、 寫入和讀取檔案 (UWP)](/windows/uwp/files/quickstart-reading-and-writing-files/)
- [Xamarin.Forms 範例](https://github.com/xamarin/xamarin-forms-samples)
- [檔案活頁簿](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/files/files.workbook)
