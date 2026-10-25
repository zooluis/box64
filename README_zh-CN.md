![Official logo](docs/img/Box64Logo.png "Official Logo")

# Box64：带特色的 Linux 用户态 x86-64 模拟器

[网站](https://box86.org/) | [更新日志](https://github.com/ptitSeb/box64/blob/main/docs/CHANGELOG.md) | [中文](https://github.com/ptitSeb/box64/blob/main/README_CN.md) | [Українська](https://github.com/ptitSeb/box64/blob/main/README_UK.md) | [报告错误](https://github.com/ptitSeb/box64/issues/new)

![Build Status](https://github.com/ptitSeb/box64/actions/workflows/release.yml/badge.svg) ![Stars](https://img.shields.io/github/stars/ptitSeb/box64) ![Forks](https://img.shields.io/github/forks/ptitSeb/box64) ![Contributors](https://img.shields.io/github/contributors/ptitSeb/box64) ![Pull Requests](https://img.shields.io/github/issues-pr/ptitSeb/box64) ![Issues](https://img.shields.io/github/issues/ptitSeb/box64)

---

**查看我们的游戏兼容性列表：[https://box86.org/app/](https://box86.org/app/)，请帮助我们[改进它](https://github.com/ptitSeb/box86-compatibility-list/issues)！**

---

Box64 可以在非 x86-64 的 Linux 系统（如 Arm，需要 64 位小端主机系统）上运行 x86_64 Linux 程序，包括游戏。

Box64 利用本机系统库（libc、libm、SDL、OpenGL），在许多应用中提供易集成性和惊人的性能。性能基准请查看[这份分析](https://box86.org/index.php/2021/06/game-performances/)。

借助 Arm、RISC-V 和 LoongArch 平台的 DynaRec，Box64 比纯解释器快 5-10 倍。深入了解 DynaRec，请看 [Inner workings](https://box86.org/2021/07/inner-workings-a-high%e2%80%91level-view-of-box86-and-a-low%e2%80%91level-view-of-the-dynarec/)。更详细地了解 Box64 内部原理和优化策略，请看我们的 EuroSys 2026 论文《[Practical and Efficient x86-64 Emulation on RISC-V](https://doi.org/10.1145/3767295.3803574)》，它聚焦 RISC-V，但涵盖了许多适用于 Box64 的更广泛技术。

<img src="docs/img/Box64Icon.png" width="96" height="96" alt="Box64 Icon">

_Logo 和图标由 @grayduck 提供 - 谢谢！_

---

## 使用

- `$ box64 ./program [args]` 运行 Linux x86_64 程序。
- `$ box64 -k` 终止所有模拟进程。
- `$ box64-bash` 获得一个 x86_64 bash 环境。
- 想用 wine？见 [Wine 文档](docs/WINE.md)。

Box64 还提供环境变量和 rcfile 来控制其行为。详见 [使用文档](docs/USAGE.md)。

---

## 编译与安装

- [编译说明](https://github.com/ptitSeb/box64/blob/main/docs/COMPILE.md)
- [捆绑 x86 库](https://github.com/ptitSeb/box64/blob/main/docs/BUNDLE-X86-LIBS.md)
- [Box64 的 Wine 安装](https://github.com/ptitSeb/box64/blob/main/docs/WINE.md)

---

## 版本历史

版本更新见[更新日志](docs/CHANGELOG.md)。

---

## 平台兼容性说明

Box64 需要主机系统上有 64 位库，因为它直接翻译 x86_64 函数调用。对于 32 位二进制，请使用 Box86 或 Box32。

**注意**

1. Box32 仍是实验性的，但现在相当稳定，可以使用。
2. 某些 shell 脚本（如 GOG 游戏安装程序）可能依赖 `uname -m` 来判断当前架构。请用 `box64 script.sh` 运行它们，以让 Box64 接管。

请注意，DynaCache 现在默认启用（带压缩）。这会在 `~/.cache/box64` 中创建文件，保存已执行二进制和库的生成代码，从而在第二次运行时大幅缩短启动时间。这些文件已压缩，默认最多占用 2Gb。你可以通过创建（或修改）`~/.box64rc` 并写入以下内容，将其切换为只读模式来禁用 DynaCache 写入：

```ini
[*]
BOX64_DYNACACHE=2
```

（或 0 表示完全禁用）。关于如何配置 Box64 的更多细节，请参考[使用文档](docs/USAGE.md)。

---

## 配置

Box64 的配置文件位于 `/etc/box64.box64rc` 和 `~/.box64rc`，格式均为 `.ini` 文件。如果你不想使用默认的 `/etc/box64.box64rc` 文件，请复制到 `~/.box64rc` 以确保兼容性。

设置优先级：`~/.box64rc` > `/etc/box64.box64rc` > 命令行。

更多信息请查看[使用文档](docs/USAGE.md)。

---

## 其他平台特定说明

### Unity 游戏模拟

- 许多 Unity 游戏需要 OpenGL 3+，这在 ARM/RISC-V SBC 上可能有挑战。
- **Pi4 和 Pi5 用户提示**：设置 `MESA_GL_VERSION_OVERRIDE=3.2` 和 `BOX64_DYNAREC_STRONGMEM=1` 可防止冻结并启用强内存模式。
- **使用 Panfrost**：为获得更好的 ARM 兼容性，启用 `PAN_MESA_DEBUG=gl3` 强制更高的 OpenGL profile。如果游戏启动但显示任何内容前就意外退出，这可能有帮助。

### Steam

见 [Steam 文档](docs/STEAM.md)。

### Wine

Box64 支持 Wine64 和 Proton。对于 32 位组件，需要 Box86。同时安装 Box64 和 Box86 的系统可以运行 32 位和 64 位的 Windows 程序。

**注意**：你可以使用 Wine WOW64 构建在仅 Box64 的环境中运行 x86 Windows 程序，这仍是实验性的，但大多数情况下可用。

更多信息请查看 [Wine 文档](docs/WINE.md)。

----
结语
----

我要感谢所有为 box64 开发做出贡献的人。
贡献的方式有很多：代码贡献、资金、硬件和宣传！
所以，按不特定的顺序，我要感谢：
 * 主要代码贡献：rajdakin、mogery、ksco、xctan
 * 主要资金贡献：[Playtron](https://playtron.one)、tohodakilla、FlyingFathead、stormchaser3000、dennis1248、sll00、[libre-computer-project](https://libre.computer/)、[CubeCoders Limited](http://cubecoders.com/)
 * 硬件贡献和 LoongArch 迁移：[xiaoji](https://www.linuxgame.cn/)、Deepin Beijing Develop Team
 * 硬件贡献：[ADLINK](https://www.adlinktech.com/Products/Computer_on_Modules/COM-HPC-Server-Carrier-and-Starter-Kit/Ampere_Altra_Developer_Platform?lang=en) 与 [Ampere](https://amperecomputing.com/home/edge)、[SpacemiT](https://www.spacemit.com/)、[SOPHGO](https://www.sophon.ai/)、[Radxa](https://rockpi.org/)、[StarFive](https://rvspace.org/)、[Pine64](https://www.pine64.org/)、[AYN](https://www.ayntec.com/)、[AYANEO](https://ayaneo.com/)、[jiangcuo](https://github.com/jiangcuo)
 * 持续为 box64 项目宣传：salva（[microLinux](https://www.youtube.com/channel/UCwFQAEj1lp3out4n7BeBatQ)）、[PILab](https://www.youtube.com/channel/UCgfQjdc5RceRlTGfuthBs7g)/[TwisterOS](https://twisteros.com/) 团队、[The Byteman](https://www.youtube.com/channel/UCEr8lpIJ3B5Ctc5BvcOHSnA)、[NicoD](https://www.youtube.com/channel/UCpv7NFr0-9AB5xoklh3Snhg)、ekianjo（[Boilingsteam](https://boilingsteam.com/)）

我还感谢许多只参与过一次这个项目的其他人。

（如果你在你的项目中使用 Box64，请不要忘记提及它！）
