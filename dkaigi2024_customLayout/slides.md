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

# 表紙

<!--
始めたいと思います、よろしくお願いします。
-->

---
layout: default
---

# 自己紹介


<!--
ウンタラカンタラ
-->

---
layout: default
---

# 目次

ウンタラカンタラ

---
layout: default
---

# 今日作るもの

- 時刻に合わせた縦軸表示  
- 時刻と対応したイベントの配置・サイズの調整  
- 同時刻に複数イベントがあった際の幅の調整  
- イベントのドラッグ&ドロップ
- 時刻へのスナッピング  
- 遅延Redering

<!--
では初めて行きましょう。
今日は、使って知るCustomLayoutということで、何も知らない状態から、ちょっとしたCustomLayoutを作ルことで、理解を深めていきます。

何を作るかというと、こちらです。
カレンダーアプリとかでよくWeeklyとかで表示されるうちの一日分を作っていきます。
このSession内ではDailySchedulerと呼ぶことにします。

具体的には
- 時刻に合わせた縦軸表示  
- 時刻と対応したイベントの配置・サイズの調整  
- 同時刻に複数イベントがあった際の幅の調整  
- イベントのドラッグ&ドロップ
- 時刻へのスナッピング  
- 遅延Redering  
-->

---
layout: default
---

# CostomLayoutとは



<!--
ちょっと最初に謝らなければいけないんですが、ComposeにCustomLayoutという関数はありません。
なので今日の話は、厳密にはCostomLayoutを作成するためのLayout関数の話になります。
https://developer.android.com/develop/ui/compose/layouts/custom


じゃあこのLayout関数がどういうものなのかを知るために、まず毎度お馴染みのcoposeの3Stepを確認する必要があります。
-->

---
layout: default
---
耳にタコでしょうね
# composeのサンステップ

composition

layout

draw


<!--
があります

compositionはインスタンスを作る

Columnとかで例に出す

layout、
子要素のサイズを測定して配置して自分のサイズを確定する

Columnでは自動でやってくれる
offsetの例

draw 描画
drawabackgroundの例


Layout関数はこのLayout部分を自分で実装するという話になります。
-->

---
layout: default
---

# 少し詳しく


<!--
例えばColmunもこの関数を使って作られています。
こんな感じで重ならないように順番に置きますよーとあります。

なので、半分重なるように置きたいんだ！
みたいな場合にこのLayout部分をいじることで、実現できます。
ずらすだけなので、offsetつければいけるかも、と思ったりするんですが、Colmunでやろうと思っても、compositionのタイミングではTextの高さがわからないので、できません。
なのでLayoutフェーズで高さを測定してから、位置を決めて配置する必要があります。


ちなみに、ここを半分重なる、ではなく10dp重ねたいんだ！とする場合はLayout使わず可能ですね。
-->

---
layout: default
---

# Layout関数以外の選択肢

- subcomopseLayout
- BoxWithConstraint
- Modifier.layout


<!--
などがあります。

これはそれぞれこう・こう・こうという感じになりますので、用途に合わせて選びましょう。

今回は複数の要素があって、カレンダーのイベントは必ず配置するんだけど長さと位置が変わるだけということでLayoutを利用します。

上からここまで5分
-->


---
layout: default
---

# 時刻の配置

ここは何を作るかの画像を貼る

<!--
まずは時刻を配置しながら、Layoutの基本を学んでいきます。

正直Layoutの使い方に関してはここだけで大体わかります。

まずは要素と並びを確認します。

時刻があって、一時間分の間隔でまた時刻がある、それを時刻の分だけ繰り返します。

今回時刻の高さは適当に定義した固定に値を使うので、高さが固定の絡むみたいなものなので、すごくシンプルです。

では、実際のコードを考えていきます。
-->


---
layout: default
---

# Create MyCustomLayout

```kotlin
@ Composable
fun MyCustomLayout(
  timeLabel: @Composable (LocalDateTime) -> Unit,
){
  // create contents
  val timeLabels = @Composable {
      repeat(24) { i ->
          timeLabel(LocalDateTime.of(now.year, now.month, now.dayOfMonth, i, 0))
      }
  }
  // 1.Add content
  Layout( // Parent
    content = timeLabels, // Children
    modifier = Modifier,
    measurePolicy = { (measureables), constraints ->
        // 2.Measure all element
        // 3.Set parent size and layout children
        layout(...
    }
  )
}
```

