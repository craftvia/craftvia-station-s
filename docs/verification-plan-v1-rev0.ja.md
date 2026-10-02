# CraftVia Station-S v1 Rev0 検証計画

> 状態: 未検証Rev0ハードウェア用の検証計画Draftです。順番に実施し、後段の通電試験は前段の検査・絶縁確認への合格を前提とします。

この文書は英語正本 [`verification-plan-v1-rev0.md`](verification-plan-v1-rev0.md) の日本語翻訳版です。解釈に差異がある場合は英語版を優先します。

## 1. 試験記録

| 項目 | 記録 |
| --- | --- |
| 基板Serial／Sample ID |  |
| PCB表示 | `CraftVia Station-S Rev0` |
| 実装元・実装日 |  |
| BOM差異・Rework |  |
| 試験日・担当者 |  |
| 測定器・校正状態 |  |
| 電源Source・電流制限値 |  |
| DEV/DUT CoreとRevision |  |
| CTRL CoreとRevision（実装時） |  |
| Firmware Commit／Build |  |
| JP1-JP11初期状態 |  |

各試験は **Pass**、**Fail**、**Blocked**、**Not run** のいずれかで記録し、測定値とEvidenceを添付します。

## 2. 安全条件と中止条件

- JP1のSource Jumperを2つ以上同時に装着しません。
- すべての入力Sourceを切り離した状態でJumperを変更し、Core-Sを挿入・取り外します。
- 通電試験はCore、LCD、外部Target、CTRL Coreを装着しない状態から開始します。
- 電流制限付きSourceを使用し、想定外の電流、Rail低下、発熱、発煙、異臭、USB Host警告があれば中止します。
- JP2は無通電時にのみ取り外し、外部Ammeterを挿入する場合だけ外します。

## 3. 必要機材

- Microscopeまたは拡大鏡
- Digital Multimeter
- 電流制限付き5 V電源と適切なUSB電源
- OscilloscopeとLogic Analyzer
- 外部AmmeterまたはSource Measure Instrument
- STLINK ToolとSerial Terminalを備えたUSB Host
- 検証済みCore-S G031または別途検証済みFixture
- Target電流測定用の既知負荷
- 任意USB経路試験専用のN2/N3 USB対応Test Core

## 4. 試験項目

