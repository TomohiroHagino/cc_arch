# クリーンアーキテクチャを意識したRailsのファイル構成
https://qiita.com/MinoDriven/items/3c7db287e2c66f36589a
こちらの記事を参考にさせていただきました

```
app/
├── domain/ # ドメイン層
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
│   │   │   └── repositories/           # リポジトリ（アグリゲートごとにコマンド、クエリフォルダを作ってもいいかもしれない）
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
│   │    │    └── active_record_user_command_repository.rb  # 書き込み系
│   │    └── queries/
│   │         └── active_record_user_query_repository.rb 　# 読み取り系
│   ├── services/  # サービス層 (汎用的な処理)
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
    │    └── user_presenter.rb       # （ビューのための）ユーザー表示フォーマット
    │
    └── serializers/  # API レスポンスフォーマット
```

```
spec/
├── factories/
│   ├── users.rb          # Userモデルのファクトリー
│   ├── orders.rb         # Orderモデルのファクトリー
│   ├── shopping_carts.rb # ShoppingCartモデルのファクトリー
│   └── shared/
│       ├── addresses.rb  # 共有値オブジェクト（住所など）のファクトリー
│       └── ...
├── domain/
│   ├── aggregates/
│   │   ├── shopping_cart/
│   │   │   ├── shopping_cart_spec.rb
│   │   │   ├── entities/
│   │   │   │   ├── shopping_cart_item_spec.rb
│   │   │   │   └── discount_spec.rb
│   │   │   ├── value_objects/
│   │   │   │   ├── product_id_spec.rb
│   │   │   │   └── money_spec.rb
│   │   │   ├── services/
│   │   │   │   └── calculate_cart_total_service_spec.rb
│   │   │   └── repositories/
│   │   │       ├── commands/
│   │   │       │   └── shopping_cart_command_repository_spec.rb
│   │   │       └── queries/
│   │   │           └── shopping_cart_query_repository_spec.rb
│   │   ├── order/
│   │   │   ├── order_spec.rb
│   │   │   ├── entities/
│   │   │   │   ├── order_item_spec.rb
│   │   │   │   ├── shipping_detail_spec.rb
│   │   │   │   └── payment_detail_spec.rb
│   │   │   ├── value_objects/
│   │   │   │   ├── money_spec.rb
│   │   │   │   ├── address_spec.rb
│   │   │   │   └── tax_rate_spec.rb
│   │   │   ├── services/
│   │   │   │   └── calculate_order_total_service_spec.rb
│   │   │   └── repositories/
│   │   │       ├── commands/
│   │   │       │   └── order_command_repository_spec.rb
│   │   │       └── queries/
│   │   │           └── order_query_repository_spec.rb
│   │   └── user/
│   │       ├── user_spec.rb
│   │       ├── entities/
│   │       │   ├── user_profile_spec.rb
│   │       │   └── user_preferences_spec.rb
│   │       ├── value_objects/
│   │       │   ├── email_spec.rb
│   │       │   ├── password_spec.rb
│   │       │   └── address_spec.rb
│   │       ├── services/
│   │       │   └── password_encryption_service_spec.rb
│   │       └── repositories/
│   │           ├── commands/
│   │           │   └── user_command_repository_spec.rb
│   │           └── queries/
│   │               └── user_query_repository_spec.rb
│   ├── shared/
│   │   ├── value_objects/
│   │   │   ├── money_spec.rb
│   │   │   ├── address_spec.rb
│   │   │   └── email_spec.rb
│   │   └── services/
│   │       └── tax_calculator_service_spec.rb
│   └── services/
│       ├── shipping_cost_service_spec.rb
│       └── calculate_tax_service_spec.rb
│
├── use_cases/
│   └── users/
│       ├── web/
│       │   ├── register_user_web_spec.rb
│       │   ├── ban_user_web_spec.rb
│       │   └── withdraw_user_web_spec.rb
│       └── mobile/
│           ├── register_user_mobile_spec.rb
│           ├── ban_user_mobile_spec.rb
│           └── withdraw_user_mobile_spec.rb
│
├── infrastructure/
│   ├── repositories/
│   │   ├── commands/
│   │   │   └── active_record_user_commands_spec.rb
│   │   └── queries/
│   │       └── active_record_user_queries_spec.rb
│   ├── services/
│   │   ├── api/
│   │   │   └── payment_service_spec.rb
│   │   ├── wapi/
│   │   │   └── payment_service_spec.rb
│   │   ├── admin/
│   │   │   └── email_service_spec.rb
│   │   └── common/
│   │       ├── email_service_spec.rb
│   │       ├── payment_service_spec.rb
│   │       └── notification_service_spec.rb
│   └── external_apis/
│       └── payment_gateway_api_spec.rb
│
└── interfaces/
    ├── controllers/
    │   ├── api/
    │   │   └── v1/
    │   │       └── users_controller_spec.rb
    │   ├── wapi/
    │   │   └── v1/
    │   │       └── users_controller_spec.rb
    │   └── admin/
    │       └── users_controller_spec.rb
    ├── views/
    │   └── users/
    │       └── index_spec.rb
    ├── presenters/
    │   └── user_presenter_spec.rb
    └── serializers/
        └── user_serializer_spec.rb

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
