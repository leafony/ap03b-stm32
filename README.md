# Leafony AP03B STM32

STM32L452REI6を搭載した、Leafony AP03B STM32リーフのハードウェア設計リポジトリです。KiCadの回路図・PCBレイアウト、専用ライブラリ、部品表（BOM）を収録しています。

設計を開く場合は [KiCadプロジェクト](./kicad/leafony-ap03-stm32.kicad_pro)、部品を確認する場合は [BOM](./docs/leafony-ap03b-stm32-bom.csv) から参照してください。

## ハードウェア概要

| 項目 | 内容 |
| --- | --- |
| MCU | STMicroelectronics STM32L452REI6（U1） |
| CPU | Arm Cortex-M4、FPU搭載、最大80 MHz |
| メモリ | Flash 512 KB / SRAM 160 KB |
| MCUパッケージ | UFBGA-64、5 × 5 mm、0.5 mmピッチ |
| 低速外部クロック | 32.768 kHz水晶（X1） |
| 電源 | Leafonyバスの3.3 V / GND |
| バス接点 | CN1、表裏各29接点 |
| 主なバス信号 | D0〜D13、A0〜A5、SDA / SCL、SWDIO / SWCLK、RESET、AREF |
| 操作・表示 | BOOT0切り替えスイッチ、リセットスイッチ、BOOT0状態表示LED |
| 基板 | 20 × 20 mm、6層、板厚0.8 mm（PCB設計値） |
| 設計リビジョン | v1.0、2026-04-27（単体基板・回路図のタイトル欄） |

