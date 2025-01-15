# クリーンアーキテクチャを意識したRailsのファイル構成
https://qiita.com/MinoDriven/items/3c7db287e2c66f36589a
こちらの記事で勉強させていただきました

<img src="https://private-user-images.githubusercontent.com/43706329/403335767-4182b645-3bc7-49f3-94a7-06efbddb2890.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MzY5MzE2ODUsIm5iZiI6MTczNjkzMTM4NSwicGF0aCI6Ii80MzcwNjMyOS80MDMzMzU3NjctNDE4MmI2NDUtM2JjNy00OWYzLTk0YTctMDZlZmJkZGIyODkwLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTAxMTUlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwMTE1VDA4NTYyNVomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPWE5ZGY3NDYzOWMzNzM4M2RiMmQ4MDQxMDBkM2JhYjI4MmRlNTcyYmM5YzBmZmViNjQzMjMxNGQ2MjVlYjU5OTUmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.pbVKZ7ZLjV3c1awxXrctb1lZ19V5qUcTXuloLErnKzo" width="33%">

<img src="https://private-user-images.githubusercontent.com/43706329/403336022-9b4c3007-c38d-40ab-b516-bae1db5030de.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MzY5MzE3MjcsIm5iZiI6MTczNjkzMTQyNywicGF0aCI6Ii80MzcwNjMyOS80MDMzMzYwMjItOWI0YzMwMDctYzM4ZC00MGFiLWI1MTYtYmFlMWRiNTAzMGRlLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTAxMTUlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwMTE1VDA4NTcwN1omWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTg0OGNlNjk3MmM5NzU5OWEyMzg5ZjYyYTQ5Mzk4ZGEyMmVjZGUwNDY0NjAyMzIyNDljNWQzNzc5ZDVjNDRmODcmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.n02ErfuYWdfX9HtHXzm9Cy3VaG5mo1CddUbOVF1gbsw" width="33%">


```
app/
│
├── 01_domain/ # ドメイン層
│   ├── aggregates/             # アグリゲートを管理するフォルダ
│   │   ├── shopping_cart/      # ショッピングカートアグリゲート
│   │   │   ├── shopping_cart.rb       # アグリゲートルート
│   │   │   ├── entities/              # エンティティを格納
│   │   │   │   ├── item.rb            # ショッピングカートアイテムエンティティ
│   │   │   │   └── discount.rb        # 割引情報のエンティティ（例）
│   │   │   ├── value_objects/           # 値オブジェクトを格納
│   │   │   │   ├── product_id.rb        # 商品ID（値オブジェクト）
│   │   │   │   └── money.rb             # 金額（値オブジェクト）
│   │   │   ├── services/               # ドメインサービス
│   │   │   │   └── calculate_cart_total_service.rb  # カート合計金額計算
│   │   │   └── repositories/           # リポジトリ
│   │   │       ├── commands/
│   │   │       │   └── shopping_cart_command_repository.rb
│   │   │       └── queries/
│   │   │           └── shopping_cart_query_repository.rb
│   │   │
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
│   │   │   └── repositories/           # リポジトリ（アグリゲートごとにコマンド、クエリフォルダを作ってもいいかもしれない）
│   │   │       ├── commands/
│   │   │       │   └── order_command_repository.rb
│   │   │       └── queries/
│   │   │           └── order_query_repository.rb
│   │   │
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
│   │
│   └── shared/                 # ドメイン全体で共有される値オブジェクトやサービス
│       ├── value_objects/
│       │   ├── money.rb        # 共有の金額値オブジェクト
│       │   ├── address.rb      # 共有の住所値オブジェクト
│       │   └── email.rb        # 共有のメール値オブジェクト
│       └── services/
│           └── tax_calculator_service.rb  # 税金計算
│           ├── shipping_cost_service.rb       # 配送料計算
│           └── calculate_tax_service.rb       # 税金計算
│
├── 02_services/  # アプリケーションサービス層 (汎用的な処理)
│   ├── api/
│   │    ├── email_service.rb       # （API用）メール送信サービス
│   │    └── ....
│   ├── wapi/
│   │    ├── email_service.rb       # （Web用）メール送信サービス
│   │    └── ....
│   ├── admin/
│   │    ├── email_service.rb         # （管理者が使用するための）メール送信サービス
│   │    └── ....
│   └── common
│       　├── email_service.rb         # メール送信サービス
│       　└── notification_service.rb  # 通知サービス
│
├── 03_use_cases/ # アプリケーション層(各ユースケースがリポジトリやサービスを組み合わせ、単一のビジネスフロー全体を管理する)
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
├── 04_infrastructure/           # インフラストラクチャー層
│   ├── repositories/            # リポジトリ実装
│   │    ├── commands/
│   │    │    └── active_record_user_command_repository.rb  # 書き込み系
│   │    └── queries/
│   │         └── active_record_user_query_repository.rb 　# 読み取り系
│   ├── models/                 # ActiveRecordに紐づいてるモデル。アソシエーションだけ定義する。
│   │                           #     バリデーションはドメイン層のエンティティと値オブジェクトで。
│   └── external_apis/          # 外部APIとの連携
│       └── payment_gateway_api.rb
│
└── 05_interfaces/              # インターフェースアダプタ層
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
    │    └── user_presenter.rb       # （ビューのための）ユーザー表示フォーマット
    │
    └── serializers/  # API レスポンスフォーマット
```

