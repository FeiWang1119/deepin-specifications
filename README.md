# 简介

`规范`是一种约定，对应的英文单词是 `Specifications`，为实现方和使用方提供了一个对接的桥梁，就像螺丝和螺母，虽然是两个独立的个体，但是如果他们都遵守同一个约定进行生产，那任意一颗螺母都能和一颗螺丝一起使用。开源社区有很多历史悠久的制定规范的组织，如：[freedesktop]( https://www.freedesktop.org )、[Khronos]( https://www.khronos.org/ )，deepin 也使用并实现了其中的一部分规范，如 freedesktop 制定的图标查找规范。

deepin 规范包含了 deepin/UOS 系统定义的所有规范文档，用于指导操作系统的开发者实现各种系统功能，同时也规定了应用程序应当如何使用这些系统功能。规范文档使用 markdown 格式的文件编写，覆盖了操作系统各个方面的设计，如“软件包格式“、“应用程序图标格式“等。

# DSG 组织

本组织全名为：“Desktop Specifications Group”，是一个致力于为 UOS 和 deepin 制定系统规范的工作组，域名为 `desktopspec.org`。此项目的所有规范文档均以 `SDG` 的名义发布，编写文档时，需要遵守以下规则：

* 规范中定义的环境变量需要以 “DSG\_” 开头，如：“DSG_APP_DATA”，且要明确说明此变量是否被强制依赖（必须提供有效的值）
* 与 DBus 接口相关的规范，需要以 “org.desktopspec.” 作为服务名的开头，如：“org.desktopspec.AppManager”
* 规范中任意需要明确组织身份的地方都使用 “DSG” 或 “dsg” 表示
* 除 “DSG” 以外，规范文档中不应该出现与某个组织/操作系统相关的定义，如不可将 “deepin/UOS” 用于环境变量、DBus 服务名称等地方

# 目录结构

规范可以是`稳定`、`不稳定`或`废弃的`三种状态，分别存放在 [stable](stable)、[unstable](unstable)、[deprecated](deprecated) 三个目录。

* 稳定规范：由维护者明确宣布进入稳定状态的规范，对此类规范的更新将一直保持向下兼容的策略。此类规范不可依赖任意不稳定的规范。
* 不稳定规范：一个规范从起草到稳定往往需要一个很长的过程，规范在发布之初即是不稳定状态，对于此类规范的更新不保证一定是向下兼容的。此类规范虽然无法保证向下兼容，但也不可进行大改动，不得偏离最新版本的核心设计。
* 废弃的规范：随着时间的发展，某个规范可能不再适用于现在的需求，它可能会被其它规范所代替，对于此类规范会明确将其移动到 `deprecated` 目录，请程序不要再使用或实现它。

规范文件名可以使用中英文命名，文件名需要概括性的体现此规范的功能，不可使用 “1.md”、“2.md” 等无意义的名称，文件后缀名必须为 “.md”。

# 规范创建流程

要提交一个新的规范，请先创建一个新的议题，在这个议题中描述此规范的意义和要解决的问题。此提议至少要得到SE团队的三名成员认同，并且没有任何一个成员明确反对此提议。

之后请创建一个PR来提交这个规范的 md 文件，对于还处于设计阶段的规范，请在提交的标题中明确以 “WIP:” 开头以告知审核者此提交的状态。

如果要修改一个已经存在的规范，请先根据这个规范当前的状态确定可以修改的范围，如对`稳定`状态的规范做出的修改必须要满足向下兼容的条件。

# 设计原则

* 创建新的规范之前请先查看当前已有的规范列表，如果和已有的某个规范高度重叠，则应当优先扩展此规范，而不是为其创建一个新的规范
* 规范的设计应当做出合理的抽象，以使其适用于更多的情况。虽然规范的设计往往是为了解决一个具体的问题，但是要避免仅为一个特例情况设计规范，需要基于此问题设计一个能解决一类问题的规范
* 很多规范之间存在着依赖关系，创建新的规范时也需要评估要在哪些已有的规范之上建立此规范，尽量做到规范的复用。如当前比较常用的跨进程通信规范是 DBus，那么当一个新规范定义一个服务需要对外提供接口时，应当优先考虑使用 DBus
* 规范中要尽量详细的说明各种情况的处理方式，避免产生未定义行为
* 新规范要考虑对业界已有规范的兼容性
* 规范中包含的 DBus 接口请提供完整的 xml 描述文件内容
* 可以使用伪代码作为辅助描述的手段
* 所定义的规范文件路径禁止使用绝对路径，需定义环境变量，将其作为顶层目录。如 “$DSG\_DATA\_DIR/appdata“，如果系统中需要将此目录对应为 “/foo/appdata”，则应该设置 “DSG\_DATA\_DIR=/foot”

# DBus 服务

对于规范文档中给出的 DBus 接口设计，需要满足以下规则：

* 遵守 [DSG 组织](#dsg-组织) 中的要求
* 需提供获取“所实现的规范文档的版本号”的 DBus 属性。如：实现规范 A 中定义的 “org.desktopspec.A” 服务时，需要提供 “org.desktopspec.A.version” 属性，类型为 “s”，值为所实现的规范文档版本号
* 实现 stable 状态的规范时，需要额外提供在名称末尾添加此规范的主版本号的服务（路径和接口名称也需要同步添加版本号后缀）。如：规范 A 的版本为 “2.1”，实现 A 中定义的 “org.desktopspec.A” 服务时，需要同时提供 “org.desktopspec.A” 和 “org.desktopspec.A2” 两个服务，且它们的功能和行为保持一致
* 其它规范可参考：[DBus API 设计](https://dbus.freedesktop.org/doc/dbus-api-design.html)

# 文件内容

* 规范文件必须写明规范的维护者、版本、创建时间等信息
* 维护者需要提供维护人的称呼（可不使用真实姓名）和邮箱地址
* 版本号由两位数字组成，如 “1.0”
* 对于 `unstable` 状态的规范，如果有无法向下兼容的改动，必须在提交信息中予以说明，并且要增加版本号的首位数，其它改动请增加版本号的第二位数
* md 内的插图请放置在 `images/${协议名称}/` 目录下，其中`协议名称`即 md 的文件名，两者要保持一致
* 详细格式请参考规范文件的模版：[template.md](template.md)

`unstable` 类型的文档必须在开头添加以下警告信息：

```md
> __警告！此规范内容是不稳定版本，可能会发生破坏兼容性的更新。当无法保障向下兼容时，将会升级此文档的主版本号，如从“1.0”更新到“2.0”。反之，普通更新只会升级次版本号，如“1.0”更新到“1.1”，其对“1.0”版本向下兼容。请在使用前确认此文档的版本号，并为将来可能发生的兼容性变化做好准备。__
```

# 将unstable转为stable状态

* 需移除文档开头的警告信息
* 需修改规范中定义的 DBus 服务名、路径、接口名，在其尾部添加规范的主版本号。如：规范 A 的版本为 “2.1”，在 unstable 状态时定义的服务名称为 “org.desktopspec.A”，则改名之后为 “org.desktopspec.A2”，路径和接口名同上
* 需将规范对应的md文件从 [unstable](unstable) 目录移动到 [stable](stable)，并为此创建一个 PR
* 至少需要四名SE团队成员的同意，并且没有任何一名成员明确反对，才可以合入相关 PR
* unstable 规范在发布的六个月之内不可转为 stable 状态
* unstable 规范未被应用于实际项目时不可转为 stable 状态

# 规范实现须知

* 在代码实现中要明确记录对应的规范描述文件的 URL
* 实现 unstable 规范时，需添加警告信息，明确告知使用者此实现在今后可能会发生不可兼容的改变。对于 C/C++ 接口，可以在提供接口的头文件中添加编译警告输出
