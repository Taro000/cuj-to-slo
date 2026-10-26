# Glossary

Use these terms for the working language. For a language not listed, or a term missing from the list, choose one translation the first time you need it, prefer the plain everyday word, and use it everywhere after that, in every stage.

## Chain and business terms

| English | 日本語 |
|---|---|
| KGI (measure of the business purpose) | KGI |
| business requirements | 事業の要求 |
| intermediate outcome | 中間成果 |
| maintain / eliminate / decrease / increase / create | 保つ / なくす / 減らす / 増やす / 生み出す（期間や時間を減らすときは「短くする」） |
| means KPI | 手段の KPI |
| stakeholder | 利害関係者 |
| user | 利用者 |
| primary user | 一次利用者 |
| secondary user (operation): administrators, security administrators, people who enter data | 二次利用者（運用）: システム管理者、セキュリティ管理者、データを入れる人 |
| secondary user (maintenance): people who maintain, analyze failures, migrate or install | 二次利用者（保守）: 保守・改修する人、障害を解析する人、移行・導入する人 |
| indirect user | 間接利用者 |
| data producer / data consumer / both / none | データを作る人 / データを使う人 / 両方 / なし |
| considered / excluded | 考慮する / 考慮しない |
| CUJ (critical user journey) | CUJ |
| context of use | 利用状況 |
| need | ニーズ |
| quality-in-use requirement | 利用時の品質要求 |
| quality-in-use measure | 利用時の品質の測定量 |
| numerator / denominator | 分子 / 分母 |
| narrowed / overall | 絞り込み / 全体 |
| overlap | 重なり |
| task completion rate | 作業完了率 |
| not targeted | 対象にしない |
| failure scenario | 失敗のシナリオ |
| threshold | しきい値 |
| placeholder (for a number not yet decided) | 未定の値 |
| hints for later stages | 後の段階へのヒント |
| operational facts | 運用の事実 |
| impact / likelihood / score | 影響 / 起こりやすさ / 点 |
| emphasized | 重視 |
| minimum line | 最低限守る線 |
| excluded (this cycle) | 今期は対象外 |
| requirements that do not come from users (laws, standards and guidelines, security risk analysis, development policies) | 利用者を経由しない要求（法令、標準・指針、セキュリティのリスク分析、開発方針・制約） |
| entry point (where such a requirement comes in) | 入口 |
| conflict (between quality requirements) | 競合 |
| supporting function | 品質を支える機能 |
| function proposal | 機能の提案 |
| proposal status: proposed / accepted / rejected | 提案の状態: 提案中 / 受け入れ / 受け入れない |
| functional requirement | 機能要件 |
| origin: cuj-step / quality-in-use / proposal | 出どころ: CUJ の手順 / 利用時の品質要求 / 機能の提案 |
| existing / new / extension | 既存 / 新規 / 拡張 |
| quality hints | 品質のヒント |
| quality-in-use coverage | 利用時の品質要求の対応 |
| functions considered and not added | 検討して加えなかった機能 |
| untraced functions | たどれない機能 |
| interface | インタフェース |
| acceptance criteria | 受け入れ基準 |
| error path | 異常系 |
| quality requirement | 品質要求 |
| non-functional requirement | 非機能要件 |
| Definition of Done | 完成の定義 |
| CI gate | CI のゲート |
| good events / valid events | 良いイベント / 有効なイベント |
| kinds of NFR: slo / gate / audit / monitor | 非機能要件の種類: SLO / ゲート / 監査 / 監視 |
| release check | リリース前の確認 |
| SLI definition | SLI 定義書 |
| measurement point | 測定点 |
| measurement method | 測定方法 |
| reshaping rules | 測定量を整える規則 |
| two-person test | 二人で数えて同じ値になるかの確認 |
| counting rule | 数え方の規則 |
| blind spot | 見えないもの（測定の制約） |
| scale type: nominal / ordinal / interval / ratio | 尺度の型: 名義 / 順序 / 間隔 / 比尺度 |
| SLI / SLO / SLA | SLI / SLO / SLA |
| window (rolling) | ウィンドウ（直近 28 日など） |
| error budget | エラーバジェット |
| budget volume / full-outage equivalent | 許容される失敗の件数 / 全停止に換算した時間 |
| error budget policy | エラーバジェット方針 |
| burn rate | バーンレート |
| long window / short window (alerts) | 長いウィンドウ / 短いウィンドウ |
| page / ticket | 呼び出し / チケット |
| progress alert (before a deadline) | 進み具合のアラート |
| traceability | トレーサビリティ |
| provisional target | 仮の目標値 |
| needs agreement | 要合意 |
| approver candidates | 承認者の候補 |
| open gaps | 未解決の点 |
| out of scope (cannot be met by software) | 範囲外 |
| basis: stated / inferred / confirmed | 根拠: 入力 / 推定 / 確認済み |
| status: draft / agreed / retired | 状態: 下書き / 合意済み / 廃止 |
| report status: draft / answers applied | 報告書の状態: 下書き / 回答反映済み |

