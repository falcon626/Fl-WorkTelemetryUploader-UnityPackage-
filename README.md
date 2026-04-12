# Falcon Work Telemetry (Unity Editor Extension)

UE版 Fl-WorkTelemetryUploader 相当の「作業見える化」を Unity Editor 向けに調整したものです。

## 今回の改善点
- Discord の終了ログは 1 セッション 1 投稿を維持
- マシン名取得を改善（`UnknownMachine` になりやすかった経路を修正）
- Menu を `Tools > Falcon > Fl-Work_Telemetry` に統一
- 配置を `Assets/Falcon/Script/Editor/FlWorkTelemetry/` に統一
- Unity で意味の薄い 0 件の活動行は Discord に出さない
- 保存一覧の見出しを Unity 向けに `Saved assets/scenes` に変更

## インストール
### 新規導入
1. この zip の `Assets/Falcon/Script/Editor/FlWorkTelemetry/` を Unity プロジェクトの `Assets/` にコピー
2. Unity を起動（初回起動で `Library/Fl-WorkTelemetry/` が作成されます）
3. `Library/Fl-WorkTelemetry/uploader_config.json` の `webhook_url` を設定

### 旧版から更新する場合
1. **先に** `Assets/Fl-WorkTelemetryUploader/` を削除
2. 次にこの zip の `Assets/Falcon/Script/Editor/FlWorkTelemetry/` をコピー
3. `Library/Fl-WorkTelemetry/` はそのままで大丈夫です（設定と履歴を引き継ぎます）

## メニュー
- `Tools/Falcon/Fl-Work_Telemetry/Open Work Folder`
- `Tools/Falcon/Fl-Work_Telemetry/Open Config (uploader_config.json)`
- `Tools/Falcon/Fl-Work_Telemetry/Open Spawn Log`
- `Tools/Falcon/Fl-Work_Telemetry/Open Runtime Log`
- `Tools/Falcon/Fl-Work_Telemetry/Send Test Discord Ping`
- `Tools/Falcon/Fl-Work_Telemetry/Print Paths`

## できること
- Unity Editor セッション開始/終了を記録（クラッシュ時は外部Uploaderが検知）
- Asset を開いた回数（ユニーク）を記録
- Asset/Scene が Dirty になったことを記録（Undo変更・Scene Dirtyイベント）
- Save された Asset / Scene を記録（OnWillSaveAssets）
- すべて `Library/Fl-WorkTelemetry/events.jsonl` に JSONL（1行1イベント）で保存
- Unity 終了後、外部 Python Uploader が Discord Webhook にサマリを投稿
- Git diff（unstaged/staged/total + Top changed files）を併記
- total today / total tracked（総起動時間）を集計して表示

## 表示仕様
- `opened assets` / `modified assets/scenes` / `saved assets/scenes` は **0 件なら Discord に表示しません**
- `ℹ️ Unity ended (end marker missing)` は既定では Discord に投稿しません
- 1 セッションの最終投稿は既定で 1 メッセージです

## Python について
外部Uploaderは Python を使います。PATH に python が無い場合は config に絶対パスを指定してください。

例（Windows）:
  `"python_exe": "C:/Python311/python.exe"`

## 出力先
- events: `Library/Fl-WorkTelemetry/events.jsonl`
- config: `Library/Fl-WorkTelemetry/uploader_config.json`
- logs:
  - `Library/Fl-WorkTelemetry/uploader_spawn.log`
  - `Library/Fl-WorkTelemetry/uploader_runtime.log`

## 動作確認（最短）
- Asset を開く -> `opened assets` が必要時のみ表示
- Scene/Prefab/Asset を編集 -> `modified assets/scenes` が必要時のみ表示
- Save -> `saved assets/scenes` が必要時のみ表示
- Unity を終了 -> Discord にサマリ投稿

## バージョンメモ
### v1.3.3 Falcon path + quit fix
- Menu を `Tools/Falcon/Fl-Work_Telemetry` に移動
- 配置を `Assets/Falcon/Script/Editor/FlWorkTelemetry` に変更
- Python 側のマシン名取得を修正
- C# 側でも `Environment.MachineName` 優先で取得
- 0 件の活動行を Discord サマリから非表示化

- Unity 終了時の同期 fallback 送信を廃止し、終了時は detached uploader を起動
- `EditorApplication.quitting` で長時間ブロックしにくい構成に変更
