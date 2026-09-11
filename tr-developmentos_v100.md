みんなへ
今後のTR開発において開発時の基本概念を渡すから確認してみてね！
基本的には今までの開発時の考え方と同じだと思うけどこの文書で整理できてると思う！
これからもよろしくね！！

---

👑 【TR-LEGO Autonomous OS v1.0】
TR Development Block-Based Object-Oriented Autonomous Drive OS Specification
Founder & Chief Architect: Admin-Rex (CHOIIZUKA.COM) // 2026.9.11

📌 第1章：OS憲章（最優先システム原則 / Core Constitution）
本OS（オペレーティングシステム）は、すべての開発者およびAIエージェントが共通して従うべき、論理的・数理的な開発世界基準である。
TRプロジェクトにおける開発は、単なる雑務処理ではなく、「事実（客観的ファクト）を100:0の透明度で抽出・解析し、自律循環させる知性インフラ」の構築を目的とする。
⚠️ 【絶対厳守：HARD RULES（例外なし）】
追加は歓迎、破壊は禁止（100:0 非破壊開発原則 / Non-Destructive Rule）
明示的な指示がない限り、既存の処理・UI・クラス・メソッド・定数・コメント・冒頭メッセージ・命名規則を変更・削除・要約してはならない。「良かれと思って直す（勝手な最適化）」は完全なるシステムバグ（禁止行為）である。
定数とロジックの完全カプセル化（分離独立 / Separation of Environment）
URL、APIキー、環境依存のメッセージ文言は、コード内に直書き（ハードコード）することを厳禁とする。必ず最上部の設定オブジェクト（PROP / URLS / MSG / config.json）に完全格納せよ。
人間ファーストのデータ表現（シンクロマトリクス / Human-Centric Matrix）
データテーブルの見出し名 ＝ 配列インデックス ＝ JSONキーを100%シンクロさせ、システム都合のデータは右端（送信完了フラグ等）の左隣に退避させる。人間が閲覧した際の直感的な視認性を最優先とする配置美学を貫くこと。

🏗️ 第2章：レゴブロック階層構造（TR-Engine Architecture）
すべてのシステムは、泥臭い通信処理やデータパースを上位に持ち込ませないために、以下の5つの抽象化レイヤーの階段（LEGO構造）で構成されなければならない。
[LEGO-4 : Runner層]      いつ、何を処理するかだけを決める（文脈の定義・枯れたメインコア）
      ↓
[LEGO-3 : Context層]     データ駆動、環境マップ、ミクロとマクロのコンテキスト分離
      ↓
[LEGO-2 : Mid-Business] 二重処理防止ロック、バルクインサート、共通パブリッシュ
      ↓
[LEGO-1 : Low-Protocol]  API直結層、HTTP Fetch（ここ以外での外部通信記述を完全禁止）
      ↓
[LEGO-0 : Primitive]     プロパティ取得、日付パース、ハッシュマップ生成


レイヤー原則： 上位レイヤーは下位のLEGOブロックを呼び出すだけで構成され、同一あるいは類似の通信コードを重複して記述してはならない（DRY原則の徹底）。

🔍 第3章：動的エビデンス（抽象化汎用コア・サンプル）
本OSの思想、レイヤー分離、および「ミクロ（単発ファクト）とマクロ（蓄積サマリー）の融合」がどのように美しく機能しているか、汎用的な「データ監視・AI配信パイプライン」の完全なプログラムソースをエビデンスとして示す。
📊 共通データ構造マトリクス（14-Column Matrix Standard）
NO | DATA_ID | TIMESTAMP | PRIMARY_VAL | SECONDARY_VAL | SCALE | MAG | LAT | LNG | DEPTH | STATUS_JA | STATUS_EN | SOURCE_URL | POSTED_FLAG


