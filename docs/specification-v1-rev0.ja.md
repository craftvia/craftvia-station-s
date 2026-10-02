# CraftVia Station-S v1 Rev0 仕様書

> [!WARNING]
> **状態：凍結済み設計・未検証ハードウェア**  
> Rev0は発注済みですが未実装・未通電・未検証です。以下の動作ModeとLimitは実機検証が必要です。[英語版](specification-v1-rev0.md)を正本とします。

## 1. 識別と正本

| 項目 | 内容 |
| --- | --- |
| 製品 | CraftVia Station-S |
| Version | v1 |
| PCB Revision | Rev0 |
| 主対応Core | CraftVia Core-S G031 v1 Rev0 |
| CAD | EasyEDA |
| 凍結済み編集可能source | [`../source/CraftVia-Station-S-v1-Rev0.eprj2`](../source/CraftVia-Station-S-v1-Rev0.eprj2) |
| PCB製造正本 | [`../fabrication/Gerber_PCB1_2026-09-30.zip`](../fabrication/Gerber_PCB1_2026-09-30.zip) |
| Release記録 | [`../RELEASE_NOTES.md`](../RELEASE_NOTES.md) |

凍結済み設計、Gerber、正本BOM、Release記録をRev0の正とします。7 pageの回路図PDFとAssembly PDFはReferenceです。

## 2. 目的

Station-SはCore-S用の共通開発・計測platformです。Rev0は次を提供する設計です。

- DEV/DUT Core-S位置
- STLINK-V3MODS SWDとVCP UART
- 手動選択する入力電源と3.3 V Regulator
- 制御系／Target系の分離Rail
- INA226によるTarget電圧・電流計測
- SWDとN2/N3 USB経路の手動切替
- 操作部、Breakout、試作領域
- CTRL Core-SとDisplay用footprint

CTRL CoreとDisplayがなくてもDEV/DUTの基本SWDを使用できる設計です。

## 3. 物理構成

| 項目 | Rev0実装 |
| --- | --- |
| 外形 | 140.0 mm x 90.0 mm、Rounded rectangle |
| Corner radius | 約3.0 mm |
| Copper | 4層 |
| Corner hole | `(5,5)`、`(135,5)`、`(5,85)`、`(135,85)` mmの3.2 mm NPTH |
| 追加NPTH | Release記録の座標に3.2 mm x4、2.2 mm x2 |
| Bottom SMT | なし |

Core-S HeaderとのSocket種類、Stack、Clearanceは未検証です。H5-H7は凍結済み設計上TBDです。

## 4. Core-S位置

### DEV/DUT

H1-H4はN/W/E/S、H5-H7はKey／電源です。K-NW/K-NEはGND、K-SWは`VDD_TARGET`へ接続します。

| Core端子 | Station機能 |
| --- | --- |
| N1 | `DEV_N1`、JP8経由RESET |
| N2 | `DEV_N2`、SWDIOまたは選択中USB D- |
| N3 | `DEV_N3`、SWCLKまたは選択中USB D+ |
| N4 | `DEV_N4`、JP4による手動BOOT |
| N5 | JP5経由USER button |
| N6 | JP6経由USER LED |
| N7/N8 | Breakout。Rev0固定Debug driverなし |
| W1 | Core UART TXからJP9経由STLINK入力 |
| W2 | JP10経由STLINK出力からCore UART RX |
| E/S/残りW | Breakout |

GPIO Breakout footprintは正本BOMでは未実装です。

### CTRL footprint

H20-H26のCTRL Core-S footprintと制御netがありますが、正本BOM対象外です。意図した部品を別途選定・実装する必要があります。

CTRL位置はオンボードSTLINK SWDへ接続されません。CTRL CoreはDEV/DUT位置または外部fixtureで書き込んでから搭載します。

| CTRL端子 | 凍結済み機能 |
| --- | --- |
| N1-N4 | `CTRL_NRST`、`CTRL_SWDIO`、`CTRL_SWCLK`、`CTRL_BOOT`のtest/breakoutのみ |
| N5/N6 | CTRL user button／status LED |
| E1/E2 | Target電源Enable／Core ID予約net |
| E5-E8 | LCD SPI |
| W3/W4/W5 | INA226 SCL/SDA/ALERT |
| W6/W7 | LCD D/C、RESET |
| K-SW | `VDD_CTRL` |

予約netは機能の実装・検証完了を意味しません。

## 5. 電源

### 5.1 入力選択

JP1では5 V sourceを必ず1系統だけ選択します。

| JP1 | Source |
| --- | --- |
| 1-2 | USB-C `USB_C_5V` |
| 3-4 | STLINK `STLINK_5V` |
| 5-6 | 保護回路後の`EXT_5V` |

