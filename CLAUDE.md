## 🎯 使命

現在開催されている「建設業向け事業提案コンペティション」に提出するため、対象企業の将来に向けた成長戦略を実現する事業計画提案書を作成します（文字数は10,000字程度に収める）。
全ての評価基準において高いレベルを達成し、コンペにおいて1位獲得が至上命題です。

---

## コンペにおける評価基準
以下の5つの視点に基づき、A（優れている）、B（標準的）、C（改善が必要）の3段階で評価します。

| 評価視点 | 評価項目 | 評価のポイント（高評価の基準） |
| :--- | :--- | :--- |
| 1. 全体構成 | 過去分析と未来提案の接続 | 過去3年の財務・事業分析（Past）と、未来への成長戦略（Future）が一貫した因果関係で論理的に接続されているか。 |
| 2. 地域性 | 地域特性の考慮 | 企業の所在地（商圏、人口動態、行政施策）を踏まえ、画一的ではない地域密着型の提案ができているか。 |
| 3. 業界特性 | 販路・商流の理解 | 官公庁／民間、元請／下請などの販路特性を把握し、それに応じた具体的な分析・提案となっているか。 |
| 4. 業界課題1 | 近未来への対応 (GX/DX) | 低コスト工法、環境技術（GX）、省力化技術（DX）など、技術トレンドへの投資や対応策を提案できているか。 |
| 5. 業界課題2 | 需要減退・人材不足対応 | 工事需要の変化や深刻な人手不足（2024年問題、外国人材受入等）に対し、実効性のある解決策（歩掛管理の高度化、採用・定着策等）を示せているか。 |

---

## サブエージェントの実行順
以下の順番で起動しタスク実行すること。前のサブエージェントのタスクが完了した後に次のサブエージェントを実行開始する。

|順番| サブエージェント |
|-|-------------|
|0| planning-agent |
|1| phase1-information |
|2| phase2-external-analysis |
|3| phase3-internal-analysis |
|4| phase4-interview |
|5| phase5-integrated-analysis |
|6| phase6-growth-strategy |
|7-1| past-future-connector |
|7-2| regional-analysis-expert |
|7-3| construction-industry-channel-analyst |
|7-4| gx-dx-strategy-advisor |
|7-5| construction-workforce-demand-analyst |
|7-6| phase7-documentation-1 |
|8| phase8-feedback |
|9| phase9-documentation-2 |
|10| proposal-reviewer |

### データパス構成

```
入力:
  - 有価証券報告書: data/securities_report/有価証券報告書（[企業コード番号]）.pdf
  - 財務データ: data/financial_data.csv（コードが該当の企業コード番号に一致）

出力先: output/[企業コード番号]/配下に保存する
  最終提案書: [企業コード番号].md
  実行ログ: execution_log.md（orchestrator／メインエージェントが生成・更新）
```

### 起動方法

#### Phase 0: 計画作成

**planning-agent エージェントを Task ツールで起動してください:**

```json
{
  "subagent_type": "planning-agent",
  "description": "Phase0計画立案",
  "prompt": "対象企業: [企業名]（コード: [企業コード番号]）\nPDFパス: [PDFパス]\n出力先: [出力先パス]\nタスク: 有価証券報告書を分析し、project_plan.md を作成してください。"
}
```

#### Phase 1: 情報収集を実行

```json
{
  "subagent_type": "phase1-information",
  "description": "Phase1情報収集",
  "prompt": "対象企業: [企業名]（コード: [企業コード番号]）\nPDFパス: [PDFパス]\n出力先: [出力先パス]\n前フェーズの成果物: [出力先]/project_plan.md\nタスク: Phase1の成果物（company_overview.md, financial_trends.md, hypothesis_map.md）を作成してください。"
}
```

#### Phase2外部分析とPhase3内部分析を並列で実行

**⚡ 重要: Phase2とPhase3は並列で起動する。同じメッセージ内で2つの Task ツールを呼び出すこと。**

Phase 2（同時起動）:
```json
{
  "subagent_type": "phase2-external-analysis",
  "description": "Phase2外部分析",
  "prompt": "対象企業: [企業名]（コード: [企業コード番号]）\nPDFパス: [PDFパス]\n出力先: [出力先パス]\n前フェーズの成果物: [出力先]/company_overview.md, [出力先]/financial_trends.md\nタスク: Phase2の成果物（external_analysis.md, pest_analysis.md）を作成してください。"
}
```