```
spec/
├── 01_domain/                  # ドメイン層
│   ├── aggregates/          # アグリゲート
│   │   ├── shopping_cart/   # ショッピングカートアグリゲートのテスト
│   │   │   ├── shopping_cart_spec.rb  # ショッピングカートアグリゲートのテスト
│   │   │   ├── entities/           # エンティティのテスト
│   │   │   │   ├── shopping_cart_item_spec.rb  # ショッピングカートアイテムエンティティのテスト
│   │   │   │   └── discount_spec.rb            # 割引情報エンティティのテスト
│   │   │   ├── value_objects/          # 値オブジェクトのテスト
│   │   │   │   ├── product_id_spec.rb          # 商品ID（値オブジェクト）のテスト
│   │   │   │   └── money_spec.rb               # 金額（値オブジェクト）のテスト
│   │   │   ├── services/               # ドメインサービスのテスト
│   │   │   │   └── calculate_cart_total_service_spec.rb  # カート合計金額計算サービスのテスト
│   │   │   └── repositories/           # リポジトリのテスト
│   │   │       ├── commands/
│   │   │       │   └── shopping_cart_command_repository_spec.rb
│   │   │       └── queries/
│   │   │           └── shopping_cart_query_repository_spec.rb
│   │   ├── order/              # 注文アグリゲート
│   │   │   ├── order_spec.rb                # 注文アグリゲートのテスト
│   │   │   ├── entities/               # エンティティのテスト
│   │   │   │   ├── order_item_spec.rb          # 注文アイテムエンティティのテスト
│   │   │   │   ├── shipping_detail_spec.rb     # 配送情報エンティティのテスト
│   │   │   │   └── payment_detail_spec.rb      # 支払い情報エンティティのテスト
│   │   │   ├── value_objects/          # 値オブジェクトのテスト
│   │   │   │   ├── money_spec.rb               # 金額（値オブジェクト）のテスト
│   │   │   │   ├── address_spec.rb             # 住所（値オブジェクト）のテスト
│   │   │   │   └── tax_rate_spec.rb            # 税率（値オブジェクト）のテスト
│   │   │   ├── services/               # ドメインサービスのテスト
│   │   │   │   └── calculate_order_total_service_spec.rb # 注文合計金額計算サービスのテスト
│   │   │   └── repositories/           # リポジトリのテスト
│   │   │       ├── commands/
│   │   │       │   └── order_command_repository_spec.rb
│   │   │       └── queries/
│   │   │           └── order_query_repository_spec.rb
│   │   └── user/               # ユーザーアグリゲート
│   │       ├── user_spec.rb                 # ユーザーアグリゲートのテスト
│   │       ├── entities/               # エンティティのテスト
│   │       │   ├── user_profile_spec.rb        # ユーザープロファイルエンティティのテスト
│   │       │   └── user_preferences_spec.rb    # ユーザー設定エンティティのテスト
│   │       ├── value_objects/          # 値オブジェクトのテスト
│   │       │   ├── email_spec.rb              # メール（値オブジェクト）のテスト
│   │       │   ├── password_spec.rb           # パスワード（値オブジェクト）のテスト
│   │       │   └── address_spec.rb            # 住所（値オブジェクト）のテスト
│   │       ├── services/               # ドメインサービスのテスト
│   │       │   └── password_encryption_service_spec.rb  # パスワード暗号化サービスのテスト
│   │       └── repositories/           # リポジトリのテスト
│   │           ├── commands/
│   │           │   └── user_command_repository_spec.rb
│   │           └── queries/
│   │               └── user_query_repository_spec.rb
│   └── shared/                 # ドメイン全体で共有される値オブジェクトやサービス
│       ├── value_objects/
│       │   ├── money_spec.rb        # 共有の金額値オブジェクトのテスト
│       │   ├── address_spec.rb      # 共有の住所値オブジェクトのテスト
│       │   └── email_spec.rb        # 共有のメール値オブジェクトのテスト
│       └── services/
│           └── tax_calculator_service_spec.rb  # 税金計算サービスのテスト
│           ├── shipping_cost_service_spec.rb       # 配送料計算サービスのテスト
│           └── calculate_tax_service_spec.rb       # 税金計算サービスのテスト
│
├── 02_services/  # アプリケーションサービス層 (汎用的な処理)
│   ├── wapi/
│   │    ├── email_service.rb       # （Web用）メール送信サービスのテスト
│   │    └── ....
│   ├── admin/
│   │    ├── email_service.rb         # （管理者が使用するための）メール送信サービスのテスト
│   │    └── ....
│   └── common
│       　├── email_service.rb         # メール送信サービスのテスト
│       　└── notification_service.rb  # 通知サービスのテスト
│
├── 03_use_cases/ # アプリケーション層
│   └── users/
│       ├── web/
│       │    ├── register_user_web_spec.rb          # ユースケース: ユーザー登録のテスト
│       │    ├── ban_user_web_spec.rb               # ユースケース: ユーザー更新（垢BAN）のテスト
│       │    └── withdraw_user_web_spec.rb          # ユースケース: ユーザー退会のテスト
│       └── mobile/
│            ├── register_user_mobile_spec.rb       # ユースケース: ユーザー登録のテスト
│            ├── ban_user_mobile_spec.rb            # ユースケース: ユーザー更新（垢BAN）のテスト
│            └── withdraw_user_mobile_spec.rb       # ユースケース: ユーザー退会のテスト
│
├── 04_infrastructure/           # インフラストラクチャー層
│   ├── repositories/            # リポジトリ実装のテスト
│   │    ├── commands/
│   │    │    └── active_record_user_command_repository_spec.rb  # 書き込み系リポジトリのテスト
│   │    └── queries/
│   │         └── active_record_user_query_repository_spec.rb 　# 読み取り系リポジトリのテスト
│   └── external_apis/           # 外部APIとの連携のテスト
│       └── payment_gateway_api_spec.rb
│
└── 05_interfaces/                 # インターフェースアダプタ層
    ├── controllers/            # コントローラのテスト
    │     ├── api/
    │     │    └── v1/
    │     │        └── users_controller_spec.rb
    │     ├── wapi/
    │     │    └── v1/
    │     │        └── users_controller_spec.rb
    │     └── admin/
    │          └── users_controller_spec.rb
    │
    ├── views/                  # ビューのテスト
    │    └── users/
    │          └── index_spec.rb
    ├── presenters/                  # プレゼンターのテスト
    │    └── user_presenter_spec.rb       # ユーザー表示フォーマットのテスト
    │
    └── serializers/  # API レスポンスフォーマットのテスト

```

