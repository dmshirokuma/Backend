# アーキテクチャ比較（Monolith、MicroServices）

## Monolith

全ての機能が１アプリケーションに集約される伝統的なアプローチ

- メリット
  - 小規模なチームやアプリケーション開発に向く
  - デプロイが簡単
  - 機能同士のネットワークレイテンシがないため、パフォーマンスがよい
- デメリット
  - 新しいテクノロジーを採用することが困難
    - 新しいフレームワークや技術を使用してアプリケーションを強化するなど
    - 新技術を導入する際は基本的にコードベース全体を更新する必要がある=古い技術を使い続けなければならない
  - 特定の機能のみのスケールが困難
  - 可能性の問題
  - 小さな変更でも全体の再デプロイが必要

アプリケーションが成長した場合、以下の問題が発生する可能性が高い

- アプリケーションの複雑度が日々高まり、全体を理解できる人がいない
- 少しの影響が大部分への影響となるため、修正に対して非常に神経を使う
- 新技術の導入が困難
- 少しの修正であっても全体をデプロイする必要がある
- 一つのミスで全体がクラッシュする
- リグレッションテストなどに多くのコストが必要

## MicroServices

UIとは別に各サービス層をそれぞれ分割されたコンテナにデプロイするパターン

- メリット
  - サービス単位の開発、テスト、デプロイが容易
  - サービス間が完全に疎結合になる
    - 各チームがそれぞれの方法で進めることが可能
  - 特定のサービスだけをスケールさせることが可能
- デメリット
  - デプロイの複雑化
  - コンテナやサービスを複数起動する必要があるため、インフラのオーバーヘッドの懸念がある
  - ネットワーク経由でサービス間の呼び出しが行われるため、セキュリティの懸念がある

## MicroServicesの境界をどのように設定するか？

- DomeinDrivenによるサイジング
  - そのアプリケーションに対するドメイン（ビジネス）境界をもってサイジングを行う
    - 銀行アプリケーションであれば以下のようなイメージ
      - アカウント
      - カード
      - ローン
  - 各ドメインに対して専門的な知識を有する人（ドメインエキスパート）およびビジネス関係者、技術者、クライアント関係者から情報を得た上で行うべき
    - 各ドメインの業務
    - チームの規模
    - 既存アプリケーション情報
