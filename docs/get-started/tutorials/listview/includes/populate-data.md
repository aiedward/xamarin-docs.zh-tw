---
ms.openlocfilehash: a13426f4213d7c306c0de6922d336d4bb7723d72
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61037418"
---
[`ListView`](xref:Xamarin.Forms.ListView) 會使用類型為 `IEnumerable` 的 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 屬性來填入資料。 上一個步驟已使用字串陣列填入 XAML 中的 `ListView`。 不過，一般而言，`ListView` 會從程式碼後置中所定義且實作 `IEnumerable` 的集合填入資料。

在此練習中，您將修改 **ListViewTutorial** 專案，將資料從 `List` 中儲存的物件集合填入 [`ListView`](xref:Xamarin.Forms.ListView)。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 [方案總管] 的 **ListViewTutorial** 專案中，新增包含下列程式碼且名為 `Monkey` 的類別：

    ```csharp
    public class Monkey
    {
        public string Name { get; set; }
        public string Location { get; set; }
        public string ImageUrl { get; set; }

        public override string ToString()
        {
            return Name;
        }
    }
    ```

    此程式碼會定義 `Monkey` 物件，此物件會儲存猴子影像的名稱、位置和 URL。 此外，該類別會覆寫 `ToString` 方法以傳回 `Name` 屬性值。