## Quality models

| English | 日本語 |
|---|---|
| quality in use | 利用時の品質 |
| product quality | 製品の品質 |
| data quality | データの品質 |
| characteristic / sub-characteristic | 特性 / 副特性 |
| external quality / internal quality | 外部品質 / 内部品質 |
| inherent / system-dependent (data) | 固有 / システム依存 |

### Quality in use

| English | 日本語 |
|---|---|
| effectiveness | 有効性 |
| efficiency | 効率性 |
| satisfaction: usefulness, trust, pleasure, comfort | 満足性: 実用性、信用性、快感性、快適性 |
| freedom from risk: economic risk mitigation, health and safety risk mitigation, environmental risk mitigation | リスク回避性: 経済リスク緩和性、健康・安全リスク緩和性、環境リスク緩和性 |
| context coverage: context completeness, flexibility | 利用状況網羅性: 利用状況完全性、柔軟性 |

### Product quality

| English | 日本語 |
|---|---|
| functional suitability: functional completeness, functional correctness, functional appropriateness | 機能適合性: 機能完全性、機能正確性、機能適切性 |
| performance efficiency: time behaviour, resource utilization, capacity | 性能効率性: 時間効率性、資源効率性、容量満足性 |
| compatibility: co-existence, interoperability | 互換性: 共存性、相互運用性 |
| usability: appropriateness recognizability, learnability, operability, user error protection, user interface aesthetics, accessibility | 使用性: 適切度認識性、習得性、運用操作性、ユーザエラー防止性、ユーザインタフェース快美性、アクセシビリティ |
| reliability: maturity, availability, fault tolerance, recoverability | 信頼性: 成熟性、可用性、障害許容性、回復性 |
| security: confidentiality, integrity, non-repudiation, accountability, authenticity | セキュリティ: 機密性、インテグリティ、否認防止性、責任追跡性、真正性 |
| maintainability: modularity, reusability, analysability, modifiability, testability | 保守性: モジュール性、再利用性、解析性、修正性、試験性 |
| portability: adaptability, installability, replaceability | 移植性: 適応性、設置性、置換性 |

### Data quality

| English | 日本語 |
|---|---|
| inherent only: accuracy, completeness, consistency, credibility, currentness | 固有のみ: 正確性、完全性、一貫性、信ぴょう性、最新性 |
| both viewpoints: accessibility, compliance, confidentiality, efficiency, precision, traceability, understandability | 両方: アクセシビリティ、標準適合性、機密性、効率性、精度、追跡可能性、理解性 |
| system-dependent only: availability, portability, recoverability | システム依存のみ: 可用性、移植性、回復性 |

Data "completeness" means no missing values. It is not the security term "integrity" (インテグリティ).