- イベントストーミングサイジング(<https://www.lucidchart.com/blog/ddd-event-storming>)
  - 以下の手順を踏む
    - 適切な人を招待する
      - テスター
      - マネージャ
      - アーキテクト
      - ビジネスリーダー
    - アプリケーション内で発生するイベントを全て質問する
      - 重複したものについてはファシリテータが削除すればOK
    - ビジネスドメインを探索する
      - 各ドメインのイベント（プロセスまたはシステム内で起きること）を特定する
        - 常に過去形であることを確認する（未来のことは追加改修で対応）
      - イベントの原因と結果を特定する
        - イベントトリガー（例えば以下）
          - ユーザー
          - 他のイベントから発生
          - 外部システム
        - イベントの反応
          - イベントの結果として何が起こるか
            - 例えば以下
              - アカウントが作成される
              - アカウント作成後にメールが送信される
    - 各イベントをグループ化（境界コンテキスト）する
      - 同様の機能を多数保持するものはグループ可する

## 12 & 15 Factors & Beyond

Heroku Cloud Platformのエンジニアチームによって定められたクラウドネイティブアプリケーション構築における12のガイドライン。  
後に上記に加えて3つの原則を追加した15のガイドラインが現在では広く使われている。
「Beyond the Twelve-Factor App」

- One codebase, one application
  - 1つのマイクロサービスアプリケーションが1つのコード（Git Repository）を保持すること
    - 1つのコードベースに対して「開発」「ステージング」「本番」が用意される
    - ただし、多くのアプリケーションで共有するコードについてはまた別の1コードベースとして管理される（つまりスタンドアロンサービスのようなイメージで使われる）
  - 禁止
    - 1アプリケーションが複数のコードベースから構成される
- API first
  - アプリケーションのすべての機能要件は可能な限りAPIの使用（組み合わせ）を通じて満たされるべき
    - つまりアプリケーションにおける最も重要な成果物はAPIという考え方
    - Webやモバイルは単なるAPIのコンシューマとして捉え、APIを先に設計する
- Dependency management
  - アプリケーションの依存するモジュールは自身で管理せず、依存性管理ツールを使用すること
    - JavaならMaven, Gradle
    - C#ならNuget
  - マイクロサービスの数が増えると手動でセットアップは現実的ではないため
- Design, build, release, run
  - 全てのステージが完全に分離されていること
  - 上記に対するCI/CDを構築すること
  - Design
    - 後続のステップで必要なテクノロジー、依存関係、ツールを全て決定する
    - 全て決定後、設計、開発、テストを行う
    - 完了後buildステージに進む
  - Build
    - 必要な全ての依存関係を含むコードベースをコンパイルしてパッケージ化する
    - 全てのビルドに一意なアーティファクトIDが振られる
    - CIによって生成されることが推奨される
    - 完了後Releaseステージに進む
  - Release
    - コードベースパッケージとデプロイメント構成を組み合わせる
    - 全てのコードベースアーティファクトを保存する（特定の地点へ戻れるように）
    - 完了後Runステージに進む
  - Run
    - 指定された環境でアプリケーションを実行する
    - CDによって行われることが推奨される
- Configration, credentials & code
  - 環境によって異なる値（アプリケーション設定や構成など）はコードから切り離されていること
    - WebサービスやSMTPサーバーなどのURL
    - データベース接続情報
    - APIへの認証情報
    - プロパティファイル、構成XML
  - 基本的には環境変数によって機密情報が提供されること
  - コードベースをそのままオープンソース化して問題ないかを考えること
- Logs
  - 全てのログは時間とともに流れるイベントストリームと考えること
  - アプリケーションから見てログの保管場所を気にしないこと
    - ログは標準出力へ出力する
  - ログの集約、処理、保管、分析はログアグリゲータに移譲する
    - ELKスタック（ElasticSearch、Logstash、Kibana）
    - Splunk
    - Sumologic
- Disposability
  - アプリケーションを素早く起動、停止することができること
    - これにより、システムの堅牢性と回復性を確保する
    - 停止はもちろんハードシャットダウンではなくグレースフルなシャットダウン
  - アプリケーションが応答しなくなった場合、終了して置き換えることが可能であること
    - 高負荷時にkubernetesなどのプラットフォームによって新しいインスタンスが自動的に作成される
  - 起動時に発生するキャッシングやフェッチの処理についてはバッキングサービスに外部化してフロントロード不要とすること
- Baking Service
  - アプリケーションが依存するサービス
    - データストア（DB, S3など）
    - メッセージングシステム（Kafkaなど）
    - キャッシュ（Redisなど）
    - メール（SESなど）
    - 基盤業務機能
    - セキュリティ実行サービス（OAuth2など）
  - これらは全て
  - 外部サービスは全て抽象化された状態でアクセスが行われること
- Environment Parity
  - アプリケーションがどのような環境でも動作するように構成されていること
    - 本番では動かないが、QA環境では動くといった事態を避ける
  - 一般的には以下に注意すること
    - Time
      - 開発者がコードチェックインしてから本番環境に到達するまでに数週間、数ヶ月かかるといったことがないようにする
        - どのような変更が行われたのかを忘れる
      - CI/CDといったパイプラインによって自動化を行い、継続的にデプロイメントされることを目指す
        - これによって環境が常に同じであるように見えるようになる
    - People
      - デプロイメントは人の手によって行われるべきではなく、１ボタンや特定のイベントに反応してデプロイされるべき
        - ローカル環境については自由
    - Resource
      - 開発を行う環境は全て本番環境相当のサービスを導入すること
        - 本番環境ではPostgreSQLを使用するのにローカル環境ではH2を使用するといったことが無いようにする
        - 導入、設定が困難であるサービスについてはDockerなどを使用して簡単に構築可能とすること
- Alternative Processes
  - アプリケーションをサポートするために必要な以下のような管理タスクは一つのパッケージとしてまとめられる必要がある
    - データ移行
    - データクリーンアップ
    - データ更新バッチジョブ
  - Cronやタイムスケジューラで起動されるようなものはマイクロサービスでは弊害を引き起こす可能性が高い
  - これらの管理タスクはアプリケーションのビジネスロジックに組み込むのではなく、独立したマイクロサービスとして構築することを考慮する
  - 他には上記のようなジョブに関しては起動するためのスイッチをRESTAPIに用意するといった方法もある
- Port binding
  - 1つのコンテナ上で複数のアプリを起動しないこと
  - 外部プロバイダからのポート接続によってアプリに接続できること
    - つまりコード変更せずに外部から接続可能であること
  - アプリケーションには内部のサーバ（Tomcatなど）に依存するようなコードを記載しないこと
    - 依存するようなコードを記述すると手動で管理する必要が出てくるため
- Stateless processes
  - アプリケーション内には基本的にステータスを保持しないこと
    - 長期的に保持するステータスがある場合はバッキングサービスにてステータスを保持すること
      - RDB
      - MongoDB
      - Redis
  - アプリケーション内にステータスを保持する=アプリケーション内にキャッシュするということなのでDisposabilityの面からも反する構成となる
  - アプリ内キャッシュを使用してしまうとスケールすることによってさらに多くのメモリを使用することになってしまう
- Concurrency
  - スケールアップではなく、スケールアウトを使用すること
    - スケールアップ
      - 限界点への到達が早い
      - 負荷分散が使えない
    - スケールアウト
      - 限界点無し
  - リクエストを同時処理させる必要がある
- Telemetry
  - クラウドアプリケーションの監視は主に以下の3つに対して行われる
    - アプリケーションパフォーマンス監視（APM）
    - ドメイン固有のテレメトリ
    - ヘルスログとシステムログ
  - これらのテレメトリの情報は一つのコンポーネントに対して集約されること
  - アプリケーションパフォーマンス監視（APM）
    - クラウドの外部ツールで監視する
    - パフォーマンス定義は開発者、管理者によって決められる
    - 複数アプリケーションから取得されることもある
    - 例えばアプリケーションが処理している1秒あたりの平均HTTPリクエスト数など
  - ドメイン固有のテレメトリ
    - ビジネス上で意味のあるイベントとデータを収集する
    - 多くの場合、保管、分析、予測のためにビッグデータシステムに渡される
    - 例えば過去20分間にiPadを使用しているユーザーに販売されたウィジェットの数など
  - ヘルスログとシステムログ
    - クラウドプロバイダーによって提供される
    - 例えばアプリケーションの起動、シャットダウン、スケーリング、Webリクエストのトレース、ヘルスチェックなど
  - 監視の設計をする場合には以下を考慮する必要がある
    - 情報が入ってくる速度
    - どれだけの情報をどの期間保存するか
    - 1インスタンスから100インスタンスにスケールされた場合に問題ないかなど
- Authentication and authorization
  - セキュリティに関する事項は全てのアプリケーションに対して重要な部分であるため、優先的に取り組むこと
    - 全てのリクエストでは以下が明確である必要がある
      - 誰がそのリクエストを送信しているか
      - 利用者がどのロールに属しているか
  - 理想的には全てロールベースのアクセス制御にて保護すること