1. 在 [方案總管] 的 **ListViewTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟。 然後在 **MainPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System.Collections.Generic;
    using Xamarin.Forms;

    namespace ListViewTutorial
    {
        public partial class MainPage : ContentPage
        {
            public IList<Monkey> Monkeys { get; private set; }

            public MainPage()
            {
                InitializeComponent();

                Monkeys = new List<Monkey>();
                Monkeys.Add(new Monkey
                {
                    Name = "Baboon",
                    Location = "Africa & Asia",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Capuchin Monkey",
                    Location = "Central & South America",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Blue Monkey",
                    Location = "Central and East Africa",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Squirrel Monkey",
                    Location = "Central & South America",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/2/20/Saimiri_sciureus-1_Luc_Viatour.jpg/220px-Saimiri_sciureus-1_Luc_Viatour.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Golden Lion Tamarin",
                    Location = "Brazil",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/8/87/Golden_lion_tamarin_portrait3.jpg/220px-Golden_lion_tamarin_portrait3.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Howler Monkey",
                    Location = "South America",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/0/0d/Alouatta_guariba.jpg/200px-Alouatta_guariba.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Japanese Macaque",
                    Location = "Japan",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Macaca_fuscata_fuscata1.jpg/220px-Macaca_fuscata_fuscata1.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Mandrill",
                    Location = "Southern Cameroon, Gabon, Equatorial Guinea, and Congo",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/7/75/Mandrill_at_san_francisco_zoo.jpg/220px-Mandrill_at_san_francisco_zoo.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Proboscis Monkey",
                    Location = "Borneo",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/e/e5/Proboscis_Monkey_in_Borneo.jpg/250px-Proboscis_Monkey_in_Borneo.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Red-shanked Douc",
                    Location = "Vietnam, Laos",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/9/9f/Portrait_of_a_Douc.jpg/159px-Portrait_of_a_Douc.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Gray-shanked Douc",
                    Location = "Vietnam",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/Cuc.Phuong.Primate.Rehab.center.jpg/320px-Cuc.Phuong.Primate.Rehab.center.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Golden Snub-nosed Monkey",
                    Location = "China",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c8/Golden_Snub-nosed_Monkeys%2C_Qinling_Mountains_-_China.jpg/165px-Golden_Snub-nosed_Monkeys%2C_Qinling_Mountains_-_China.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Black Snub-nosed Monkey",
                    Location = "China",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/5/59/RhinopitecusBieti.jpg/320px-RhinopitecusBieti.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Tonkin Snub-nosed Monkey",
                    Location = "Vietnam",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/9/9c/Tonkin_snub-nosed_monkeys_%28Rhinopithecus_avunculus%29.jpg/320px-Tonkin_snub-nosed_monkeys_%28Rhinopithecus_avunculus%29.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Thomas's Langur",
                    Location = "Indonesia",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/3/31/Thomas%27s_langur_Presbytis_thomasi.jpg/142px-Thomas%27s_langur_Presbytis_thomasi.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Purple-faced Langur",
                    Location = "Sri Lanka",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/0/02/Semnopithèque_blanchâtre_mâle.JPG/192px-Semnopithèque_blanchâtre_mâle.JPG"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Gelada",
                    Location = "Ethiopia",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/13/Gelada-Pavian.jpg/320px-Gelada-Pavian.jpg"
                });

                BindingContext = this;
            }
        }
    }
    ```

    此程式碼會定義 `IList<Monkey>` 類型的 `Monkeys` 屬性，並且將其初始化為類別建構函式中的空白清單。 接著，`Monkey` 物件會新增至 `Monkeys` 集合，而頁面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 會設為 `MainPage` 物件。 如需 `BindingContext` 屬性的詳細資訊，請參閱 [Xamarin.Forms 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)指南中[具有繫結內容的繫結](~/xamarin-forms/app-fundamentals/data-binding/basic-bindings.md#bindings-with-a-binding-context)一節。

    > [!IMPORTANT]
    > [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 屬性會透過視覺化樹狀結構繼承。 因此，由於其已設定於 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件，所以 `ContentPage` 的子物件會繼承其值，包括 [`ListView`](xref:Xamarin.Forms.ListView)。

1.  在 **MainPage.xaml** 中修改 [`ListView`](xref:Xamarin.Forms.Image) 宣告，以將 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 屬性設定為 `Monkeys` 集合：

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}" />
    ```

    此程式碼資料會將 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 屬性繫結到 `Monkeys` 集合。 在執行階段中，[`ListView`](xref:Xamarin.Forms.ListView) 會針對 `Monkeys` 集合查看其 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)，並且從此集合填入資料。 如需資料繫結的詳細資訊，請參閱 [Xamarin.Forms 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

1. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式：

    [![iOS 和 Android 中從集合填入資料的 ListView 螢幕擷取畫面](../images/populate-data.png "從集合顯示資料的 ListView")](../images/populate-data-large.png#lightbox "從集合顯示資料的 ListView")

    [`ListView`](xref:Xamarin.Forms.ListView) 會顯示 `Monkeys` 集合中每個 `Monkey` 的 `Name` 屬性。 這是因為從集合顯示物件時，`ListView` 預設會呼叫 `ToString` 方法 (已在 `Monkey` 類別中進行覆寫，進而傳回 `Name` 屬性值)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 [Solution Pad] 的 **ListViewTutorial** 專案中，新增包含下列程式碼且名為 `Monkey` 的類別：

    ```csharp
    public class Monkey
    {
        public string Name { get; set; }
        public string Location { get; set; }
        public string ImageUrl { get; set; }

        public override string ToString()
        {
            return Name;
        }
    }
    ```

    此程式碼會定義 `Monkey` 物件，此物件會儲存猴子影像的名稱、位置和 URL。 此外，該類別會覆寫 `ToString` 方法以傳回 `Name` 屬性值。

1. 在 [Solution Pad] 的 **ListViewTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟。 然後在 **MainPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System.Collections.Generic;
    using Xamarin.Forms;

    namespace ListViewTutorial
    {
        public partial class MainPage : ContentPage
        {
            public IList<Monkey> Monkeys { get; private set; }

            public MainPage()
            {
                InitializeComponent();

                Monkeys = new List<Monkey>();
                Monkeys.Add(new Monkey
                {
                    Name = "Baboon",
                    Location = "Africa & Asia",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Capuchin Monkey",
                    Location = "Central & South America",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Blue Monkey",
                    Location = "Central and East Africa",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Squirrel Monkey",
                    Location = "Central & South America",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/2/20/Saimiri_sciureus-1_Luc_Viatour.jpg/220px-Saimiri_sciureus-1_Luc_Viatour.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Golden Lion Tamarin",
                    Location = "Brazil",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/8/87/Golden_lion_tamarin_portrait3.jpg/220px-Golden_lion_tamarin_portrait3.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Howler Monkey",
                    Location = "South America",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/0/0d/Alouatta_guariba.jpg/200px-Alouatta_guariba.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Japanese Macaque",
                    Location = "Japan",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Macaca_fuscata_fuscata1.jpg/220px-Macaca_fuscata_fuscata1.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Mandrill",
                    Location = "Southern Cameroon, Gabon, Equatorial Guinea, and Congo",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/7/75/Mandrill_at_san_francisco_zoo.jpg/220px-Mandrill_at_san_francisco_zoo.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Proboscis Monkey",
                    Location = "Borneo",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/e/e5/Proboscis_Monkey_in_Borneo.jpg/250px-Proboscis_Monkey_in_Borneo.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Red-shanked Douc",
                    Location = "Vietnam, Laos",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/9/9f/Portrait_of_a_Douc.jpg/159px-Portrait_of_a_Douc.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Gray-shanked Douc",
                    Location = "Vietnam",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/Cuc.Phuong.Primate.Rehab.center.jpg/320px-Cuc.Phuong.Primate.Rehab.center.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Golden Snub-nosed Monkey",
                    Location = "China",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c8/Golden_Snub-nosed_Monkeys%2C_Qinling_Mountains_-_China.jpg/165px-Golden_Snub-nosed_Monkeys%2C_Qinling_Mountains_-_China.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Black Snub-nosed Monkey",
                    Location = "China",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/5/59/RhinopitecusBieti.jpg/320px-RhinopitecusBieti.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Tonkin Snub-nosed Monkey",
                    Location = "Vietnam",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/9/9c/Tonkin_snub-nosed_monkeys_%28Rhinopithecus_avunculus%29.jpg/320px-Tonkin_snub-nosed_monkeys_%28Rhinopithecus_avunculus%29.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Thomas's Langur",
                    Location = "Indonesia",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/3/31/Thomas%27s_langur_Presbytis_thomasi.jpg/142px-Thomas%27s_langur_Presbytis_thomasi.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Purple-faced Langur",
                    Location = "Sri Lanka",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/0/02/Semnopithèque_blanchâtre_mâle.JPG/192px-Semnopithèque_blanchâtre_mâle.JPG"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Gelada",
                    Location = "Ethiopia",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/13/Gelada-Pavian.jpg/320px-Gelada-Pavian.jpg"
                });

                BindingContext = this;
            }
        }
    }
    ```

    此程式碼會定義 `IList<Monkey>` 類型的 `Monkeys` 屬性，並且將其初始化為類別建構函式中的空白清單。 接著，`Monkey` 物件會新增至 `Monkeys` 集合，而頁面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 會設為 `MainPage` 物件。 如需 `BindingContext` 屬性的詳細資訊，請參閱 [Xamarin.Forms 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)指南中[具有繫結內容的繫結](~/xamarin-forms/app-fundamentals/data-binding/basic-bindings.md#bindings-with-a-binding-context)一節。

    > [!IMPORTANT]
    > [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 屬性會透過視覺化樹狀結構繼承。 因此，由於其已設定於 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件，所以 `ContentPage` 的子物件會繼承其值，包括 [`ListView`](xref:Xamarin.Forms.ListView)。

1.  在 **MainPage.xaml** 中修改 [`ListView`](xref:Xamarin.Forms.Image) 宣告，以將 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 屬性設定為 `Monkeys` 集合：

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}" />
    ```

    此程式碼資料會將 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 屬性繫結到 `Monkeys` 集合。 在執行階段中，[`ListView`](xref:Xamarin.Forms.ListView) 會針對 `Monkeys` 集合查看其 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)，並且從此集合填入資料。 如需資料繫結的詳細資訊，請參閱 [Xamarin.Forms 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![iOS 和 Android 中從集合填入資料的 ListView 螢幕擷取畫面](../images/populate-data.png "從集合顯示資料的 ListView")](../images/populate-data-large.png#lightbox "從集合顯示資料的 ListView")

    [`ListView`](xref:Xamarin.Forms.ListView) 會顯示 `Monkeys` 集合中每個 `Monkey` 的 `Name` 屬性。 這是因為從集合顯示物件時，`ListView` 預設會呼叫 `ToString` 方法 (已在 `Monkey` 類別中進行覆寫，進而傳回 `Name` 屬性值)。
