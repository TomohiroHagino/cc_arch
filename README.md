# クリーンアーキテクチャを意識した各フレームワークのファイル構成を考えてみる
https://qiita.com/MinoDriven/items/3c7db287e2c66f36589a
こちらの記事で勉強させていただきました

 ただのメモ書きですが、プロジェクトの最初の雛形として使ってます。

 フォルダの構造を統一させることで、言語を選ばず、チームメンバーが迷わない様に。
 いらない層はあとで消してしまう。


# Ruby on Rails

```
app/
│
├── 01_domain/                  # ドメイン層
│   ├── aggregates/             # アグリゲートを管理するフォルダ
│   │   ├── shopping_cart/      # ショッピングカートアグリゲート
│   │   │   ├── shopping_cart.rb       # アグリゲートルート
│   │   │   ├── entities/              # エンティティを格納
│   │   │   │   ├── item.rb            # ショッピングカートアイテムエンティティ
│   │   │   │   └── discount.rb        # 割引情報のエンティティ（例）
│   │   │   ├── value_objects/           # 値オブジェクトを格納
│   │   │   │   ├── product_id.rb        # 商品ID（値オブジェクト）
│   │   │   │   └── shopping_cart_money.rb             # ショッピングカート固有のロジックを持った金額（値オブジェクト）
│   │   │   ├── services/               # ドメインサービス
│   │   │   │   └── calculate_cart_total_service.rb  # カート合計金額計算
│   │   │   └── repositories/           # リポジトリ
│   │   │       ├── commands/
│   │   │       │   └── shopping_cart_command_repository.rb
│   │   │       └── queries/
│   │   │           └── shopping_cart_query_repository.rb
│   │   │
│   │   ├── order/                      # 注文アグリゲート
│   │   │   ├── order.rb                # アグリゲートルート
│   │   │   ├── entities/               # エンティティを格納
│   │   │   │   ├── order_item.rb          # 注文アイテムエンティティ
│   │   │   │   ├── shipping_detail.rb     # 配送情報エンティティ
│   │   │   │   └── payment_detail.rb      # 支払い情報エンティティ
│   │   │   ├── value_objects/          # 値オブジェクトを格納
│   │   │   │   ├── order_address.rb             # order固有のロジックを持った住所（値オブジェクト）
│   │   │   │   └── tax_rate.rb            # 税率（値オブジェクト）
│   │   │   ├── services/               # ドメインサービス
│   │   │   │   └── calculate_order_total_service.rb # 注文合計金額計算
│   │   │   └── repositories/           # リポジトリ（アグリゲートごとにコマンド、クエリフォルダを作ってもいいかもしれない）
│   │   │       ├── commands/
│   │   │       │   └── order_command_repository.rb
│   │   │       └── queries/
│   │   │           └── order_query_repository.rb
│   │   │
│   │   └── user/                       # ユーザーアグリゲート
│   │       ├── user.rb                 # アグリゲートルート
│   │       ├── entities/               # エンティティを格納
│   │       │   ├── user_profile.rb        # ユーザープロファイルエンティティ
│   │       │   └── user_preferences.rb    # ユーザー設定エンティティ
│   │       ├── value_objects/          # 値オブジェクトを格納
│   │       │   ├── user_email.rb              # ユーザー固有のロジックを持ったメール（値オブジェクト）
│   │       │   ├── password.rb           # パスワード（値オブジェクト）
│   │       │   └── user_address.rb            # ユーザー固有のロジックを持った住所（値オブジェクト）
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
│       │   └── address.rb      # 共有の住所値オブジェクト
│       └── services/
│           └── tax_calculator_service.rb  # 税金計算
│           ├── shipping_cost_service.rb       # 配送料計算
│           └── calculate_tax_service.rb       # 税金計算
│
├── 02_use_cases/ # アプリケーション層(各ユースケースがリポジトリやサービスを組み合わせ、単一のビジネスフロー全体を管理する)
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
├── 03_services/  # アプリケーションサービス層 (汎用的な処理)
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
│           　├── email_service.rb         # メール送信サービス
│           　└── notification_service.rb  # 通知サービス
│    
├── 04_infrastructure/           # インフラストラクチャー層
│   ├── repositories/            # リポジトリ実装
│   │    ├── commands/
│   │    │    └── active_record_user_command_repository.rb  # 書き込み系
│   │    └── queries/
│   │         └── active_record_user_query_repository.rb 　# 読み取り系
│   │
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
    ├── assets/
    ├── javascripts/
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
├── 01_domain/               # ドメイン層
│   ├── aggregates/          # アグリゲート
│   │   ├── shopping_cart/   # ショッピングカートアグリゲートのテスト
│   │   │   ├── shopping_cart_spec.rb  # ショッピングカートアグリゲートのテスト
│   │   │   ├── entities/           # エンティティのテスト
│   │   │   │   ├── shopping_cart_item_spec.rb    # ショッピングカートアイテムエンティティのテスト
│   │   │   │   └── discount_spec.rb              # 割引情報エンティティのテスト
│   │   │   ├── value_objects/                    # 値オブジェクトのテスト
│   │   │   │   ├── product_id_spec.rb            # 商品ID（値オブジェクト）のテスト
│   │   │   │   └── shopping_cart_money_spec.rb   # 金額（値オブジェクト）のテスト
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
│   │   │   │   ├── order_money_spec.rb               # 金額（値オブジェクト）のテスト
│   │   │   │   ├── order_address_spec.rb             # 住所（値オブジェクト）のテスト
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
│   │       │   ├── user_email_spec.rb              # メール（値オブジェクト）のテスト
│   │       │   ├── password_spec.rb           # パスワード（値オブジェクト）のテスト
│   │       │   └── user_address_spec.rb            # 住所（値オブジェクト）のテスト
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
│       │   └── address_spec.rb      # 共有の住所値オブジェクトのテスト
│       │   └── email_spec.rb        # 共有のメール値オブジェクトのテスト
│       └── services/
│           └── tax_calculator_service_spec.rb  # 税金計算サービスのテスト
│           ├── shipping_cost_service_spec.rb       # 配送料計算サービスのテスト
│           └── calculate_tax_service_spec.rb       # 税金計算サービスのテスト
│
├── 02_use_cases/ # アプリケーション層
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
│   │
│   ├── services/  # アプリケーションサービス層 (汎用的な処理)
│   │   ├── wapi/
│   │   │    ├── email_service.rb       # （Web用）メール送信サービスのテスト
│   │   │    └── ....
│   │   ├── admin/
│   │   │    ├── email_service.rb         # （管理者が使用するための）メール送信サービスのテスト
│   │   │    └── ....
│   │   └── common
│   │       　├── email_service.rb         # メール送信サービスのテスト
│   │       　└── notification_service.rb  # 通知サービスのテスト
│   │
│   └── external_apis/           # 外部APIとの連携のテスト
│       └── payment_gateway_api_spec.rb
│
└── 05_interfaces/                 # インターフェースアダプタ層
    ├── requests/                  # コントローラのテスト
    │     ├── api/
    │     │    └── v1/
    │     │        └── users_controller_request_spec.rb
    │     ├── wapi/
    │     │    └── v1/
    │     │        └── users_controller_request_spec.rb
    │     └── admin/
    │          └── users_controller_request_spec.rb
    │
    ├── systems/
    │    └── users/
    │          └── index_spec.rb
    ├── presenters/                  # プレゼンターのテスト
    │    └── user_presenter_spec.rb       # ユーザー表示フォーマットのテスト
    │
    └── serializers/  # API レスポンスフォーマットのテスト

```

