---
# You can also start simply with 'default'
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: Welcome to Slidev
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# https://sli.dev/guide/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/guide/syntax#mdc-syntax
mdc: true
---

# 使って知るCustomLayout 
# vs DeilyScheduler


<!--
それでは始めます、よろしくお願いします。
-->

---
layout: two-cols
---

# Self-introduction

::right::



<!--
まずは自己紹介をさせていただきます。

改めまして、飯島 彩輝と申します。
現在は株式会社UNEXTにてAndroid端末やFireTablet向けのアプリの開発をしております。

Droidkaigiに関連した話だと、2018年にTDDに関する発表をさせていただいて、2021年にはdroidNinjaに記事を投稿させていただいたりしておりまして、今回はかなり久々の登壇者としての参加となります。

-->

---
layout: default
---

# Table of Contents

- Leanig Custom layout
    - What's CustomLayout?
    - Which method?

- Practice Custom layout
    - Let's create Daily Scheluer


<!--
本日のお品書きです。
今日は、使って知るCustomLayou vs DailyScheulerということで、
序盤はCostomLayoutってなんだっけっていうのを軽く確認して、

後半では、カレンダーアプリなどにありそうな、1日のスケジュールを表示するViewを作りながら、より理解を深めていきます。
-->

---
layout: two-cols
---

# Daily Scheduler

## with Layout()

<br/>

- Show time label 
- Show Vertical axis
- Show event
- Adjusting width for overlapping events
- Drag and drop
- Snapping events
- Lazy rendering  


::right::

<br/>
<br/>

<video autoplay muted style="height:400px; margin:0 auto;">
  <source src="/first.mov" type="video/mp4" >
</video>

<!-->

その日の予定が表示されて、スクロールができて、イベントをドラッグ＆ドロップで移動できるようなビューを、Layout()関数を使って、作っていきます。

-->

---
layout: section
---

## Today's Goal
# Maybe I can create a custom layout! 

<!--

ゴールは、Layout()関数を使って簡単な　CustomLayout を作れるようになることです。

一息

-->


---
layout: section
---

# What's Custom layout?
https://developer.android.com/develop/ui/compose/layouts/custom?hl=ja

<!--

では早速、CostomLayoutとは何かという話からしていきましょう。

Custom layoutについてはAndroidDevelopersにCustomLayoutというタイトルのページがあるのですが、意外と定義が明文化はされていなかったため、
とりあえず今日の発表では、このページを参考に、

-->


---
layout: section
---

# Compose has 3 phases

<br/>

<img src="/3phases.png" style="margin:0 auto;"/>

https://developer.android.com/develop/ui/compose/phases


<!-->

Compsoeの3つのフェーズ

coposition,layout,draw、のうちの二番目のLayoutフェーズを独自で実装したComposableのことをCustomLayoutと呼ぼうと思います。

Jetpack Composeが提供する標準のレイアウトコンポーネント Column、Row、Boxなどでは実現できない挙動を実装する際に作成することが多いです。

-->


---
layout: section
---

# Which method should I choose?

<!-->
さて、Rowやカラムで実現できないUIを作るために、カスタムレイアウトを作りたい。
と思った時に、取れる方法にはいくつか選択肢があります。
まずは、その中から、どの方法を選ぶのが良いのか、というところから確認していきましょう。
-->

---
layout: default
---

# Which method should I choose?

- Layout()
- modifier.layout()
- SubcmposeLayout()
- BoxWithConstraints()


<!--
選択肢としては、

- Layout()
- modifier.layout()
- subcmposeLayout()
- BoxWithConstraints

ここら辺が上がってきます。

BoxWithConstraintsは今回の定義からは外れているのですが、雰囲気としては近いものがあるので合わせて見ていきます。

それぞれがどういった特徴を持っているのかは最初の迷いどころだと思うので、しっかり確認しておきましょう。

-->

---
layout: section
---

# Layout()


<!-->

まずはレイアウト関数です。

その名の通り、Layoutフェーズを独自に実装するためのComposable関数です。

-->

---
layout: default
---

# Layout()

```kt {*|1-6|8-13}
Layout(
    content = {
        Text("1")
        Text("2")
        Text("3")
    },
    modifier = Modifier
) { measurables, constraints ->
        ..
    layout(100, 100) {
        ..
    }
}

```

<!-->
詳細は後半でやるのですが、
使い方としてはこのように→
contentにComposableを渡して、→最後のブロック内で渡したそれぞれのコンテンツについてLayoutを行います。
-->

---
layout: default
---

# Layout

<br/>

## Can

<br/>

- Layout elements
- Handle multiple child elements
- Reference parent constraints
- Reference the size of other elements

## Use case

<br/>

### Customize the position and size of mulitiple elements during the layout phase.

<!-->
Layout関数では、要素をレイアウトすることが可能です。
また、複数の子要素、先ほどの例ではTextを三つ渡していましたが、それぞれ個別にレイアウトすることができます。
親の制約と他の要素のサイズを参照できます。
なので、複数の要素を渡し、親の制約や、他の要素のサイズを条件に、それぞれの要素のサイズや配置を確定することができます。

ただし、配置するContentそのものを変更することはできません。例えば一つ目の要素の幅が100以上かどうかで残りの要素をテキストにするか、Spacerにするかを動的に変える、ということはできません。

なので、用途としては、レイアウトフェーズで風数の要素の位置とサイズをカスタマイズしたい場合に利用できます
-->


---
layout: section
---

# modifier.layout()

<!-->

次がmodifier.Layout()です。

先ほどのLayout関数と近い使い方ができるのですが、こちらはモディファイアの関数として定義されています。

-->


---
layout: default
---

# modifier.layout()

```kt {*|4|4-6|*}
Column(
        horizontalAlignment = androidx.compose.ui.Alignment.End,
        modifier = modifier
            .layout { measurable, constraints ->
                // do anything
            }
    ) {
        Text(
            text = "1",
        )
        Text(
            text = "22222222222",
        )
    }
``` 
<!-->
使い方としては、このように->modifer.で呼び出して使います。
この場合はColmunに付与しています。->

そしてこのbloackの中でColmunに渡されあているコンテンツに対してLayout処理ができます。

（一息）

→Layout関数と大きく区別される点が、modifier.layoutは単一の要素しか扱えないということです。
ColmunのコンテンツにはTextが二つ入っていますが、Layoutのタイミングではcontentをまとめて一つにした要素しか入ってこないので、
text1とtext2を両方同じようにずらすといった使い方はできますが、text1は左にずらして、text2は右にずらす、というような別々な処理はここではできません。

-->


---
layout: default
---

# modifier.layout()

<br/>

## Can<br/>

- Handle a single element
- Reference parent constraints

<br/>

## Use case

<br/>

### Customize the position and size of a single element during the layout phase.


<!--
なのでmodifier.layoutとLayout関数は、扱いたい子要素が複数なのか一つなのかで選ぶことができます。
-->



---
layout: section
---

# SubComposeLayout


<!-->
次に、SubComposeLayoutです。

こちらはLayoutと比べてより複雑な実装が可能にです。
<-->

---
layout: default
---

# SubComposeLayout

