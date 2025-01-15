# クリーンアーキテクチャを意識したRailsのファイル構成
#### こんな感じに構成を組めたら、とてもスッキリすると思ってます
```
app/
├── domain/
│   ├── aggregates/             # アグリゲートを管理するフォルダ
│   │   ├── shopping_cart/      # ショッピングカートアグリゲート
│   │   │   ├── shopping_cart.rb       # アグリゲートルート
│   │   │   ├── entities/              # エンティティを格納
│   │   │   │   ├── shopping_cart_item.rb  # ショッピングカートアイテムエンティティ
│   │   │   │   └── discount.rb            # 割引情報のエンティティ（例）
│   │   │   ├── value_objects/          # 値オブジェクトを格納
│   │   │   │   ├── product_id.rb          # 商品ID（値オブジェクト）
│   │   │   │   └── money.rb               # 金額（値オブジェクト）
│   │   │   ├── services/               # ドメインサービス
│   │   │   │   └── calculate_cart_total_service.rb  # カート合計金額計算
│   │   │   └── repositories/           # リポジトリ
│   │   │       ├── commands/
│   │   │       │   └── shopping_cart_command_repository.rb
│   │   │       └── queries/
│   │   │           └── shopping_cart_query_repository.rb
│   │   ├── order/              # 注文アグリゲート
│   │   │   ├── order.rb                # アグリゲートルート
│   │   │   ├── entities/               # エンティティを格納
│   │   │   │   ├── order_item.rb          # 注文アイテムエンティティ
│   │   │   │   ├── shipping_detail.rb     # 配送情報エンティティ
│   │   │   │   └── payment_detail.rb      # 支払い情報エンティティ
│   │   │   ├── value_objects/          # 値オブジェクトを格納
│   │   │   │   ├── money.rb               # 金額（値オブジェクト）
│   │   │   │   ├── address.rb             # 住所（値オブジェクト）
│   │   │   │   └── tax_rate.rb            # 税率（値オブジェクト）
│   │   │   ├── services/               # ドメインサービス
│   │   │   │   └── calculate_order_total_service.rb # 注文合計金額計算
│   │   │   └── repositories/           # リポジトリ
│   │   │       ├── commands/
│   │   │       │   └── order_command_repository.rb
│   │   │       └── queries/
│   │   │           └── order_query_repository.rb
│   │   └── user/               # ユーザーアグリゲート
│   │       ├── user.rb                 # アグリゲートルート
│   │       ├── entities/               # エンティティを格納
│   │       │   ├── user_profile.rb        # ユーザープロファイルエンティティ
│   │       │   └── user_preferences.rb    # ユーザー設定エンティティ
│   │       ├── value_objects/          # 値オブジェクトを格納
│   │       │   ├── email.rb              # メール（値オブジェクト）
│   │       │   ├── password.rb           # パスワード（値オブジェクト）
│   │       │   └── address.rb            # 住所（値オブジェクト）
│   │       ├── services/               # ドメインサービス
│   │       │   └── password_encryption_service.rb  # パスワード暗号化
│   │       └── repositories/           # リポジトリ
│   │           ├── commands/
│   │           │   └── user_command_repository.rb
│   │           └── queries/
│   │               └── user_query_repository.rb
│   ├── shared/                 # 共有される値オブジェクトやサービス
│   │   ├── value_objects/
│   │   │   ├── money.rb        # 共有の金額値オブジェクト
│   │   │   ├── address.rb      # 共有の住所値オブジェクト
│   │   │   └── email.rb        # 共有のメール値オブジェクト
│   │   └── services/
│   │       └── tax_calculator_service.rb  # 税金計算
│   └── services/              # ドメイン全体で使用するサービス
│       ├── shipping_cost_service.rb       # 配送料計算
│       └── calculate_tax_service.rb       # 税金計算
│
├── use_cases/ # ユースケース層(各ユースケースがリポジトリやサービスを組み合わせ、単一のビジネスフロー全体を管理する)
│   └── users/
│       ├── web/
│       │    ├── register_user_web.rb          # ユースケース: ユーザー登録
│       │    ├── ban_user_web.rb               # ユースケース: ユーザー更新（垢BAN
│       │    └── withdraw_user_web.rb          # ユースケース: ユーザー退会
│       └── mobile/
│            ├── register_user_mobile.rb       # ユースケース: ユーザー登録
│            ├── ban_user_mobile.rb            # ユースケース: ユーザー更新（垢BAN
│            └── withdraw_user_mobile.rb       # ユースケース: ユーザー退会
│
├── infrastructure/              # フレームワーク & ドライバ層
│   ├── repositories/            # リポジトリ実装
│   │    ├── commands/
│   │    │    └── active_record_user_commands.rb  # 書き込み系
│   │    └── queries/
│   │         └── active_record_user_queries.rb 　# 読み取り系
│   ├── services/                    # サービス層 (汎用的な処理)
│   │    ├── api/
│   │    │    ├── payment_service.rb       # （API用）支払いサービス
│   │    │    └── ....
│   │    ├── wapi/
│   │    │    ├── payment_service.rb       # （Web用）支払いサービス
│   │    │    └── ....
│   │    ├── admin/
│   │    │    ├── email_service.rb         # （管理者が使用するための）メール送信サービス
│   │    │    └── ....
│   │    └── common
│   │        　├── email_service.rb         # メール送信サービス
│   │        　├── payment_service.rb       # 支払いサービス
│   │        　└── notification_service.rb  # 通知サービス
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
├── use_cases/                   # ユースケース層のテスト
│   └── users/
│       ├── create_user_spec.rb           # ユースケース: ユーザー作成
│       ├── update_user_spec.rb           # ユースケース: ユーザー更新
│       ├── delete_user_spec.rb           # ユースケース: ユーザー削除
│       └── get_user_spec.rb              # ユースケース: ユーザー取得
│
├── infrastructure/           # インフラストラクチャ層のテスト
│   ├── repositories/         # リポジトリ実装のテスト (ActiveRecordなど)
│   │    ├── commands/
│   │    │    └── active_record_user_commands_spec.rb  # 書き込み系
│   │    └── queries/
│   │         └── active_record_user_queries_spec.rb 　# 読み取り系
│   ├── services/                    # サービス層 (汎用的な処理)
│   │    ├── api/
│   │    │    ├── payment_service.rb       # （モバイルの）支払いサービス
│   │    │    └── ....
│   │    ├── wapi/
│   │    │    ├── payment_service.rb       # （Webの）支払いサービス
│   │    │    └── ....
│   │    ├── admin/
│   │    │    ├── email_service.rb         # （管理者が使用するための）メール送信サービス
│   │    │    └── ....
│   │    └── share
│   │        　├── email_service.rb         # メール送信サービス
│   │        　├── payment_service.rb       # 支払いサービス
│   │        　└── notification_service.rb  # 通知サービス
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
│       │   ├── repositories/
│       │   │   ├── queries/
│       │   │   │   ├── ...
│       │   │   │   └── __init__.py
│       │   │   ├── commands/
│       │   │   │   ├── ...
│       │   │   │   └── __init__.py
│       │   │   └── __init__.py
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
│       │   ├── services/
│       │   │   ├── user_service.py
│       │   │   ├── ....
│       │   │   └── __init__.py
│       │   ├── ~models.py~　(消す)
│       │   ├── serializers.py
│       │   ├── views.py
│       │   └── admin.py
│       │
│       ├── application/
│       │   ├── use_cases/
│       │   │   ├── users/
│       │   │   │   ├── create_user.py
│       │   │   │   ├── update_user.py
│       │   │   │   ├── ....
│       │   │   │   └── __init__.py
│       │   │   ├── products/
│       │   │   │   ├── create_product.py
│       │   │   │   ├── update_product.py
│       │   │   │   ├── ....
│       │   │   │   └── __init__.py
│       │   │   └── orders/
│       │   │       ├── create_order.py
│       │   │       ├── update_order.py
│       │   │       ├── ....
│       │   │       └── __init__.py
│       │   └── __init__.py
│       │
│       ├── presentation/
│       │   ├── controllers/
│       │   │   ├── user_controller.py
│       │   │   ├── product_controller.py
│       │   │   ├── order_controller.py
│       │   │   └── __init__.py
│       │   ├── templates/
│       │   │   ├── partials/
│       │   │   │   ├── header.html
│       │   │   │   ├── footer.html
│       │   │   │   └── ...
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
│   └── users/               # ユーザー関連のルート
│       ├── page.tsx         # ユーザー一覧ページ
│       └── [id]/            # ユーザー詳細ページ
│           └── page.tsx     # 個別ユーザー詳細
├── components/              # UIコンポーネント
│   ├── common/              # 再利用可能な汎用コンポーネント
│   │   └── Button.tsx       # 汎用ボタン
│   └── users/               # ユーザー関連コンポーネント
│       ├── UserCard.tsx     # ユーザー情報カード
│       ├── UserList.tsx     # ユーザー一覧表示
│       └── UserDetail.tsx   # ユーザー詳細表示
├── infrastructure/          # API通信やデータアクセス関連
│   └── api/                 # Rails APIクライアント
│       └── UserApi.ts       # ユーザーAPIクライアント
├── styles/                  # スタイル関連
│   ├── globals.css          # グローバルCSS
│   └── variables.css        # カスタムプロパティ（変数）
├── types/                   # 型定義
│   ├── User.ts              # ユーザー関連の型
│   └── index.ts             # 他の型のエントリーポイント
└── utils/                   # ユーティリティ関数
    └── formatDate.ts        # 日付フォーマットなどの関数
```