**複数のJP1 pairを同時に短絡しないでください。** Rev0にAutomatic ORingはありません。

USB-C VBUSはResettable fuse、EXT_5VはResettable fuseとP-channel MOSFET逆接保護を通ります。保護動作は未検証です。

### 5.2 Regulated Rail

U1 AP63203WU-7が3.3 Vを生成します。`VDD_CTRL`からJP2外部Ammeter bypass、RS1=0.1 ohmを経て`VDD_TARGET`へ給電します。`VDD_TARGET`はDEV/DUTとTarget出力、`VDD_CTRL`はStation制御系と任意CTRL Coreを給電します。

CTRL、INA226、LCD、Station LEDはTarget電流測定対象外です。STLINK 5 Vの予算は200 mAとして扱い、実負荷を測定します。

## 6. 計測

U2 INA226AIDGSRは`VDD_CTRL`で動作し、RS1 high-side shuntとTarget bus voltageを測定します。Kelvin senseと関連Test pointを備えます。

JP2は通常shortです。外部Ammeter使用時は無通電でJP2 capを外し、極性・rangeを確認して直列接続してから給電します。RS1とINA226は回路内に残ります。Rev0は精密microamp測定対象外です。

## 7. STLINKとVCP

U3はSTLINK-V3MODSで、SWDはDEV/DUTにだけ接続されます。凍結済みVCP配線はTarget側命名です。

- U3 pin 12 `T_VCP_TX`は**STLINKへの入力**。R16、JP9、`DEV_W1`を経てCore PA9/UART_TXへ接続
- U3 pin 15 `T_VCP_RX`は**STLINKからの出力**。R17、JP10、`DEV_W2`を経てCore PA10/UART_RXへ接続

旧設計メモのProbe側方向表記より、本凍結設計を優先します。

JP8はNRST、JP9/JP10はVCP、JP11はTarget voltage senseを分離します。STLINK使用時は通常closed、USB-only経路ではopenとします。

## 8. SWD、BOOT、USB

| Jumper | 役割 | 標準意図 |
| --- | --- | --- |
| JP3 | SWCLK接続／分離 | SWD時closed |
| JP4 | `DEV_N4`をGND／VDD_TARGETへ選択 | 通常bootはopen |
| JP5 | DEV USER button | Closed |
| JP6 | DEV USER LED | Closed |
| JP7 | 1-2 SWD、2-3 USB | G031は1-2 |
| JP8 | NRST分離 | STLINK時closed |
| JP9 | VCP入力分離 | STLINK時closed |
| JP10 | VCP出力分離 | STLINK時closed |
| JP11 | T_VCC分離 | STLINK時closed |

USB1はUSB4085-GF-A、CC1/CC2は各5.1 kOhm Rdです。D+/D-はESD保護とU10 FSUSB42を通り、選択中のN2/N3へ接続します。Level translatorはありません。

USB経路はN2/N3 USBを明示対応するCore用です。Core-S G031 Rev0はN2/N3 USBに対応しません。Jumper変更・Core交換は無通電で行います。

## 9. UI・拡張

- DEV RESET／USER button／USER LED
- CTRL USER button／STATUS LED path
- USB VBUS／USB MODE LED
- H27 10 pin未実装LCD interface（I2C/SPI）
- 未実装DEV／CTRL Breakout
- 独立2.54 mm prototype領域

LCD、CTRL Core、Breakout headerは明示的に追加しない限り正本BOMに含まれません。

## 10. 実装状態

正本BOMは69 designator、未filter CPLは114 designatorです。CPLをBOMと照合してください。BOMにはDEV socket、STLINK、USB、電源・計測回路、JP1-JP11 headerを含みますが、Jumper capは別手配です。

Supplier fieldには通常のLCSC番号ではないEasyEDA aliasがあります。特にU1、U3、U10、USB1、Headerは実部品を解決してからAssemblyを確定してください。

## 11. Rev0既知制約

- 組立・通電sampleなし
- CTRL socketとLCD connectorは正本BOM対象外
- CTRL位置にオンボードSWDなし
- H5-H7とCore-S stack未確定
- 電源保護、Regulation、Measurement精度、Thermal margin未検証
- USB/SWD MUX未検証
- 自動電源選択、USB Hub、自動Probe切替、Target電源switch、Overcurrent shutdown、精密microamp測定なし

## 12. 検証

[`verification-plan-v1-rev0.ja.md`](verification-plan-v1-rev0.ja.md)に従い、全Jumper、Option部品、Core Revision、Firmware、測定器、電源sourceを記録します。