<!--
まずは基本部分からです。
お馴染みのComposableでこれから作るLayoutのfunを定義します。

一応時刻の見た目を簡単にカスタマイズできるように引数で時刻表示用のComposableを受け取るようにしています。

そしてMainPointであるLayoutの定義ですね。

引数にはcontentとお馴染みmodifierとmeasurePolicy、これは普段は呼び出し末尾にラムダでかけますが一応わかりやすく書いています。

Layoutを作っていくステップとしては
コメントにある通り、contentをLayoutにわたし、全てのエレメントを測定し、親のサイズをきめ、こ要素を配置します。

今回の発表ないでは、Layout関数自体を親、contentに追加したComposableたちを子要素と呼びます。

さて、まずはcontentの作成です。
こちらはサラッと書いてしまっているのですが、渡されたcomposableを今回は一日分なので24回repeatしてDateTimeを渡して呼び出しています。
TimeLabelComponentの中でDateTimeが呼べるので好きに表示をいじります。今は単純にテキストを置いています

// TODO: TimeLabelの中身かく

そして、あとはこれをcontetntとして渡してあげます。
これでstep1のcontentの追加は完了です。

追加されたcontentをMeeasurePolicyのラムダの中で処理することができます。

Step2にコヨウソの測定に入ります。

測定を始める前に、ラムダに何が渡されてくるのかを確認しましょう

-->

---
layout: default 
---

# MeasurePolicy?

```kotlin
fun interface MeasurePolicy {

    fun MeasureScope.measure(
        measurables: List<Measurable>,
        constraints: Constraints
    ): MeasureResult

.... some functions...

```

<!--


MeasurePolicyはInterface　のfunctionになっています。
いくつかfunctionが生えているのですが、MeasureScope.measrue以外はデフォルト実装が入っているため、
measrueだけオーバーライドすればokでkotlinであればその場合ラムダで記述で切るのでここの作りを機にする必要は特にないです。

大事なのは、measurablesとconstraintsという値が渡されてくるということです。

measureblesはMeasurableのリストです。Measurableっていうのは何て説明すればいいのかわかっていないのですが、その名の通り、測定が可能なものです。

// TODO 書くか考える
//  * A part of the composition that can be measured. This represents a layout.
 * The instance should never be stored.

一応こういう説明にはなっています。とりあえず、このMeasurrableの状態では測定はまだされておらず、配置もできない、というのを覚えておいてください。
で、先ほどわたしたcontentは大元は一つのラムダだったんですが、ここではListになって入ってくるというのも大事な点で、

わたしたcontentのchild、今回だとtimeLabelのmesurrableがそれぞれListに入ってきます。コード追いきれなかったんですがlayoutNode.childMeasurablesが入ってくるっぽいです。

コードとしてはinterfaceで定義されていて、受け取るmeasure関数のみを持っています。

ここまで４分

// TODO:ここで資料分ける

次にconstraintsです。

コンストラインツはその名の通り、制約、ここで入ってくるものは親に与えられた制約になります。

minWidth, minHeight, maxWidth, と maxHeigh

を持っていて、これをmesurableに渡してmeasureを呼ぶことで要素の測定ができます。

親の制約っていうのを少し確認してみます。

FillMaxSizeのScaffoldにMyCustomLyouatを入れている場合。

Layoutのmodifierに幾つか制約をつけてここで渡されてくるconstraintsをprintしてみます。

// TODO Slide
-->

---
layout: default
---

# watch the constraints 

``` kotlin
// なし
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
何もなかった場合は、minが0,maxがそろぞれ画面の最大サイズ

maxHeightを指定した場合にはminHeightとmaxHeightが一致します。

今回スクロールしたいのでverticalScrollを入れてみるとmaxHeightがInfinityになり、両方入れるとminが表示領域の高さ、maxがinfinityになります。

さて、なんとなくイメージが湧いたところで実際に測定をしてみましょう。

-->

---
layout: default
---

# Prepare


``` kotlin

    val density = LocalDensity.current
    val minuteHeightDp = 2.dp
    val minuteHeightPx = with(density) {
        minuteHeightDp.roundToPx()
    }
    val hourHeightPx = minuteHeightPx * 60
