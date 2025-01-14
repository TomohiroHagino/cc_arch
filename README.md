# クリーンアーキテクチャを意識したRailsのファイル構成
#### こんな感じに構成を組めたら、とてもスッキリすると思ってます
```
app/
├── domain/                      # ドメイン層（フレームワークや外部システムに依存しない純粋なRubyコードで記述）
│   ├── entities/                # エンティティ
│   │   └── user.rb
│   ├── services/                          # ドメインサービス層
│   │   ├── shipping_cost_service.rb       # ドメインサービス: 配送費計算
│   │   └── calculate_tax_service.rb       # ドメインサービス: 税計算
│   └── repositories/            # リポジトリ
│        ├── commands/
│        │    └── active_record_user_commands.rb  # 書き込み系
│        └── queries/
│             └── active_record_user_queries.rb 　# 読み取り系
│
├── use_cases/                   # ユースケース層
│   ├── users/
│   │   ├── create_user.rb           # ユースケース: ユーザー作成
│   │   ├── update_user.rb           # ユースケース: ユーザー更新
│   │   ├── delete_user.rb           # ユースケース: ユーザー削除
│   │   └── get_user.rb              # ユースケース: ユーザー取得
│   └── services/                    # サービス層 (汎用的な処理)
│　　     ├── api/
│　　     │    ├── payment_service.rb       # （モバイルの）支払いサービス
│　　     │    └── ....
│　　     ├── wapi/
│　　     │    ├── payment_service.rb       # （Webの）支払いサービス
│　　     │    └── ....
│　　     ├── admin/
│　　     │    ├── email_service.rb         # （管理者が使用するための）メール送信サービス
│　　     │    └── ....
│　　     └── share
│            　├── email_service.rb         # メール送信サービス
│            　├── payment_service.rb       # 支払いサービス
│            　└── notification_service.rb  # 通知サービス
│
├── infrastructure/              # フレームワーク & ドライバ層
│   ├── repositories/            # リポジトリ実装
│   │    ├── commands/
│   │    │    └── active_record_user_commands.rb  # 書き込み系
│   │    └── queries/
│   │         └── active_record_user_queries.rb 　# 読み取り系
│   └── external_apis/           # 外部APIとの連携
│       └── payment_gateway_api.rb
│
└── interfaces/                 # インターフェースアダプタ層
    ├── controllers/            # インターフェース (コントローラ)
    │     ├── api/
    │     │    └── v1/
    │     │        └── users_controller.rb
    │     ├── wapi/
    │     │    └── v1/
    │     │        └── users_controller.rb
    │     └── admin/
    │          └── users_controller.rb
    │
    ├── views/                  # インターフェース(ビュー)
    │    └── users/
    │          └── index.html.erb
    ├── presenters/                  # プレゼンター
    │    └── user_presenter.rb       # ユーザー表示フォーマット
    │
    └── serializers/
```

```
spec/
├── domain/                   # ドメイン層に関するテスト
│   ├── entities/             # ドメインエンティティのテスト
│   │   └── user_spec.rb
│   └── repositories/         # リポジトリインターフェースのテスト
│        ├── commands/
│        │    └── active_record_user_commands_spec.rb
│        └── queries/
│             └── active_record_user_queries_spec.rb
│
├── use_cases/                # ユースケース層のテスト
│   └── users/
│       └── create_user_spec.rb
│
├── infrastructure/           # インフラストラクチャ層のテスト
│   ├── repositories/         # リポジトリ実装のテスト (ActiveRecordなど)
│   │    ├── commands/
│   │    │    └── active_record_user_commands_spec.rb  # 書き込み系
│   │    └── queries/
│   │         └── active_record_user_queries_spec.rb 　# 読み取り系
│   └── external_apis/        # 外部API連携のテスト
│       └── payment_gateway_api_spec.rb
│
├── interfaces/               # インターフェース層のテスト
│   ├── requests/          # コントローラのリクエストスペック
│   │   ├── api/
│   │   │   └── users_controller_request_spec.rb
│   │   ├── admin/
│   │   │   └── users_controller_request_spec.rb
│   │   ├── wapi/
│   │   │   └── users_controller_request_spec.rb
│   │   └── share
│   │       ├── email_service_spec.rb         # メール送信サービス
│   │       ├── payment_service_spec.rb       # 支払いサービス
│   │       └── notification_service_spec.rb  # 通知サービス
│   └── serializers/          # シリアライザのテスト
│       └── user_serializer_spec.rb
│
└── support/                  # テストのヘルパーモジュールや共通設定
    ├── factories/            # FactoryBotのファクトリ
    │   └── users.rb
    ├── shared_examples/      # 共通のテストケース
    │   └── user_repository_examples.rb
    └── helpers/              # カスタムヘルパー
        └── api_helper.rb
```



