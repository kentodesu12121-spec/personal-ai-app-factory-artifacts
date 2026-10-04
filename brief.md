# BuildBrief v0.3

## Meta
id: 001-split-bill
source: manual
tier: experiment
created: 2026-10-04

## App
名称: かんたん精算メモ
一文説明: 立替額から、誰が誰へ何円払えば精算できるか表示する。

## Problem & Target
複数人で食事や旅行をしたとき、立替額を簡単に精算したい人向け。

## Core Value
参加者と支出を入れるだけで、最終的な支払先と金額が分かる。

## MVP Features
1. 2〜8人の参加者を登録できる。
2. 支払者と整数円の支出額を登録できる。
3. 各支出を登録済み全参加者で均等負担する。
4. 各人の残高と「誰→誰へ何円」を表示する。
5. 支出削除後に即座に再計算する。

## Participant Rules
PR1. 名前は入力値の前後空白をtrimして保存する。
PR2. trim後1〜20文字のみ登録可能。
PR3. 空文字・空白のみは禁止。
PR4. 重複名は禁止。ただし大文字小文字は区別する。「A」と「a」は別人。
PR5. 最大8人。
PR6. 参加者削除機能は作らない。
PR7. 支出が1件でも存在する場合、新しい参加者は追加できない。

## Expense Input Rules
ER1. 支出登録には参加者が2人以上必要。
ER2. 支払者は登録済み参加者から選択する。
ER3. 金額入力はASCII数字のみ許可する。
ER4. 金額は1〜9,999,999円の整数のみ。
ER5. 先頭ゼロ、全角数字、カンマ、小数、符号、前後を含む空白がある値は拒否する。

## Calculation Rules
CR1. 各支出は、それぞれ独立して端数処理する。
CR2. 参加人数をN、支出額をAとする。
CR3. 基本負担額 = floor(A / N)。
CR4. 余りR = A mod N。
CR5. 登録順の先頭R人へ、それぞれ基本負担額+1円を割り当てる。残りは基本負担額。
CR6. 各人の残高 = 全支出で本人が実際に支払った合計 − 全支出で本人に割り当てられた負担額。
CR7. 残高>0を受取側、残高<0を支払側、0は精算対象外とする。
CR8. 支払側は残高絶対値の大きい順、同額なら参加者登録順に並べる。
CR9. 受取側は残高の大きい順、同額なら参加者登録順に並べる。
CR10. 両リストの先頭同士について、精算額 = min(支払側残高の絶対値, 受取側残高) とする。
CR11. 精算後に残高を更新し、CR8〜CR10を繰り返す。
CR12. 精算結果は生成された順に表示する。

## Storage Contract
SC1. localStorageキーは `split-bill-v1` とする。
SC2. 保存形式は次のJSONオブジェクトとする。
```json
{
  "version": 1,
  "participants": ["A", "B"],
  "expenses": [
    {"id": "任意の一意な非空文字列", "payer": "A", "amount": 1000}
  ]
}
```
SC3. participants配列の順番が登録順、expenses配列の順番が支出登録順を表す。
SC4. 保存データはversion=1、participantsがPR1〜PR5を満たす配列、expensesが非空文字列id・登録済みpayer・ER4を満たす整数amountを持つ配列でなければ無効とする。expense idは配列内で一意とする。
SC5. JSONとして壊れている、またはSC4を満たさない場合はクラッシュせず、`{"version":1,"participants":[],"expenses":[]}` に初期化して同じキーへ保存する。

## UI Contract
UC1. 参加者名入力は `data-testid="participant-name-input"`。
UC2. 参加者追加ボタンは `data-testid="participant-add-button"`。
UC3. 各参加者は `data-testid="participant-item" data-name="<参加者名>"`。
UC4. 支出入力は `expense-payer-select`、`expense-amount-input`、`expense-add-button` の各data-testidを使う。
UC5. 各支出は `data-testid="expense-item" data-payer="<支払者名>" data-amount="<整数>"` とし、その内部に `data-testid="expense-delete-button"` を置く。
UC6. 各残高は `data-testid="balance-item" data-name="<参加者名>" data-amount="<符号付き整数>"`。
UC7. 各精算は `data-testid="settlement-item" data-from="<支払者名>" data-to="<受取者名>" data-amount="<正の整数>"`。
UC8. 入力拒否時のメッセージ領域は `data-testid="form-error"`。文言は自由。
UC9. h1を1つ置き、アプリ名を表示する。
UC10. 360×800 viewportで横スクロールを発生させず、主要入力・ボタンは x>=0 かつ x+width<=360 に収める。

## Constraints
CT1. 静的Webのみ。HTML/CSS/JavaScriptで完結する。
CT2. 認証、決済、バックエンド、外部APIを使わない。
CT3. 外部CDN、外部フォント、外部画像等を使わず、localhost/127.0.0.1以外へ通信しない。
CT4. localStorage以外へ永続化しない。
CT5. 360px幅で横スクロールなしで操作可能にする。
CT6. 壊れた保存データでも未処理例外を出さない。

## Do Not Build
- 認証・ユーザー登録を作らない。
- 参加者削除機能を作らない。
- 共有・SNS投稿・CSV/PDF出力を作らない。
- 外部API・バックエンド・決済を使わない。
- 外部CDN・外部フォントを使わない。
- 「最小送金回数」の数学的最適化は行わない。

## Screens
1画面のみ。

## Acceptance Criteria
AC1. PR1〜PR5を満たして参加者登録できる。
AC2. ER1〜ER5を満たした支出だけ登録できる。
AC3. A/B/Cの順で登録し、Aが1,000円を支払うと、残高A=+666,B=-333,C=-333、精算B→A 333円、C→A 333円となる。
AC4. A/B/Cの順で登録し、Cが1円を支払うと、残高A=-1,B=0,C=+1、精算A→C 1円となる。
AC5. A/B/Cの順で登録し、Aが1,200円、Bが600円支払うと、残高A=+600,B=0,C=-600、精算C→A 600円のみとなる。
AC6. AC5の状態からBの600円支出を削除すると、残高A=+800,B=-400,C=-400、精算B→A 400円、C→A 400円となる。
AC7. 正常データは再読込後も保持される。不正JSONおよびJSONとして正しいがSC4違反の値ではSC5どおり初期化され、未処理例外を出さない。
AC8. 360×800 viewportでUC10を満たす。
AC9. 支出登録後はparticipant-add-buttonが無効になり、参加者が増えない。全支出削除後は再び追加可能になる。
AC10. 実行時にlocalhost/127.0.0.1以外へのネットワークリクエストを発生させない。

## Budget
max_wall_clock_min: 30
max_initial_agent_turns: 16
max_fix_turns_per_loop: 8
max_fix_loops: 3
max_total_agent_turns: 40
max_api_cost_usd: 0

## Done Definition
- 公開AC全合格。
- Evaluator側の隠しケース全合格。
- 外部通信0件。
- pageerror 0件。
- ローカルPreview正常表示。
- README.mdとreport.md生成。
- brief.md不変。
- Do Not Build未実装。
- 未解決の仕様曖昧性なし。
