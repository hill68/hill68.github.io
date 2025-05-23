+++
date = '2025-05-21T15:43:38+08:00'
draft = false
title = 'Tacview Technical Reference'
summary= "This summary is independent of the content."
+++


[原文链接](https://www.tacview.net/documentation/acmi/en/)

### 1、引言

Tacview 1.5引入了一种新的通用公共文件格式。目的是要克服以前格式的复杂性，同时使其功能更强大。

和之前一样，这个新的文件格式仍是以纯[UTF-8]文本编写。这样，可以很容易地用最简单的编程语言来导出飞行数据。 Tacview 1.4.3中引入了调试日志，它的语法很容易被阅读，现在很容易诊断出任何输出问题。这种新格式非常简单，如果数据量不是天文数字的话甚至都可以用手工编写！

尽管格式简单，但它提供了一种非常强大的方式来实时设置和更改战场上任何物体的任何属性。例如，现在可以即时更改联盟、颜色甚至对象的类型！同样，您可以轻松设置和更改全局属性，例如天气。

重要的是要注意，Tacview尚不支持的数据将保留并在原始遥测窗口中可见。如果您认为重要数据应由Tacview本地支持并显示，请随时与我们联系。

### 2、ACMI 2.1 文件格式入门

先从最简单的文件开始：

```
FileType=text/acmi/tacview
FileVersion=2.1
```

首先任何ACMI文件中必须具有上述两行强制性的文件头。 这个文件头告诉Tacview期望使用哪种格式。 后面的任何数据都是可选的。

当然从道理上说，即使Tacview会正常加载这个空文件，但我们也需要更多数据才能使其有用！ 这是一个更有意义的文件：

```
FileType=text/acmi/tacview
FileVersion=2.1
0,ReferenceTime=2011-06-02T05:00:00Z
#47.13
3000102,T=41.6251307|41.5910417|2000.14,Name=C172
```

为了更好地理解这种结构，我们需要知道，除了其头两行的文件头之外，文件的每一行都可以是：

- 符号`＃`后的数字表示相对于`ReferenceTime`以秒为单位的一个新的时间帧
- 对象ID（在此示例中为`0`和`3000102`），后跟任意多个用逗号分隔的属性，并使用等号 `=`为每个属性赋予一个新值。
- 第三种可能性（此处未显示 ），以减号 `-` 开头的行，后面跟着的是我们要从战场中删除的对象ID（可能被摧毁或超出记录范围）。

下面让我们详细了解每一行的语法：

```
0,ReferenceTime=2011-06-02T05:00:00Z
```

此行将值 `2011-06-02T05:00:00Z` 赋予给ID=0的始终是全局对象“零”的`ReferenceTime`属性。换句话说：此行定义用于确定整个飞行记录的基准/参考时间。 为了更好地理解这个含义，让我们看一下接下来这行：

```
#47.13
```

该行定义了一个相对于`ReferenceTime`的时间帧（以秒为单位）。 在这种情况下，这意味着以下事件或属性发生在`ReferenceTime + 47.13秒`⇒`2011-06-02T05:00:00Z + 47.13`⇒`2011-06-02T05:00:47.13Z`

再看下一行：

```
3000102,T=41.6251307|41.5910417|2000.14,Name=C172
```

该行定义了对象3000102的两个属性。为节省空间，对象ID以十六进制表示，没有任何前缀或前导零。

第一个属性`T`（代表Transform）是一种特殊的属性，用于定义空间中的对象坐标。稍后我们将了解`T`支持哪些语法。现在，我们仅关注这种情况：`T =经度|纬度|高度`。

请注意，纬度和经度以度表示。正值朝北和朝东。由于整个文件始终采用公制，因此高度以米[MSL]表示（海平面以上，在某些国家中也称为ASL）。

后面的属性名称显然定义了对象名称`Name`是`C172`，这是指定Cessna 172飞机的一种缩写。

创建飞行记录的所有基本知识就是这些了，然后我们将这个飞机向东移动一点。为此，我们可以简单地在文件中添加另一帧：

```
#49
3000102,T=41.626||
```

如所见，我们在`2011-06-02T05:00:49Z`的时间帧为飞机定义了新的经度值`41.626`。

可能已经注意到，我们不需要再次指定飞机名称，只是因为自上条记录以来就没有改变过！ 与上一条记录的另一个区别是我们省略了纬度和海拔参数，因为它们也没有改变。 在生成长途飞行数据时，这有助于节省大量空间。 虽然飞机通常机动性很强，但这种优化特别适用于可以静止不动或时不时移动的地面物体。

### 3、文件结构详解

为更好地了解ACMI文件的结构，下面让我们一起总结一下文件的要求和与文件格式有关的一些技巧：

#### 3.1 要求

- 文本数据必须以UTF-8格式写入。 从而文本属性就支持所有语言。
- 所有数据都以公制表示，以米为单位，如用米每秒数表示速度、用度表示角度、UTC时间等。
- 对象ID使用64位十六进制数字表示（不带前缀或前导零以节省空间）
- 对象`0`用于定义全局属性（如`ReferenceTime`或`Briefing`）
- 当要赋予一个包含逗号的文本属性时，必须在其前面加上转义字符 `\`，以便Tacview不会将其解释为字符串的结尾。

```
Briefing=Here is a text value\, which contains an escaped comma in it!
```

#### 3.2 技巧

- 为了节省空间，强烈建议仅使用LF `\n`字符结束行。
- 使用UTF-8 字节顺序标记（[BOM](https://en.wikipedia.org/wiki/Byte_order_mark) ）标头为文本数据添加前缀码会更干净。
- 整个文本数据可以包装在zip或7z容器中，以节省带宽或磁盘空间。
- 数据可以乱序显示， Tacview会在内存中重新排序。

### 4、对象坐标

现在，让我们仔细看看对象坐标的不同表示法。 为了优化文件大小，Tacview提供了四种不同的表示法。

这是两个示例：导出一个子弹坐标时，我们不需要有关其旋转角度的任何数据。 相反的例子是飞行模拟器中的飞机在像Falcon 4.0这样的平面世界中飞行：在这种情况下，为了获得准确的重放，我们需要导出飞机在平面世界中的原始位置、旋转以及在球形的世界的坐标 。 这样，飞机不仅可以在Tacview的球形世界中正确显示，而且遥测计算也可在对象的原始坐标系中完成，因此屏幕上见到的数字将与在原始飞行模拟器中看到的数字匹配。

| Object Position Syntax                                       | Purpose                                                      |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| `T = Longitude \| Latitude \| Altitude`                      | 球形世界中的简单对象（通常是子弹之类的次要对象）。 也可以与没有旋转信息的低端数据源（如GPX文件）相关。 |
| `T = Longitude \| Latitude \| Altitude \| U \| V`            | 来自平面世界坐标的简单对象。 U＆V代表本机x和y。 即使原始坐标以英尺为单位，也不要忘记换算为以米为单位。 海拔高度不重复省略掉了，因为原始世界坐标和球形世界坐标都一样。 |
| `T = Longitude \| Latitude \| Altitude \| Roll \| Pitch \| Yaw` | 球形世界中的复杂对象。 飞机向右滚转时，滚转为正。 起飞时俯仰为正。 偏航角相对于正北的顺时针方向夹角。 |
| `T = Longitude \| Latitude \| Altitude \| Roll \| Pitch \| Yaw \| U \| V \| Heading` | 来自平面世界的复杂对象。 和之前一样。 航向（Heading）是相对于平面世界正北的偏航角。 之所以需要这样做，是因为由于投影误差，原始世界的北通常与球形世界的北并不一致。 |

请记住，您可以省略自上次以来未更改的组件。 这样可以节省很多空间。

如果某些数据丢失（例如，对象旋转），Tacview将会去尽量模拟它，以提供良好的重放。 与优化无关，在对象生命周期中，应为每个对象保留相同的数据表示法。 如果在某一时刻使用了不同的表示法，Tacview将会把对象升级为更复杂的表示法。 但是，由于最初缺乏数据，最终结果可能难以预料。

### 5、全局属性

我们已经看到，最重要的全局属性之一是`ReferenceTime`。 显然，您可以在飞行记录中注入许多其他元数据，以使重放更加详细。

#### 文本属性

| Property Name   | Meaning                                                      |
| :-------------- | :----------------------------------------------------------- |
| `DataSource`    | 源模拟器，控制台或文件格式。<br>`DataSource=DCS 2.0.0.48763 `<br>`DataSource=GPX File` |
| `DataRecorder`  | 用于记录数据的软件或硬件。<br>`DataRecorder=Tacview 1.5 `<br>`DataRecorder=Falcon 4.0` |
| `ReferenceTime` | 当前任务的基准时间（UTC）。 该时间与每个帧偏移（以秒为单位）相结合，以获得每个数据样本最终的绝对UTC时间。<br>`ReferenceTime=2011-06-02T05:00:00Z` |
| `RecordingTime` | 记录（文件）创建（UTC）时间。<br>`RecordingTime=2016-02-18T16:44:12Z` |
| `Author`        | 创建此记录的作者或操作员。<br>`Author=Lt. Cmdr. Rick 'Jester' Heatherly` |
| `Title`         | 任务/飞行的标题或名称。<br>Title=Counter Attack              |
| `Category`      | 飞行/任务的类别。<br>`Category=Close air support`            |
| `Briefing`      | 包含飞行/任务简介的自由文本。 <br>`Briefing=Destroy all SCUD launchers` |
| `Debriefing`    | 包含任务报告的自由文本。<br>`Debriefing=Managed to stay ahead of the airplane.` |
| `Comments`      | 关于飞行的自由评论。 不要忘记转义任何要插入注释中的行尾字符。<br>`Comments=Part of the recording is missing because of technical difficulties.` |

#### 5.2 数字属性

| Property Name                                 | Unit | Meaning                                                      |
| :-------------------------------------------- | :--- | :----------------------------------------------------------- |
| `ReferenceLongitude`<br/>` ReferenceLatitude` | deg  | 这些属性用于通过将坐标以一个中间点为原点来减小文件大小。 它们会被加到每个对象的经度和纬度来求得最终坐标。 <br>`ReferenceLongitude=-129 `<br>`ReferenceLatitude=43` |



#### 5.3 事件

事件可用于将任何类型的文本、记号和调试信息注入飞行记录中。 它们有点特殊：它们像属性一样被声明，但是与属性不同，您可以在同一帧中声明多个事件而不会覆盖前一个事件。

这是有关如何注入事件的示例：

```
#8.62
0,Event=Message|3000100|Here is a generic event linked to the object 3000100
0,Event=Bookmark|Here is a bookmark to highlight a specific part of the mission!
#8.72
0,Event=Debug|Here is some debug text, visible only with the /Debug:on command line option
```

事件声明的结构：

```
Event = EventType | FirstObjectId | SecondObjectId | ... | EventText
```

对于每个事件，我们必须首先声明事件的类型（例如：`标记`），然后可选地声明有关对象的ID。 例如，当用户双击事件时，Tacview将使用这些ID将摄像机自动围绕相关对象居中。 最后一部分是强制性的文本信息。 虽然可以提供空白文本，但是建议您提供有用的信息，从而可以充分利用报告。

这是Tacview当前支持的不同类型的事件：

| Event Name  | Meaning                                                      |
| :---------- | :----------------------------------------------------------- |
| `Message`   | 通用事件。<br/>`0,Event=Message\|705\|Maverick has violated ATC directives` |
| `Bookmark`  | 记号在时间线和事件日志中突出显示。 它们很容易发现，并且易于突出飞行中的某些部分，例如轰炸或受训者最后着陆时。<br/>`0,Event=Bookmark\|Starting precautionary landing practice` |
| `Debug`     | 调试事件高亮显示，很容易在时间轴和事件日志中发现。 由于必须将它们用于开发目的，因此仅在使用命令行启动Tacview时带上参数/Debug:on时才会显示它们<br/>`0,Event=Debug\|327 active planes` |
| `LeftArea`  | 此事件对于指定何时将飞机（或任何物体）从战场上完全地移走（不是被摧毁）非常有用。 这样可以防止Tacview错误生成Destroyed事件。<br/>`0,Event=LeftArea\|507\|` |
| `Destroyed` | 当物体被正式摧毁时。<br/>`0,Event=Destroyed\|6A56\|` |
| `TakenOff`  | 由于Tacview可能无法始终正确地自动检测起飞事件，因此在飞行记录中手动添加此事件可能很有用。<br/>`0,Event=TakenOff\|2723\|Col. Sinclair has taken off from Camarillo Airport` |
| `Landed`    | 由于Tacview可能无法始终正确地自动检测降落事件，因此在飞行记录中手动添加此事件可能很有用。<br/>`0,Event=Landed\|705\|Maverick has landed on the USS Ranger` |
| `Timeout`     | 主要用于现实世界中的训练报告，以指定武器（通常是导弹）何时达到或错过其目标。 Tacview将在射击日志以及3D视图中报告射击结果。 大多数参数是可选的。 SourceId指定发射武器的对象，而TargetId为指定的射击目标。 即使显示的结果可能是海里，也必须以米为单位指定靶心坐标。 必须使用独立于此事件的适当属性来明确（手动）销毁或禁用目标。<br/>`0,Event=Timeout\|SourceId:507\|AmmoType:FOX2\|AmmoCount:1\|Bullseye:50/15000/2500`<br/>`\|TargetId:201\|IntendedTarget:Leader\|Outcome:Kill` |





### 6、对象属性

从Tacview 1.5开始，可以实时设置和更改任何对象属性。 即使新属性可能并不总是在3D视图中可见，您也可以始终在原始遥测窗口中查看当前选定对象的每个属性的当前值。

Tacview 1.7引入了一个新的对象  [database](https://www.tacview.net/documentation/database/en/) ，可以预定义`Type`和`Name`所需的任何对象属性。 例如，您可以在该数据库中预定义F-16C的默认形状。 如果遥测文件中未定义Shape属性值，则Tacview将使用数据库中存储的值，并在3D视图中显示F-16C的自定义3D模型。

通过阅读[专用文档](https://www.tacview.net/documentation/database/en/).了解如何更新和扩展Tacview数据库。

#### 6.1 文本属性

| Property Name    | Meaning                                                      |
| :--------------- | :----------------------------------------------------------- |
| `Name`           | 对象名称应为每个对象使用最通用的符号。 强烈建议使用ICAO或北约名称，例如：C172或F A-18C。 这将有助于Tacview将每个对象与其数据库中的相应条目相关联。 只有类型Type和名称Name是不能在Tacview数据库中预定义的属性。<br/>`Name=F-16C-52` |
| `Type`           | 对象类型是使用标签构建的。 与以前的独占类型相比，这使对象管理更加强大和透明。 （请参阅下面的受支持类型列表）。只有类型`Type`和名称`Name`是不能在Tacview[数据库](https://www.tacview.net/documentation/database/en/)中预定义的属性。<br/>`Type=Air+FixedWing` |
| `AdditionalType` | 此处定义的所有标签都将添加到当前对象类型中。 这对强制在遥测数据中未明确定义的对象类型很有用。 例如，您可以使用此属性为来自Garmin csv文件（通常不包含任何类型声明）的`Cessna 172`遥测数据自动设置`FixedWing`标签。 出于明显的原因，此属性只能在Tacview数据库中使用，而在遥测文件中则不能使用。<br/>`<AdditionalType>Air+FixedWing</AdditionalType>` |
| `Parent`         | 父级十六进制对象ID。 例如，可用于关联导弹（子对象）及其发射飞机（父对象）。<br/> `Parent=2D50A7` |
| `Next`           | 跟随对象的十六进制ID。 通常用于将航路点链接在一起。<br/>`Next=40F1` |
| `ShortName`      | 该缩写名称将显示在3D视图中，并且在任何其他情况下都将以较小的空间显示对象名称。 通常在Tacview数据库中定义。 不应在遥测数据中定义。<br/>`ShortName=A-10C` |
| `LongName`       | 更详细的对象名称，用在比杂乱的3D视图有更多空间但空间不足以显示完整详细名称的小窗口中。 为了便于阅读，建议首先以短名称（通常是北约代码的缩写）开头，然后是对象昵称/北约名称。 通常在Tacview数据库中定义。 不应在遥测数据中定义。<br/>`LongName=A-10C Thunderbolt II` |
| `FullName`       | 完整的对象名称，通常在窗口和其他日志中显示，只要有足够的空间可以显示很多数据而不会出现混乱的情况。 通常在Tacview数据库中定义。 不应在遥测数据中定义。<br/>`FullName=Fairchild Republic A-10C Thunderbolt II` |
| `CallSign`       | 呼叫符号将优先于对象名称（有时是飞行员名称）显示，尤其是在3D视图和选择框中。 这对于任务报告很方便，在这些任务报告中，呼号比飞机名称更有意义。<br/>`CallSign=Jester` |
| `Registration`   | 飞机注册号（又名尾号）<br/>`Registration=N594EX`             |
| `Squawk`         | 当前的应答器代码。 任何代码都是可能的，没有像旧的4位数字应答器那样的限制。<br/>`Squawk=1200` |
| `Pilot`          | 飞机驾驶员的指挥名称。<br/>`Pilot=Iceman`                    |
| `Group`          | 对对象所属的组进行分组。 用于将对象分组在一起。 例如，一个F-16编队一起飞过CAP。<br/>Group=Springfield |
| `Country`        | [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) 国家代码<br/> `Country=us` |
| `Coalition`      | 联军属性（敌军、友军）<br/>`Coalition=Allies`                                  |
| `Color`          | 可以是以下颜色之一：Red, Orange, Green, Blue, Violet。 颜色是预定义的，以确保在所有情况下都能清晰显示整个战场。<br/>`Color=Blue` |
| `Shape`          | 3D模型的文件名，将用于表示3D视图中的对象。 3D模型必须为[Wavefront .obj文件格式](https://en.wikipedia.org/wiki/Wavefront_.obj_file)，并存储在`%ProgramData%\Tacview\Data\Meshes\`或`%APPDATA%\Tacview\Data\Meshes\`中。可以阅读[专用文档](https://www.tacview.net/documentation/3dobjects/en/)，了解有关3D模型的更多信息<br/>`Shape=Rotorcraft.Bell 206.obj` |
| `Debug`          | 使用/Debug:on命令行参数启动Tacview时，调试文本在3D视图中可见。<br/>`Debug=ObjectHandle:0x237CB9` |
| `Label`          | 可在3D视图和遥测窗口中显示的自由实时文本（向最终用户提供其他信息）<br/>`Label=Lead aircraft` |
| `FocusedTarget`  | 该对象当前瞄准的目标（通常用于指定激光束目标对象，也可用于显示飞行员当前瞄准的目标）<br/>`FocusedTarget=3001200` |
| `LockedTarget`   | 首要目标十六进制ID（可以使用任何设备锁定，例如雷达，IR，NVG等）<br/>`LockedTarget=3001200` |

#### 6.2 数字属性

| Property Name                                                | Unit    | Meaning                                                      |
| :----------------------------------------------------------- | :------ | :----------------------------------------------------------- |
| `Importance`                                                 | ratio   | 比率越高，对象就越重要（例如，本地模拟的飞机可能是1.0的重要因子）<br/>`Importance=1` |
| `Slot`                                                       | index   | 机群中的飞机地位（最低的是领导者）<br/>` Slot=0`             |
| `Disabled`                                                   | boolean | 指定禁用一个对象（通常是战斗结束）而不将其销毁。 这对于战斗训练和射击记录特别有用。<br/>`Disabled=1` |
| `Length`                                                     | m       | 对象长度。 在显示建筑物时特别有用。<br/>`Length=20.5`        |
| `Width`                                                      | m       | 对象宽度。 在显示建筑物时特别有用。<br/>`Width=10.27`        |
| `Height`                                                     | m       | 对象高度。 在显示建筑物时特别有用。 <br/>`Height=4`          |
| `Radius`                                                     | m       | 对象边界球半径。 可用于定义自定义爆炸，烟雾/手榴弹半径。 可以用于动画。<br/>`Radius=82` |
| `IAS`                                                        | m/s     | 指示空速<br/>` IAS=69.4444`                                  |
| `CAS`                                                        | m/s     | 标定空速<br/>` CAS=250`                                      |
| `TAS`                                                        | m/s     | 真空速<br/>` TAS=75`                                         |
| `Mach`                                                       | ratio   | 马赫数<br/>` Mach=0.75`                                      |
| `AOA`                                                        | deg     | 迎角 <br/>`AOA=15.7`                                         |
| `AGL`                                                        | m       | 物体高于地面的高度<br/>` AGL=1501.2`                         |
| `HDG`                                                        | deg     | 飞机的航向。 如果没有可用的滚转和俯仰数据，则此属性可用于指定偏航，同时在3D视图中保持完整的旋转仿真。 <br/>`HDG=185.3` |
| `HDM`                                                        | deg     | 飞机的磁航向。 相对于局部磁北偏航。 <br/>`HDM=187.3`         |
| `Throttle`                                                   | ratio   | 主/发动机1号油门手柄位置（对于加力燃烧室，该值可以> 1，对于倒档来说可以<0） <br/>`Throttle=0.75` |
| `Afterburner`                                                | ratio   | 主/引擎1加力燃烧器状态 <br/>`Afterburner=1`                  |
| `AirBrakes`                                                  | ratio   | 空气制动器状态<br/>`AirBrakes=0`                             |
| `Flaps`                                                      | ratio   | 襟翼位置<br/>Flaps=0.4                                       |
| `LandingGear`                                                | ratio   | 起落架状态 <br/>`LandingGear=1`                              |
| `LandingGearHandle`                                          | ratio   | 起落架手柄位置<br/>`LandingGearHandle=0`                     |
| `Tailhook`                                                   | ratio   | 抓钩状态 <br/>Tailhook=1                                     |
| `Parachute`                                                  | ratio   | 降落伞状态（不要误认为DragChute） <br/>Parachute=0           |
| `DragChute`                                                  | ratio   | 拖曳伞状态<br/>DragChute=1                                   |
| `FuelWeight`  to `FuelWeight9`                               | kg      | 当前每个油箱中可用的燃油量（最多支持10个油箱）。<br/>FuelWeight4=8750 |
| `FuelVolume` to `FuelVolume9`                                | l       | 当前每个油箱中可用的燃油量（最多支持10个油箱）。 <br/>FuelVolume=75 |
| `FuelFlowWeight ` to `FuelFlowWeight8`                       | kg/hour | 每个引擎的燃油流量（最多支持8个引擎）。 <br/>FuelFlowWeight2=38.08 |
| `FuelFlowVolume`  to `FuelFlowVolume8`                       | l/hour  | 每个引擎的燃油流量（最多支持8个引擎）。<br/>FuelFlowVolume2=53.2 |
| `RadarMode`                                                  | number  | 雷达模式 (0 = off) <br/>RadarMode=1                          |
| `RadarAzimuth`                                               | deg     | 相对于飞机方向的雷达方位角（航向） <br/>RadarAzimuth=-20     |
| `RadarElevation`                                             | deg     | 相对于飞机方向的雷达仰角 <br/>RadarElevation=15              |
| `RadarRange`                                                 | m       | 雷达扫描范围 <br/>RadarRange=296320                          |
| `RadarHorizontalBeamwidth`                                   | deg     | 雷达水平波束宽度 <br/>RadarHorizontalBeamwidth=40            |
| `RadarVerticalBeamwidth`                                     | deg     | 雷达垂直波束宽度 <br/>RadarVerticalBeamwidth=12              |
| `LockedTargetMode`                                           | number  | 首要目标锁定模式 (0 = no lock/no target) <br/>LockedTargetMode=1 |
| `LockedTargetAzimuth`                                        | deg     | 相对于飞机方向的首要目标方位角（航向）<br/>LockedTargetAzimuth=14.5 |
| `LockedTargetElevation`                                      | deg     | 相对于飞机方向的首要目标俯仰角 <br/>LockedTargetElevation=0.9 |
| `LockedTargetRange`                                          | m       | 距飞机的主要目标距离<br/>LockedTargetRange=17303             |
| `EngagementMode`<br/> `EngagementMode2`                      | number  | 开关机状态（例如，当SAM站点关闭其雷达时）(0 = off) <br/>EngagementMode=1 |
| `EngagementRange`<br/> `EngagementRange2` <br/>`VerticalEngagementRange` <br/>`VerticalEngagementRange2` | m       | 防空单元的有效范围。 这是将在3D视图中显示的球体的半径。 通常用于SAM和AAA单位，但这也可能与军舰有关。 EngagementRange = 2500您可以选择指定垂直有效范围以绘制蛋形有效气泡形状。<br/>VerticalEngagementRange=1800 |
| `RollControlInput` <br/>`PitchControlInput` <br/>`YawControlInput` | ratio   | 原始玩家HOTAS /Yoke的真实位置（飞行模拟输入设备）<br/>PitchControlInput=0.41 |
| `RollControlPosition` <br/>`PitchControlPosition` <br/>`YawControlPosition`<br/> | ratio   | 模拟（带有响应曲线）或现实驾驶舱中的HOTAS/Yoke位置 <br/>PitchControlPosition=0.3 |
| `RollTrimTab`<br/> `PitchTrimTab` <br/>`YawTrimTab`          | ratio   | 每个轴的修剪位置 Trim position for each axis<br/>PitchTrimTab=-0.15 |
| `AileronLeft`<br/> `AileronRight`<br/> `Elevator Rudder`     | ratio   | 控制面在飞机上的位置 <br/>Elevator=0.15                      |
| Visible                                                      | boolean | 此标志对于从3D视图隐藏特定对象很有用。 可以用于战争迷雾效果，或防止显示虚拟对象。<br/>Visible=0 |
| `PilotHeadRoll`<br/> `PilotHeadPitch`<br/> `PilotHeadYaw`    | deg     | 在驾驶舱内飞行员头部方位相对于飞机方位的方位<br/>PilotHeadPitch=12 |



#### 6.3 对象类型 (aka Tags)

现在，可以使用标签的自由组合来定义对象类型。 标签越多，定义的对象越准确。 标签以加号+分隔。 这里有些例子：

| Object Kind      | Type (Tags)                               |
| :--------------- | :---------------------------------------- |
| Aircraft Carrier | Type=Heavy+Sea+Watercraft+AircraftCarrier |
| F-16C            | Type=Medium+Air+FixedWing                 |
| Bicycle          | Type=Light+Ground+Vehicle                 |
| AIM-120C         | Type=Medium+Weapon+Missile                |
| Waypoint         | Type=Navaid+Static+Waypoint               |

这是当前支持的标签的列表。 Tacview将使用它们进行显示和分析。

| Use                                   | Tags                                                         |
| :------------------------------------ | :----------------------------------------------------------- |
| Class | Air<br>Ground<br>Sea<br>Weapon<br>Sensor<br>Navaid<br>Misc   |
| Attributes                            | Static<br>Heavy<br>Medium<br>Light<br>Minor                  |
| Basic Types                           | FixedWing<br>Rotorcraft<br>Armor<br>AntiAircraft<br>Vehicle<br>Watercraft<br>Human<br>Biologic<br>Missile<br>Rocket<br>Bomb<br>Torpedo<br>Projectile<br>Beam<br>Decoy<br>Building<br>Bullseye<br>Waypoint |
| Specific Types                        | Tank<br>Warship<br>Aircraft<br>Carrier<br>Submarine<br>Infantry<br>Parachutist<br>Shell<br>Bullet<br>Flare<br>Chaff<br>SmokeGrenade<br>Aerodrome<br>Container<br>Shrapnel<br>Explosion |



以下是推荐的常见类型（标记的组合），您应该使用它们来描述大多数对象以在Tacview 1.x中显示：

| Type             | Tags                                       |
| :--------------- | :----------------------------------------- |
| Plane            | Air + FixedWing                            |
| Helicopter       | Air + Rotorcraft                           |
| Anti-Aircraft    | Ground + AntiAircraft                      |
| Armor            | Ground + Heavy + Armor + Vehicle           |
| Tank             | Ground + Heavy + Armor + Vehicle + Tank    |
| Ground Vehicle   | Ground + Vehicle                           |
| Watercraft       | Sea + Watercraft                           |
| Warship          | Sea + Watercraft + Warship                 |
| Aircraft Carrier | Sea + Watercraft + AircraftCarrier         |
| Submarine        | Sea + Watercraft + Submarine               |
| Sonobuoy         | Sea + Sensor                               |
| Human            | Ground + Light + Human                     |
| Infantry         | Ground + Light + Human + Infantry          |
| Parachutist      | Ground + Light + Human + Air + Parachutist |
| Missile          | Weapon + Missile                           |
| Rocket           | Weapon + Rocket                            |
| Bomb             | Weapon + Bomb                              |
| Projectile       | Weapon + Projectile                        |
| Beam             | Weapon + Beam                              |
| Shell            | Projectile + Shell                         |
| Bullet           | Projectile + Bullet                        |
| Ballistic Shell  | Projectile + Shell + Heavy                 |
| Decoy            | Misc + Decoy                               |
| Flare            | Misc + Decoy + Flare                       |
| Chaff            | Misc + Decoy + Chaff                       |
| Smoke Grenade    | Misc + Decoy + SmokeGrenade                |
| Building         | Ground + Static + Building                 |
| Aerodrome        | Ground + Static + Aerodrome                |
| Bullseye         | Navaid + Static + Bullseye                 |
| Waypoint         | Navaid + Static + Waypoint                 |
| Container        | Misc + Container                           |
| Shrapnel         | Misc + Shrapnel                            |
| Minor Object     | Misc + Minor                               |
| Explosion        | Misc + Explosion                           |

### 7、注释

为了在导出程序的调试过程中提供帮助，可以在文件的任何行加上双斜杠`//`作为前缀，与C ++一致。

```
// This line and the following are commented
// 3000102,T=41.6251307|41.5910417|2000.14,Name=C172
```

加载文件时，Tacview将忽略这些行。 注释不保留。 您会注意到，下次您从Tacview保存文件时，它们将被丢弃。 如果要包括保留的调试信息，则可以使用前面在全局属性中描述的专用的`Debug` `Event` 。

由于加载性能的考虑，只能在每行的开头插入注释。