### Railsプロジェクトのフォルダも加えるとこんな感じに
```
app/
├── 01_domain/               # ドメイン層
│   ├── aggregates/
│   ├── services/
│   └── value_objects/
│
├── 02_use_cases/            # アプリケーション層
│   ├── users/
│   ├── orders/
│   └── payments/
│
├── 03_services/             # アプリケーションサービス層
│   ├── api/
│   ├── wapi/
│   ├── admin/
│   └── common/
│
├── 04_infrastructure/       # インフラストラクチャ層
│   ├── repositories/
│   ├── mailers/
│   ├── models/
│   ├── jobs/
│   └── external_apis/
│
├── 05_interfaces/           # インターフェース層
│   ├── controllers/
│   ├── views/
│   ├── presenters/
│   ├── javascripts/
│   └── serializers/
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
├── node_modules/
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
├── spec/                    # テストコード
│
├── tmp/                     # 一時ファイル
│
├── vendor/                  # サードパーティコード
│
├── Gemfile                  # Gemの管理
├── Gemfile.lock             # Gemのバージョン固定
├── Rakefile                 # Rakeタスク管理
└── README.md                # プロジェクト説明、環境構築などの手順を書く
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

### Next.js（App Routerで。基本バックエンドからapiで情報を受け取るのでドメイン層は必要最低限。 openapi使うともっと楽かも）
```
src/
├── 01_domain/                         # ドメイン層
│   ├── entities/                      # エンティティ
│   │   ├── User.ts                    # ユーザーエンティティ
│   │   └── Product.ts                 # 商品エンティティ
│   ├── valueObjects/                  # 値オブジェクト
│   │   ├── Price.ts                   # 商品価格値オブジェクト
│   │   └── Email.ts                   # メール値オブジェクト
│   └── types/                         # ドメイン固有の型
│       ├── UserTypes.ts               # ユーザー型
│       └── ProductTypes.ts            # 商品型
│
├── 02_application/                       # アプリケーション層（ユースケースや状態管理）
│   ├── hooks/                         # カスタムフック
│   │   ├── useFetchUsers.ts           # ユーザー取得
│   │   ├── useFetchProducts.ts        # 商品取得
│   │   └── useUserAuth.ts             # 認証状態管理
│   ├── state/                         # 状態管理（React ContextやZustandなど）
│   │   ├── authStore.ts               # 認証情報管理
│   │   ├── userStore.ts               # ユーザー情報管理
│   │   └── productStore.ts            # 商品情報管理
│   └── types/                         # アプリケーション層の型
│       ├── AuthTypes.ts               # 認証関連型
│       ├── StateTypes.ts              # 状態管理型
│       └── HookTypes.ts               # カスタムフック型
│
├── 03_infrastructure/                 # インフラストラクチャ層
│   ├── api/                           # APIクライアント
│   │   ├── clients/                   # HTTPクライアント（Axios設定など）
│   │   │   └── httpClient.ts
│   │   ├── services/                  # 各リソース用のAPIサービス
│   │   │   ├── userService.ts         # ユーザー関連APIサービス
│   │   │   └── productService.ts      # 商品関連APIサービス
│   │   └── types/                     # APIレスポンス型
│   │       ├── UserDTO.ts             # ユーザーDTO型
│   │       ├── ProductDTO.ts          # 商品DTO型
│   │       └── ApiResponse.ts         # 共通レスポンス型
│   ├── storage/                       # ストレージ操作
│   │   ├── localStorage.ts            # ローカルストレージ操作
│   │   └── sessionStorage.ts          # セッションストレージ操作
│   └── external/                      # 外部サービス連携
│       └── firebaseClient.ts          # Firebase設定
│
├── 04_interface/                      # インターフェース層
│   ├── app/                           # App Routerディレクトリ
│   │   ├── layout.tsx                 # 共通レイアウト
│   │   ├── page.tsx                   # ホームページ
│   │   ├── users/                     # ユーザーページ
│   │   │   ├── page.tsx               # ユーザー一覧
│   │   │   ├── [id]/                  # 動的ルート
│   │   │   │   └── page.tsx           # ユーザー詳細
│   │   │   └── types/                 # ユーザーページ固有の型
│   │   │       ├── UserPageProps.ts   # ページプロパティ型
│   │   │       └── UserDetailProps.ts # 詳細ページプロパティ型
│   │   └── products/                  # 商品ページ
│   │       ├── page.tsx               # 商品一覧
│   │       ├── [id]/                  # 商品詳細
│   │       │   └── page.tsx
│   │       └── types/                 # 商品ページ固有の型
│   │           ├── ProductPageProps.ts # ページプロパティ型
│   │           └── ProductDetailProps.ts # 詳細ページプロパティ型
│   │
│   ├── components/                    # 再利用可能なUIコンポーネント
│   │   ├── common/                    # 汎用コンポーネント
│   │   │   ├── Button.tsx             # ボタン
│   │   │   └── Modal.tsx              # モーダル
│   │   ├── layout/                    # レイアウト関連
│   │   │   ├── Header.tsx             # ヘッダー
│   │   │   └── Footer.tsx             # フッター
│   │   ├── users/                     # ユーザー関連コンポーネント
│   │   │   └── UserCard.tsx           # ユーザーカード
│   │   └── products/                  # 商品関連コンポーネント
│   │       └── ProductList.tsx        # 商品一覧
│   ├── pages/                         # ページコンポーネント
│   │   ├── HomePage.tsx               # ホームページ
│   │   ├── UsersPage.tsx              # ユーザーページ
│   │   └── ProductsPage.tsx           # 商品ページ
│   └── styles/                        # スタイル
│       ├── index.css                  # グローバルスタイル
│       └── theme.ts                   # テーマ設定
│
├── shared/                            # 再利用可能な型や定数
│   ├── types/                         # 共通型定義
│   │   ├── CommonTypes.ts             # 汎用型（例: ID, Nullable）
│   │   ├── PaginationTypes.ts         # ページング型
│   │   └── ApiResponseTypes.ts        # APIレスポンス共通型
│   ├── constants/                     # 定数
│   │   ├── apiEndpoints.ts            # APIエンドポイント
│   │   └── appConfig.ts               # アプリ設定
│   └── helpers/                       # 汎用ヘルパー関数
│       ├── formatDate.ts              # 日付フォーマット
│       └── logger.ts                  # ログ出力
│
└── utils/                             # 汎用的なユーティリティ関数
    ├── formatDate.ts                  # 日付フォーマット
    ├── logger.ts                      # ログ出力
    └── validators.ts                  # 入力バリデーション
