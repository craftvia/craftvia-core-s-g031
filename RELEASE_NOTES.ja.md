# CraftVia Core-S G031 v1 Rev0 ハードウェアRelease

> 本文書は英語正本 [`RELEASE_NOTES.md`](RELEASE_NOTES.md) の日本語翻訳版です。解釈に差異がある場合は英語版を優先します。

## Release状態

- Revision：Rev0
- Release日：2026-09-30
- 状態：初回試作PCB製造向けにFROZEN
- 想定用途：Rev0 Engineering Prototype。量産向けReleaseではない
- CAD：EasyEDA v3.2.149.88089769
- 正本の編集可能Source：`source/CraftVia-Core-S-G031-v1-Rev0.eprj2`
- 正本のPCB製造Upload：`fabrication/Gerber_PCB1_2026-09-30.zip`

本Releaseは、2026-09-30にEasyEDAで開いたProjectと照合しました。回路図・PCB Document UUID、部品実装状態、PCB Primitive数、Net数は凍結済みSource Packageと一致しています。

## Release判定

以下の発注・実装条件を前提として、初回Rev0 PCB試作発注に使用可能なPackageです。

- PCB Strict DRC：PASS、Error 0
- Schematic DRC：Fatal 0、Error 0、Warning 1
- 残る回路図Warningは、J1-J7、R1-R3、R7、R10に対する既知のEasyEDA Supplier Part属性正規化Warningであり、電気的接続Errorではない
- Gerber ZIP整合性：PASS
- 基板：25.4 mm x 25.4 mm、銅箔4層
- 確認済みPCB内容：29 Component、配線Line Primitive 287、Via 32、Copper Pour 2、Net 40

## Release収録物

| Path | 用途 |
| --- | --- |
| `source/CraftVia-Core-S-G031-v1-Rev0.eprj2` | 正本の編集可能EasyEDA Project |
| `source/ProPrj_CraftVia-Core-S-G031-v1-Rev0.epro` | Portable EasyEDA Project Archive |
| `fabrication/Gerber_PCB1_2026-09-30.zip` | 正本のPCB製造Package |
| `assembly/BOM_Board1_PCB1_2026-09-30.csv` | 正本の実装対象BOM |
| `assembly/PickAndPlace_PCB1_2026_09_30.csv` | 基板全体の座標Export。Turnkey Assemblyへ渡す前にFilterが必要 |
| `assembly/PCB_PCB1_2026-09-30.pdf` | PCB、BOM、Assembly／Layer参考Export |
| `docs/SCH_Core-S-G031-Rev0_2026-09-30.pdf` | 人が確認するための回路図Reference |
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
- PTH Component Hole Drill、PTH Via Drill

NPTH Drill Fileはありません。本Core基板には意図的な非Plated Mounting Holeがないため、これは正常です。Bottom側SMT部品がないため、Bottom Paste Fileもありません。

発注前に、正確なZIPをJLCDFMまたは製造業者のGerber Previewへ読み込み、次を確認してください。

- 25.4 mm x 25.4 mmの外形
- 銅箔4層
- HeaderとTest Pointの穴がPTHとして認識されていること
- Top／Bottom SilkscreenとSolder Mask開口が存在すること
- 想定外のNPTH HoleまたはDrill Offsetが報告されないこと

製造業者で発注時に選択したStack-upを正とします。後続ReleaseでControlled Stack-upを指定しない限り、一般的な4層FR-4 Prototype Stackを使用してください。

## 実装対象

BOMは15行、実装対象Designatorは16個です。

`C1, C2, C3, J1, J2, J3, J4, J5, J6, J7, R1, R2, R3, R7, R10, U1`

次の部品はRev0で意図的にDNPです。

`C4, R4, R5, R6, R8, R9, R11, X1`

次のTest Pointは物理的なPCB Featureですが、BOM実装品ではありません。

`TP1, TP2, TP3, TP4, TP5`

### Pick-and-placeに関する警告

`assembly/PickAndPlace_PCB1_2026_09_30.csv`は29 Designatorを含む未Filterの基板全体Exportです。16個のBOM Designatorに加えて、8個のDNP Optionと5個のTest Pointを含みます。

- PCB製造だけを行う場合、Gerber発注には影響しない
- Turnkey Assemblyでは、CPLを未変更のままUploadしない
- CPLを正本BOMの16 DesignatorへFilterするか、実装業者が追加DNP／Test Point行を明示的に受け付けることを確認する
- BOMの実装状態は、未Filter CPLおよびAssembly PDF上の赤いX表示より優先する

J1-J4のSupplier Referenceは`C49256`ですが、Library Dataには必要な角Pin寸法、Mating Pin長、Insulator高さが定義されていません。購入・実装前にStation-S Socket Stackに対して実部品を確認してください。J5-J7は`C81276`で、公称Mating Pin 6 mm、Insulator高さ2.5 mmです。

## 標準電気構成

- 入力電源：3.3 V
- R7：実装、0 ohm。`VDD_CORE`へ給電
- R3：実装、0 ohm GPIO Link
- R10：実装、0 ohm
- 外付け8 MHz Oscillator Option：DNP
- 標準Clock Source：内蔵HSI
- Optional Oscillator関連部品：上記一覧どおりDNP
- PA14はSWCLKとBOOT0で共有。Boot状態を変更するときはStation-Sの分離・選択構成を使用する

## 既知の文書上の注意

回路図PDFには過去の`SCHEMATIC DRAFT`および`DESIGN NOTES - NOT RELEASED FOR FABRICATION`表示が残っています。この凍結Folderでは、回路図PDFは回路Review用Referenceであり、製造正本ではありません。本READMEとSHA-256 ManifestがRev0のRelease状態を定め、Gerber ZIPだけがPCB製造正本です。

後続Revisionまたは文書のみを整理した再Packageでは、EasyEDAのTitle／Noteから過去の表示を削除し、それ以外のSourceを変更せず回路図PDFを再生成します。

## 初品確認

1. CoreをStation-Sへ挿入する前にJ1-J7の向きと導通を確認する
2. 電流制限付き3.3 V電源を使用し、`VDD_CORE`とGND間に短絡がないことを確認する
3. U1 VDD／VDDAで3.3 Vを測定し、想定Idle Currentを確認する
4. PA13/SWDIO、PA14/SWCLK、PF2/NRSTを介したSWD接続を確認する
5. PA14共有Pin NetworkでBOOT動作を確認する
6. Continuity FixtureまたはStation-Sを使用し、全N/E/S/W Edge Pinを確認する
7. 初回の内蔵HSI Bring-upではX1とOption NetworkをDNPのままにする

## 変更管理と整合性

発注前に`SHA256SUMS.txt`記載の全Fileを照合してください。本Releaseを発注に使用した後は、`v1-rev0`tagまたはGitHub Release Assetに記録されたFileを上書きしません。電気回路、Footprint、配線、Silkscreen、実装対象の変更には新しいRevisionが必要です。文書のみの訂正は、tagを移動・置換せず後続Commitで行います。
