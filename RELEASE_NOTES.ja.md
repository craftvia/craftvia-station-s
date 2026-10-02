# CraftVia Station-S v1 Rev0 ハードウェアRelease

> 本文書は英語正本 [`RELEASE_NOTES.md`](RELEASE_NOTES.md) の日本語翻訳版です。解釈に差異がある場合は英語版を優先します。

## Release状態

- Revision：Rev0
- Release日：2026-09-30
- 状態：初回試作PCB製造向けにFROZEN
- 想定用途：Rev0 Engineering Prototype。量産向けReleaseではない
- CAD：EasyEDA v3.2.149.88089769
- 正本の編集可能Source：`source/CraftVia-Station-S-v1-Rev0.eprj2`
- 正本のPCB製造Upload：`fabrication/Gerber_PCB1_2026-09-30.zip`

本Releaseは、2026-09-30にEasyEDAで開いたProjectと照合しました。Project、回路図、PCBの識別子、VCP修正、部品実装状態、PCB Primitive数、Net数は凍結済みSource Packageと一致しています。

## Release判定

以下の実装・発注条件を前提として、初回Rev0 PCB試作発注に使用可能なPackageです。

- PCB Strict DRC：PASS、Error 0
- Schematic DRC：Fatal 0、Error 0、Warning 2
- 残る回路図WarningはNon-fatal。EasyEDA Bridge Resultでは件数のみ確認でき、詳細なUI Messageは取得できないが、PCB接続・Clearance Errorには関連しない
- Gerber ZIP整合性：PASS
- 基板：140.0 mm x 90.0 mm、角半径約3.0 mm、銅箔4層
- 確認済みPCB内容：114 Component／Feature Footprint、配線Line Primitive 736、Via 50、Copper Pour 7、Net 108

## VCP接続確認

修正後のSTLINK-V3MODS VCP配線を凍結設計で確認しました。

- U3 Pin 12 `T_VCP_TX`（STLINK入力）-> R16 -> JP9 -> `DEV_W1` -> Core-S PA9/UART_TX
- U3 Pin 15 `T_VCP_RX`（STLINK出力）-> R17 -> JP10 -> `DEV_W2` -> Core-S PA10/UART_RX

STLINKのSignal名はTarget側基準です。`T_VCP_TX`はSTLINKへの入力、`T_VCP_RX`はSTLINKからの出力です。

## Release収録物

| Path | 用途 |
| --- | --- |
| `source/CraftVia-Station-S-v1-Rev0.eprj2` | 正本の編集可能EasyEDA Project |
| `source/ProPrj_CraftVia-Station-S-v1-Rev0_2026-09-30.epro` | Portable EasyEDA Project Archive |
| `fabrication/Gerber_PCB1_2026-09-30.zip` | 正本のPCB製造Package |
| `assembly/BOM_Board1_PCB1_2026-09-30.csv` | 正本の実装対象BOM |
| `assembly/PickAndPlace_PCB1_2026_09_30.csv` | 基板全体の座標Export。Turnkey Assemblyへ渡す前にFilterが必要 |
| `assembly/PCB_PCB1_2026-09-30.pdf` | PCB、BOM、Assembly／Layer参考Export |
| `docs/SCH_Schematic1_2026-09-30.pdf` | 人が確認するための7 Page回路図Reference |
| `SHA256SUMS.txt` | SHA-256整合性Manifest |

## PCB製造Package

`fabrication/Gerber_PCB1_2026-09-30.zip`を編集・再圧縮せず、そのままPCB製造業者へUploadしてください。

ZIPには次が含まれます。

- Top、Inner 1、Inner 2、Bottom Copper
- Top／Bottom Solder Mask
- Top／Bottom Silkscreen
- Top Paste Mask
- Top Assembly Layer、Document Layer
- Board Outline
- NPTH、Plated Component Hole、Plated Via Drill Data

Bottom側SMT部品がないため、Bottom Paste Fileはありません。

発注前に、正確なZIPをJLCDFMまたは製造業者のGerber Previewへ読み込み、次を確認してください。

- 140.0 mm x 90.0 mmの角丸長方形外形
- 銅箔4層
- `(5,5)`、`(135,5)`、`(5,85)`、`(135,85)` mmにある4つのCorner Mounting Holeが3.2 mm NPTHであること
- `(20.099,5.474)`、`(41.099,5.474)`、`(20.099,28.474)`、`(41.099,28.474)` mmにある追加4つの穴が3.2 mm NPTHであること
- `(62.484,6.477)`、`(62.484,43.477)` mmにある2つの穴が2.2 mm NPTHであること
- Header、Breakout、Universal Areaの穴がPTHとして認識されていること
- Top／Bottom Silkscreen、Component Label、操作表示が判読可能で正しい向きであること
- Drill Offset、Duplicate Drill、想定外のSlot Warningが報告されないこと

