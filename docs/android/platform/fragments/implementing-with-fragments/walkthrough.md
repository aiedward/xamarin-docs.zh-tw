---
title: Xamarin.安卓碎片演練 - 第 1 部分
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: ED368FA9-A34E-DC39-D535-5C34C32B9761
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: 043ad02f9ca9148910364ac82917551ee58d72ba
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027409"
---
# <a name="fragments-walkthrough-ndash-phone"></a>片段演練&ndash;電話

這是演練的第一部分,將創建一個 Xamarin.Android 應用程式,以縱向的 Android 設備為目標。 本演練將討論如何在 Xamarin.Android 中創建片段以及如何將它們添加到範例中。

[![](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

將此應用程式建立以下類別:

1. `PlayQuoteFragment`&nbsp;這個片段將顯示威廉·莎士比亞的戲劇引言。 它會由承載`PlayQuoteActivity`。
1. `Shakespeare`&nbsp;此類將包含兩個硬編碼陣列作為屬性。
1. `TitlesFragment`&nbsp;這個片段將顯示威廉·莎士比亞創作的戲劇標題清單。 它會由承載`MainActivity`。
1. `PlayQuoteActivity`將啟動`PlayQuoteActivity`以`TitlesFragment`回應 使用者在中選擇&nbsp;`TitlesFragment`播放。

## <a name="1-create-the-android-project"></a>1. 建立 Android 專案

建立新的Xamarin.Android專案,是**一個是一個磁碟子樣本**。
# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![建立新的 Xamarin.Android 專案](./walkthrough-images/01-newproject.w157-sml.png)](./walkthrough-images/01-newproject.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![建立新的 Xamarin.安卓專案](./walkthrough-images/01-newproject.m742-sml.png)](./walkthrough-images/01-newproject.m742.png#lightbox)

建議為本演練選擇 **「現代發展**」。

建立項目後,將檔案**佈局/Main.axml**重新命名為**佈局/activity_main.axml**。

-----

## <a name="2-add-the-data"></a>2. 新增資料

此應用程式的資料儲存在兩個硬編碼字串陣列中,它們是類別名稱`Shakespeare`的屬性:

* `Shakespeare.Titles`&nbsp;這個陣列將包含威廉·莎士比亞的戲劇清單。 這是的`TitlesFragment`數據源。
* `Shakespeare.Dialogue`&nbsp;此陣列包含中包含的其中一個戲劇的報價清單`Shakespeare.Titles`。 這是的`PlayQuoteFragment`數據源。

將**片段範例**專案加入新 C# 類別,並將指定**的 Scrikespeare.cs**。 在此檔案中,建立使用以下內容呼叫`Shakespeare`的新 C# 類別

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

## <a name="3-create-the-playquotefragment"></a>3. 建立播放報價區段

是`PlayQuoteFragment`一個 Android 片段,將顯示使用者在應用程式早期選擇的莎士比亞戲劇的報價,此片段不會使用 Android 佈局檔;相反,它將動態地創建其用戶介面。 新增項目加入`Fragment`的`PlayQuoteFragment`類別:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![新增 C# 類別](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/02-addclass.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![新增 C# 類別](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/02-addclass.m742.png#lightbox)

-----

然後,更改片段的代碼以類似於此代碼段:

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

在 Android 應用中,提供將實例化片段的工廠方法是一種常見的模式。 這可確保使用必要的參數創建片段以正常運行。 在本演練中,應用應使用`PlayQuoteFragment.NewInstance`該方法在每次選擇報價時創建新片段。 該方法`NewInstance`將採用報價單的索&ndash;引 的單個參數來顯示。

當`OnCreateView`需要在螢幕上渲染片段時,Android 將調用該方法。 它將返回一個`View`Android 物件,該對像是片段。 此片段不使用佈局檔創建檢視。 相反,它將通過實例化**TextView**來保存報價,以程式設計方式創建檢視,並將該小部件顯示在**ScrollView**中。

> [!NOTE]
> 片段子類必須具有沒有參數的公共預設構造函數。

## <a name="4-create-the-playquoteactivity"></a>4. 建立播放報價活動

片段必須托管在活動中,因此此應用需要承載 的`PlayQuoteFragment`活動 。 活動將在運行時動態地將片段添加到其佈局中。 新增應用程式加入新活動並命名為`PlayQuoteActivity`:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![新增 Android 活動到專案](./walkthrough-images/03-addactivity.w157-sml.png)](./walkthrough-images/03-addactivity.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![新增 Android 活動到專案](./walkthrough-images/03-addactivity.m742-sml.png)](./walkthrough-images/03-addactivity.m742.png#lightbox)

-----

編輯的`PlayQuoteActivity`代碼 :

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

創建`PlayQuoteActivity`時,它將實例化一個`PlayQuoteFragment`新的片段,並在其根視圖中載入該片段`FragmentTransaction`。 請注意,此活動不會為其使用者介面載入 Android 佈局檔。 相反,將新`PlayQuoteFragment`添加到應用程式的根視圖中。 資源標識符`Android.Resource.Id.Content`用於引用活動的根視圖,而不知道其特定標識符。

## <a name="5-create-titlesfragment"></a>5. 建立標題區段

將`TitlesFragment`類一個稱為`ListFragment`a 的專用片段,該片段封裝用於`ListView`在片段中 顯示的邏輯。 公開`ListFragment``ListAdapter`屬性`ListView`(由 用於顯示其內容)和`OnListItemClick`名為的事件 處理程式,允許片段回應對 顯示的`ListView`行的按一下。

要開始,向專案新增新片段並將其命名為**Titles片段**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![將 Android 片段新增到專案](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/04-addfragment.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![將 Android 片段新增到專案](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/04-addfragment.m742.png#lightbox)

-----

編輯片段內的代碼:

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

創建活動時,Android 將調用`OnActivityCreated`片段 的方法;因此,將調用片段的方法。這是創建的清單`ListView`配接器的位置。  該方法`ShowQuoteFromPlay`將啟動`PlayQuoteActivity`的 實例以顯示所選播放的報價。

## <a name="display-titlesfragment-in-mainactivity"></a>顯示標題的標題

最後一步是在`TitlesFragment``MainActivity`中 顯示。 活動不會動態載入片段。 相反,片段將使用`fragment`元素在活動的佈局檔中聲明,從而靜態載入該片段。 通過將`android:name`屬性設置為片段類(包括類型的命名空間)來標識要載入的片段。 例如,要使用,`TitlesFragment``android:name`則設定為`FragmentSample.TitlesFragment`。

編輯佈局檔案**activity_main.axml,** 將現有的 XML 取代為以下內容:

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
> 屬性`class`是的有效替代`android:name`。 沒有關於哪個窗體首選的正式指導,有許多代碼庫範例將與 交替`class``android:name`使用 。

主活動不需要更改代碼。 該類中的代碼應非常類似於此代碼段:

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

代碼完成後,在設備上運行應用以查看其運行。

[![在手機上運行的應用程序的螢幕截圖。](./walkthrough-images/05-app-screenshots-sml.png)](./walkthrough-images/05-app-screenshots.png#lightbox)

[本演練的第 2 部分](./walkthrough-landscape.md)將針對在橫向模式下運行的設備來圖此應用程式。