| ID | 試験 | 方法 | 合格基準 | Evidence |
| --- | --- | --- | --- | --- |
| `STS-R0-001` | Release識別 | 基板表示、Source Release、BOM、Gerber、SHA-256 Manifestを照合する。 | 全Fileと表示がv1 Rev0を示し、Manifestと一致する。 | 写真、Checksum Log |
| `STS-R0-002` | 外観・実装検査 | 極性、IC方向、USB/STLINK Connector、Header、DNP Site、はんだ付け、Jumper Headerを確認する。 | 実装がBOMと一致し、目視欠陥がない。 | 注釈付き写真 |
| `STS-R0-003` | 外形・穴 | 140 x 90 mm外形、角半径、4つのCorner Hole、追加4つの3.2 mm NPTH、2つの2.2 mm NPTHを測定する。 | 寸法、穴種類、穴位置が製造公差内でRelease記録と一致する。 | 測定表 |
| `STS-R0-004` | Core Socket機構 | DEV Socket／Key方向、意図するCore Headerとの無通電嵌合、H5-H7接点選定を確認する。 | 向きが正しく、完全に装着でき、衝突、過大な力、接点曲がりがない。 | 写真、測定値 |
| `STS-R0-005` | 無通電Rail絶縁 | 全Jumperを外し、`USB_C_5V`、`STLINK_5V`、`EXT_5V`、`SYS_5V`、`VDD_CTRL`、`VDD_SHUNT_IN`、`VDD_TARGET`、GND間の抵抗を測定する。 | 意図しない短絡やSource並列経路がなく、測定値が安定し説明可能である。 | 抵抗Matrix |
| `STS-R0-006` | JP1 Truth Table | JP1の1-2、3-4、5-6を1組ずつ導通確認する。 | 各Pairが指定Sourceだけを`SYS_5V`へ接続し、別SourceをBridgeしない。 | 導通表 |
| `STS-R0-007` | Regulator起動 | Coreなしで利用可能な各入力Sourceを個別に給電し、`SYS_5V`と`VDD_CTRL`を測定する。 | 起動が安定し、電流制限が作動せず、`VDD_CTRL`が3.135–3.465 Vで、異常発熱がない。 | Waveform、電圧・電流表 |
| `STS-R0-008` | Target経路 | JP2を装着し、無負荷と既知負荷で`VDD_CTRL`、`VDD_SHUNT_IN`、RS1両端、`VDD_TARGET`を測定する。 | 極性・導通が正しく、電圧降下が部品・測定器公差内で負荷と0.1 ohm Shuntに整合する。 | 負荷表 |
| `STS-R0-009` | 外部Ammeter Mode | 電源を切り、JP2を外してAmmeterを挿入し、既知負荷を再測定する。 | 安全な直列接続になり、RS1をBypassせず外部値とINA226経路を比較できる。 | Setup写真、測定値 |
| `STS-R0-010` | INA226通信 | 実装・Program済みCTRL Coreまたは外部I2C ControllerからU2をScan・Readする。 | 期待Addressが応答し、Bus電圧とShunt測定値が安定する。 | I2C Log |
| `STS-R0-011` | INA226測定応答 | 無負荷またはほぼ無負荷の状態と、安全に区別できる複数の既知負荷を外部Meterと比較する。これは精密校正ではなく機能特性の確認とする。 | INA226のBus電圧およびShunt／電流値が安定し、極性が正しく、負荷変化に対して整合して変化する。外部Meterとの差は記録するが、Rev0の精度保証値として扱わない。 | 比較表、Raw測定値 |
| `STS-R0-012` | STLINK Enumeration | JP1 3-4だけを選択し、U3をPCへ接続する。 | USB FaultなくSTLINKが認識され、期待するInterfaceを報告する。 | Host Log |
| `STS-R0-013` | DEV給電・T_VCC | JP2/JP11を装着し、無通電でCoreを取り付けてから給電し、`VDD_TARGET`とSTLINK Target電圧を測定する。 | Coreへ3.3 Vが供給され、STLINKがShunt後の同じTarget Domainを検出する。 | 電圧表 |
| `STS-R0-014` | SWD | JP3をSWD、JP4をOpen、JP7を1-2、JP8/JP11をClosedに設定し、Core-S G031をProgram・Debugする。 | Detect、Erase、Program、Run、Halt、Step、Reset、Connect-under-resetがすべて成功する。 | STLINK Log |
| `STS-R0-015` | Reset・Boot操作 | DEV Resetと文書化されたJP3/JP4分離Sequenceを使い、Normal BootとSystem Memory Bootを試験する。 | Resetが確実で、N3/N4競合なくBoot Modeを選択できる。 | Scope Capture、Boot Log |
| `STS-R0-016` | VCP方向 | JP9/JP10をClosedにし、Core PA9/W1からSTLINK入力、およびSTLINK出力からCore PA10/W2へ送信する。 | 双方向通信が成功し、U3 Pin 12がSTLINK入力、Pin 15がSTLINK出力として動作する。 | Serial Log、Capture |
| `STS-R0-017` | STLINK絶縁 | JP8-JP11をOpenにし、STLINK無通電時と単独通電時のDEV Netを確認する。 | 分離経路が、文書化したLeakageを超えてDEV CoreをClampまたは回り込み給電しない。 | 電圧・抵抗Log |
| `STS-R0-018` | User操作 | JP5/JP6をClosedにし、Reset、DEV User Button、DEV User LEDを操作する。 | ButtonとLEDの極性が仕様と一致し、JP5/JP6を開くと分離される。 | Firmware／Log |
| `STS-R0-019` | USB-C給電 | JP1 1-2だけを選択し、USB-Cから給電してCC/VBUS/保護動作を確認する。 | Host警告がなく、Regulated Railが規定内で、他の5 V Sourceへ回り込み給電しない。 | USB、Rail Log |
| `STS-R0-020` | USB Data経路 | 対応Test Coreを用い、無通電でJP7を2-3、JP8-JP11をOpenにしてN2/N3 USBを試験する。 | Deviceが認識されData転送でき、SWD/STLINK経路が分離される。 | Host、Signal Integrity Log |
| `STS-R0-021` | Breakout導通 | 全DEV接点と、関連するCTRL/LCD/Test Point Footprintを公開Net Mapと照合する。 | 全意図Nodeが導通し、隣接短絡がない。 | 完成したMatrix |
| `STS-R0-022` | Source・負荷Budget | 代表的なDEV、CTRL、LCD、Indicator、Target負荷で入力電流とRegulator温度を測定する。 | 各Sourceが宣言Budget内で、Railが規定内に留まり、温度が許容可能かつ記録される。 | 負荷・温度Report |
| `STS-R0-023` | 反復Power Cycle | Cold Start、Reset、Programming、UART、Measurement試験を繰り返す。 | Connector、Jumper、起動、Enumerationに間欠Failureがない。 | Cycle回数、Log |

## 5. 検証判定

Station-Sの基本検証には、安全、電源、DEV Socket、SWD、Reset、VCP、Breakout、Measurementの全試験合格が必要です。任意のUSBおよびCTRL/LCD機能は個別に報告できますが、未試験機能は明示的にUnverifiedのまま残します。設計変更には新しいPCB Revisionが必要で、Rev0のFail Evidenceは保存します。