Djangoだとこんな感じだろうか
```
project/
├── apps/
│   └── your_app/
│       ├── domain/
│       │   ├── entities.py
│       │   ├── repositories.py
│       │   └── services/
│       │       ├── user_service.py
│       │       ├── product_service.py
│       │       ├── order_service.py
│       │       └── __init__.py
│       │
│       ├── infrastructure/
│       │   ├── repositories/
│       │   │   ├── queries/
│       │   │   │   ├── user_query_repository.py   # ユーザーデータ取得用
│       │   │   │   ├── product_query_repository.py # 商品データ取得用
│       │   │   │   ├── order_query_repository.py   # 注文データ取得用
│       │   │   │   └── __init__.py
│       │   │   ├── commands/
│       │   │   │   ├── user_command_repository.py # ユーザーデータ操作用
│       │   │   │   ├── product_command_repository.py # 商品データ操作用
│       │   │   │   ├── order_command_repository.py # 注文データ操作用
│       │   │   │   └── __init__.py
│       │   │   └── __init__.py
│       │   ├── ~models.py~　(消す)
│       │   ├── serializers.py
│       │   ├── views.py
│       │   └── admin.py
│       │
│       ├── application/
│       │   ├── use_cases/
│       │   │   ├── create_something.py
│       │   │   ├── update_something.py
│       │   │   └── ...
│       │   ├── services/
│       │   │   ├── notification_service.py
│       │   │   ├── report_service.py
│       │   │   ├── logging_service.py
│       │   │   └── __init__.py
│       │   └── __init__.py
│       │
│       ├── presentation/
│       │   ├── controllers/
│       │   │   ├── user_controller.py
│       │   │   ├── product_controller.py
│       │   │   ├── order_controller.py
│       │   │   └── __init__.py
│       │   ├── templates/
│       │   │   ├── users/
│       │   │   │   ├── list.html
│       │   │   │   ├── detail.html
│       │   │   │   └── form.html
│       │   │   ├── products/
│       │   │   │   ├── list.html
│       │   │   │   ├── detail.html
│       │   │   │   └── form.html
│       │   │   └── base.html
│       │   ├── views/
│       │   │   ├── user_views.py
│       │   │   ├── product_views.py
│       │   │   ├── order_views.py
│       │   │   └── __init__.py
│       │   └── __init__.py
│       │
│       └── tests/
│           ├── domain/
│           ├── infrastructure/
│           ├── application/
│           └── presentation/
│
├── project/
│   ├── settings.py
│   ├── urls.py
│   ├── wsgi.py
│   └── asgi.py
└── manage.py
```

Next.js（バックエンドを用意して、フロントエンドではスタイルを整えて基本表示させるだけ）
```
src/
├── app/                     # App Routerディレクトリ (ページとレイアウト)
│   ├── layout.tsx           # 全体のレイアウト
│   ├── page.tsx             # トップページ
│   ├── users/               # ユーザー関連のルート
│   │   ├── page.tsx         # ユーザー一覧ページ
│   │   └── [id]/            # ユーザー詳細ページ
│   │       └── page.tsx     # 個別ユーザー詳細
├── components/              # UIコンポーネント
│   ├── common/              # 再利用可能な汎用コンポーネント
│   │   └── Button.tsx       # 汎用ボタン
│   ├── users/               # ユーザー関連コンポーネント
│   │   ├── UserCard.tsx     # ユーザー情報カード
│   │   ├── UserList.tsx     # ユーザー一覧表示
│   │   └── UserDetail.tsx   # ユーザー詳細表示
├── infrastructure/          # API通信やデータアクセス関連
│   ├── api/                 # Rails APIクライアント
│   │   └── UserApi.ts       # ユーザーAPIクライアント
├── styles/                  # スタイル関連
│   ├── globals.css          # グローバルCSS
│   └── variables.css        # カスタムプロパティ（変数）
├── types/                   # 型定義
│   ├── User.ts              # ユーザー関連の型
│   └── index.ts             # 他の型のエントリーポイント
└── utils/                   # ユーティリティ関数
    └── formatDate.ts        # 日付フォーマットなどの関数
```