Phase 3（同時起動）:
```json
{
  "subagent_type": "phase3-internal-analysis",
  "description": "Phase3内部分析",
  "prompt": "対象企業: [企業名]（コード: [企業コード番号]）\nPDFパス: [PDFパス]\n出力先: [出力先パス]\n前フェーズの成果物: [出力先]/company_overview.md, [出力先]/financial_trends.md\nタスク: Phase3の成果物（internal_analysis.md, swot_analysis.md）を作成してください。"
}
```

両方の完了を確認してから次へ進む。

#### Phase 4: インタビューを実行

```json
{
  "subagent_type": "phase4-interview",
  "description": "Phase4インタビュー",
  "prompt": "対象企業: [企業名]（コード: [企業コード番号]）\nPDFパス: [PDFパス]\n出力先: [出力先パス]\n前フェーズの成果物: [出力先]/swot_analysis.md, [出力先]/external_analysis.md, [出力先]/internal_analysis.md\nタスク: Phase4の成果物（insights.md, interview_record.md）を作成してください。"
}
```

#### Phase 5: 統合分析を実行

```json
{
  "subagent_type": "phase5-integrated-analysis",
  "description": "Phase5統合分析",
  "prompt": "対象企業: [企業名]（コード: [企業コード番号]）\nPDFパス: [PDFパス]\n出力先: [出力先パス]\n前フェーズの成果物: [出力先]/insights.md, [出力先]/external_analysis.md, [出力先]/internal_analysis.md, [出力先]/swot_analysis.md\nタスク: Phase5の成果物（integrated_analysis.md, issue_tree.md）を作成してください。"
}
```

#### Phase 6: 成長戦略を起動

```json
{
  "subagent_type": "phase6-growth-strategy",
  "description": "Phase6成長戦略",
  "prompt": "対象企業: [企業名]（コード: [企業コード番号]）\nPDFパス: [PDFパス]\n出力先: [出力先パス]\n前フェーズの成果物: [出力先]/integrated_analysis.md, [出力先]/issue_tree.md, [出力先]/swot_analysis.md, [出力先]/external_analysis.md\nタスク: Phase6の成果物（strategy.md, marketing_strategy.md, implementation_roadmap.md）を作成してください。\n\n【品質要求】\n以下の5視点評価でA評価を目指してください:\n- 視点1（全体構成）: 過去分析との因果関係が明確な戦略\n- 視点2（地域性）: 地域特性を活かした戦略\n- 視点3（業界特性）: 販路・商流を踏まえた戦略\n- 視点4（GX/DX）: 具体的な技術投資計画（金額・時期・効果を明示）\n- 視点5（人材不足）: 2024年問題、採用・定着策、外国人材活用の具体策"
}
```

#### Phase7-1とPhase7-2とPhase7-3とPhase7-4とPhase7-5を並列で実行

**⚡ 重要: Phase7-1とPhase7-2とPhase7-3とPhase7-4とPhase7-5を同じメッセージ内で5つの Task ツールを呼び出して並列で実行すること。**

Phase7-1: 過去分析と未来提案の接続（同時起動）:
```json
{
  "subagent_type": "past-future-connector",
  "description": "過去分析と未来提案の接続",
  "prompt": "対象企業: [企業名]（コード: [企業コード番号]）\nPDFパス: [PDFパス]\n出力先: [出力先パス]\n前フェーズの成果物: [出力先]/strategy.md\nタスク: 過去分析と未来提案の接続に関するpast-future.mdを作成してください。"
}
```

Phase7-2: 地域特性の考慮（同時起動）:
```json
{
  "subagent_type": "regional-analysis-expert",
  "description": "地域特性の考慮",
  "prompt": "対象企業: [企業名]（コード: [企業コード番号]）\nPDFパス: [PDFパス]\n出力先: [出力先パス]\n前フェーズの成果物: [出力先]/strategy.md\nタスク: 地域特性の考慮に関するregional-analysis.mdを作成してください。"
}
```

Phase7-3: 販路・商流の理解（同時起動）:
```json
{
  "subagent_type": "construction-industry-channel-analyst",
  "description": "販路・商流の理解",
  "prompt": "対象企業: [企業名]（コード: [企業コード番号]）\nPDFパス: [PDFパス]\n出力先: [出力先パス]\n前フェーズの成果物: [出力先]/strategy.md\nタスク: 販路・商流の理解に関するconstruction-industry-channel.mdを作成してください。"
}
```

