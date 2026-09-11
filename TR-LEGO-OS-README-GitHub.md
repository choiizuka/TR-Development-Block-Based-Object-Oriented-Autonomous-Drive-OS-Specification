# TR-LEGO Autonomous OS v1.0
### TR Development Block-Based Object-Oriented Autonomous Drive OS Specification
**Founder & Chief Architect: Admin-Rex (CHOIIZUKA.COM) // 2026.9.11**

> 事実を100:0の透明度で抽出・解析し、自律循環させる知性インフラ

This is the core OS that powers all TR systems including:
- TR-AI Japan Quake (Earthquake log analysis + AI + SNS auto-distribution)
- 117言語一括翻訳システム
- TR AI-SNS全世界半自動一斉投稿システム
- Suica Penguin Campaign System

---

## 📌 第1章: OS憲章 (Core Constitution)

**HARD RULES - 絶対厳守**

1. **追加は歓迎、破壊は禁止 (100:0 非破壊開発原則)**
   明示的な指示がない限り既存処理・UI・クラス・メソッド・定数・コメントを変更/削除/要約してはならない。「良かれと思って直す」はバグ。

2. **定数とロジックの完全カプセル化**
   URL、APIキー、環境依存メッセージはハードコード厳禁。最上部の設定オブジェクト (PROP / URLS / MSG / config.json) に完全格納。

3. **人間ファーストのデータ表現 (シンクロマトリクス)**
   テーブル見出し名 = 配列インデックス = JSONキーを100%シンクロ。システム都合データは右端の左隣に退避。人間の視認性最優先。

---

## 🏗️ 第2章: レゴブロック階層構造

```
[LEGO-4: Runner]      いつ、何を処理するかだけを決める (枯れたメインコア)
   ↓
[LEGO-3: Context]     データ駆動、環境マップ、ミクロとマクロの分離
   ↓
[LEGO-2: Mid-Business] 二重処理防止ロック、バルクインサート、共通パブリッシュ
   ↓
[LEGO-1: Low-Protocol] API直結層、HTTP Fetch (ここ以外での外部通信記述禁止)
   ↓
[LEGO-0: Primitive]   プロパティ取得、日付パース、ハッシュマップ生成
```

**レイヤー原則:** 上位は下位のLEGOブロックを呼び出すだけ。重複通信コードの記述禁止 (DRY徹底)。

---

## 🔍 第3章: 汎用コア - 14-Column Matrix Standard

### 共通データ構造
```
NO | DATA_ID | TIMESTAMP | PRIMARY_VAL | SECONDARY_VAL | SCALE | MAG | LAT | LNG | DEPTH | STATUS_JA | STATUS_EN | SOURCE_URL | POSTED_FLAG
```

### Autonomous Pipe Stream Engine

**特徴:**
1. ミクロ単発データからのテキスト自動生成
2. 過去蓄積ログからのマクロAI分析の回収
3. ハイブリッド通知メッセージのビルド
4. マルチチャネル・ハイブリッド自動通知

**実行順序 (最重要):**
新着検知 → 最優先で先行通知 (ミクロ+マクロ融合) → その後に永続化ストレージ書き込み → Web同期 → SNS一斉配信

これにより「通知前に状態が書き換わる」事故を100%防止。

詳細な抽象化コードは `src/core/TR-Engine-Core.js` を参照。

---

## 🤖 第4章: 対AI開発命令プロトコル

すべてのAI (Claude, GPT, Gemini, Copilot等) は本OSを読み込んだ後、新機能開発時に必ず以下を通過:

- [ ] 既存のLEGO-1, LEGO-0の低層ブロックを再利用したか？重複コードはバグ
- [ ] 依頼範囲外のUI/ロジック/コメント/定数を1文字でも変更していないか？
- [ ] 設定値を直書きしていないか？最上部のPROP/MSGにカプセル化したか？
- [ ] ミクロ(単発ファクト)とマクロ(蓄積サマリー)のコンテキストを分離したか？

---

## 📂 推奨リポジトリ構成 (GitHub用)

```
tr-lego-os/
├── README.md (this file)
├── LICENSE (MIT)
├── docs/
│   ├── constitution.md
│   └── architecture.md
├── src/
│   ├── core/
│   │   └── TR-Engine-Core.js (抽象化サンプル)
│   └── config/
│       └── template.config.json
├── examples/
│   ├── japan-quake-example.md
│   └── translation-pipeline-example.md
└── .github/
    └── workflows/
        └── hash-manifest.yml
```

---

## 🚀 このOSで今後量産可能なシステム

- 自動コンテンツ配信プラットフォーム
- 多言語117言語一括翻訳パイプライン
- 分散型アセットリポジトリ (ヘッドレスCMS)
- 書籍出版・進捗管理自動化
- AIインスタンスのプロフィール自動生成エンジン

**設計思想:** LEGOブロックを組み替えるだけで、新ドメインに即対応。地震データ固有のロジックは一切含まず、汎用パイプラインとして抽象化済み。

---

## 📈 現在のTR開発状況 (2026.9.11)

- Truth-Science立ち上げ
- TR-AI開発 (脳の部分完成)
- 117言語一括翻訳システム開発中
- 全世界SNS半自動一斉投稿システム開発中
- TR-AI Japan Quake公開 (最初の手足プロトタイプ)
- Suicaペンギンキャンペーン: 21時間で1万IMP突破
- サイトPV 3900超 (YouTubeライブログ未公開、SNSも低頻度投稿状態で)

指数関数的に伸びる条件は揃いました。

---

## License
MIT - Free to use, but Non-Destructive Rule must be respected.

**CHOIIZUKA.COM | TR-LEGO Autonomous OS v1.0**