## Can

<br/>

- Layout elements
- Handle multiple child elements
- Reference parent constraints
- Reference the size of other elements
- **Dynamically switch content during the layout phase**

## Use case

<br/>

### Customize the position and size of multiple elements during the layout phase and change the elements themselves.
<!--
具体的には先ほどLayoutでできないといっていた、LayoutフェーズでContentそのものを変更することが可能です。

親の制約、他の要素のサイズ、位置を見ながら、それぞれの要素のサイズと位置、さらに、何を配置するかまで決めることができます。

SubcompsoeLayoutはその名の通りSubcomopseという仕組みを利用していて、より複雑なので、ここではひとまず、何ができるのかと、Layoutで足りる場合はパフォーマンス的にはLayoutを使った方が良いというところを押さえておきたいです。

-->


---
layout: section
---

# BoxWithConstraints

<!-->
最後に

BoxWithConstraints

です。

-->


---
layout: default
---

# BoxWithConstraints

<br/>

## Can

<br/>

- Reference parent constraints

## Use case

<br/>

### Change the layout elements based on the parent’s constraints


<!-->
BoxWithConstraintsは、親の制約から、配置するコンテンツを変えることができるBoxです。

親のmaxやminが取れるのでそれを使って、中のComposableを変更することができます。

逆に、それ以外はできないので、サイズや、配置は、通常のBoxの挙動に任せることになります。

-->

---
layout: default
---

# BoxWithConstraints


``` kt
BoxWithConstraints {
     if (maxWidth < 600.dp) {
         Column {
             Text("small screen")
             Text("small screen")
         }
     } else {
         Row {
             Text("Large screen")
             Text("Large screen")
         }
     }
 }

```

<!--
これは親のmaxWidthが600.dpより大きいか小さいかで、表示するComposableを絡むか、ろうにだし分けています。

他の方法だった場合親の制約から取れる値はピクセルなのですがboxWithconstraintの場合はdpに変換された状態で取れるのでとてもお手軽です。
-->


---
layout: default
---

# Which method should I choose?

- Layout()
    - Freely position&size multiple elements.
- modifier.layout()
    - Freely position&size a single element.
- SubcomposeLayout
    - Choose and freely position&size elements.
- BoxWithConstraints
    - Select elements based on the parent’s constraints.

<!-->

まとめるとこのようになります。
CostomLayoutを作りたいと思った時は、用途に合わせて選んでみてください。

では、今回作るDailySchedulerはというと、
ラベル、イベントなど複数の要素があり、
それぞれ位置とサイズの調整はしますが、場合によってcontentを変えることはしないので複数の要素を自由に配置するLayoutを使うことができます。

fuga20:6:20
-->


---
layout: default
---

# Place time label
<img src="/time_label_only.png" style="height:450px; margin:0 auto;"/>


<!--
ではLayoutを使うというのがわかったところで、早速実践に入っていきます。

まずは時刻を配置しながら、Layoutの基本を学んでいきます。
-->

---
layout: default
---

# Keyword
  
- Layout  // TimeLabel
- Constraints  // TimeLabel 
- Measurable  // TimeLabel
- Placeable  // TimeLabel
- place // TimeLabel 
- ParentDataModifier


<!--
実はこの時刻の配置だけでこの発表の大事な部分の説明がほぼほぼ入っているので、やることは単純な割に結構長くなります、ご注意ください。
-->

---
layout: default
---

# Place time label
<img src="/time_label_only.png" style="height:450px; margin:0 auto;"/>


<!-->
では要素と並びを確認します。

時刻があって、一時間分の間隔でまた時刻がある、それを時刻の分だけ繰り返します。

今回時刻の高さは適当に定義した固定の値を使います。
つまり、高さが固定の絡むなので、すごくシンプルです。

では、実際のコードを考えていきます。
-->


---
layout: default
---

# Create MyCustomLayout

```kotlin {*|1-4|8-11|5-9|11-14}
@Composable
fun MyCustomLayout(
  timeLabel: @Composable (LocalDateTime) -> Unit,
){
  // Create contents
  val timeLabels = TODO()
  // Add contents
  Layout( // Parent
    content = timeLabels, // Children
    modifier = Modifier,
  ){ measureables, constraints ->
        // Layout contents
        layout(...
    }
}
```

<!--
まずは基本部分からです。

これから作るLayoutのfunをComposableで定義します。
時刻の見た目を外からカスタマイズできるように引数でtimelabelのComposableを受け取るようにしています。->

ここがLayoutの定義です。

今回の発表ないでは、Layout関数自体を親、contentに追加したComposableたちを子要素と呼びます。

引数にはcontentとmodifierとmeasurePolicyを受け取ります。メジャーポリシーは末尾にラムダで書く事ができます。

Layoutを作るステップとしては ->
contentを作ってLayoutにわたし、measurePolicyのblock内でレイアウトを行う。基本的にはこれだけ何ですが、

-->

---
layout: 
---

# 3 step in layout phase 

<img src="/3step.png" style="height:400px; margin:0 auto;"/>

https://developer.android.com/develop/ui/compose/layouts/custom


<!--
レイアウトの中でもコヨウソを測定して、親のサイズを決めてコヨウソを配置するという三つの手順を踏む必要がありますので、こちらも順に見ていきます。
-->

---
layout: 
---

# Add Content 

```kt {*|1-6|8-12}
// create contents
val timeLabels = @Composable {
  repeat(24) { i ->
      timeLabel(LocalDateTime.of(now.year, now.month, now.dayOfMonth, i, 0)) // In the list.
  }
}

// add content
Layout(
        content = sideBarTimeLabels,
        modifier = Modifier
    ){ ..

```

<!-->
ではまずはLayoutに渡すcontent、コヨウソの作成です。->
今回は一日分なので24回repeatして渡されたtimeLabel関数にDateTimeを渡して呼び出しています。
TimeLabelのComponentの中でDateTimeが呼べるので好きに表示することができます。

そして、あとはこれをcontetntとして渡してあげます。
これでstep1のcontentの追加は完了です。

これで、追加されたcontentをMeeasurePolicyのブロックの中で処理することができます。

layoutに入っていきます。

layoutを始める前にラムダに何が渡されてくるのかを確認しましょう

-->

---
layout: default 
---

# MeasurePolicy?

```kt 

fun interface MeasurePolicy {

    fun MeasureScope.measure(
        measurables: List<Measurable>,
        constraints: Constraints
    ): MeasureResult

.... some functions...

```

<!--

ラムダの部分ではMeasurePolicyのmeasure関数に処理を書いていることになり、ここにはmeasurablesとconstraintsという値が渡されてきます。

-->

---
layout: default 
---

# Measurable

```kt

/**
 * A part of the composition that can be measured. This represents a layout.
 * The instance should never be stored.
 */
interface Measurable : IntrinsicMeasurable {
    /**
     * Measures the layout with [constraints], returning a [Placeable] layout that has its new
     * size. A [Measurable] can only be measured once inside a layout pass.
     */
    fun measure(constraints: Constraints): Placeable
}

```

<!--
measureblesはMeasurableのリストです。
Measurableは、measure関数が定義されたinterfaceで、このメソッドを呼ぶことで、子要素のサイズを測定することが可能です。

