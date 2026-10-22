# CraftVia Station-S

> [!WARNING]
> **未検証のRev0ハードウェアです**  
> Rev0基板は発注済みで、現在は到着待ちです。未実装・未通電・未検証であり、製造・組立は自己責任となります。

> [!IMPORTANT]
> **Core-S USB Profile改訂**
> Rev0は過去DraftのN2/N3 USB経路を実装しています。現行Core-S Native USB ProfileはN7/N8を使用します。Rev0はST-LINK／SWDを主検証対象とし、文書化されたReworkなしでは現行Native USB Profileに対応しません。PCB対応はStation-S v1 Rev1で予定します。

[English](README.md) · [CraftVia](https://craftvia.org/ja/) · [Rev0リリースノート](RELEASE_NOTES.ja.md)

![CraftVia Station-S v1 Rev0 CADレンダリング](docs/images/station-s-v1-rev0-3d.webp)

*Rev0 CADレンダリング — [表面](docs/images/station-s-v1-rev0-front.webp) · [裏面](docs/images/station-s-v1-rev0-back.webp)。画像は説明用であり、凍結済み設計SourceとGerber Packageを正とします。*

CraftVia Station-Sは、Core-Sボードのための共通開発・計測プラットフォームです。組み込み開発中に必要な機能をStationへ集約し、各CoreをMCUとその動作回路へ集中させます。

配線、SWD書き込み・デバッグ、ターゲット電源操作、UARTログ、Coreと外部回路の2系統電源、電圧・電流測定を一つの環境で扱うことを目指します。

## 現在の状態

| 項目 | 状態 |
| --- | --- |
| 製品Version | v1 |
| PCB Revision | Rev0 |
| 基板製造 | 発注済み・到着待ち |
| 実装 | 未着手 |
| 通電 | 未実施 |
| 電気的検証 | 未実施 |
| Release tag | `v1-rev0`を予定 |

## Rev0概要

- 基板外形: 140.0 mm × 90.0 mm
- PCB: 4層
- STLINK-V3MODSインターフェース
- Core-S DEV/DUTソケット、CTRLソケット
- SWD、UART VCP、RESET、BOOT、ユーザー操作、旧Draft準拠N2/N3 USB経路
- 制御系・ターゲット系の分離された電源経路
- INA226によるターゲット電流測定経路
- ブレイクアウト・ユニバーサル配線領域
- CADソース: EasyEDA

## Repo構成

```text
source/                      正本のEasyEDA編集可能ソース
fabrication/                 正本のGerberパッケージ
assembly/                    BOM、CPL、組立参考資料
docs/                        回路図PDFと製品ドキュメント
product.json                 Web参照用の機械可読製品Metadata
RELEASE_NOTES.md / RELEASE_NOTES.ja.md
                             Rev0の詳細と初品確認項目（英語正本／日本語翻訳）
SHA256SUMS.txt               整合性確認用manifest
```

Rev0の[`仕様書`](docs/specification-v1-rev0.ja.md)と[`検証計画`](docs/verification-plan-v1-rev0.ja.md)を参照してください。Core-S共通要件は[CraftVia共通ドキュメント](https://github.com/craftvia/craftvia-docs)で管理します。

## Rev0の重要条件

基板発注・実装前に[`v1-rev0`のリリースノート](RELEASE_NOTES.ja.md)を必ず確認してください。

- JP1で複数の電源入力を同時に選択しないでください。
- 出力済みCPLは未フィルタであり、BOMとの照合なしに実装サービスへ使用できません。
- Supplier Partの一部は実装発注前に実部品を確認する必要があります。
- ジャンパキャップは独立したBOM品目ではないため、別途手配または実装指示が必要です。
- CTRL Core-Sソケットは内蔵STLINKのSWDへ接続されていません。CTRL用CoreはDEV/DUTソケットで書き込んでから移動します。
- Rev0のN2/N3 USB経路は撤回済み未検証Draft準拠であり、現行N7/N8 Native USB Profileではありません。
- H5-H7のメス接点とCore-Sの機械的スタックを実装前に確定する必要があります。

## 初品検証

外形・穴位置、電源短絡、電流制限付き通電、3.3 V電源、電流計測、ST-LINK／SWD、Target電圧検出、UART VCP方向、Jumper状態、両Core-S Socketの導通を優先して確認します。旧USB経路は現行Profile適合試験から除外します。安全なN7/N8 Reworkが可能とReview・記録できた場合に限り、USBを任意評価します。検証後も`v1-rev0`タグは移動せず、設計変更が必要な場合は新しいRevisionを作成します。

## ライセンスとブランド

個別の表示がない限り、本リポジトリのハードウェア設計ファイルは[CERN-OHL-P-2.0](LICENSE)で提供します。区分と帰属表示については[CraftVia Licensing（英語正本）](LICENSES/README.md)および[参考訳](LICENSES/README.ja.md)を参照してください。

CraftViaの名称・ロゴはKohei Ikedaが所有し、ハードウェアライセンスの対象外です。[CraftVia Trademark Policy（英語正本）](LICENSES/TRADEMARK_POLICY.md)および[参考訳](LICENSES/TRADEMARK_POLICY.ja.md)を参照してください。改変品や第三者製造品を公式CraftVia製品として表示することはできません。