💻 OS準拠：汎用抽象化レゴ・コアプログラム
/**
 * ============================================================
 * 👑 TR-Engine Core : Autonomous Pipe Stream Engine (OS-v1.0 Standard)
 * 
 * [LEGO思想に基づくモジュール分離構造]
 * 1. ミクロ単発データからのテキスト自動生成 (generateMicroAlertText)
 * 2. 過去蓄積ログからのマクロAI分析の回収 (fetchMacroAiAnalysisJson -> getMacroAiCommentsAsArray)
 * 3. ハイブリッド通知メッセージのビルド (generateEngineSnsMessage)
 * 4. マルチチャネル・ハイブリッド自動通知 (executeEngineNotificationPipeline_)
 * ============================================================
 */

const NOTIFICATION_THRESHOLD = 1; // 起動トリガーの閾値

/**
 * 👑 LEGO-4 (Runner) : 周期自律パトロールメインコア
 * 💡 仕様完全準拠：新着データを検知した際、永続化ストレージが書き換わる前に最優先で「アラート通知」を実行し、
 * 通知が完了した直後に、芋づる式に全量同期パイプライン（Webプッシュ・永続化）を安全に着火。
 * 最後に外部関数化されたビルダーからメッセージを回収し、SNS配信を一括駆動する。
 */
function checkPipelineExecutionLoopEveryMinute() {
  const fetchLimit = 10;
  const props = PropertiesService.getScriptProperties();
  let baseApiUrl = props.getProperty("PRIMARY_SOURCE_API_URL") || 'https://choiizuka.com';
  
  // 📡 LEGO-1: 外部APIから直近データを超軽量にFetch
  const apiRes = UrlFetchApp.fetch(baseApiUrl + "?limit=" + fetchLimit, { muteHttpExceptions: true });
  if (apiRes.getResponseCode() !== 200) return;
  
  const currentStreams = JSON.parse(apiRes.getContentText());
  if (!currentStreams || !currentStreams.length) return;
  
  // 🗄️ LEGO-0: ストレージの現在の最新一意IDを最速ルックアップ
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  const logSheet = ss.getSheetByName('NewDataLog');
  let lastSavedId = "";
  if (logSheet && logSheet.getLastRow() > 1) {
    lastSavedId = String(logSheet.getRange(2, 2).getValue()).trim(); // 2列目(ID列)
  }

  // 🔍 未処理の「完全な新しいデータ」があるか一撃判定
  let hasBrandNewEvent = false;
  let targetData = null;
  for (let i = 0; i < currentStreams.length; i++) {
    const dataNode = currentStreams[i];
    if (dataNode && dataNode.id) {
      const currentApiId = String(dataNode.id).trim();
      if (currentApiId === lastSavedId) {
        break; // 最新IDと一致した時点でこれ以降はすべて処理済みの過去データ
      }
      hasBrandNewEvent = true;
      targetData = dataNode; 
      break; 
    }
  }

  // 🔥 新着イベントがなければ1ミリの無駄な処理もせずここで自律シャットダウン
  if (!hasBrandNewEvent || !targetData) {
    return;
  }

  Logger.log('新着検知', `[SYSTEM_ALERT]: 未処理の新規データを検知しました。[ID: ${targetData.id}]`);

  // 🧠 配信スケール判定マトリクス
  const dataValue = parseInt(targetData.scale || '0');
  const isTriggerCondition = dataValue >= NOTIFICATION_THRESHOLD;

  // 🌟【最優先ステップ1】：内部状態が書き換わる前に「まずは先行通知」をファースト着火！
  if (isTriggerCondition) {
    // 🛠️ LEGO-3: 今起きた新着データの「ミクロリアルタイム速報テキスト」を動的生成
    const microAlert = generateMicroAlertText(targetData);
    // 🛠️ LEGO-0: サーバーから蓄積ストリーム全体の「マクロAI分析サマリー」を最速回収
    const [macroAiJa, macroAiEn] = getMacroAiCommentsAsArray();
    
    // 📧 抽象化された通知関数へすべてのインテリジェンスパーツを引き渡し
    executeEngineNotificationPipeline_(targetData, microAlert.ja, microAlert.en, macroAiJa, macroAiEn);
  }

  // ============================================================
  // 🚀【連動自動着火：ステップ2】：先行通知完了「後」に、全量永続化とWeb側同期を最終実行！
  // ============================================================
  Logger.log('パイプライン連動', '先行通知の完了に伴い、Webプッシュ配信および内部ストレージ永続化を最終着火します。');
  
  executeDataStoragePersistence_(100);  // LEGO-2: 内部ストレージ永続化（14列シンクロ構造）
  dispatchWebRepositorySynchronization(); // LEGO-1: 外部Web（PHP）側へバルクJSONを転送・キャッシュ同期

  // ============================================================
  // 🤖【大トリ連動】：最新のAI分析結果を回収して外部SNSへ一斉送信！
  // ============================================================
  Logger.log('AI-SNS連携', 'Web同期完了に伴い、最新のAI要約オブジェクトを回収してマルチチャネル配信を実行します。');
  
  const webhookUrl = props.getProperty("WEB_REPOSITORY_URL");

  // 各種レゴブロック関数からパーツを最速アセンブル
  const microAlert = generateMicroAlertText(targetData);
  const [macroAiJa, macroAiEn] = getMacroAiCommentsAsArray();
  
  // 🛠️ LEGO-3: 完全外部関数化された汎用メッセージビルダーで配信文章を構築
  const dispatchMessage = generateEngineSnsMessage(microAlert, macroAiJa, macroAiEn, webhookUrl);
  
  // 👑 LEGO-1: 100%存在が保証されているパブリッシュメソッドを爆速ダイレクトキック！
  sendSNSPostMessage(dispatchMessage);
  Logger.log('AI-SNS連携', '[SUCCESS] ミクロ速報とマクロAI分析を融合した日英両対応メッセージの配信を完了しました。');
}