-->
---
layout: default
---

# Mesurables

```kt

// create contents
val timeLabels = @Composable {
  repeat(24) { i ->
      timeLabel(LocalDateTime.of(now.year, now.month, now.dayOfMonth, i, 0)) // In the list.
  }
}

```

<!--

先ほどわたしたcontentは大元は一つのこのようなCoposableなラムダだったんですが、
ここでのmeasurePolicyのmeasure関数にはtimeLabelひとつひとつに対応したMesurableがListになって入ってきます。

-->

---
layout: section
---

# Constraints

<!-->
次にconstraintsです。

コンストラインツはサイズに関する制約で、

minWidth, minHeight, maxWidth, と maxHeigh

を持っています。これをmesurableに渡すことで、制約の範囲内で測定が行われます。

-->

---
layout: default
---
# Constraints

```kt
  Layout( // Parent
    ..
    measurePolicy = { measureables, constraints ->
        // This constraints is **parent's** constraints
    }
  )

```

<!-->

ここで入ってくるものは親に与えられた制約になります。
具体的に親の制約というのをを少し確認してみます。

FillMaxSizeのScaffoldにMyCustomLyouatを入れているとして、

親のmodifierに幾つか制約をつけてここで渡されてくるconstraintsをprintしてみます。

-->

---
layout: default
---

# watch the constraints 

```kt {*|1-2|4-5|7-8|10-12}
// none
Constraints(minWidth = 0, maxWidth = 1080, minHeight = 0, maxHeight = 2400)

// .fillMaxHeight()
Constraints(minWidth = 0, maxWidth = 1080, minHeight = 2400, maxHeight = 2400)

// .verticalScroll(state = rememberScrollState()),
Constraints(minWidth = 0, maxWidth = 1080, minHeight = 0, maxHeight = Infinity)

// .fillMaxHeight()
// .verticalScroll(state = rememberScrollState()),
Constraints(minWidth = 0, maxWidth = 1080, minHeight = 2400, maxHeight = Infinity)

```

<!--

幅はさわっていないので、ハイトに注目してみましょう。
何もなかった場合は、minが0,maxが表示領域の高さになり、->

fillMaxHeightを指定した場合にはminHeightとmaxHeightが表示領域の高さになります。->

verticalScrollを入れてみるとmaxHeightがInfinityになり->
両方入れるとminが表示領域の高さ、maxがinfinityになります。

子要素のmeasureでは、この親の制約をそのまま使うか、copyして一部上書きして使うか、新しい制約を使うことになります。

-->


---
layout: default
---
# Copy 

```kt {1-4|6-9|11-16}
 
val placeable = measurable.measure(constraints.copy(
    minHeight = 0,
    maxHeight = constraints.maxHeight,
))

val placeable = measurable.measure(constraints.copy(
    minHeight = 100,
    maxHeight = 100,
))

val placeable = measurable.measure(constraints.copy(
    minHeight = constraints.maxHeight,
    maxHeight = constraints.maxHeight,
))
```

<!-->
例えば、親のサイズないでcomposable自体のサイズに任せてサイズを決める場合はminが0,maxはそのままmaxHeight
->サイズを固定したい場合には、両方に固定の値を入れたり、
->とにかく最大まで伸ばしたい場合には両方にmaxを入れるなどといった使い方ができます。

-->

---
layout: default
---
# Fixed size 

```kt
 
Constraints.fixed(width,height)

Constraints.fixedWidth(width)

Constraints.fixedHeight(height)

```

<!-->
また、親の制約に関係なく固定のサイズにしたい場合にはConstraintsにそれ用のfixed系のメソッドが生えているのでそれらを利用することも可能です。
-->

---
layout: default
---
# Be careful about crashes 

```kt {*|1-7|9-15|18-19}
// minHeigh > maxHeight
val placeable = measurable.measure(constraints.copy(
    minHeight = 100,
    maxHeight = 90,
))
// error message : 
// java.lang.IllegalArgumentException: maxHeight(90) must be >= minHeight(100)

// When constraints.maxHeight == infinity
val placeable = measurable.measure(constraints.copy(
    minHeight = constraints.maxHeight, 
    maxHeight = constraints.maxHeight,
))
// error message :
// java.lang.IllegalArgumentException: Can't represent a size of 2147483647 in Constraint 


If min gets Infinity, it will definitely crash.
constraints.hasBoundedHeight can be used to check if maxHeight is infinity
```

<!-->
ここで注意点がありまして、使い方によってはクラッシュするケースがあります。
→
まずはminの方がmaxより大きくなってしまった場合にはクラッシュします。
→

そして、minとmaxにInfinityが入ってしまった場合にもクラッシュします。

こういうmeasureをしていて、親にverticalScrollがついている場合などです。

つまり、minにInfinityが入る場合はクラッシュするので、minの方にconstraintsのmaxを入れる場合には特に注意する必要があります。->

具体的には、constraintsにmaxはいともしくはwidthがinfinityかどうかをチェックするhasBounded何たらというメソッドが生えているので、それを使って、処理を分岐させることができます

-->

---
layout: default
---
# If maxHeight is infinity 

```kt {1,3-6|2|*}
// When constraints.maxHeight == infinity
// measurable　== Text(Modifier = modifier.fillMaxHeight())
val placeable = measurable.measure(constraints.copy(
    minHeight = 0, 
    maxHeight = constraints.maxHeight,
))

// placeable.height is text height.

```

<!-->

あとこれはおまけなんですが、感覚的にはmaxHeightにinfinityが入っていて、mesurableのmodifierにMaxHeightが入ってきた時に高さがInfinityになってしまってクラッシュするんじゃないかと思ったんですが、->
確認して見たところ、クラッシュはせずTextの場合はwrapContentと同じような挙動になりました。
fillMaxをつけているのに最大サイズにならないときは親の制約を気にしてみると良いかもしれません。

（ここで一息つく）

では、実際にconstraintsを利用して子要素の測定をしていきます。

-->

---
layout: default
---

# Prepare

```kt
    val density = LocalDensity.current
    val minuteHeightDp = 2.dp
    val minuteHeightPx = with(density) {
        minuteHeightDp.roundToPx()
    }
    val hourHeightPx = minuteHeightPx * 60
```

```kt

    Layout(
        content = sideBarTimeLabels,
        modifier = Modifier
            .fillMaxHeight()
            .verticalScroll(state = rememberScrollState()),
...

```
<!-->

事前準備として、一分あたりの高さを2dpとして、そのPxと60をかけた一時間あたりの高さを定義しておきます。
LayoutPhaseで扱われる数字は基本的にdpではなくpixelです。

縦方向にスクロールしたいため親のModifierにverticalScrollをつけておきます。

これで準備ができたのでレイアウトをしていきます。

-->


---
layout: default
---

# Layout phase

1. Measure children: A node measures its children if any exist.
2. Decide own size: Based on these measurements, a node decides on its own size.
3. Place children: Each child node is placed relative to a node's own position.

<img src="/3step.png" style="height:300px; margin:0 auto;"/>


> https://developer.android.com/develop/ui/compose/phases#layout