```

### React (基本バックエンドからapiで情報を受け取るのでドメイン層は必要最低限。 openapi使うともっと楽かも)
```
src/
├── 01_domain/                         # ドメイン層
│   ├── entities/                      # エンティティ
│   │   ├── Product.ts                 # 商品エンティティ
│   │   └── User.ts                    # ユーザーエンティティ
│   ├── valueObjects/                  # 値オブジェクト
│   │   ├── Price.ts                   # 金額値オブジェクト
│   │   └── Address.ts                 # 住所値オブジェクト
│   └── types/                         # ドメイン層固有の型
│       ├── ProductTypes.ts            # 商品関連型
│       └── UserTypes.ts               # ユーザー関連型
│
├── 02_application/                    # アプリケーション層（ユースケース・状態管理）
│   ├── hooks/                         # カスタムフック
│   │   ├── useFetchUsers.ts           # ユーザー一覧取得
│   │   ├── useFetchProducts.ts        # 商品一覧取得
│   │   └── useUserAuth.ts             # 認証状態管理
│   ├── state/                         # グローバル状態管理
│   │   ├── auth/                      # 認証関連の状態
│   │   │   ├── authSlice.ts           # Reduxスライス
│   │   │   └── authActions.ts         # 認証アクション
│   │   ├── products/                  # 商品関連の状態
│   │   │   ├── productSlice.ts        # Reduxスライス
│   │   │   └── productActions.ts      # 商品アクション
│   │   └── store.ts                   # Reduxストア設定
│   └── types/                         # アプリケーション層の型
│       ├── AuthTypes.ts               # 認証関連型
│       └── StateTypes.ts              # 状態管理型
│
├── 03_infrastructure/                 # インフラストラクチャ層（外部リソースとの通信）
│   ├── api/                           # API通信
│   │   ├── clients/                   # HTTPクライアント
│   │   │   └── httpClient.ts          # Axios設定
│   │   ├── services/                  # 各リソース用API呼び出し
│   │   │   ├── userService.ts         # ユーザー関連API
│   │   │   └── productService.ts      # 商品関連API
│   │   └── types/                     # APIレスポンス型
│   │       ├── UserDTO.ts             # ユーザーDTO型
│   │       ├── ProductDTO.ts          # 商品DTO型
│   │       └── ApiResponse.ts         # 共通レスポンス型
│   ├── storage/                       # ストレージ操作
│   │   ├── localStorage.ts            # ローカルストレージ操作
│   │   └── sessionStorage.ts          # セッションストレージ操作
│   └── external/                      # 外部サービス連携
│       └── firebaseClient.ts          # Firebaseクライアント設定
│
├── 04_interface/                      # インターフェース層（UIとプレゼンテーション）
│   ├── components/                    # 再利用可能なUIコンポーネント
│   │   ├── common/                    # 汎用コンポーネント
│   │   │   ├── Button.tsx             # ボタン
│   │   │   └── Modal.tsx              # モーダル
│   │   ├── layout/                    # レイアウト関連
│   │   │   ├── Header.tsx             # ヘッダー
│   │   │   └── Footer.tsx             # フッター
│   │   ├── users/                     # ユーザー関連コンポーネント
│   │   │   └── UserCard.tsx           # ユーザーカード
│   │   └── products/                  # 商品関連コンポーネント
│   │       └── ProductList.tsx        # 商品一覧
│   ├── pages/                         # ページコンポーネント
│   │   ├── HomePage.tsx               # ホームページ
│   │   ├── UsersPage.tsx              # ユーザーページ
│   │   └── ProductsPage.tsx           # 商品ページ
│   └── styles/                        # スタイル
│       ├── index.css                  # グローバルスタイル
│       └── theme.ts                   # テーマ設定
│
├── shared/                            # 共有リソース
│   └── types/                         # 再利用可能な型
│       ├── CommonTypes.ts             # 汎用型（例: ID, Nullable）
│       ├── PaginationTypes.ts         # ページング関連型
│       └── ApiResponseTypes.ts        # 共通APIレスポンス型
│
├── utils/                             # ユーティリティ関数
│   ├── formatDate.ts                  # 日付フォーマット
│   ├── logger.ts                      # ログ出力
│   ├── validators.ts                  # バリデーション
│   └── types/                         # ユーティリティ型
│       ├── LoggerTypes.ts             # ログ関連型
│       └── ValidatorTypes.ts          # バリデーション関連型
│
├── App.tsx                            # アプリケーション全体のエントリーポイント
└── index.tsx                          # Reactアプリの起動スクリプト