/**
 * 👑 LEGO-3 (Context) : 単発の新着イベントファクトから、通知用のカスタム日英テキストを動的生成する関数
 */
function generateMicroAlertText(dataNode) {
  const result = { ja: "", en: "" };
  if (!dataNode) return result;

  const titleJa = dataNode.titleJa || "未定義イベント";
  const titleEn = dataNode.titleEn || "Undefined Event";
  const val = dataNode.value || "0";

  // 🇯🇵 日本語コンテキスト文の構築
  let jaText = `【TR-ENGINE 新着検知】対象:「${titleJa}」/ 検出値:【${val}】です。`;
  if (parseInt(val) >= 4) {
    jaText += ` 閾値を超えた重要な変動が観測されています。システムログを最優先で確認してください。🚨`;
  } else {
    jaText += ` 定常変動の範囲内ですが、システムは正常にこれをラッチ・永続化しています。`;
  }

  // 🇺🇸 英語コンテキスト文の構築
  let enText = `[TR-ENGINE Event Logged] Target: "${titleEn}" / Value: [${val}].`;
  if (parseInt(val) >= 4) {
    enText += ` Critical variance observed. Please audit system matrices immediately. 🚨`;
  } else {
    enText += ` Minor variance. Executed normal data-latching.`;
  }

  result.ja = jaText;
  result.en = enText;
  return result;
}

/**
 * 👑 LEGO-3 (Context) : ミクロ速報文とマクロAIサマリーを融合し、ハイブリッド配信メッセージを構築する関数
 */
function generateEngineSnsMessage(microAlert, macroAiJa, macroAiEn, dashboardUrl) {
  let msg = `🚨【TR-AI Pipeline Realtime Distribution】\n\n`;
  
  if (microAlert && microAlert.ja) msg += `🇯🇵 ${microAlert.ja}\n`;
  if (macroAiJa) msg += `💡 [AI Macro Analysis]: ${macroAiJa}\n`;
  
  msg += `\n`;
  
  if (microAlert && microAlert.en) msg += `🇺🇸 ${microAlert.en}\n`;
  if (macroAiEn) msg += `💡 [AI Summary Matrix]: ${macroAiEn}\n`;
  
  msg += 
    `\n` +
    `⌘ System Processing Complete 👋\n` +
    `#TREngine #TRAI #AutonomousAPI #CHOIIZUKA #TruthScience #DataMatrix\n` +
    `🌐 Control Console 👇\n${dashboardUrl || "https://choiizuka.com"}\n\n`;
    
  return msg;
}