<!-->

このレイアウトではLayoutPhaseの3Stepの通りに

コヨウソを測定して、親のサイズを決めて、コヨウソの配置をする必要があります。

まずはコヨウソを測定していきます。

-->


---
layout: default
---

# Let's measure

``` kt
{ timeLabelMeasureables, constraints ->
      val timeLabelPlacables: List<Placeable> = timeLabelMeasureables.map { measurable ->
          measurable.measure(
               constraints.copy(
                  minHeight = 0,
                  maxHeight = hourHeightPx
              )
          )
       }
```

<!--

具体的には、MesurableのListを回して、それぞれの要素で.measureを読んでplaceableを作成します。

先ほど親にはFillMasHeightとVerticalScrollをつけたので、
そのまま使うと、ラベルは全て表示領域いっぱいのの高さになってしまいますから、0と一時間あたりの高さを指定してやります。

こうすると、一時間分の領域内で、labelのcomponentのサイズが決まります。

これだけで測定は終わりです。

今回のラベルはTextをただ置いただけなので、テキストのサイズになります。

もし、ラベルにFillMaxHeightをつけていた場合は、一時間分の高さまでは伸びることになります。

measureの結果はPlaceableというクラスで返ってきます。このPlaceeableから高さや幅を取得することが可能です。このラベルと同じサイズの何かを作りたいといった場合にもPlaceable.haitを参照することで実現ができます。

// ちなみにmeasuredHeighというものも生えていて、heightは最終的に表現されるサイズ、mesuredHeightは測定されたサイズで、基本的には一致しているが、場合によっては一致しないらしいので注意が必要です。

// いろいろ試したんですが、一致しないケースを再現できなかったので、再現方法がわかる方いたらぜひ教えてください。
-->

---
layout: default
---

# Let's layout

```kt
// interface MeasureScope { ...

fun layout(
        width: Int,
        height: Int,
        alignmentLines: Map<AlignmentLine, Int> = emptyMap(),
        placementBlock: Placeable.PlacementScope.() -> Unit
    ): MeasureResult

```

alignmentLines:https://developer.android.com/develop/ui/compose/layouts/alignment-lines?hl=ja

<!--

次に親のサイズを決め、コヨウソを配置します

ここでは小文字の方のlayout関数を使います

この関数はwidth height alimentLines placeMentBlockを受け取ります。

widthとheightで親のサイズを確定し、placementBlockで小要素を配置します。

-->


---
layout: section
---

# alignmentLines
https://developer.android.com/develop/ui/compose/layouts/alignment-lines?hl=ja

<!--

alignmentLinesは今回使わないのですごく軽く説明するのですが、これを使うことで、親の直接的な親および間接的な親のLayoutフェーズに任意のIntを渡すことが可能です。

例えばTextのFirstBaselineなんかがこの仕組みを利用しています。

詳しくは公式のページをご覧ください。名前だけでも覚えておくといざとのときに役に立つのではないかと思います。

（ここで一息）

ではlayout関数を使って配置をしていきましょう

-->


---
layout: default
---

# Let's layout

```kt {*|3-4,6|1,3,5,6|7-13}
val totalHeight = hourHeightPx * timeLabelMeasureables.size

layout(
    width = constraints.maxWidth,
    height = totalHeight
) {
  timeLabelPlacables.forEachIndexed { index, placeable ->
      placeable.place(
        x = 0,
        y = hourHeightPx * index,
        zIndex = 0f // 0f is specified by default, so there is no need to specify it.
      )
  }
}

```


<!--
まずは親のWidhtyとはいとを設定します。->
幅はできる限りいっぱいに広がってほしいため、ConstraintsのmaxWidthを使って指定します。
->高さは表示する時間全てを置けるだけ必要なので、ラベルの数に一時間あたりの高さをかけたものになります。
ここにMaxHeightを入れるとinfinityが入ってしまい、IllegalStateExceptionでcrashするのでご注意ください。

java.lang.IllegalStateException: Size(1080 x 2147483647) is out of range. Each dimension must be between 0 and 16777215.

->
blockの中ではこ要素を配置します。
やることは先ほどmesureすることで作られたPlaceableのplaceを呼び出すだけです。
xとyとzindexが指定できます。

Composeの世界では0,0が左上なので右に動かしたければxに、下に動かしたければyに+の値を入れます。

zIndexは描画の順で、大きい値を指定するほど手前に描画されます。

今回は横にはずらさずに下に順に置いていきたいので、一時間あたりの高さにindexをかけたものをyに入れています。

これで時刻ラベルの実装は終わりです。

-->


---
layout: default
---

# Label

<div style=" display: flex; justify-content: center;">
<img src="/label_wrapp.png" style="height:450px; margin:0 auto;"/>
<img src="/label_fill.png" style="height:450px; margin:0 auto;"/>
</div>

<!--

左がlabelにmodifierを指定していない場合、右がFillMaxHeightとpaddingを指定してみた場合です。
想定通り、1時間の範囲でサイズが決まっているのがわかります。

では次、に縦軸（横線）の表示

fuga20: 13:10
-->

---
layout: default
---

# Vertical axis display according to time


<img src="/axis.png" style="height:450px; margin:0 auto;"/>

<!--

これですね。

特に難しいことはないです。

-->

---
layout: default
---

# Add to contents

```kt
    val backGroundLines = @Composable {
        repeat(timeLabelCount) { i ->
            Box(
                modifier = Modifier
                    .fillMaxWidth()
                    .height(1.dp)
                    .background(MaterialTheme.colorScheme.onSurface.copy(alpha = 0.2f))
            )
        }
    }

```
<!-->

とりあえず、ラベルと同じようにLayoutに渡すcomopsesableを定義します。
ラベルと同じ個数の横幅いっぱいで高さ1dpの色つきBoxにしてみます。

<-->

---
layout: default
---

# Add to contents

```kt {*}
Layout(
    /*  
    content = {
        sideBarTimeLabels()
        backGroundLines()
    }
    */
    contents = listOf(
        sideBarTimeLabels,
        backGroundLines
    ),
    ..
```

<!--

そしてレイアウトに渡します。

ここでワンポイントなんですが、Layout関数にはContentとしてComposableを受け取るものとContentsとしてcomposableのListを受け取るものとがあります。

contentの方でも渡せると言えば渡せるんですが、そうするとmeasureBlockに入ってくるmesurablesがラベルとラインの区別なく全て一つのリストとして入ってくるため使いずらいので、属性の違うcomposableを扱う場合はListで渡す方が使いやすいです。

-->


---
layout: default
---


# Add to contents

```kt
    { (timeLabelMeasureables, backGroundLinesMeasureables), constraints ->
    // measure and layout...
    ...
```

<!--
使う時はこのように括弧で囲むとそれぞれに代入できるので、あとは先ほどと同じように

-->

---
layout: default
---


# measure line

```kt
val linePlaceables = backGroundLinesMeasureables.map {
    it.measure(
        constraints.copy(
            minHeight = 0,
            maxHeight = hourHeightPx
        )
    )
}

```

<!-->

measureして

<-->

---
layout: default
---


# Layout line