ZIPには`Drill_PTH_Through.DRL`と`Drill_PTH_Through_Via.DRL`の両方が含まれます。Drill Fileを個別に選んでUploadせず、完全なZIPを使用して製造業者Previewを確認してください。

製造業者で発注時に選択したStack-upを正とします。後続ReleaseでControlled Stack-upを指定しない限り、一般的な4層FR-4 Prototype Stackを使用してください。

## 実装対象

BOMは59行、実装対象Designatorは69個です。CPLでは、そのうち46個がSMD、23個がNon-SMD／THTです。

次の23個のBOM品目はNon-SMDで、通常はTHT Serviceまたは手実装が必要です。

`H1-H7, H35-H37, J1, JP1-JP11, USB1`

次のPCB Featureは意図的にBOMから除外しています。

- GPIO／Control Breakout Header：`H8-H34`、`H38`
- DNP Resistor：`R10`、`R11`
- Test Point：`TP1-TP15`

### Pick-and-placeに関する警告

`assembly/PickAndPlace_PCB1_2026_09_30.csv`は114 Designatorを含む未Filterの基板全体Exportです。69個の正本BOM Designatorに加えて、45個の除外／DNP／Test Point／Breakout行を含みます。

- PCB製造だけを行う場合、Gerber発注には影響しない
- Turnkey Assemblyでは、Import結果を確認せずCPLを未変更のままUploadしない
- CPLを69個のBOM DesignatorへFilterするか、実装業者がBOM／CPLのDesignator積集合を使用し、それ以外をDNPにすることを確認する
- BOM CSVは、未Filter CPLおよびAssembly PDF上の赤いX表示より優先する

一部のSupplier Part Fieldには、通常のLCSC `C`番号ではなく`.1`で終わるEasyEDA Project／Library Aliasが入っています。実装料金を支払う前に、すべてのImport済みBOM行、特にU1、U3、U10、USB1、Headerの実Manufacturer Partを確認してください。

### Jumper Capに関する警告

JP1-JP11のHeader FootprintはBOMに含まれますが、取り外し可能な2.54 mm Jumper Capは独立したBOM品目ではありません。必要なJumper Capを別途手配するか、工場での構成を明示してください。

Rev0 Silkscreen／Schematicの標準状態は次のとおりです。

- JP1：電源Source Pairを1組だけ装着。3-4でSTLINK電源を選択
- JP2：通常のTarget電流経路では装着。外部Ammeter接続前に取り外す
- JP3：SWD用に装着
- JP4：通常BootではOpen
- JP5／JP6：標準User Button／LED接続用に装着
- JP7：1-2でSWD、2-3でUSB Mode
- JP8-JP11：STLINK動作時はClosed、USB-only動作時はOpen

複数のJP1電源Source Pairを同時に選択しないでください。

## Rev0の既知条件

- 基板にはDEV/DUTとCTRLのCore-S Socketがあります。CTRL Core-SはSTLINK-V3MODS SWDに接続されていないため、DEV/DUT SocketでProgramしてからCTRL Socketへ移動する必要がある
- H5-H7は`TBD`表示。Assembly前に最終Female Contact TypeとMechanical Stackを確認する
- Assembly PDFはReference Export。赤いX Overlayは除外／DNP Featureを示すが、BOMを置き換えない
- USB Indicator LEDは、回路図NoteでDNP可としているが、現在のBOMでは実装対象

## 初品確認

1. 製造受入前に140.0 mm x 90.0 mm外形、10個すべてのNPTH、両面Silkscreenを確認する
2. Jumper装着前に`STLINK_5V`、`SYS_5V`、`VDD_CTRL`、`VDD_TARGET`、GND間の短絡を確認する
3. JP1電源Source Pairを1組だけ設定し、電流制限付き電源で給電する
4. AP63203 3.3 V Railを確認し、RS1／INA226経由でTarget電流を測定する
5. 外部Ammeter使用前にJP2動作を確認する
6. JP3、JP8、JP11を介してSWDIO、SWCLK、NRST、Target Voltage Senseを確認する
7. JP9／JP10を介したVCP LoopbackとTarget通信をTX/RX方向を含めて確認する
8. JP7によるSWD／USB選択と、USB-C CC、ESD、VBUS、MUX経路を確認する
9. Core-Sを挿入する前に、Continuity FixtureでDEV/DUTおよびCTRL Socketの全Pinを確認する
10. 毎回の通電前に、全Jumper Cap状態を意図するTest Modeと照合する

## 変更管理と整合性

発注前に`SHA256SUMS.txt`記載の全Fileを照合してください。本Releaseを発注に使用した後は、`v1-rev0`tagまたはGitHub Release Assetに記録されたFileを上書きしません。電気回路、Footprint、配線、Artwork、Silkscreen、実装対象の変更には新しいRevisionが必要です。文書のみの訂正は、tagを移動・置換せず後続Commitで行います。