### Railsプロジェクトのフォルダも加えるとこんな感じに
```
project_root/
├── 01_domain/               # ドメイン層
│   ├── aggregates/
│   ├── services/
│   └── value_objects/
│
├── 02_services/             # アプリケーションサービス層
│   ├── api/
│   ├── wapi/
│   ├── admin/
│   └── common/
│
├── 03_use_cases/            # アプリケーション層
│   ├── users/
│   ├── orders/
│   └── payments/
│
├── 04_infrastructure/       # インフラストラクチャ層
│   ├── repositories/
│   ├── models/
│   └── external_apis/
│
├── 05_interfaces/           # インターフェース層
│   ├── controllers/
│   ├── views/
│   ├── presenters/
│   └── serializers/
│
├── app/                     # Rails標準のディレクトリ
│   ├── controllers/         # Rails用コントローラー（MVC用）
│   ├── helpers/
│   ├── jobs/
│   ├── mailers/
│   ├── models/              # ActiveRecordモデル
│   └── views/               # Rails用ビュー
│
├── bin/                     # Rails実行ファイル
│   ├── rails
│   └── rake
│
├── config/                  # Rails設定ファイル
│   ├── application.rb
│   ├── database.yml
│   ├── environment.rb
│   └── routes.rb
│
├── db/                      # データベース関連
│   ├── migrate/
│   ├── schema.rb
│   └── seeds.rb
│
├── lib/                     # ライブラリ（カスタムコードを配置する場所）
│   └── tasks/
│
├── log/                     # ログファイル
│
├── public/                  # 静的ファイル（HTML、CSSなど）
│
├── storage/                 # ActiveStorage用
│
├── test/                    # テストコード
│   ├── fixtures/
│   ├── integration/
│   ├── system/
│   └── unit/
│
├── tmp/                     # 一時ファイル
│
├── vendor/                  # サードパーティコード
│
├── Gemfile                  # Gemの管理
├── Gemfile.lock             # Gemのバージョン固定
├── Rakefile                 # Rakeタスク管理
└── README.md                # プロジェクト説明
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