```kt
layout(constraints.maxWidth, totalHeight) {
    timeLabelPlacables.forEachIndexed { index, placeable ->
        val y = hourHeightPx * index
        placeable.place(
            x = 0,
            y = y,
        )
                    
        linePlaceables[index].place(
            x = 0,
            y = y,
        )
    }
}

```


<!-->

配置すれば完成です。
TimeLabelと同じだけ作っているので、雑に時刻ラベルと一緒に同じ位置にplaceしています。

<-->


---
layout: default
---

# Compolete 


<img src="/axis.png" style="height:450px; margin:0 auto;"/>


<!-->

（ここで一息）

<-->

---
layout: default
---

# Put Event

<img src="/event.png" style="height:450px; margin:0 auto;"/>


<!--
さて、次はイベントを置いていきます。

まずはイベントのデータ構造を考えていきます。

-->

---
layout: default
---

# Event Data

```kt

data class CalendarEvent(
    val id: String,
    val title: String,
    val startTime: LocalDateTime,
    val endTime: LocalDateTime
)

```

<!--
表示に必要な最低限の情報としては
start
end
のタイムがあれば良いですが、一応idとtitleだけ入れています。
interfaseとして定義して使う側で拡張できるようにとかいう話もあるんですが今回は割愛します。
-->

---
layout: default
---

# Add event

```kt

@Composable
fun DailySchedule(
    modifier: Modifier = Modifier,
    events: List<CalendarEvent>, // add
    timeLabel: @Composable (LocalDateTime) -> Unit = { StandardTimeLabel(time = it) },
    eventContent: @Composable (CalendarEvent) -> Unit = { EventItem(event = it) }, // add
) { ...


```

<!--

まずはカスタムレイアウトの引数に、表示するeventのリストとconposableを追加します

-->

---
layout: default
---

# Create eventContents

```kt {1-8|9-20}
val eventContents = @Composable {
    events.sortedBy { it.startTime }.forEach {
        Box(modifier = Modifier.calenderEventModifier(it)){
            eventContent(it)
        }
    }
}
..
Layout(
    contents = listOf(
        sideBarTimeLabels,
        backGroundLines,
        eventContents // add
    ),
    ..
    { (timeLabelMeasureables, backGroundLinesMeasureables, eventMeasureables /* add */) .. ->

```

<!--
イベントのリストから、ebentのcomposeを同じだけ作って→渡します。

これでLayoutの中でstartTimeとendTimeを見て配置すればいいのですが、一つ問題があります。
ブロックに入ってきたmesurabeはeventの情報を持っていないので、今のままだとstartとendをmeasureのblock内で取ってくることができません。
-->

---
layout: section
---

# ParentDataModifier

<!-->
そこで
- ParentDataModifier
というものを使います。

これを利用することで、任意のdataを親に伝えることが可能です。

使い方は結構簡単で
-->
---
layout: default
---

# ParentDataModifier

```kt {1-5|9-16}
fun Modifier.calenderEventModifier(event: CalendarEvent) = this.then(
    object : ParentDataModifier {
        override fun Density.modifyParentData(parentData: Any?): Any = event
    }
)

----------

    val eventContents = @Composable {
        events.sortedBy { it.startTime }.forEach {
            Box(modifier = Modifier.calenderEventModifier(it)){ // here
                eventContent(it)
            }
        }
    }

```

<!--
このように、Modifierの拡張カンスうして定義します。
拡張関数では親に渡したいで－たを受け取り、
中ではParentDataModifierを継承したオブジェクトを作成し、modifParentDataに受け取ったデータをセットします。

→あとは、使いたい要素、今回だとeventのmodifierに追加して親から参照したいデータを渡します。

eventContentは中がどういう構造になっているかがわからないので、直接modifierを追加せずにBoxで囲んで追加するのが安全です。
-->

---
layout: default
---

# Get parent data

<br>
<br>
<br>

```kt

    val eventPlaceablesWithEvent = eventMeasureables.map { measurable ->
        val event = measurable.parentData as CalendarEvent

```


<!--

こうすると、layoutの中で、mesurable.parentDataとしてCalendarEventを参照することができます。

問題が解決できたので次に進みましょう。

-->

---
layout: default
---

# Layout event

```kt {*|2|3-4|3-5|6|7-14}
val eventPlaceablesWithEvent = eventMeasureables.map { measurable ->
    val event = measurable.parentData as CalendarEvent
    val eventDurationMinutes =
        ChronoUnit.MINUTES.between(event.startTime, event.endTime)
    val eventHeight = (eventDurationMinutes * minuteHeightPx).toInt()
    val eventWidth = constraints.maxWidth - labelMaxWidth                
    measurable.measure(
        Constraints.fixed(
            width = eventWidth,
            height = eventHeight
        )
    ) to event
}

```


<!-->

まずは、先ほどの通りeventをparentDataから取ります。->
そしてeventが何分なのかをstartとendから求めます。->
一分あたりの高さをかけて高さを求めます。->
幅はlabelと重ならないように、一番幅のあるラベル分だけ引いておきます。->

そしてEventは幅も高さも固定のサイズなので、Fixedで固定して、測定します。

これで、イベントの時間による高さと、ラベルを避けて収まる幅のplaceableができました。
-->

---
layout: default
---

# Layout event

```kt {*|2,7|9-10,12-13,15}
layout(constraints.maxWidth, totalHeight) {
    val dataTimeYMap = hashMapOf<LocalDateTime, Int>()
    timeLabelPlacablesWithDataTime.forEachIndexed { index, (placeable, dateTime) ->
        val y = hourHeightPx * index
        placeable.place( x = 0, y = y )
        linePlaceables[index].place( x = 0, y = y )
        dataTimeYMap[dateTime] = y
    }
    eventPlaceablesWithEvent.forEach { (placeable, event) ->
        placeable.place(
            x = labelMaxWidth,
            y = dataTimeYMap[event.startTime.getZeroMinuteLocalDateTime()] 
                    + event.data.startTime.minute * minuteHeightPx ?: 0,
        )
    }
}

```

<!--
最後にLayout関数で配置しますー＞
タイムラベルのyの位置を基準にしてEventのStartの位置を計算したいので、dateTimeとYのMapを作っておきます。
→
そしてStartTimeからラベルのY位置をとってきて、分の分だけずらしてやればokです。。
-->

---
layout: default
---

# Add event


<img src="/event.png" style="height:450px; margin:0 auto;"/>


<!--
これでイベントの配置ができました。


fuga20:4:50
-->

---
layout: default
---

# Multiple events occur at the same time

<img src="/event_overwrap.png" style="height:450px; margin:0 auto;"/>


<!--
次はこういう状態です。

同じ時間にイベントがあったときに、幅と位置を調整して全て見えるようにします。

-->

---
layout: default
---

# Step

- Grouping event
- Adjust width by group count
- Adjust offset by index


<!--

stepとしては、　

- 重なりのあるイベントをグルーピング
- グループのかずに合わせて幅を調整
- グループ内の何番目かによってxOffsetを調整

順にやっていきます。
位置とサイズを調整するためには重なりのあるイベントを探す必要があるのでまずはグルーピングします。