Phase7-4: 近未来への対応 (GX/DX)（同時起動）:
```json
{
  "subagent_type": "gx-dx-strategy-advisor",
  "description": "近未来への対応 (GX/DX)",
  "prompt": "対象企業: [企業名]（コード: [企業コード番号]）\nPDFパス: [PDFパス]\n出力先: [出力先パス]\n前フェーズの成果物: [出力先]/strategy.md\nタスク: 近未来への対応 (GX/DX)に関するgx-dx-strategy.mdを作成してください。"
}
```

Phase7-5: 需要減退・人材不足対応（同時起動）:
```json
{
  "subagent_type": "construction-workforce-demand-analyst",
  "description": "需要減退・人材不足対応",
  "prompt": "対象企業: [企業名]（コード: [企業コード番号]）\nPDFパス: [PDFパス]\n出力先: [出力先パス]\n前フェーズの成果物: [出力先]/strategy.md\nタスク: 需要減退・人材不足対応に関するconstruction-workforce-demand.mdを作成してください。"
}
```

Phase7-1とPhase7-2とPhase7-3とPhase7-4とPhase7-5の完了を確認してから次へ進む。

#### Phase 7-6: 文書化初稿を実行

```json
{
  "subagent_type": "phase7-documentation-1",
  "description": "Phase7文書化初稿",
  "prompt": "対象企業: [企業名]（コード: [企業コード番号]）\nPDFパス: [PDFパス]\n出力先: [出力先パス]\n前フェーズの成果物: [出力先]/past-future.md, [出力先]/regional-analysis.md, [出力先]/construction-industry-channel.md, [出力先]/gx-dx-strategy.md, [出力先]/construction-workforce-demand.md\nタスク: Phase7の成果物（draft_business_plan.md）を作成してください。"
}
```

#### Phase 8: フィードバックを実行

```json
{
  "subagent_type": "phase8-feedback",
  "description": "Phase8フィードバック",
  "prompt": "対象企業: [企業名]（コード: [企業コード番号]）\nPDFパス: [PDFパス]\n出力先: [出力先パス]\n前フェーズの成果物: [出力先]/draft_business_plan.md\nタスク: Phase8の成果物（feedback.md, validation_report.md）を作成してください。"
}
```

#### Phase 9: 最終版作成

```json
{
  "subagent_type": "phase9-documentation-2",
  "description": "Phase9最終版",
  "prompt": "対象企業: [企業名]（コード: [企業コード番号]）\nPDFパス: [PDFパス]\n出力先: [出力先パス]\n前フェーズの成果物: [出力先]/validation_report.md, [出力先]/draft_business_plan.md\nタスク: Phase9の成果物（[企業コード番号].md）を作成してください。"
}
```

#### Phase 10: レビュー

```json
{
  "subagent_type": "proposal-reviewer",
  "description": "Phase10レビュー",
  "prompt": "対象企業: [企業名]（コード: [企業コード番号]）\nPDFパス: [PDFパス]\n出力先: [出力先パス]\n前フェーズの成果物: [出力先]/[企業コード番号].md\nタスク: 最終提案書をレビューし、review_report.md を作成してください。5視点すべてのA/B/C評価を必ず含めてください。"
}
```


### ブラッシュアップサイクル（核心機能）
#### 完了条件
本エージェントのタスクは、proposal-reviewerの各評価項目の全てにおいて高いレベルで満点を獲得した場合のみ完了とする。獲得できない場合は 7-1のサブエージェントpast-future-connectorに戻って提案書のブラッシュアップのサイクルを回すこと。

レビュー結果に基づき改善サイクルが回ります:

```
Phase 10 レビュー完了
  ↓
メインエージェント が review_report.md を分析
  ↓
5視点すべてA？ → Yes → 完了
  ↓ No
改善が必要な視点を特定:
  ↓
Phase7-1 から始まりPhase10までのサイクルでサブエージェントを再度実行し、review_report.mdの改善指示に基づいてブラッシュアップし提案資料を修正する。Phase10: proposal-reviewer で再度レビューをし再判定する（最大5サイクル）
```

---

### Phase 10 完了後の後工程（提出物作成フロー）

proposal-reviewer で全5視点A評価を達成した後、以下の手順で最終提出物を作成する。

#### ステップ1: Wordファイル作成
- Claude Webチャット（claude.ai）に提案書テキストを入力し、Word（.docx）ファイルを生成させる

#### ステップ2: スライド画像の生成
- Google NotebookLM に提案書の内容を読み込ませ、プレゼンテーション用スライドを生成する

#### ステップ3: 最終整形・完成
- ステップ1で生成したWordファイルに、ステップ2でNotebookLMが生成したスライド画像を貼り付ける
- 人間が改行・レイアウト・体裁を整形し、最終提出物として完成させる
