---
title: Xamarin. Android 片段逐步解說-第1部分
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: ED368FA9-A34E-DC39-D535-5C34C32B9761
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: 043ad02f9ca9148910364ac82917551ee58d72ba
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027409"
---
# <a name="fragments-walkthrough-ndash-phone"></a>&ndash; 電話的片段逐步解說

這是逐步解說的第一個部分，將會建立以直向的 Android 裝置為目標的 Xamarin Android 應用程式。 本逐步解說將討論如何在 Xamarin 中建立片段，以及如何將它們新增至範例。

[![](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

將會為此應用程式建立下列類別：

1. `PlayQuoteFragment` &nbsp; 此片段會顯示 William Shakespeare 播放的報價。 它將由 `PlayQuoteActivity`主控。
1. `Shakespeare` &nbsp; 這個類別會將兩個硬式編碼的陣列保存為屬性。
1. `TitlesFragment` &nbsp; 此片段會顯示 William Shakespeare 所撰寫的播放標題清單。 它將由 `MainActivity`主控。
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` 將會啟動 `PlayQuoteActivity`，以回應在 `TitlesFragment`中選取 [播放] 的使用者。

## <a name="1-create-the-android-project"></a>1. 建立 Android 專案

建立名為**FragmentSample**的新 Xamarin. Android 專案。
# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![建立新的 Xamarin Android 專案](./walkthrough-images/01-newproject.w157-sml.png)](./walkthrough-images/01-newproject.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![建立新的 Xamarin Android 專案](./walkthrough-images/01-newproject.m742-sml.png)](./walkthrough-images/01-newproject.m742.png#lightbox)

建議您在此逐步解說中選取 [**現代化開發**]。

建立專案之後，將檔案**layout/axml**重新命名為**layout/activity_main. axml**。

-----

## <a name="2-add-the-data"></a>2. 新增資料

此應用程式的資料會儲存在兩個硬式編碼的字串陣列中，這些是類別名稱的屬性 `Shakespeare`：

* `Shakespeare.Titles` &nbsp; 此陣列會保留 William Shakespeare 的播放清單。 這是 `TitlesFragment`的資料來源。
* `Shakespeare.Dialogue` &nbsp; 此陣列會保存 `Shakespeare.Titles`中包含的其中一個播放的引號清單。 這是 `PlayQuoteFragment`的資料來源。

將新類別C#新增至**FragmentSample**專案，並將其命名為**Shakespeare.cs**。 在此檔案中，使用下列C#內容建立名為 `Shakespeare` 的新類別

```csharp
class Shakespeare
{
    public static string[] Titles = {
                                      "Henry IV (1)",
                                      "Henry V",
                                      "Henry VIII",
                                      "Richard II",
                                      "Richard III",
                                      "Merchant of Venice",
                                      "Othello",
                                      "King Lear"
                                    };

    public static string[] Dialogue = {
                                        "So shaken as we are, so wan with care, Find we a time for frighted peace to pant, And breathe short-winded accents of new broils To be commenced in strands afar remote. No more the thirsty entrance of this soil Shall daub her lips with her own children's blood; Nor more shall trenching war channel her fields, Nor bruise her flowerets with the armed hoofs Of hostile paces: those opposed eyes, Which, like the meteors of a troubled heaven, All of one nature, of one substance bred, Did lately meet in the intestine shock And furious close of civil butchery Shall now, in mutual well-beseeming ranks, March all one way and be no more opposed Against acquaintance, kindred and allies: The edge of war, like an ill-sheathed knife, No more shall cut his master. Therefore, friends, As far as to the sepulchre of Christ, Whose soldier now, under whose blessed cross We are impressed and engaged to fight, Forthwith a power of English shall we levy; Whose arms were moulded in their mothers' womb To chase these pagans in those holy fields Over whose acres walk'd those blessed feet Which fourteen hundred years ago were nail'd For our advantage on the bitter cross. But this our purpose now is twelve month old, And bootless 'tis to tell you we will go: Therefore we meet not now. Then let me hear Of you, my gentle cousin Westmoreland, What yesternight our council did decree In forwarding this dear expedience.",
                                        "Hear him but reason in divinity, And all-admiring with an inward wish You would desire the king were made a prelate: Hear him debate of commonwealth affairs, You would say it hath been all in all his study: List his discourse of war, and you shall hear A fearful battle render'd you in music: Turn him to any cause of policy, The Gordian knot of it he will unloose, Familiar as his garter: that, when he speaks, The air, a charter'd libertine, is still, And the mute wonder lurketh in men's ears, To steal his sweet and honey'd sentences; So that the art and practic part of life Must be the mistress to this theoric: Which is a wonder how his grace should glean it, Since his addiction was to courses vain, His companies unletter'd, rude and shallow, His hours fill'd up with riots, banquets, sports, And never noted in him any study, Any retirement, any sequestration From open haunts and popularity.",
                                        "I come no more to make you laugh: things now, That bear a weighty and a serious brow, Sad, high, and working, full of state and woe, Such noble scenes as draw the eye to flow, We now present. Those that can pity, here May, if they think it well, let fall a tear; The subject will deserve it. Such as give Their money out of hope they may believe, May here find truth too. Those that come to see Only a show or two, and so agree The play may pass, if they be still and willing, I'll undertake may see away their shilling Richly in two short hours. Only they That come to hear a merry bawdy play, A noise of targets, or to see a fellow In a long motley coat guarded with yellow, Will be deceived; for, gentle hearers, know, To rank our chosen truth with such a show As fool and fight is, beside forfeiting Our own brains, and the opinion that we bring, To make that only true we now intend, Will leave us never an understanding friend. Therefore, for goodness' sake, and as you are known The first and happiest hearers of the town, Be sad, as we would make ye: think ye see The very persons of our noble story As they were living; think you see them great, And follow'd with the general throng and sweat Of thousand friends; then in a moment, see How soon this mightiness meets misery: And, if you can be merry then, I'll say A man may weep upon his wedding-day.",
                                        "First, heaven be the record to my speech! In the devotion of a subject's love, Tendering the precious safety of my prince, And free from other misbegotten hate, Come I appellant to this princely presence. Now, Thomas Mowbray, do I turn to thee, And mark my greeting well; for what I speak My body shall make good upon this earth, Or my divine soul answer it in heaven. Thou art a traitor and a miscreant, Too good to be so and too bad to live, Since the more fair and crystal is the sky, The uglier seem the clouds that in it fly. Once more, the more to aggravate the note, With a foul traitor's name stuff I thy throat; And wish, so please my sovereign, ere I move, What my tongue speaks my right drawn sword may prove.",
                                        "Now is the winter of our discontent Made glorious summer by this sun of York; And all the clouds that lour'd upon our house In the deep bosom of the ocean buried. Now are our brows bound with victorious wreaths; Our bruised arms hung up for monuments; Our stern alarums changed to merry meetings, Our dreadful marches to delightful measures. Grim-visaged war hath smooth'd his wrinkled front; And now, instead of mounting barded steeds To fright the souls of fearful adversaries, He capers nimbly in a lady's chamber To the lascivious pleasing of a lute. But I, that am not shaped for sportive tricks, Nor made to court an amorous looking-glass; I, that am rudely stamp'd, and want love's majesty To strut before a wanton ambling nymph; I, that am curtail'd of this fair proportion, Cheated of feature by dissembling nature, Deformed, unfinish'd, sent before my time Into this breathing world, scarce half made up, And that so lamely and unfashionable That dogs bark at me as I halt by them; Why, I, in this weak piping time of peace, Have no delight to pass away the time, Unless to spy my shadow in the sun And descant on mine own deformity: And therefore, since I cannot prove a lover, To entertain these fair well-spoken days, I am determined to prove a villain And hate the idle pleasures of these days. Plots have I laid, inductions dangerous, By drunken prophecies, libels and dreams, To set my brother Clarence and the king In deadly hate the one against the other: And if King Edward be as true and just As I am subtle, false and treacherous, This day should Clarence closely be mew'd up, About a prophecy, which says that 'G' Of Edward's heirs the murderer shall be. Dive, thoughts, down to my soul: here Clarence comes.",
                                        "To bait fish withal: if it will feed nothing else, it will feed my revenge. He hath disgraced me, and hindered me half a million; laughed at my losses, mocked at my gains, scorned my nation, thwarted my bargains, cooled my friends, heated mine enemies; and what's his reason? I am a Jew. Hath not a Jew eyes? hath not a Jew hands, organs, dimensions, senses, affections, passions? fed with the same food, hurt with the same weapons, subject to the same diseases, healed by the same means, warmed and cooled by the same winter and summer, as a Christian is? If you prick us, do we not bleed? if you tickle us, do we not laugh? if you poison us, do we not die? and if you wrong us, shall we not revenge? If we are like you in the rest, we will resemble you in that. If a Jew wrong a Christian, what is his humility? Revenge. If a Christian wrong a Jew, what should his sufferance be by Christian example? Why, revenge. The villany you teach me, I will execute, and it shall go hard but I will better the instruction.",
                                        "Virtue! a fig! 'tis in ourselves that we are thus or thus. Our bodies are our gardens, to the which our wills are gardeners: so that if we will plant nettles, or sow lettuce, set hyssop and weed up thyme, supply it with one gender of herbs, or distract it with many, either to have it sterile with idleness, or manured with industry, why, the power and corrigible authority of this lies in our wills. If the balance of our lives had not one scale of reason to poise another of sensuality, the blood and baseness of our natures would conduct us to most preposterous conclusions: but we have reason to cool our raging motions, our carnal stings, our unbitted lusts, whereof I take this that you call love to be a sect or scion.",
                                        "Blow, winds, and crack your cheeks! rage! blow! You cataracts and hurricanoes, spout Till you have drench'd our steeples, drown'd the cocks! You sulphurous and thought-executing fires, Vaunt-couriers to oak-cleaving thunderbolts, Singe my white head! And thou, all-shaking thunder, Smite flat the thick rotundity o' the world! Crack nature's moulds, an germens spill at once, That make ingrateful man!"
                                    };
}
```

## <a name="3-create-the-playquotefragment"></a>3. 建立 PlayQuoteFragment

`PlayQuoteFragment` 是 Android 片段，會針對使用者在應用程式中稍早選取的 Shakespeare 播放顯示報價，此片段不會使用 Android 版面配置檔案;相反地，它會以動態方式建立其使用者介面。 將名為 `PlayQuoteFragment` 的新 `Fragment` 類別加入至專案：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![新增C#類別](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/02-addclass.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![新增C#類別](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/02-addclass.m742.png#lightbox)

-----

然後，將片段的程式碼變更為類似此程式碼片段：

```csharp
public class PlayQuoteFragment : Fragment
{
    public int PlayId => Arguments.GetInt("current_play_id", 0);

    public static PlayQuoteFragment NewInstance(int playId)
    {
        var bundle = new Bundle();
        bundle.PutInt("current_play_id", playId);
        return new PlayQuoteFragment {Arguments = bundle};
    }

    public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
    {
        if (container == null)
        {
            return null;
        }

        var textView = new TextView(Activity);
        var padding = Convert.ToInt32(TypedValue.ApplyDimension(ComplexUnitType.Dip, 4, Activity.Resources.DisplayMetrics));
        textView.SetPadding(padding, padding, padding, padding);
        textView.TextSize = 24;
        textView.Text = Shakespeare.Dialogue[PlayId];

        var scroller = new ScrollView(Activity);
        scroller.AddView(textView);

        return scroller;
    }
}
```

這是 Android 應用程式中常見的模式，可提供將具現化片段的 factory 方法。 這可確保會使用必要的參數來建立片段，以便正常運作。 在此逐步解說中，應用程式應該使用 `PlayQuoteFragment.NewInstance` 方法，在每次選取報價時建立新的片段。 `NewInstance` 方法會接受單一參數，&ndash; 要顯示的引號索引。

當您要在螢幕上呈現片段時，Android 會叫用 `OnCreateView` 方法。 它會傳回做為片段的 Android `View` 物件。 此片段不會使用版面配置檔案來建立視圖。 相反地，它會以程式設計方式建立**TextView**以保存引號，並將該 widget 顯示在**ScrollView**中。

> [!NOTE]
> 片段子類別必須具有沒有參數的公用預設函式。

## <a name="4-create-the-playquoteactivity"></a>4. 建立 PlayQuoteActivity

片段必須裝載于活動內部，因此此應用程式需要將主控 `PlayQuoteFragment`的活動。 活動會在執行時間以動態方式將片段加入其版面配置中。 將新活動新增至應用程式，並將其命名為 `PlayQuoteActivity`：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![將 Android 活動新增至專案](./walkthrough-images/03-addactivity.w157-sml.png)](./walkthrough-images/03-addactivity.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![將 Android 活動新增至專案](./walkthrough-images/03-addactivity.m742-sml.png)](./walkthrough-images/03-addactivity.m742.png#lightbox)

-----

編輯 `PlayQuoteActivity`中的程式碼：

```csharp
[Activity(Label = "PlayQuoteActivity")]
public class PlayQuoteActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        var playId = Intent.Extras.GetInt("current_play_id", 0);

        var detailsFrag = PlayQuoteFragment.NewInstance(playId);
        FragmentManager.BeginTransaction()
                        .Add(Android.Resource.Id.Content, detailsFrag)
                        .Commit();
    }
}
```

建立 `PlayQuoteActivity` 時，它會將新的 `PlayQuoteFragment` 具現化，並在 `FragmentTransaction`內容的根視圖中載入該片段。 請注意，此活動不會載入其使用者介面的 Android 配置檔案。 而是將新的 `PlayQuoteFragment` 新增至應用程式的 [根] 視圖。 資源識別碼 `Android.Resource.Id.Content` 是用來參考活動的根視圖，而不需要知道其特定識別碼。

## <a name="5-create-titlesfragment"></a>5. 建立 TitlesFragment

`TitlesFragment` 會將特定片段（稱為 `ListFragment`）子類別化，以封裝在片段中顯示 `ListView` 的邏輯。 `ListFragment` 會公開 `ListAdapter` 屬性（由 `ListView` 用來顯示其內容），以及名為 `OnListItemClick` 的事件處理常式，可讓片段回應 `ListView`所顯示之資料列上的按下動作。

若要開始使用，請將新的片段新增至專案，並將其命名為**TitlesFragment**：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![將 Android 片段新增至專案](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/04-addfragment.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![將 Android 片段新增至專案](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/04-addfragment.m742.png#lightbox)

-----

編輯片段內的程式碼：

```csharp
public class TitlesFragment : ListFragment
{
    int selectedPlayId;