-->


---
layout: default
---

# Create group

``` kt
fun groupOverlappingEvents(events: List<CalendarEvent>): List<List<CalendarEvent>> {
    val sortedEvents = events.sortedBy { it.startTime }
    val groupedEvents = mutableListOf<MutableList<CalendarEvent>>()
    var currentGroup = mutableListOf<CalendarEvent>()
    currentGroup.add(sortedEvents[0])
    for (i in 1 until sortedEvents.size) {
        val currentEvent = sortedEvents[i]
        val lastEventInGroup = currentGroup.last()
        if (currentEvent.startTime < lastEventInGroup.endTime) {
            currentGroup.add(currentEvent)
        } else {
            groupedEvents.add(currentGroup)
            currentGroup = mutableListOf()
            currentGroup.add(currentEvent)
        }
    }
    groupedEvents.add(currentGroup)
    return groupedEvents
}
```

<!--
コード的にはこんな感じで、CustomLayoutと直接関係ないので説明は省きますが、重なっているものが同じListに入るListのListを作っています。

実はバグが取り切れてなくて複雑な重なりに対応できていないので参考程度に見ていただければと思います。
-->

---
layout: default
---

# Add group data to event

```kt {*|1-4,12-15}
data class WrappedCalendarEvent(
    val group: Group,
    val data: CalendarEvent
) {
    data class CalendarEvent(
        val id: String,
        val title: String,
        val startTime: LocalDateTime,
        val endTime: LocalDateTime
    )

    data class Group(
        val size: Int,
        val index: Int,
    )
}

```


<!--

そうしましたら、重なるイベントの数と位置をEventに持たせます。
レイアウトするとき、幅を決めるのとxのoffsetを決めるのに必要です。ー＞
CalenderEventを一枚ラップして、追加の情報として、Groupを追加しています。
ここの構造は多分お好みで大丈夫です。

-->

---
layout: default
---

# Add group data to event

``` kt
    val eventContents = @Composable {
        groupOverlappingEvents(events).forEach { group ->
            group.forEachIndexed { index, event ->
                // Wrap
                val wrappedEvent = WrappedCalendarEvent(
                    group = Group(index = index, size = group.size),
                    data = event
                )
                Box(
                    modifier = Modifier.calenderEventModifier(wrappedEvent)
                ) {
                    eventContent(wrappedEvent)
                }
            }
        }
    }

```


<!--
そして、eventContentsを作る際にWrapします。

eventContentはWrappedEventを受け取るようにしておくことで、indexを使って見た目を変えたりする事ができます。

これで準備完了です。

-->

---
layout: default
---

# Add group data to event

```kt　{*|1-8|13}
// calculate with group size
val eventWidth = (constraints.maxWidth - labelMaxWidth)  / event.group.size
measurable.measure(
    Constraints.fixed(
        width = eventWidth,
        height = eventHeight
    )
) to event

// ---------------------------------- layout

placeable.place(
    x = labelMaxWidth + (placeable.width * event.group.index),
    y = dataTimeYMap[event.data.startTime.getZeroMinuteLocalDateTime()] 
        + event.data.startTime.minute * minuteHeightPx ?: 0,
)

```

<!--
あとは特に特別なことはなくて、->幅をsizeで割ってやって、->xの座標をindex分だけずらしてやればokです。
-->

---
layout: default
---

# 完成

<img src="/event_overwrap.png" style="height:450px; margin:0 auto;"/>


<!--

完成です。


fuga20:2:30

-->

---
layout: default
---

# Drag and drop


<video autoplay muted style="height:450px; margin:0 auto;">
  <source src="/drag2.mov" type="video/mp4" >
</video>


<!-->

つづいてがドラッグ＆ドロップの実装です。

イベントをつまんで動かすこの動きですね。

-->

---
layout: default
---

# Choices

1. 各イベントアイテムにDraggableをつけてそれぞれでドラッグを管理する
2. カスタムViewにpointerInputをつけて管理する

<!-->

TODO:英語

思いつく方針としては、二つありました

各イベントアイテムにDraggableをつけてそれぞれでドラッグを管理する
と
カスタムViewにpointerInputをつけて管理する

方法です。
今回は簡単そうな1を選択しました。

ドラッグでもっと色々な動き、例えば、イベントの入れ替えなどをするのであれば2の方針も検討する必要がありそうです。

具体的な実装を見てみましょう。
-->


---
layout: default
---

# Remember draggingItemOffset, Add DragState.

```kt　{1-4|6-21}
// In CustomComponent
var draggingItemYOffset: Float by remember {  
    mutableFloatStateOf(0f)  
}
----------------------------
data class WrappedCalendarEvent(
    val group: Group,
    val dragState: DragState = DragState.None, // add
    val data: CalendarEvent
) {
    ..
    sealed class DragState {
        data object None : DragState()
        data class Dragging(
            val startTime: LocalDateTime,
            val endTime: LocalDateTime
        ) : DragState()
    }
}

```

<!--
事前準備として、ドラッグしたアイテムのY位置のオフセットを大元のcomposableに持たせます。

そして、各イベントにはドラッグ状態を表すdragStateを持たせます。

Drag中かどうかというのと、drag中のDateTimeはイベントの描画に利用したいため各イベントに持たせています。
offsetの更新はイベントの描画の更新よりも高頻度なため、イベントに持たせると余計にcompositionが走ることになるため別で持たせた方が良いです。
-->

---
layout: default
---

# Draggable

```kt {*|5-7|10}
Box(
    modifier = Modifier
        .calenderEventModifier(wrappedEvent)
        .draggable(
            state = rememberDraggableState { delta -> 
                draggingItemYOffset += delta
            },
            onDragStarted = { .. },
            onDragStopped = { .. },
            orientation = Orientation.Vertical
        )
    ) { eventContent(wrappedEvent) }
```

<!--
あとは、やることは結構簡単で、eventのcontentにDraggableをつけます。->
そして、stateのラムダがdrag中に呼ばれるので、入ってくるdeltaでdraggingItemYOffsetを更新しましょう。->
縦のドラッグなので、orientationはBerticalにしておきます。

-->

---
layout: default
---

# Draggable

```kt {6-17}
Box(
    modifier = Modifier
        .calenderEventModifier(wrappedEvent)
        .draggable(
            state = rememberDraggableState { delta -> /* TODO */ },
            onDragStarted = {
                wrappedEvents = wrappedEvents.map {
                    if (it.data == wrappedEvent.data) {
                        it.copy(
                            dragState = DragState.Dragging(
                                startTime = it.data.startTime,
                                endTime = it.data.endTime
                            )
                        )
                    } else { it }
                }
            },
            onDragStopped = { /* TODO */}, orientation = Orientation.Vertical
            ) ) { eventContent(wrappedEvent) }
```

<!--
>

そしてOnDragStartedでeventのDragの状態を更新してやります。
-->

---
layout: default
---

# Draggable