```


### Vue.js (Vue 3)

```
src/
├── 01_domain/                         # ドメイン層
│   ├── entities/                      # エンティティ
│   │   ├── User.ts                    # ユーザーエンティティ
│   │   └── Product.ts                 # 商品エンティティ
│   ├── valueObjects/                  # 値オブジェクト
│   │   ├── Price.ts                   # 商品価格値オブジェクト
│   │   └── Email.ts                   # メール値オブジェクト
│   └── types/                         # ドメイン固有の型
│       ├── UserTypes.ts               # ユーザー型
│       └── ProductTypes.ts            # 商品型
│
├── 02_application/                    # アプリケーション層（ユースケースや状態管理）
│   ├── composables/                   # カスタムコンポーザブル
│   │   ├── useFetchUsers.ts           # ユーザー取得
│   │   ├── useFetchProducts.ts        # 商品取得
│   │   └── useUserAuth.ts             # 認証状態管理
│   ├── stores/                        # 状態管理（PiniaやVuexなど）
│   │   ├── authStore.ts               # 認証情報管理
│   │   ├── userStore.ts               # ユーザー情報管理
│   │   └── productStore.ts            # 商品情報管理
│   └── types/                         # アプリケーション層の型
│       ├── AuthTypes.ts               # 認証関連型
│       ├── StoreTypes.ts              # 状態管理型
│       └── ComposableTypes.ts         # カスタムコンポーザブル型
│
├── 03_infrastructure/                    # インフラストラクチャ層
│   ├── api/                           # APIクライアント
│   │   ├── clients/                   # HTTPクライアント（Axios設定など）
│   │   │   └── httpClient.ts
│   │   ├── services/                  # 各リソース用のAPIサービス
│   │   │   ├── userService.ts         # ユーザー関連APIサービス
│   │   │   └── productService.ts      # 商品関連APIサービス
│   │   └── types/                     # APIレスポンス型
│   │       ├── UserDTO.ts             # ユーザーDTO型
│   │       ├── ProductDTO.ts          # 商品DTO型
│   │       └── ApiResponse.ts         # 共通レスポンス型
│   ├── storage/                       # ストレージ操作
│   │   ├── localStorage.ts            # ローカルストレージ操作
│   │   └── sessionStorage.ts          # セッションストレージ操作
│   └── external/                      # 外部サービス連携
│       └── firebaseClient.ts          # Firebase設定
│
├── 04_interface/                         # インターフェース層
│   ├── router/                        # Vue Routerの設定
│   │   ├── index.ts                   # ルート設定
│   │   └── guards/                    # ルートガード
│   │       ├── authGuard.ts           # 認証ガード
│   │       └── roleGuard.ts           # 権限ガード
│   ├── views/                         # ページコンポーネント
│   │   ├── HomeView.vue               # ホームページ
│   │   ├── UsersView.vue              # ユーザーページ
│   │   └── ProductsView.vue           # 商品ページ
│   ├── components/                    # 再利用可能なUIコンポーネント
│   │   ├── common/                    # 汎用コンポーネント
│   │   │   ├── Button.vue             # ボタン
│   │   │   └── Modal.vue              # モーダル
│   │   ├── layout/                    # レイアウト関連
│   │   │   ├── Header.vue             # ヘッダー
│   │   │   └── Footer.vue             # フッター
│   │   ├── users/                     # ユーザー関連コンポーネント
│   │   │   └── UserCard.vue           # ユーザーカード
│   │   └── products/                  # 商品関連コンポーネント
│   │       └── ProductList.vue        # 商品一覧
│   └── styles/                        # スタイル
│       ├── index.css                  # グローバルスタイル
│       └── theme.ts                   # テーマ設定
│
├── shared/                            # 再利用可能な型や定数
│   ├── types/                         # 共通型定義
│   │   ├── CommonTypes.ts             # 汎用型（例: ID, Nullable）
│   │   ├── PaginationTypes.ts         # ページング型
│   │   └── ApiResponseTypes.ts        # APIレスポンス共通型
│   ├── constants/                     # 定数
│   │   ├── apiEndpoints.ts            # APIエンドポイント
│   │   └── appConfig.ts               # アプリ設定
│   └── helpers/                       # 汎用ヘルパー関数
│       ├── formatDate.ts              # 日付フォーマット
│       └── logger.ts                  # ログ出力
│
└── utils/                             # 汎用的なユーティリティ関数
    ├── formatDate.ts                  # 日付フォーマット
    ├── logger.ts                      # ログ出力
    └── validators.ts                  # 入力バリデーション
