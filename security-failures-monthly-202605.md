# セキュリティ失敗事例レポート - 2026年5月

**作成日**: 2026年05月17日
**対象期間**: 2026年04月〜2026年05月（過去1ヶ月）
**リサーチ対象**: 公開Webページのみ（ログイン不要）

---

## 1. APIキー漏洩の新事例

### 事例1-1: Grafana - GitHubトークン漏洩による恐喝未遂

- **日付**: 2026年5月17日（公開）
- **概要**: Grafanaは不正アクターがGitHubリポジトリへのアクセストークンを入手し、同社のGitHub環境に侵入してソースコード全体をダウンロードしたことを公表。攻撃者はCoinbaseCartelと名乗るグループ（ShinyHunters/LAPSUS$の派生グループ）で、身代金を要求したが、Grafana側はFBIの勧告に従い支払いを拒否した。
- **影響**: ソースコード全体の流出（顧客データ・本番システムへの影響なし）
- **教訓**: GitHubトークンの厳格な管理と最小権限の原則が重要。トークン漏洩は直接コードベースへのアクセスを許す
- **出典**: https://thehackernews.com/2026/05/grafana-github-token-breach-led-to.html

### 事例1-2: Braintrust - AWS APIキー漏洩で全顧客にキーローテーション要求

- **日付**: 2026年5月6日
- **概要**: AIモデル評価スタートアップBraintrustがAWSクラウドアカウントへの不正アクセスを確認。侵害されたAWSアカウントには顧客がクラウドAIモデルへのアクセスに使用するAPIキーが含まれていた。Braintrustは全顧客にAPIキーのローテーションを要求した。
- **影響**: 顧客のAPIキーが漏洩リスクにさらされた
- **教訓**: サードパーティサービスに保管しているAPIキーも漏洩リスクがある。定期的なキーローテーションが重要
- **出典**: https://techcrunch.com/2026/05/06/ai-evaluation-startup-braintrust-confirms-breach-tells-every-customer-to-rotate-sensitive-keys/

### 事例1-3: OpenAI - TanStack/Shai-Hulud サプライチェーン攻撃でAPIトークン漏洩

- **日付**: 2026年5月12〜14日
- **概要**: TeamPCPによる「Mini Shai-Hulud」サプライチェーン攻撃により400以上のnpm/PyPIパッケージが汚染。OpenAIでは2名の従業員デバイスが侵害され、GitHubトークン・npmトークン・AWS認証情報などが窃取された。コード署名証明書も漏洩リスクにさらされ全面ローテーション。macOSユーザーは2026年6月12日までにアプリ更新が必要。
- **影響**: 従業員デバイスの侵害、ソースコードへの限定アクセス、コード署名証明書の失効
- **教訓**: CI/CDパイプラインのGitHub Actionsワークフローのスコープを最小化し、パッケージ整合性を検証することが重要
- **出典**: https://www.bleepingcomputer.com/news/security/openai-confirms-security-breach-in-tanstack-supply-chain-attack/

### 事例1-4: Trellix - ソースコードリポジトリへの不正アクセス

- **日付**: 2026年5月2日
- **概要**: セキュリティ企業Trellixがソースコードリポジトリへの不正アクセスを公表。「ソースコードの一部」へのアクセスが発生。侵害の詳細は法医学調査中として未公開。TrellixはMcAfee EnterpriseとFireEyeの合併により設立されたセキュリティ大手。
- **影響**: ソースコードの一部が不正アクセスされた（顧客データへの影響なし）
- **教訓**: セキュリティ企業もソースコードリポジトリへの不正アクセスに対して脆弱である
- **出典**: https://thehackernews.com/2026/05/trellix-confirms-source-code-breach.html

---

## 2. SNS自動化によるアカウント凍結の新事例

### 事例2-1: node-ipc npmパッケージを通じたSNS APIトークン窃取

- **日付**: 2026年5月15日
- **概要**: 人気npmパッケージ「node-ipc」（週間69万DL）に悪意あるコードが注入され、インストール時に自動実行。.envファイル、SSH鍵、GitHub/GitLab/npm/Gitトークン等を広範に窃取した。SNS APIトークンを含む認証情報が標的。窃取されたSNS APIキーが悪用されると、スパム行為がプラットフォームに検知されアカウント凍結につながる可能性がある。
- **影響**: 69万DL以上の開発者環境が潜在的に影響を受けた
- **教訓**: npmパッケージのサプライチェーン攻撃はSNS APIトークンを含む全認証情報を標的にしている
- **出典**: https://www.bleepingcomputer.com/news/security/popular-node-ipc-npm-package-compromised-to-steal-credentials/

### 事例2-2: Shai-Hulud攻撃 - CI/CDトークン窃取によるSNS認証情報の露出

- **日付**: 2026年5月12日
- **概要**: TeamPCPによるShai-Hulud攻撃では、GitHub Actions OIDCトークンを悪用してCI/CDパイプラインから認証情報を窃取。マーケティング自動化ツールやSNS管理ツールのAPIキーが含まれる.envファイルが標的となった。攻撃者がSNS APIキーを入手すると、自動投稿ツールとして悪用しスパム行為によるアカウント凍結が発生するリスクがある。
- **影響**: 400以上のパッケージが侵害。開発者のSNS API認証情報が漏洩した可能性
- **教訓**: CI/CDシークレットにSNS APIキーを保存する場合は適切なシークレット管理サービスを使用すること
- **出典**: https://www.bleepingcomputer.com/news/security/shai-hulud-attack-ships-signed-malicious-tanstack-mistral-npm-packages/