```kt {6-16}
Box(
    modifier = Modifier
        .calenderEventModifier(wrappedEvent)
        .draggable(
            state = rememberDraggableState { delta -> /* TODO */ }, onDragStarted = { .. },
            onDragStopped = { 
                draggingItemYOffset = 0f
                wrappedEvents = wrappedEvents.map {
                    if (it.dragState is DragState.Dragging) {
                        onFinishDragEvent(it.data, it.dragState)
                        it.copy(dragState = DragState.None)
                    } else {
                        it
                    }
                }                
            },
            orientation = Orientation.Vertical
        )
    ) { eventContent(wrappedEvent) }
```

<!--
最後にonDragStoppedで、DragStateとyOffsetをを元に戻します。
そのタイミングで、onFinishDragEventをよんでやります。これはCostomLayoutの引数で関数を受け取るようにしておきましょう。
-->

---
layout: default
---

# Add OffsetY

```kt　{1,7-11}
eventPlaceablesWithEvent.forEach { (placeable, event) ->
    val standardY = dataTimeYMap.getOrDefault(
        event.data.startTime.getZeroMinuteLocalDateTime(),
        0
    ) + event.data.startTime.minute * minuteHeightPx

    val (y, z) = if (event.dragState is DragState.None) {
        standardY to 0f
    } else {
        standardY + draggingItemYOffset.toInt() to 1f
    }

    placeable.place(
        x = labelMaxWidth + (placeable.width * event.group.index),
        y = y,
        zIndex = z,
    )
}

```


<!--
これでlayoutのタイミングでドラッグの状態が取れるようになったので、Layoutのblockの中でもしdtag中のイベントだった場合に、offSetぶんずらしてやるようにします。
単純にoffsetyを足すだけです。
ついでにzindexも1fにして、常に一番上に描画されるようにしています。

これで一応ドラッグができるようになりました。

-->

---
layout: default
---

# Drag and drop

<video autoplay muted style="height:450px; margin:0 auto;">
  <source src="/drag2.mov" type="video/mp4" >
</video>


<!--
ドラッグ中のアイテムの見た目の更新と、ドラッグした後の処理はスナッピングの後にやります。


fuga20:3:24

-->

---
layout: default
---


# Snapp to round number

<video autoplay muted style="height:450px; margin:0 auto;">
  <source src="/draging.mov" type="video/mp4" >
</video>


<!--
次はキリのいい時刻へのスナッピングです。

このドラッグしたアイテムがカクカクとする動きですね。今回は15の倍数の分にスナップしてみます

やることは簡単で、ドラッグ中に単純にoffsetを反映した位置ではなく一番近い位置に起くだけです。

-->


---
layout: default
---


# Find target minute

```kt

private fun findClosestFiveMinute(dateTime: LocalDateTime): Int {
    val minute = dateTime.minute
    val tickMinutes = 15
    val remainder = minute % tickMinutes
    return if (remainder < tickMinutes / 2 + 1) {
        minute - remainder
    } else {
        minute + (tickMinutes - remainder)
    }
}

```


<!--


まずは一番近い15の倍数の分を取得するメソッドを書きます。
これはLayoutと直接関係ないので説明は割愛します。


-->

---
layout: default
---

# Find target minute

```kt {*|5|6|7-10|11|13|15,17-19}
eventPlaceablesWithEvent.forEach { (placeable, event) ->
    val (y, z) = if (event.dragState is DragState.None) {
        // not changed .. 
    } else {
        val offsetMinute = draggingItemYOffset/(minuteHeightPx.toFloat())
        val draggingStartTime = event.data.startTime.plusMinutes(offsetMinute.toLong())
        val targetHourY = dataTimeYMap.getOrDefault(
            draggingStartTime.getZeroMinuteLocalDateTime(), 0
        )
        val findClosestFiveMinute = findClosestFiveMinute(draggingStartTime)
        
        targetHourY + findClosestFiveMinute*minuteHeightPx to 1f
    }
    placeable.place(
        x = labelMaxWidth + (placeable.width * event.group.index),
        y = y,
        zIndex = z,
    )
}

```

<!--
これを使っていきます。

offsetから何分分移動しているかを計算して、->そこから時刻を計算して、->mapから0分だった場合のyを取ってきて、->先ほどのメソッドで見つけた一番近くのキリがいい分の分だけ高さを加えて->yに反映してあげればOKです。
-->

---
layout: default
---

# Dragging

``` kt
eventPlaceablesWithEvent.forEach { (placeable, event) ->
    ..
        wrappedEvents = wrappedEvents.map {
            if (it.data == event.data) {
                it.copy(
                    dragState = DragState.Dragging(
                        startTime = targetStartTime,
                        endTime = targetEndTime,
                    )
                )
            } else {
                it
            }
        }
.. 
        targetHourY + findClosestFiveMinute*minuteHeightPx to 1f         


```

<!--

ドラッグ中のイベントの見た目が変えられるように、同じタイミングでeventが持っているDragg中のstartとendも更新してあげます。
あとはこれを使ってイベントの見た目を変えるようにしてあげれば完成です。

-->

---
layout: default
---

# Dragging


<video autoplay muted style="height:450px; margin:0 auto;">
  <source src="/draging.mov" type="video/mp4" >
</video>


<!--

ドラッグ完了したタイミングのonFinishDragEventでListの中身を更新する処理は呼び出し元の方に入れていますが特別なことはしていないので割愛します。

で、ですね、これで一応ドラッグアンドドロップはできたのですが、実は一つパフォーマンスに関する問題が発生しています。

-->


---
layout: default
---

# Recomposition?


<video autoplay muted style="height:450px; margin:0 auto;">
  <source src="/update.mov" type="video/mp4" >
</video>


<!--
先ほどの動きをLayout Inspectorで見てみると、こんな感じでドラッグ中に動かしているアイテム以外もreCompositionが走っています。

ただ、ドラッグ中に中身を更新しているのはドラッグ中のeventだけなので、動かしていないイベントはRecompositionが走る必要性がないはずです。

これは、LocalDateTimeが安定していないとみなされていことが原因なので、安定してるとみなして、不要なrecompositionをskipできるようにしてあげると解決します。

-->


---
layout: default
---

# Stability configuration file

``` kt

// -------  compose_compiler_config.conf
// Consider LocalDateTime stable
java.time.LocalDateTime


// -------  build.gradle(:app)
kotlinOptions {
  freeCompilerArgs += listOf(
      "-P",
      "plugin:androidx.compose.compiler.plugins.kotlin:stabilityConfigurationPath=" +
      "${project.absolutePath}/compose_compiler_config.conf"
  )
}

```

https://developer.android.com/develop/ui/compose/performance/stability/fix#kotlin


<!--

具体的にはandroid developerにあるように、.confファいルに対象のpackage名を追加します。今回はLocalDateTimeです。そしてbuildGradleこれらのKotlinOptionsを追加すればokdesu.

詳細はリンク先を見て見ていただければと思います。

-->

---
layout: default
---

# Ok!

<video autoplay muted style="height:450px; margin:0 auto;">
  <source src="/drag_notUpdated.mov" type="video/mp4" >
</video>



<!-->

これでリビルドすると、このようにドラッグしているアイテム以外のrecompositionは走らずにすみます。
挙動に影響が出ているわけではないので大きな問題ではないのですが、頭の片隅に入れておくと役に立つ日が来るかもしれません。