```

``` kotlin

    Layout(
        content = sideBarTimeLabels,
        modifier = Modifier
            .fillMaxHeight()
            .verticalScroll(state = rememberScrollState()),
...

```


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

具体的には、measurable.measureを読んでplaceableを作成します。

Listで入ってきたmeasurablesをそれぞれmeasureしていきます。

constraintsにはcopyのメソッドがあるので、お好みの制約にします。

先ほど親にはFillMasHeightとVerticalScrollをつけたので、constraintsのminHeightには表示領域の高さ、maxにはInfinityが入ってくるはずです。
そのまま使うと、ラベルは全てminHeightの高さになってしまいますから、このように0と一時間あたりの高さを指定してやります。

こうすると、一時間分の領域内で、labelのcomponentのサイズが決まります。minもhourHeightPxにすれば必ず一時間分の高さに広げるようにもできますので、ここはお好みで大丈夫です。

なんとこれだけで測定は終わりですね。

今回のラベルはTextをただ置いただけなので、テキストのサイズになります。

もし、ラベルにFillMaxHeightをつけていた場合は、一時間分の高さまでは伸びることになります。

// TODO 比較画像入れてもいいかも


測定後の高さはplacedable.heightでpixelで取得できるので、例えばこのラベルと同じサイズの何かを作りたい場合に参照することで実現ができます。

ちなみにplaceableにはmeasuredHeighというものも生えていて、heightは最終的に表現されるサイズ、mesuredHeightは測定されたサイズで、基本的には一致しているが、場合によっては一致しないらしいので注意が必要です。

いろいろ試したんですが、一致しないケースを再現できなかったので、再現方法がわかる方いたらぜひ教えてください。

では最後、配置していきましょう。

layout関数を使って配置します

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

<!--

最後にlayoutします。

width height alimentLines placeMentBlockを受け取るlayoutFunを使います。

widthとheightで親のサイズを確定し、placementBlockで小要素を配置します。

// TODO 時間あったら入れる。
alignmentLinesは使わないケースも多く今回も使わないので割愛します。

実際にやっていきましょう。

-->
---
layout: default
---

# Let's layout

```kt

val totalHeight = hourHeightPx * timeLabelMeasureables.size

layout(constraints.maxWidth, totalHeight) {
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
まずはWidhtyとHeit,今回幅は親に与えられた制約のまま使いますので、constraintsから取ります。
高さは表示する時間全てを置けるだけ必要なので、ラベルの数に一時間あたりの高さをかけたものになります。

blockの中ではこ要素を配置します。
やることはPlaceable.placeを呼び出すだけです。
xとyとzindexが指定できます。

Composeの世界では0,0が左上なので右に動かしたければxに値を、下に動かしたければyに値を入れます。

zIndexは描画の順で、大きい値を指定するほど手前に描画されます。

今回は横にはずらさずに下に順に置いていきたいので、一時間あたりの高さにindexをかけたものをyに入れています。

はい、これで時刻の配置は終わりです。ビルドするとこうなります。

-->


---
layout: default
---
# 完成

TODO 画像を貼る



<!--

渡すラベルがわでfillMaxSizeを指定してpaddingを指定するとかでこのように引き伸ばしたりもできる状態になります。

右にはみ出てほしくない場合などは幅の制限も加えるいいですね。

では次、に縦軸（横線）の表示

-->


---
layout: default
---

# 時刻に合わせた縦軸表示

TODO これです画像を貼る


<!--

これですね。

特に難しいことはないんですが、ポイントが一つだけあります。


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

とりあえず、ラベルと同じようにLayoutに渡すcomopsesableを定義しましょう。
ラベルと同じ個数の横幅いっぱいで高さ1dpの色つきBoxにしてみます。

ここも外から渡せるようにしてもいいですね。

<-->

---
layout: default
---

# Add to contents

```kt

    Layout(
        contents = listOf(
            sideBarTimeLabels,
            backGroundLines
        ),

        /* not
        content = {
            sideBarTimeLabels()
            backGroundLines()
        }
        */

```



<!--

そしてレイアウトに渡します。

ここでワンポイントなんですが、Layout関数にはContentとしてComposableを受け取るものとContentsとしてcomposableのListを受け取るものとがあります。

contentの方でも渡せると言えば渡せるんですが、soうするとmeasureBlockに入ってくるmesurablesがラベルとラインの区別なく全て一つのリストとして入ってくるため使いずらいので、属性の違うcomposableを扱う場合はListで渡す方が使いやすいです。

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
使う時はこのように括弧で囲むとそれぞれに代入できるので、あとは先ほどと同じように測定して配置していきます。

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

あとは同じようにmeasureして

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

layoutすれば完成です

<-->


---
layout: default
---

# Line 完成  

TODO 画像


---
layout: default
---

# 時刻と対応したイベントの配置・サイズの調整  

TODO: 画像をおく

<!--
さて、いよいよイベントを置いていきます。

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
とりあえず表示に必要な最低限の情報としては
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
    timeLabel: @Composable (LocalDateTime) -> Unit = { StandardTimeLabel(time = it) },
    // add below.
    events: List<CalendarEvent>,
    eventContent: @Composable (CalendarEvent) -> Unit = { EventItem(event = it) },
) { ...


```

<!--

まずはカスタムレイアウトの引数に表示するeventのリストとconposableを追加します

-->

---
layout: default
---

# Create eventContents

```kt

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

        { (timeLabelMeasureables, backGroundLinesMeasureables, eventMeasureables /* add */), constraints ->


```


<!--
イベントのリストから、ebentのcomposeを同じだけ作って渡します。

これでLayoutの中でサイズを時間の長さに合わせて、開始位置をlabelの位置と合わせればよいのですがここで一つ問題があります。
ブロックに入ってきたmesurabeはeventの情報を持っていないので、開始時刻と終了時刻を取ってくることができません。

-->


---
layout: default
---

# ParentdataModifier

<!-->
そこで
- ParentDataModifier
を使います。

これを利用することで、任意のdataを親に伝えることが可能です。

使い方は結構簡単で
-->
---
layout: default
---

```kt

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

こんな感じで定義して

eventのmodifierに追加して親から参照したいデータを渡します。

eventContentは中がどういう構造になっているかがわからないので、直接modifierを追加せずにBoxで囲んで追加するのが安全です。

-->

---
layout: default
---

# Add event

```kt

            val eventPlaceablesWithEvent = eventMeasureables.map { measurable ->
                val event = measurable.parentData as CalendarEvent

```



<!--


これで、このようにdataを取ってくることが可能です。

そうすれば、開始と終了が取れますから、あとはその二つから高さを求めて、ラベルの位置に合わせて配置すれば完成です。

-->

---
layout: default
---

# Add event

```kt

            val eventPlaceablesWithEvent = eventMeasureables.map { measurable ->
                val event = measurable.parentData as CalendarEvent
                val eventDurationMinutes =
                    ChronoUnit.MINUTES.between(event.startTime, event.endTime)
                val eventHeight = (eventDurationMinutes * minuteHeightPx).toInt()
                val eventWidth = constraints.maxWidth - labelMaxWidth                
                measurable.measure(
                    constraints.copy(
                        minWidth = eventWidth,
                        maxWidth = eventWidth,
                        minHeight = eventHeight,
                        maxHeight = eventHeight
                    )
                ) to event
            }

```


<!-->

まず高さを開始と終了時刻から計測します。

今回は高さを指定したいので、まずは単純に高さを計算します。
幅は、ラベルと被らないようにラベルの分だけ短くしておきます。このlabelMaxWidthはLabelのPlaceableのWidhtから計算したものです。

そしてmaxでもminでもこの高さですよ、と制限を変更して、測定します。

-->
---
layout: default
---

# Layout event

```kt

      layout(constraints.maxWidth, totalHeight) {
          val dataTimeYMap = hashMapOf<LocalDateTime, Int>()
           timeLabelPlacablesWithDataTime.forEachIndexed { index, (placeable, dateTime) ->
              val y = hourHeightPx * index
               placeable.place(
                  x = 0,
                  y = y,
               )
              linePlaceables[index].place(
                  x = 0,
                  y = y,
              )
              dataTimeYMap[dateTime] = y
          }

          eventPlaceablesWithEvent.forEach { (placeable, event) ->
              placeable.place(
                  x = labelMaxWidth,
                  y = dataTimeYMap[event.startTime.getZeroMinuteLocalDateTime()] ?: 0,
              )
          }
      }

```


<!--
あとはStartTimeで時刻のY位置に揃えて配置してやるだけでokです。
-->
---
layout: default
---

# Add event

// TODO イベント完成



<!--


1.5分
-->

---
layout: default
---

# 同時刻に複数イベントがあった際の幅の調整

// TODO 作るもの

<!--
次はこういう状態ですね。

-->

---
layout: default
---

# Step

- 重なりのあるイベントをグルーピング
- グループのかずに合わせて幅を調整
- グループ内の位置に合わせてxOffsetを調整


<!--


- 重なりのあるイベントをグルーピング
- グループのかずに合わせて幅を調整
- グループ内の位置に合わせてxOffsetを調整

一夜サイズを調整するためには重なりのあるイベントを探す必要があるのでまずはグルーピングします。

CustomLayoutあんまり関係ないんで軽くなんですが。

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
            // イベントが重なっている場合、現在のグループに追加
            currentGroup.add(currentEvent)
        } else {
            // 重ならない場合、新しいグループを作成
            groupedEvents.add(currentGroup)
            currentGroup = mutableListOf()
            currentGroup.add(currentEvent)
        }
    }
    // 最後のグループを追加
    groupedEvents.add(currentGroup)
    return groupedEvents
}