### 注記

今月のSNS自動化によるアカウント凍結については、セキュリティ専門メディア（BleepingComputer、The Hacker News等）での単独報告事例は確認できなかった。上記事例はSNS APIキー漏洩から凍結につながるリスクを示す関連事例として記録した。

---

## 3. .envファイル誤公開の新事例

### 事例3-1: Tabiq（ホテルチェックインシステム）- AWS S3バケット公開設定ミス

- **日付**: 2026年5月15日（TechCrunch報告）
- **概要**: 日本スタートアップReqrea社のホテルチェックインシステム「Tabiq」がAWS S3バケットをパブリック設定にしており、100万件以上の顧客パスポート・運転免許証・顔写真が誰でもアクセス可能な状態に。バケット名「tabiq」を知るだけでブラウザからアクセス可能だった。デフォルトでプライベートのはずのAWS S3バケットが誤ってパブリックに設定されていた。
- **影響**: 2020年〜2026年5月までの宿泊客の個人情報100万件以上が公開状態に
- **教訓**: AWS S3バケットの公開設定は明示的な理由がない限り行ってはならない。クラウドストレージの設定を定期的に監査すること
- **出典**: https://techcrunch.com/2026/05/15/a-hotel-check-in-system-left-a-million-passports-and-drivers-licenses-open-for-anyone-to-see/

### 事例3-2: Shai-Hulud攻撃 - .envファイルの大規模窃取

- **日付**: 2026年5月12日
- **概要**: npm/PyPIパッケージへの悪意ある注入を通じて.envファイルが大規模に標的となった。マルウェアはインストール時に自動実行し、プロジェクト内の.envファイルを検索・圧縮・外部送信する。クラウドプロバイダー認証情報（AWS、Azure、GCP等）、DB接続情報、各種APIキーが含まれる.envファイルが標的。なお、攻撃はSLSA Build Level 3の正当な署名を持つパッケージを通じて行われたため通常のチェックでは検知困難だった。
- **影響**: 400以上のパッケージを使用する開発者の.envファイルが標的。数万の開発環境に影響した可能性
- **教訓**: .envファイルはGit管理外にすること（.gitignoreに追加）。CI/CDではシークレット管理サービスを使用すること
- **出典**: https://www.bleepingcomputer.com/news/security/shai-hulud-attack-ships-signed-malicious-tanstack-mistral-npm-packages/

### 事例3-3: node-ipc - .envファイルを明示的に標的とするインフォスティーラー

- **日付**: 2026年5月15日
- **概要**: 侵害されたnode-ipcパッケージは.envファイルとDB認証情報を明示的に標的としていた。AWS、Azure、GCP、DigitalOcean等のクラウド認証情報、SSH鍵、Kubernetesシークレット、npm/GitHub/GitLabトークン、.envファイルをDNS TXTクエリで外部送信（通常のHTTP通信でないため検知が困難）。
- **影響**: 週間69万DLのパッケージを通じて.envファイルが窃取されるリスク
- **教訓**: 人気パッケージのアップデートも必ず内容を確認すること。メンテナーアカウント乗っ取りによるサプライチェーン攻撃に注意
- **出典**: https://www.bleepingcomputer.com/news/security/popular-node-ipc-npm-package-compromised-to-steal-credentials/

---

## まとめと傾向分析

### 今月の主要トレンド

1. **ソフトウェアサプライチェーン攻撃の激化**: TeamPCPグループによるnpm/PyPIパッケージへの組織的な攻撃が拡大。400以上のパッケージが侵害され、.envファイルやAPIキーが大規模に標的となった。
2. **AI・開発ツール企業が主要標的**: OpenAI、Mistral AI、BraintrustなどのAI企業が攻撃を受け、APIキーや認証情報が漏洩。
3. **恐喝型攻撃（データ窃取後の身代金要求）の増加**: Grafanaへの恐喝未遂、Mistral AIリポジトリの販売試みなど、コードを盗んで身代金を要求するパターンが増加。
4. **クラウド設定ミスによる露出の継続**: AWSストレージバケットの設定ミスによる個人情報漏洩は依然として発生している。

### 推奨される対策

- GitHubトークン・APIキーは定期的にローテーションし、最小権限を設定する
- .envファイルは必ず.gitignoreに追加し、シークレット管理サービス（AWS Secrets Manager等）を使用する
- npmパッケージのインストール前に整合性を確認する（npm audit、lockfile確認等）
- CI/CDパイプラインのGitHub Actionsワークフローのスコープを最小化し、OIDC設定を適切に行う
- クラウドストレージバケットの公開設定は定期的に監査する（AWS Config等を活用）

---

*このレポートは公開Webページのみを参照しており、ログイン不要のソースのみを使用しています*
*参照元: The Hacker News、BleepingComputer、TechCrunch（2026年4月〜5月の記事）*