fuga20:3:20

<-->


---
layout: section
---

# Lazy redering  

<!-->

最後に遅延レンダリングです。


-->

---
layout: section
---

# ~~Lazy redering~~  

# Scroll smoothly even with a large list.

<!--

遅延レンダリングと書いてしまったのですが、やりたいこととしては、方法はさておき、
カスタムレイアウトが縦、もしくは横に非常に長く、かつアイテムも大量に存在した場合にも、滑らかにスクロールできるようにしたい。
という話になります。

ここに関してはかなり語弊のある書き方をしてしまい大変申し訳ありません。

LazyColmunのようなことをやりたい、というとわかりやすいかもしれません。

これも正直Costom Layoutと直接関係ある話ではないので参考程度に聞いていただけると良いと思います。

（ここで一息）

ではまず効果を実感できるように、ここまでに作成したDailySchedulerで365日分の予定をを縦に並べてみます。

-->


---
layout: default
---

# Layout 365 days  

## OOM occured

<br/>
<br/>
<br/>

<img src="/oom.png" style="margin:0 auto;"/>


<!--

単純にラベルとイベントを365日分表示してみると、アウトオブメモリでクラッシュしてしまいます。

これを表示して、普通にスクロールできるようにしたいです。

// まずはLayoutでできるのか、ということを考えます。

// というのも、LazyColmunとかを見てみると、中はSubcomposeで作られていルようです。
// ユーザーが渡してくるcomposeによってアイテムのサイズが変わるため、何個配置すべきかが測定するまでわからないので、確かにSubComposeLayoutでないと実現が難しそうです。

// 一方で今回の我々のケースはというと、中のアイテムのサイズは時刻と固定値によって計算しているので実はLayoutフェーズより前に決められます。
// なので、画面に表示されるContentを事前に計算して、それだけをLayoutに渡すようにすれば実現ができそうです。

-->


---
layout: default
---

# Step

- ScrolStateのviewPortから画面に表示できる個数を計算する
- scrollのoffsetから今表示したいindexを計算する
- indexと個数から今表示したい時刻を決める
- 表示する時刻から表示するeventを決める
- 今まで通りのLayout処理を行う


<!--
手順としては、

こんな感じです。順にやっていきます。
-->

---
layout: default
---

# Calculate item count & index

```kt {*|1,3-8,|1,10-16}
val scrollState = rememberScrollState()

// viewPortから画面に表示できる個数を計算する
val visibleItemCount by remember {
    derivedStateOf {
        scrollState.viewportSize / (hourHeightPx) + 12
    }
}

// scrollのoffsetから今表示したいindexを計算する
val visibleItemStartIndex by remember {
    derivedStateOf {
        // Align the start position of the Event, so leave some margin in front.
        max(0, (scrollState.value / hourHeightPx) - 10)
    }
}
```

<!--
まずは画面に表示できる個数の計算です。
scrollStateからviewPortのサイズ、Viewの表示サイズが取れるので、それを一時間あたりの高さで割って表示する時刻の数を求めます。
この12は、前後に余裕を持つためのマージンです。->

次に、何番目から表示すればいいかをscrollのオフセットから計算します、scrollStateのvalueからoffsetが取れるので、それを一時間あたりの高さで割ることで求められます。

この10引いているのは、EventをStartTime基準で並べているため、手前にある程度長く余裕を持つためにやっています。
これはちょっと雑な実装で10時間以上の予定があると多分うまく動かないのですが、本筋ではないので今回は一旦おいておいています。

どちらもderivedStateOfにしておいた方が無駄がないです。

-->


---
layout: default
---

# Decide time label to show 

```kt {1-7|9-20}
val visibleTimeLabel: Set<LocalDateTime> =
    remember(visibleItemStartIndex, visibleItemCount) {
        createShouldShowTimeLabelSet(
            visibleItemStartIndex,
            visibleItemCount,
        )
    }
// ----------
private fun createShouldShowTimeLabelSet(
    visibleItemStartIndex: Int,
    viewItemCount: Int,
): Set<LocalDateTime> {
    val mutableSet = mutableSetOf<LocalDateTime>()
    for (i in visibleItemStartIndex..visibleItemStartIndex + viewItemCount) {
        val dateTime = starTime.plusHours(i.toLong())
        mutableSet.add(dateTime)
    }
    return mutableSet.toSet()
}

```

<!--
この二つの値から、表示したい時刻のSetを作ります。
startIndexからitemCount分のtimeLabelを作っています。

startのIndexからViewCountの分回して、DateTimeを詰めます。特に特別なことはないです。
-->

---
layout: default
---

# indexと個数から今表示したい時刻を決める

```kt {*|5-10}
var wrappedEvents by remember(events, visibleTimeLabel) {
    mutableStateOf(
        groupOverlappingEvents(events).flatMap { group ->
            group.mapIndexed { index, event ->
                // Not considering very long events
                if (!visibleTimeLabel.contains(event.startTime.getZeroMinuteLocalDateTime())&&
                    !visibleTimeLabel.contains(event.endTime.getZeroMinuteLocalDateTime())
                ) {
                    return@mapIndexed null
                }
                WrappedCalendarEvent(
                    group = Group(index = index, size = group.size),
                    data = event
                )
            }
        }.filterNotNull()
    )
}
```

<!-->
そしてこのsetの中ににstartかendが含まれているEventを抽出します。

ここなんですが、ちょっとバグが取りきれていなくてすごく長いeventがあったら表示されないんですが、お許しください。

-->


---
layout: default
---

# Layout

```kt {1-2|5-8}
// val totalHeight = hourHeightPx * timeLabelMeasureables.size
val totalHeight = hourHeightPx * timeLabelCount // 24*365

// ----------------------
timeLabelPlacablesWithDataTime.forEachIndexed { index, (placeable, dateTime) ->
    // val y = hourHeightPx * index
    val y = hourHeightPx * (index + visibleItemStartIndex)

```

<!--
あとはLayoutのTotalHeightを表示したい時刻の数だけ分だけ伸ばすようにして、今回だと24時間かける365日分ですね。

TimeLabelのyをstartIndexの分だけずらすようにしてやるだけで完成です。

eventや背景の線は時刻を基準に配置しているのでこれだけで一緒にズレます。

-->
---
layout: default
---

# We can scroll

<video autoplay muted style="height:450px; margin:0 auto;">
  <source src="/scrll_ok.mov" type="video/mp4" >
</video>



<!-->

これで、実行してみると、無事スクロールができました！

-->

---
layout: default
---

# We can scroll

<img src="/scroll_inspecter.png" style="height:450px; margin:0 auto;"/>

<!-->

Layout Inspectorを見てみると、必要な分の要素だけが配置されていることがわかります。

-->

---
layout: default
---

# Recomposition

<video autoplay muted style="height:450px; margin:0 auto;">
  <source src="/scroll_recomposition.mov" type="video/mp4" >
</video>


<!-->
そして一時間分スクロールするたびにRecompositionが走るようになっています。

ということで無事、一通り実装を終えることができました。

fuga20:4:20
-->

---
layout: default
---

# まとめ


<!-->

TODO 考える

-->