```

<!--

コード的にはこんな感じで、CostomLayoutと関係ないしgeminiに書いてもらったので解説は省きますが、重なっているものが同じListに入るListのListを作っています。

で、ここで免責事項なのですが、実はこのロジックどうやらバグってまして、複雑なeventの重なりがあった場合うまくいかないケースがあります。

ただ、このロジック自体はそこまで重要ではないのと、時間が足りなかったのでそのままぼぐとりきれていない状態になっております。

実装の方針としてはこんな感じなんだなあという感じで見ていただければと思います。

高さとyの位置を先に計算しておいて、重なりをチェックするみたいな方法も取れそうです。

//TODO この二択は最初に出しとく


-->


---
layout: default
---

# Add group data to event

``` kt

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

そして、レイアウトする際には、自分の幅を決めるために、グループに何個アイテムが存在するかと、位置を決めるために、グループの何番目に存在しているのかを取れるようにしておく必要があるため。
CalenderEventを一枚ラップして、追加の情報として、Groupを追加してみました。
ここの構造はお好みで良いですが、ラップする形にしておくと、このカスタムレイアウトを使う側ではラップしていないイベントを渡して利用することができるため、少し便利かと思います。

-->

---
layout: default
---

# Add group data to event

``` kt
    val eventContents = @Composable {
        groupOverlappingEvents(events).forEach { group ->
            group.forEachIndexed { index, event ->
                // Wrapp
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

そして、eventContentsでWrapしてあげます。

eventContentはWrappedを受け取るようにしておくと、indexで色を変えたりとかできるようになります。

これで準備完了です。

ちなみにこれはlayoutのラムダの中でやってもいいように見えますが、そうするとcompositionは走らないがlayoutが走るような場合、次にやるイベントをドラッグしている時とかですね、にも処理が走ってしまって無駄なので、今回のケースではcompositionの方でやっています。

ドラッグに合わせてここの表示も更新する場合はlayoutでやる必要があるかもしれません。

-->

---
layout: default
---

# Add group data to event

``` kt
        // calculate with group size
        val eventWidth = (constraints.maxWidth - labelMaxWidth)  / event.group.size
        measurable.measure(
            constraints.copy(
                minWidth = eventWidth,
                maxWidth = eventWidth,
                minHeight = eventHeight,
                maxHeight = eventHeight
            )
        ) to event