CPU・メモリの仕様は [STMicroelectronicsの製品情報](https://www.st.com/en/microcontrollers-microprocessors/stm32l452re.html)、実装部品と接続は本リポジトリの回路図・PCB・BOMを参照しています。最大クロックはMCUの仕様値で、実際の動作クロックはファームウェアの設定によります。

CN1のVBUS端子は未接続で、AREFの接続用抵抗R5は未実装（DNP）です。各端子の接続は [Leafonyバス回路図](./kicad/leafony_bus.kicad_sch) と [MCU回路図](./kicad/stm32.kicad_sch) で確認できます。

### スイッチとLED

| 部品 | 型番 | 回路上の役割 |
| --- | --- | --- |
| SW1（PG） | CUS-12TB | BOOT0のHigh / Lowを切り替える |
| SW2（RST） | EVQ-P7B01P | 押すとRESETをGNDへ接続する |
| D1 | SML-A12U8TT86 | BOOT0がHighのときに点灯する |

詳細は [リセット・プログラム切り替え回路](./kicad/reset_prog.kicad_sch) を参照してください。

## リポジトリ構成

| パス | 内容 |
| --- | --- |
| [kicad/leafony-ap03-stm32.kicad_pro](./kicad/leafony-ap03-stm32.kicad_pro) | 単体基板のKiCadプロジェクト |
| [kicad/leafony-ap03-stm32.kicad_sch](./kicad/leafony-ap03-stm32.kicad_sch) | トップレベル回路図。以下の3シートをまとめる |
| [kicad/stm32.kicad_sch](./kicad/stm32.kicad_sch) | MCU、電源、クロック周辺回路 |
| [kicad/leafony_bus.kicad_sch](./kicad/leafony_bus.kicad_sch) | Leafonyバス接点と信号接続 |
| [kicad/reset_prog.kicad_sch](./kicad/reset_prog.kicad_sch) | リセット、BOOT0切り替え、LED、信号切り替え回路 |
| [kicad/leafony-ap03-stm32.kicad_pcb](./kicad/leafony-ap03-stm32.kicad_pcb) | 単体基板のPCBレイアウト |
| [kicad/panelized/](./kicad/panelized/) | 面付け基板のプロジェクト・PCBレイアウト |
| [kicad/lib/](./kicad/lib/) | Leafony専用シンボル、フットプリント、3Dモデル配置先 |
| [docs/leafony-ap03b-stm32-bom.csv](./docs/leafony-ap03b-stm32-bom.csv) | 部品表（CSV） |
| [examples/](./examples/) | サンプルコード用ディレクトリ。現時点ではコード未収録 |

製品名はAP03Bですが、KiCadファイル名は `leafony-ap03-stm32` です。

## KiCadで開く

回路図とPCBは **KiCad 9.0** で保存されています。KiCad 9.0形式を読み込める環境と、KiCad標準のシンボル・フットプリントライブラリを用意してください。

1. リポジトリを取得します。

   ```sh
   git clone https://github.com/leafony/ap03b-stm32.git
   cd ap03b-stm32
   ```

2. KiCadで `kicad/leafony-ap03-stm32.kicad_pro` を開きます。
3. プロジェクト内の `.kicad_sch` を開くと回路図、`.kicad_pcb` を開くと基板レイアウトを確認できます。
4. 回路図のトップシートから「STM32 MCU」「Leafony Bus」「Reset Prog」の各階層シートへ移動します。

専用ライブラリは [sym-lib-table](./kicad/sym-lib-table) と [fp-lib-table](./kicad/fp-lib-table) に登録済みです。どちらも `${KIPRJMOD}/lib/` を参照するため、`kicad/` 以下のディレクトリ構成を保って開いてください。

### 面付け基板について

`kicad/panelized/` のPCBタイトル欄は「Leafony AP03 STM32 / 2025-07-06」で、単体基板の「Leafony AP03B STM32 / 2026-04-27」と異なります。製造に使用する場合は、単体基板の変更が反映されているか確認してください。

面付けプロジェクトを開くと `${KIPRJMOD}` の基準は `kicad/panelized/` になります。3Dモデルを表示する場合は、参照先が `kicad/lib/3dmodels/` に解決されるようにモデルパスを調整してください。

## 部品表（BOM）

[BOMのCSV](./docs/leafony-ap03b-stm32-bom.csv) には、部品番号（Reference）、値・型番（Value）、データシート、フットプリント、数量（Qty）、実装対象外の指定（DNP）、メーカー品番（MPN）、メーカー名（MANUFACTURER）を記載しています。

- `R5`、`R7`、`R12`、`R14` はDNP（未実装）です。
- `CN1` はBOMに含まれていません。バス接点の形状・接続は回路図とPCBを参照してください。
- 部品を変更した場合は、回路図と合わせてBOMも更新してください。

## 3Dモデルについて

一部部品の3Dモデルはライセンスの都合上公開できません。`kicad/lib/3dmodels/` にはモデル本体を収録していないため、必要に応じて別途入手し、以下のファイル名で配置してください。Leafony専用の回路図シンボルとフットプリントは同梱しています。

単体基板のPCBが参照するモデルは次のとおりです。

| 部品 | ファイル名（`kicad/lib/3dmodels/` 内） |
| --- | --- |
| U1：STM32L452REI6 | `UFBGA64-5x5mm.step` |
| U2：TS3A4751RUCR | `RUC0014A.stp` |
| SW1：CUS-12TB | `CUS-12TB.step` |
| SW2：EVQ-P7B01P | `EVQP7-KB-01P.STEP` |
| X1：32.768 kHz水晶 | `XTAL_NX2012SA_NDK.step` |
| Q1：RE1C002UNTCL | `DIO_RB558WMF_ROM.step` |
| D1：SML-A12U8TT86 | `SML-A12U8TT86P.stp` |

モデル名は既存のPCB参照に合わせたものです。入手したモデルのパッケージ形状を確認し、PCBエディターの3Dビューアーで位置・向き・縮尺を確認してください。3Dモデルがなくても回路図やPCBレイアウトは確認できます。

標準部品のモデルには `${KICAD8_3DMODEL_DIR}` の参照が残っています。KiCad 9では、旧バージョンの変数が未定義なら現行バージョンの対応する変数へ自動解決されます。表示されない場合は、標準3Dモデルライブラリの導入状況と「パスを設定」で古いパスが明示設定されていないか確認してください。詳細は [KiCad公式ドキュメントのパス設定](https://docs.kicad.org/9.0/en/kicad/kicad.html) を参照してください。

## ファームウェア・関連資料

このリポジトリには、ファームウェアのビルド設定や書き込みスクリプト、実行可能なサンプルコードは含まれていません。STM32 MCUリーフの開発環境については、Leafony公式資料を参照してください。

- [STM32 MCU スタートガイド](https://docs.leafony.com/getting-started/stm32/)
- [STM32 MCU向けArduino IDEの設定](https://docs.leafony.com/environment/stm32/arduino_ide/)
- [AP03 STM32 MCU 技術資料](https://docs.leafony.com/leaf/processor/ap03/)

上記のAP03向け資料を利用する際は、本リポジトリのAP03B回路図でピン接続や実装部品を確認してください。
