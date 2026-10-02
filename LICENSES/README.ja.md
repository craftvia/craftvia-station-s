# CraftViaライセンス方針（参考訳）

Copyright (c) 2026 Kohei Ikeda.

CraftViaでは、ハードウェア、ソフトウェア、文書、ブランド資産にそれぞれ別のライセンスを適用します。ある区分のライセンスは、別の区分に関する権利を許諾するものではありません。

## ライセンス一覧

| 対象 | ライセンス |
| --- | --- |
| ハードウェア設計ソースおよび製造データ | [CERN Open Hardware Licence Version 2 - Permissive](CERN-OHL-P-2.0.txt) (`CERN-OHL-P-2.0`) |
| ソフトウェアのソースコード | [Apache License 2.0](Apache-2.0.txt) (`Apache-2.0`) |
| 文書、図表その他文書として指定されたコンテンツ | [Creative Commons Attribution 4.0 International](CC-BY-4.0.txt) (`CC-BY-4.0`) |
| CraftViaの名称、ロゴ、ワードマーク、シンボルその他ブランド資産 | All rights reserved。[CraftVia Trademark Policy](TRADEMARK_POLICY.md)を参照 |

各ライセンスの全文とブランドポリシーは、このディレクトリに収録されています。リポジトリルートの`LICENSE`には、GitHubによる検出と容易な参照のため、そのリポジトリの主ライセンスを配置します。リポジトリに複数区分の素材が含まれる場合は、この文書が区分の境界を定めます。

## 適用範囲

1. 個別ファイルにライセンス表示またはSPDX識別子がある場合、その表示を優先します。
2. 第三者の素材には、元のライセンスと帰属表示が引き続き適用されます。
3. ハードウェアには、回路図、PCBレイアウト、編集可能なEDAソース、Gerberデータ、BOM、実装位置データ、機械設計ファイルなど、ハードウェア設計資料として明示されたファイルが含まれます。
4. ソフトウェアには、ソースコード、スクリプト、ファームウェア、ライブラリ、テスト、ビルド設定など、ソフトウェアとして明示されたファイルが含まれます。
5. 文書には、個別の表示がない限り、文章、仕様書、チュートリアル、図表が含まれます。
6. 生成物には、その生成元および生成物に付された表示に応じた条件が適用されます。
7. オープンライセンスはCraftViaのブランド資産の使用を許諾しません。説明目的の言及および互換性表示は、CraftVia Trademark Policyに従います。

## リポジトリで使用する表示

### ハードウェア

```text
Copyright (c) 2026 Kohei Ikeda.
SPDX-License-Identifier: CERN-OHL-P-2.0
```

### ソフトウェア

```text
Copyright (c) 2026 Kohei Ikeda.
SPDX-License-Identifier: Apache-2.0
```

### 文書

```text
Copyright (c) 2026 Kohei Ikeda.
SPDX-License-Identifier: CC-BY-4.0
```

## 文書の帰属表示

適切な帰属表示の例は次のとおりです。

> CraftVia documentation, copyright Kohei Ikeda, licensed under CC BY 4.0.

可能な場合は「CraftVia documentation」から原資料へ、「CC BY 4.0」から<https://creativecommons.org/licenses/by/4.0/>へリンクしてください。変更した場合は、その旨も表示してください。

## 推奨・公認を意味しないこと

オープンライセンスに基づいて資料を使用、複製、改変または製造できることは、その製品やサービスがCraftViaまたはKohei Ikedaによる公式品、公認品、認証品、推奨品であることを意味しません。

この文書は英語で定められたプロジェクト方針の参考訳です。英語版の[`README.md`](README.md)を正本とし、日本語版は理解の便宜のために提供します。