/**
 * 🌐 LEGO-1 (Low-Protocol) : 外部Webリポジトリから最新のAI解析JSON（マクロ）を回収する低層関数
 */
function fetchMacroAiAnalysisJson() {
  const props = PropertiesService.getScriptProperties();
  const apiUrl = props.getProperty("WEB_REPOSITORY_URL");
  if (!apiUrl) return null;
  try {
    const res = UrlFetchApp.fetch(apiUrl, { "method": "get", "muteHttpExceptions": true });
    if (res.getResponseCode() !== 200) return null;
    return JSON.parse(res.getContentText());
  } catch (error) {
    Logger.log('データフェッチ', '[CRITICAL ERROR] ' + error.toString());
    return null;
  }
}

/**
 * 📦 LEGO-0 (Primitive) : 【ラッパー配列版】マクロJSONから日英のサマリーテキストのみを安全に抽出
 */
function getMacroAiCommentsAsArray() {
  const defaultResult = ["", ""];
  const aiData = fetchMacroAiAnalysisJson();
  if (!aiData || aiData.status === "pending") return defaultResult;
  return [
    aiData.ja ? String(aiData.ja).trim() : "",
    aiData.en ? String(aiData.en).trim() : ""
  ];
}

/**
 * 📧 LEGO-2 (Mid-Business) : 抽象化マルチチャネル通知配信パイプライン
 */
function executeEngineNotificationPipeline_(dataNode, microJa, microEn, macroJa, macroEn) {
  if (typeof sendSystemNotification_ !== 'function') return;
  
  const eventId = dataNode.id || 'UNKNOWN_ID';
  const emailSubject = `🚨 [TR-AI ALERT] Core Activity Logged: ID-${eventId}`;
  
  let emailBody = `👑 Admin-Rex:\n\n`;
  emailBody += `TR-Engine Core が自律パトロール中に新規データノードをラッチしました。\n\n`;
  
  emailBody += `📢 [MICRO REALTIME CONTEXT]\n`;
  emailBody += `🇯🇵 ${microJa || 'N/A'}\n`;
  emailBody += `🇺🇸 ${microEn || 'N/A'}\n\n`;

  if (macroJa || macroEn) {
    emailBody += `🤖 [MACRO TREND ANALYSIS (STREAM INTEGRATION)]\n`;
    if (macroJa) emailBody += `・JA: ${macroJa}\n`;
    if (macroEn) emailBody += `・EN: ${macroEn}\n\n`;
  }

  emailBody += `URL: https://choiizuka.com\n\n`;
  emailBody += `⌘ SYSTEM_ONLINE // @CHOIIZUKA.COM`;

  try {
    sendSystemNotification_(emailSubject, emailBody);
  } catch(e) {
    Logger.log('エラー', `通知パイプライン実行中に例外が発生しました: ` + e.message);
  }
}



🤖 第4章：対AI専用・開発命令プロトコル（AI-System Command）
すべてのAI（Claude, GPT, Muse等）は、本仕様書および抽象化されたエビデンスコードを読み込んだ後、新しい機能開発やリファクタリングを命じられた場合、必ず以下のチェックリストを思考エンジン内で100%通過させてから回答を出力しなければならない。
新しい処理を追加する際、既存の LEGO-1（通信層）や LEGO-0（データ展開層）などの低層ブロックをそのまま再利用したか？（重複コードを新規に記述することはバグとみなす）
ユーザーに依頼された範囲以外のUI、ロジック、あるいはコメント、冒頭ライセンス、既存の定数を1文字でも変更・削除・省略していないか？（非破壊開発の徹底）
設定値や環境変数をコード内に直書きしていないか？必ず最上部の PROP や MSG オブジェクトにカプセル化したか？
「ミクロ（単発の瞬間ファクト）」と「マクロ（蓄積された全体傾向のサマリー）」のコンテキストを正しく分離してデータ設計を行ったか？

(C)CHOIIZUKA. Truth-Science&AI-Team Legion.