```

### Android(Kotlin, JetPack compose, MVVM)
```
MyComposeApp/
├── app/
│   ├── src/
│   │   └── main/
│   │       ├── AndroidManifest.xml       # アプリのマニフェスト
│   │       ├── kotlin/com/example/mycomposeapp/
│   │       │   ├── 01_domain/            # ドメイン層（アプリケーションの中心）
│   │       │   │   ├── models/           # ビジネスモデル
│   │       │   │   │   └── User.kt
│   │       │   │   ├── usecases/         # ユースケース
│   │       │   │   │   └── GetUserUseCase.kt
│   │       │   │   └── repositories/     # リポジトリインターフェース
│   │       │   │       └── UserRepository.kt
│   │       │   ├── 02_application/       # アプリケーション層（ユースケース制御）
│   │       │   │   ├── services/         # アプリケーションサービス
│   │       │   │   └── types/            # アプリケーション層の型
│   │       │   ├── 03_infrastructure/    # インフラストラクチャ層（外部接続）
│   │       │   │   ├── data/             # データ層（インフラの一部）
│   │       │   │   │   ├── repositories/ # リポジトリ実装
│   │       │   │   │   │   └── UserRepositoryImpl.kt
│   │       │   │   │   ├── datasources/  # データソース（APIやDBアクセス）
│   │       │   │   │   │   └── UserApi.kt
│   │       │   │   │   ├── mappers/      # データ変換（DTO→モデル）
│   │       │   │   │   │   └── UserMapper.kt
│   │       │   │   │   └── models/       # データ層のモデル（DTOなど）
│   │       │   │   │       └── UserDto.kt
│   │       │   │   ├── network/          # ネットワーク設定
│   │       │   │   │   └── RetrofitClient.kt
│   │       │   │   ├── database/         # データベース設定(必要に応じて)
│   │       │   │   │   └── RoomDatabase.kt
│   │       │   │   └── di/               # 依存性注入
│   │       │   │       ├── RepositoryModule.kt
│   │       │   │       ├── NetworkModule.kt
│   │       │   │       └── DatabaseModule.kt
│   │       │   ├── 04_presentation/      # プレゼンテーション層（UI）
│   │       │   │   ├── screens/          # UI画面
│   │       │   │   │   ├── UserScreen.kt # ユーザー画面
│   │       │   │   │   ├── HomeScreen.kt # ホーム画面
│   │       │   │   │   └── components/   # 再利用可能なUIコンポーネント
│   │       │   │   │       └── UserCard.kt
│   │       │   │   ├── viewmodels/       # ViewModel（状態管理）
│   │       │   │   │   └── UserViewModel.kt
│   │       │   │   └── navigation/       # ナビゲーション
│   │       │   │       └── AppNavHost.kt
│   │       │   ├── utils/                # 汎用ユーティリティ
│   │       │   │   ├── Logger.kt
│   │       │   │   └── Validator.kt
│   │       │   └── constants/            # 定数
│   │       │       └── AppConstants.kt
│   │       ├── res/                      # リソース（Composeでは使用頻度が低い）
│   │       │   ├── layout/               # XMLレイアウト（必要な場合のみ）
│   │       │   │   └── activity_main.xml
│   │       │   ├── values/               # 定義リソース
│   │       │   │   ├── colors.xml
│   │       │   │   ├── dimens.xml
│   │       │   │   └── strings.xml
│   │       │   ├── drawable/             # 画像リソース
│   │       │   └── mipmap/               # アイコンリソース
│   │       └── test/                     # テストコード
│   │           ├── ExampleUnitTest.kt
│   │           └── ExampleInstrumentedTest.kt
|   └──  build.gradle(app)                # アプリのGradle設定
├── build.gradle(project)                 # プロジェクト全体のGradle設定
├── settings.gradle                       # Gradleの設定ファイル
├── gradlew                               # Gradleラッパースクリプト
├── gradlew.bat                           # Windows用Gradleラッパー
└── local.properties                      # SDKのパス情報
```

### Swift

```
MySwiftApp/
├── Domain/
│   ├── Entities/
│   │   └── User.swift
│   └── Repositories/
│       └── UserRepository.swift
├── Application/
│   ├── UseCases/
│   │   └── GetUserUseCase.swift
│   └── Services/
│       └── UserService.swift
├── Infrastructure/
│   ├── Config/                     # 設定ファイル関連
│   │   ├── ApiConfig.swift         # API設定
│   │   ├── AppConstants.swift      # アプリ全体で使う定数
│   │   ├── AppInfo.swift           # アプリのバージョンやビルド情報
│   │   ├── Secrets.xcconfig        # シークレット情報（git管理外）
│   │   └── Environment.swift       # ビルド環境（Debug/Release）ごとの設定
│   ├── Logging/                    # ロギング関連
│   │   └── Logger.swift
│   ├── Localization/               # ローカリゼーション関連
│   │   ├── Strings.swift           # Swiftから参照するローカライズキー
│   │   ├── en.lproj/
│   │   │   └── Localizable.strings # 英語用のローカライズ
│   │   └── ja.lproj/
│   │       └── Localizable.strings # 日本語用のローカライズ
│   ├── Data/                       # データ関連
│   │   ├── Repositories/
│   │   │   └── UserRepositoryImpl.swift
│   │   ├── Network/
│   │   │   └── ApiClient.swift
│   │   └── Models/
│   │       └── UserDto.swift
│   └── DI/                         # 依存性注入関連
│       └── DependencyInjector.swift
├── Presentation/
│   ├── ViewModels/
│   │   └── UserViewModel.swift
│   ├── Views/
│   │   ├── UserView.swift
│   │   └── Components/
│   │       └── UserCardView.swift
│   └── Styles/                     # デザインシステム関連
│       ├── AppColors.swift         # カラーテーマ
│       └── AppTypography.swift     # テキストスタイル
├── Tests/                          # テスト関連
│   ├── DomainTests/
│   │   └── GetUserUseCaseTests.swift
│   ├── ApplicationTests/
│   │   └── UserServiceTests.swift
│   ├── InfrastructureTests/
│   │   ├── UserRepositoryImplTests.swift
│   │   └── ApiClientTests.swift
│   └── PresentationTests/
│       └── UserViewModelTests.swift
└── Configurations/                 # ビルド設定
    ├── Debug.xcconfig
    ├── Release.xcconfig
    └── Staging.xcconfig

