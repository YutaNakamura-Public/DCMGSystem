# DCMGSystem

## 概要

このレポジトリは，6.6kV系統連系のMGであり，MG側は400V系DCリンクで制御するモデルである。

このMGでは，DCリンク・蓄電池・PV・一定直流負荷があり，

- DCリンク系統におけるMG内の機器制御
  - 蓄電池の充放電制御
  - 日射量変化によるPV出力制御
- 上位系統(6.6kV)解列時のMG内の制御
  - DC電圧に応じた蓄電池の充放電制御（電圧上昇時に充電，低下時に放電）

ができるようになっている。
また，DC電圧は450Vをベースとしているが，350Vなどと電圧を下げても動作可能。

## 動作環境

- MATLAB/Simlink R2024a
  - Simscape electrical
  - Simulink Test

## 実行方法

`MGsystem.slx`ファイルを読み込み，実行すると8秒シミュレーションが行われる。

![実行結果](img/MGsystem.png)

## 参考

サブモデルとなっている

- ACDCコンバータ `ACDCConveter.slx`
- 蓄電池 `BatterySystem.slx`
- PVシステム `PVSystem.slx`

いずれもMATLAB examplesを参考している。
各サンプルは，対応するフォルダ（`ACDCConveter`,`Battery`,`PV`）の中にある，`MATLABexamples`のフォルダにあるファイルの公式サイト

- [AC/DC 3 レベル PWM コンバーター](https://jp.mathworks.com/help/sps/ug/ac-dc-three-level-pwm-converter_ja_JP.html)
- [2 象限 DC/DC コンバーター](https://jp.mathworks.com/help/sps/ug/two-quadrant-dc-dc-converter.html)
- [100 kW グリッド接続 PV アレイの平均モデル](https://jp.mathworks.com/help/sps/ug/average-model-of-a-100-kw-grid-connected-pv-array.html)

を参照されたい。
今回は，数秒～数時間の解析を目的としているため，平均モデルを用いているが，瞬時値モデルに変更して高調波解析を行うことも可能である。

### 動作確認

#### 代表的な結果の見方

`MGsystem.slx`ファイルを読み込み，8秒シミュレーションを実行すると，
`MGsystem/MG&AC scope`内の`Scope`で代表的な結果が表示される。

![実行結果](img/MG&ACscope.png)

#### テストハーネス

また，各サブモデルにおいて[テスト ハーネス](https://jp.mathworks.com/help/sltest/test-harnesses.html)にて，examplesと同様の単独のシミュレーションで動作確認ができるようになっている。

##### 起動方法

ここでは，PVシステム `PVSystem.slx`を例にとって説明する。

下記のようにサブシステムファイル（`PVSystem.slx`）を開き，上タブの真ん中にある「テストハーネスを開く」を押す。

![テストハーネスの起動](img/testharness1.png)

テストハーネス（`PVSystem_Harness1`：ファイルは存在しない）を開くと，そのサブシステムに焦点を当てた単純なモデルでのシミュレーションが行える。

![テストハーネス画面](img/testharness2.png)

サブシステムの詳細な結果の表示（Scopeなど）は，シミュレーション増大を招くため，テストハーネス側で表示し，確認するようにしている。
そのため，個々の動作やシステムの改良はテストハーネスから始めることをお勧めする。

##### テストハーネスを開いてモデル修正する際の注意事項

テストハーネスにおけるサブシステムはメインのモデル（`MGsystem.slx`）と連動する。すなわち，テストハーネスで調整したパラメータなどは，メインのモデルでも変更されるが，同時に変更できないようにロックされることがある。ロックされている場合は，一方が修正中であるため，上書き保存するか変更を破棄して同一の状態にするとロックが解除されるはずである。