// ---------------------------------- layout

        placeable.place(
            x = labelMaxWidth + (placeable.width * event.group.index),
            y = dataTimeYMap[event.data.startTime.getZeroMinuteLocalDateTime()] ?: 0,
        )

```


<!--

あとは特に特別なことはなくて、幅をsizeで割ってやって、xの座標をindex分だけずらしてやればokです。

-->


---
layout: default
---

# 完成


<!--

一応完成です。バグあり

-->

---
layout: default
---

#  イベントのドラッグ&ドロップ


TODO 動画


---
layout: default
---

#  イベントのドラッグ&ドロップ


1. 各イベントアイテムにDraggableをつけてそれぞれでドラッグを管理する
2. カスタムViewにpointerInputをつけて管理する


<!--
さて、つづいが結構悩ましそうなドラッグ＆ドロップの実装です。

この動きですね。

思いつく方針としては、二つありました

1. 各イベントアイテムにDraggableをつけてそれぞれでドラッグを管理する
2. カスタムViewにpointerInputをつけて管理する

今回は1を選択しました。
1の方が簡単そうだったからですね。
2だとドラッグの開始位置からどのイベントをドラッグするのかを計算する必要があってそれがなんとなくめんどくさそうだったためです。
ドラッグでもっと色々な動きを、入れ替えとかするのであれば2の方針も検討する必要がありそうです。

では具体的な実装を見てみましょう。



事前準備として、ドラッグしたアイテムのY位置のオフセットを大元のcomposeに持たせます。
 
var draggingItemYOffset: Float by remember {  
    mutableFloatStateOf(0f)  
}

そして、各イベントにはドラッグ状態を表すisDraggingを持たせます。

offsetを各イベントに持たせず大元に持たせるのは、ここの値はdrag中に頻繁に書き換えるため、リストの中のアイテムのパラメータとして持たせると更新に多少コストがかかりそうだと思ったからです。おそらくよほどアイテムの量が多くなければ中に持たせても特に問題はないとは思います。
逆にdragg中のイベントがどれかという情報を大元にもたせなかったのは、drag中はイベントの色を変えるなど、各イベントcomponentで見た目を変更する際に参照したかったからです。
// TODO isDragも持たせればいいのでは？

やることは結構簡単で、まず、ebentにDraggableをつけます。

// TODOcode

中身はこんな感じで、ドラッグ開始でドラッグをtrueにする、endでfalseにする。
そして、移動のたびにyOffsetを更新します。



-->


---
layout: default
---

# Add OffsetY

``` kt

        eventPlaceablesWithEvent.forEach { (placeable, event) ->
            val standardY = dataTimeYMap.getOrDefault(
                event.data.startTime.getZeroMinuteLocalDateTime(),
                0
            )
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
そして、Layoutのblockの中でもしtrueだった場合に、offSetぶんずらしてやるようにしていきましょう。

これで一応ドラッグができるようになりました。

-->

---
layout: default
---

TODO:動画


<!--
ここで、一つ大事なことがあります。

移動中にyOffSetが更新し続けられるのですが、これをreadしているところはlayoutのblock内だけなので、理論的にはCompositionのフェーズをスキップしてlayoutとドローのフェイズのみ実行させることができそうですよね。
-->


---
layout: default
---

TODO:スクショ



<!--
ただ、実際にやってみると、こんな感じでドラッグ中に動かしているアイテム以外もcompositionが走ってしまいます。


これは、composeにこのListが安定していないとみなされている、skipできていない状態になっています。

そこでですね、こちらの手順を踏んで、Listは安定しているんだと教えてあげると、無事スキップされるようになります。

知らないとそのままにしてしまうところではあるよなあと思ったので是非今日はここだけでも覚えて帰っていただければと思います。

さて、ドラッグの話に戻ります。

あとはですね、isDragを見て、見た目もこんな感じに半透明にするとかやってあげれば完成です。

ドラッグが終わったタイミングで大抵eventの更新をリポジトリなどを通してすることがあるのでonFinishのイベントを発生させる必要があるのですが、それはスナッピングの後に考えてみましょう。

-->

---
layout: default-
---


# 時刻へのスナッピング  


<!--
次はスナッピングですね。

この動きですね。今回は5の倍数の分にスナップしてみます

やることは簡単で、ドラッグ中は単純にoffsetを反映した位置ではなく一番近い5で割れるふんの位置に起くだけです。

なので、まずはgeminiに聞きます

こんな感じで聞くと

まあロジックを書いてくれるので、あってるかなあっていうのを読んでまああってそうだったので使います。

これで一番近い置き場所がわかるので、あとは、まず時から位置を取ってきて、その後一番近いふんの分だけoffsetを足してやります。

そしてあとはおく。と。

これでokです

ついでに先ほど実装しなかったドラッグが終わったイベントを発行しましょう。

ドラッグが終わった時に、ドラッグされたイベントが何時何分に移動されたかは、このlayoutblockの中でしかまだわからないので、onDragFinishで使えるように外に値を反映してやる必要があります。

eventItemに持たせるか、親のこんぽーずにもたせるかどっちでもやれるのですが、今回は親のコンポーズに持たせてみます。パフォーマンスの点で少し部があるためです。

というのも、layoutBlockではdragがいつ終わったかは検知できないので、普通にやるとdragのたびにイベントを更新することになり、それによってcompositionが入るため、若干コストがかかります。
一方、親で持ってその値を使う場合は、readがonFinishのタイミングのみになりますから、ドラッグ中は相変わらずComposeitionが走りません。

ということで、やってみます。

こんな感じでできそうですが、実際に動かしてみると、値が更新されていません。

これもCustomLayoutに限らないポイントなんですが、Listenerの中でstateをみている場合はUpdatedStateを使わないと最新の値がつかわれないのでご注意ください。

ということで、UpdatedStateを使い無事通知されました。
compositionも走っていませんね。

2分
-->

---
layout: default
---

# 遅延Redering  


<!--
いよいよ最後に遅延レンダリングです。
最初に言っておくと、結構力技で無理やりやっているので、流石にもうちょっとマシなやり方あるだろうという感じではあるのですが、参考程度に聞いていただければと思います。

正直デイリーのカレンダーで遅延レンダリングを必要とすることって普通ないとは思うんですが、それだとモチベーションも湧かないので今回は365日分を縦に並べてみようと思います。

単純に365日表示してみると、しっかりアウトオブメモリでクラッシュします。（久しぶりに見た）

これを表示して、スクロールできるように頑張ってみます。

とりあえず遅延レンダリングといえばLazy系のComposeでしょうということでLayColmunを読んでみたんですが、難しくてよくわからない上に最終的にSubComposeLayoutを使っていました。

確かに、表示しないところはそもそもCoposeを配置しないというのが正しいのでSubComposeLayoutを使うと良さそうではあるのですが、今回はどうしてもLayoutで済ませたいのでLayoutでできないかを考えてみます。

SubComposeLayoutを使う必要があるケースとしてはlayoutフェーズで測定するまでどの要素を配置すべきかが確定しないケースがあります。Lazy系のものはまさにそれで、ユーザーが渡してくるcomposeによってアイテムのサイズが変わるため、何個配置すべきかが測定するまでわからないので、SubComposeLayoutでないと実現が難しそうです。
一方で今回の我々のケースでは、中のアイテムのサイズは時刻と固定値にって計算できるのでLayoutより前に決められます。
つまり、Layoutでも、必要な分だけComposeを渡すということができそうですね。
パフォーマンスの懸念はありそうですが、とりあえずやってみます。

手順としては、

viewPortから画面に表示できる個数を計算する
scrollのoffsetから今表示したいindexを計算する
indexと個数から今表示したい時刻を決める
表示する時刻から表示するeventを決める
今まで通りの処理に渡す

こんな感じです。


やる

やる
やる

やる



これで、実行してみると、スクロールができました！

スクロールのたびにcomposeが走るのでちょっとスムーズではないのですが、リリースビルドだと何もわからないレベルで高速スクロールが可能な程度には動きました。

もう一つ別の方針で試したことがあって、これはcomposeは全て渡すけど必要なところ以外はレイアウトしない、というようなことをやってみました。

一応動いたんですがこっちの方が目に見えてカクツクのと、一度レイアウトしたcompseは残るようで、スクロールすればするほど目に見えないcompsoeが増える怖い挙動になるため、やめた方が良さそうです。

3分+コードで1分

-->


