```

### Flutter

```
flutter_project/
├── android/
├── ios/
├── lib/
│   ├── 01_domain/              # 01_domain: エンティティ、バリューオブジェクト、タイプ
│   │   ├── entities/
│   │   ├── types/
│   │   └── value_objects/
│   ├── 02_application/         # 02_application: ユースケース、状態管理、hooksのような概念
│   │   ├── hooks/
│   │   ├── providers/
│   │   ├── state/
│   │   └── types/
│   │       └── auth_user_types.dart
│   ├── 03_infrastructure/      # 03_infrastructure: APIクライアント、外部サービスとの連携
│   │   ├── api_clients/
│   │   ├── external/
│   │   └── storage/
│   └── 04_presentation/        # 04_interface: UI、ページ、ウィジェット
│       ├── components/
│       │   ├── investment_property_card.dart
│       │   ├── investment_property_card_list.dart
│       ├── pages/              # Next.js の `app/` や、Railsのコントローラーの階層とそっくりにする
│       │   ├── about/
│       │   │   ├── about_page.dart
│       │   │   ├── about_layout.dart
│       │   │   └── about_view_model.dart
│       │   ├── admin/
│       │   │   ├── admin_page.dart
│       │   │   ├── admin_layout.dart
│       │   │   └── admin_view_model.dart
│       │   ├── contact/
│       │   │   ├── contact_page.dart
│       │   │   ├── contact_layout.dart
│       │   │   └── contact_view_model.dart
│       │   ├── faq/
│       │   ├── invest_properties/
│       │   │   ├── invest_property_detail_page.dart
│       │   │   ├── invest_property_detail_view_model.dart
│       │   │   └── invest_property_detail_layout.dart
│       │   ├── local_support/
│       │   ├── market/
│       │   ├── rent_properties/
│       │   │   ├── rent_property_detail_page.dart
│       │   │   ├── rent_property_detail_view_model.dart
│       │   │   └── rent_property_detail_layout.dart
│       │   ├── search/
│       │   └── user/
│       ├── shared_components/
│       │   ├── client/
│       │   │   └── client_google_map.dart
│       │   └── server/
│       ├── styles/
│       │   └── global_styles.dart
│       └── types/
│           ├── ui_component_types.dart
│           └── forms/
│               └── form_input_types.dart
├── test/                    # テストディレクトリ
├── pubspec.yaml             # Flutterのパッケージ管理ファイル
├── analysis_options.yaml     # Linterの設定
└── README.md
```