    public TitlesFragment()
    {
        // Being explicit about the requirement for a default constructor.
    }

    public override void OnActivityCreated(Bundle savedInstanceState)
    {
        base.OnActivityCreated(savedInstanceState);
        ListAdapter = new ArrayAdapter<String>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
        }
    }

    public override void OnSaveInstanceState(Bundle outState)
    {
        base.OnSaveInstanceState(outState);
        outState.PutInt("current_play_id", selectedPlayId);
    }

    public override void OnListItemClick(ListView l, View v, int position, long id)
    {
        ShowPlayQuote(position);
    }

    void ShowPlayQuote(int playId)
    {
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

建立活動時，Android 會叫用片段的 `OnActivityCreated` 方法;這是用來建立 `ListView` 清單介面卡的位置。  `ShowQuoteFromPlay` 方法將啟動 `PlayQuoteActivity` 的實例，以顯示所選播放的報價。

## <a name="display-titlesfragment-in-mainactivity"></a>在 MainActivity 中顯示 TitlesFragment

最後一個步驟是在 `MainActivity`內顯示 `TitlesFragment`。 活動不會動態載入片段。 而是以靜態方式載入片段，方法是使用 `fragment` 元素在活動的配置檔案中宣告。 要載入的片段是藉由將 `android:name` 屬性設定為片段類別（包括類型的命名空間）來識別。 例如，若要使用 `TitlesFragment`，則 `android:name` 會設定為 [`FragmentSample.TitlesFragment`]。

編輯設定檔案**activity_main axml**，以下列內容取代現有的 XML：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <fragment
        android:name="FragmentSample.TitlesFragment"
        android:id="@+id/titles"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
```

> [!NOTE]
> `class` 屬性是 `android:name`的有效替代。 沒有慣用的表單指引，有許多程式碼基底範例會使用 `class` 與 `android:name`交換。

MainActivity 不需要變更程式碼。 該類別中的程式碼應該與此程式碼片段非常類似：

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        SetContentView(Resource.Layout.activity_main);
    }
}
```

## <a name="run-the-app"></a>執行應用程式

現在程式碼已完成，請在裝置上執行應用程式，以查看其運作方式。

[![在手機上執行之應用程式的螢幕擷取畫面。](./walkthrough-images/05-app-screenshots-sml.png)](./walkthrough-images/05-app-screenshots.png#lightbox)

[本逐步解說的第2部分](./walkthrough-landscape.md)將針對以橫向模式執行的裝置 optimtize 此應用程式。
