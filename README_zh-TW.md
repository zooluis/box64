![Official logo](docs/img/Box64Logo.png "Official Logo")

# Box64：帶特色的 Linux 使用者態 x86-64 模擬器

[網站](https://box86.org/) | [更新日誌](https://github.com/ptitSeb/box64/blob/main/docs/CHANGELOG.md) | [中文](https://github.com/ptitSeb/box64/blob/main/README_CN.md) | [Українська](https://github.com/ptitSeb/box64/blob/main/README_UK.md) | [報告錯誤](https://github.com/ptitSeb/box64/issues/new)

![Build Status](https://github.com/ptitSeb/box64/actions/workflows/release.yml/badge.svg) ![Stars](https://img.shields.io/github/stars/ptitSeb/box64) ![Forks](https://img.shields.io/github/forks/ptitSeb/box64) ![Contributors](https://img.shields.io/github/contributors/ptitSeb/box64) ![Pull Requests](https://img.shields.io/github/issues-pr/ptitSeb/box64) ![Issues](https://img.shields.io/github/issues/ptitSeb/box64)

---

**查看我們的遊戲相容性清單：[https://box86.org/app/](https://box86.org/app/)，請幫助我們[改進它](https://github.com/ptitSeb/box86-compatibility-list/issues)！**

---

Box64 可以在非 x86-64 的 Linux 系統（如 Arm，需要 64 位元小端主機系統）上執行 x86_64 Linux 程式，包括遊戲。

Box64 利用本機系統函式庫（libc、libm、SDL、OpenGL），在許多應用程式中提供易於整合的特性和驚人的效能。效能基準請查看[這份分析](https://box86.org/index.php/2021/06/game-performances/)。

借助 Arm、RISC-V 和 LoongArch 平台的 DynaRec，Box64 比純直譯器快 5-10 倍。深入了解 DynaRec，請看 [Inner workings](https://box86.org/2021/07/inner-workings-a-high%e2%80%91level-view-of-box86-and-a-low%e2%80%91level-view-of-the-dynarec/)。更詳細地了解 Box64 內部原理和最佳化策略，請看我們的 EuroSys 2026 論文《[Practical and Efficient x86-64 Emulation on RISC-V](https://doi.org/10.1145/3767295.3803574)》，它聚焦 RISC-V，但涵蓋了許多適用於 Box64 的更廣泛技術。

<img src="docs/img/Box64Icon.png" width="96" height="96" alt="Box64 Icon">

_Logo 和圖示由 @grayduck 提供 - 謝謝！_

---

## 使用

- `$ box64 ./program [args]` 執行 Linux x86_64 程式。
- `$ box64 -k` 終止所有模擬程序。
- `$ box64-bash` 獲得一個 x86_64 bash 環境。
- 想用 wine？見 [Wine 文件](docs/WINE.md)。

Box64 還提供環境變數和 rcfile 來控制其行為。詳見 [使用文件](docs/USAGE.md)。

---

## 編譯與安裝

- [編譯說明](https://github.com/ptitSeb/box64/blob/main/docs/COMPILE.md)
- [捆綁 x86 函式庫](https://github.com/ptitSeb/box64/blob/main/docs/BUNDLE-X86-LIBS.md)
- [Box64 的 Wine 安裝](https://github.com/ptitSeb/box64/blob/main/docs/WINE.md)

---

## 版本歷史

版本更新見[更新日誌](docs/CHANGELOG.md)。

---

## 平台相容性說明

Box64 需要主機系統上有 64 位元函式庫，因為它直接翻譯 x86_64 函式呼叫。對於 32 位元二進位檔，請使用 Box86 或 Box32。

**注意**

1. Box32 仍是實驗性的，但現在相當穩定，可以使用。
2. 某些 shell 指令碼（如 GOG 遊戲安裝程式）可能依賴 `uname -m` 來判斷目前架構。請用 `box64 script.sh` 執行它們，以讓 Box64 接管。

請注意，DynaCache 現在預設啟用（帶壓縮）。這會在 `~/.cache/box64` 中建立檔案，儲存已執行二進位檔和函式庫的產生程式碼，從而在第二次執行時大幅縮短啟動時間。這些檔案已壓縮，預設最多佔用 2Gb。你可以透過建立（或修改）`~/.box64rc` 並寫入以下內容，將其切換為唯讀模式來停用 DynaCache 寫入：

```ini
[*]
BOX64_DYNACACHE=2
```

（或 0 表示完全停用）。關於如何設定 Box64 的更多細節，請參考[使用文件](docs/USAGE.md)。

---

## 設定

Box64 的設定檔位於 `/etc/box64.box64rc` 和 `~/.box64rc`，格式均為 `.ini` 檔案。如果你不想使用預設的 `/etc/box64.box64rc` 檔案，請複製到 `~/.box64rc` 以確保相容性。

設定優先順序：`~/.box64rc` > `/etc/box64.box64rc` > 命令列。

更多資訊請查看[使用文件](docs/USAGE.md)。

---

## 其他平台特定說明

### Unity 遊戲模擬

- 許多 Unity 遊戲需要 OpenGL 3+，這在 ARM/RISC-V SBC 上可能有挑戰。
- **Pi4 和 Pi5 使用者提示**：設定 `MESA_GL_VERSION_OVERRIDE=3.2` 和 `BOX64_DYNAREC_STRONGMEM=1` 可防止凍結並啟用強記憶體模式。
- **使用 Panfrost**：為獲得更好的 ARM 相容性，啟用 `PAN_MESA_DEBUG=gl3` 強制更高的 OpenGL profile。如果遊戲啟動但在顯示任何內容前就意外結束，這可能有幫助。

### Steam

見 [Steam 文件](docs/STEAM.md)。

### Wine

Box64 支援 Wine64 和 Proton。對於 32 位元元件，需要 Box86。同時安裝 Box64 和 Box86 的系統可以執行 32 位元和 64 位元的 Windows 程式。

**注意**：你可以使用 Wine WOW64 建置在僅 Box64 的環境中執行 x86 Windows 程式，這仍是實驗性的，但大多數情況下可用。

更多資訊請查看 [Wine 文件](docs/WINE.md)。

----
結語
----

我要感謝所有為 box64 開發做出貢獻的人。
貢獻的方式有很多：程式碼貢獻、資金、硬體和宣傳！
所以，按不特定的順序，我要感謝：
 * 主要程式碼貢獻：rajdakin、mogery、ksco、xctan
 * 主要資金貢獻：[Playtron](https://playtron.one)、tohodakilla、FlyingFathead、stormchaser3000、dennis1248、sll00、[libre-computer-project](https://libre.computer/)、[CubeCoders Limited](http://cubecoders.com/)
 * 硬體貢獻和 LoongArch 遷移：[xiaoji](https://www.linuxgame.cn/)、Deepin Beijing Develop Team
 * 硬體貢獻：[ADLINK](https://www.adlinktech.com/Products/Computer_on_Modules/COM-HPC-Server-Carrier-and-Starter-Kit/Ampere_Altra_Developer_Platform?lang=en) 與 [Ampere](https://amperecomputing.com/home/edge)、[SpacemiT](https://www.spacemit.com/)、[SOPHGO](https://www.sophon.ai/)、[Radxa](https://rockpi.org/)、[StarFive](https://rvspace.org/)、[Pine64](https://www.pine64.org/)、[AYN](https://www.ayntec.com/)、[AYANEO](https://ayaneo.com/)、[jiangcuo](https://github.com/jiangcuo)
 * 持續為 box64 專案宣傳：salva（[microLinux](https://www.youtube.com/channel/UCwFQAEj1lp3out4n7BeBatQ)）、[PILab](https://www.youtube.com/channel/UCgfQjdc5RceRlTGfuthBs7g)/[TwisterOS](https://twisteros.com/) 團隊、[The Byteman](https://www.youtube.com/channel/UCEr8lpIJ3B5Ctc5BvcOHSnA)、[NicoD](https://www.youtube.com/channel/UCpv7NFr0-9AB5xoklh3Snhg)、ekianjo（[Boilingsteam](https://boilingsteam.com/)）

我還感謝許多只參與過一次這個專案的其他的人。

（如果你在你的專案中使用 Box64，請不要忘記提及它！）
