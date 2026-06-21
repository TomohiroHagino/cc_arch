# DDDを意識した各フレームワークのファイル構成を考えてみる
https://qiita.com/MinoDriven/items/3c7db287e2c66f36589a
こちらの記事で勉強させていただきました

 ただのメモ書きです
 プロジェクトの最初の雛形として使ってます。

 フォルダの構造を統一させることで、言語を選ばず、チームメンバーが迷わない様に。
 いらない層はあとで消してしまう。


# Ruby on Rails

```
app/
├── 01_domain/
│   ├── sales/                              # コンテキストをこんな感じで分けていく
│   │   └── aggregates/
│   │       ├── shopping_cart/
│   │       │   ├── shopping_cart.rb        # アグリゲートルート
│   │       │   ├── entities/
│   │       │   │   ├── item.rb
│   │       │   │   └── discount.rb
│   │       │   ├── value_objects/
│   │       │   │   ├── product_id.rb
│   │       │   │   └── shopping_cart_money.rb
│   │       │   ├── services/
│   │       │   │   └── calculate_cart_total_service.rb
│   │       │   └── repositories/
│   │       │       ├── commands/
│   │       │       │   └── shopping_cart_command_repository.rb
│   │       │       └── queries/
│   │       │           └── shopping_cart_query_repository.rb
│   │       └── order/
│   │           ├── order.rb
│   │           ├── entities/
│   │           │   ├── order_item.rb
│   │           │   ├── shipping_detail.rb
│   │           │   └── payment_detail.rb
│   │           ├── value_objects/
│   │           │   ├── order_address.rb
│   │           │   └── tax_rate.rb
│   │           ├── services/
│   │           │   └── calculate_order_total_service.rb
│   │           └── repositories/
│   │               ├── commands/
│   │               │   └── order_command_repository.rb
│   │               └── queries/
│   │                   └── order_query_repository.rb
│   ├── identity/                           # コンテキスト
│   │   └── aggregates/
│   │       └── user/
│   │           ├── user.rb
│   │           ├── entities/
│   │           │   ├── user_profile.rb
│   │           │   └── user_preferences.rb
│   │           ├── value_objects/
│   │           │   ├── user_email.rb
│   │           │   ├── password.rb
│   │           │   └── user_address.rb
│   │           ├── services/
│   │           │   └── password_encryption_service.rb
│   │           └── repositories/
│   │               ├── commands/
│   │               │   └── user_command_repository.rb
│   │               └── queries/
│   │                   └── user_query_repository.rb
│   └── shared/            # コンテキストをまたぐ共有（分けない）
│       ├── value_objects/
│       │   └── address.rb
│       └── services/
│           ├── tax_calculator_service.rb
│           └── shipping_cost_service.rb
│
├── 02_use_cases/
│   ├── sales/
│   │   ├── place_order.rb
│   │   └── confirm_order.rb
│   └── identity/
│       ├── web/
│       │   ├── register_user.rb
│       │   ├── ban_user.rb
│       │   └── withdraw_user.rb
│       └── mobile/
│           ├── register_user.rb
│           ├── ban_user.rb
│           └── withdraw_user.rb
│
├── 03_services/      #（汎用処理。メール・通知等）横断（文脈で分けない）
│   ├── api/
│   │   └── email_service.rb
│   ├── wapi/
│   │   └── email_service.rb
│   ├── admin/
│   │   └── email_service.rb
│   └── common/
│       ├── email_service.rb
│       └── notification_service.rb
│
├── 04_infrastructure/
│   ├── sales/                              # インフラも文脈ごと
│   │   ├── repositories/
│   │   │   ├── commands/
│   │   │   │   └── active_record_order_command_repository.rb
│   │   │   └── queries/
│   │   │       └── active_record_order_query_repository.rb
│   │   └── models/
│   │       ├── order.rb
│   │       ├── order_item.rb
│   │       └── cart.rb
│   ├── identity/
│   │   ├── repositories/
│   │   │   ├── commands/
│   │   │   │   └── active_record_user_command_repository.rb
│   │   │   └── queries/
│   │   │       └── active_record_user_query_repository.rb
│   │   └── models/
│   │       └── user.rb
│   └── external_apis/                      # 横断
│       └── payment_gateway_api.rb
│
└── 05_interfaces/                          # 横断（HTTPの出入口は1つ）
    ├── controllers/
    │   ├── api/
    │   │   └── v1/
    │   │       └── users_controller.rb
    │   ├── wapi/
    │   │   └── v1/
    │   │       └── users_controller.rb
    │   └── admin/
    │       └── users_controller.rb
    ├── views/
    │   └── users/
    │       └── index.html.erb
    ├── presenters/
    │   └── user_presenter.rb
    ├── serializers/
    │   └── user_serializer.rb
    ├── assets/
    └── javascripts/
```

---

## spec/ ツリー（A適用・app の完全な鏡）

```
spec/
├── 01_domain/
│   ├── sales/
│   │   └── aggregates/
│   │       ├── shopping_cart/
│   │       │   ├── shopping_cart_spec.rb
│   │       │   ├── entities/
│   │       │   │   ├── item_spec.rb
│   │       │   │   └── discount_spec.rb
│   │       │   ├── value_objects/
│   │       │   │   ├── product_id_spec.rb
│   │       │   │   └── shopping_cart_money_spec.rb
│   │       │   └── services/
│   │       │       └── calculate_cart_total_service_spec.rb
│   │       └── order/
│   │           ├── order_spec.rb
│   │           ├── entities/
│   │           │   ├── order_item_spec.rb
│   │           │   ├── shipping_detail_spec.rb
│   │           │   └── payment_detail_spec.rb
│   │           ├── value_objects/
│   │           │   ├── order_address_spec.rb
│   │           │   └── tax_rate_spec.rb
│   │           └── services/
│   │               └── calculate_order_total_service_spec.rb
│   ├── identity/
│   │   └── aggregates/
│   │       └── user/
│   │           ├── user_spec.rb
│   │           ├── entities/
│   │           │   ├── user_profile_spec.rb
│   │           │   └── user_preferences_spec.rb
│   │           ├── value_objects/
│   │           │   ├── user_email_spec.rb
│   │           │   ├── password_spec.rb
│   │           │   └── user_address_spec.rb
│   │           └── services/
│   │               └── password_encryption_service_spec.rb
│   └── shared/
│       ├── value_objects/
│       │   ├── money_spec.rb
│       │   └── address_spec.rb
│       └── services/
│           ├── tax_calculator_service_spec.rb
│           └── shipping_cost_service_spec.rb
│
├── 02_use_cases/
│   ├── sales/
│   │   └── place_order_spec.rb
│   └── identity/
│       ├── web/
│       │   ├── register_user_spec.rb
│       │   ├── ban_user_spec.rb
│       │   └── withdraw_user_spec.rb
│       └── mobile/
│           ├── register_user_spec.rb
│           ├── ban_user_spec.rb
│           └── withdraw_user_spec.rb
│
├── 03_services/
│   ├── api/
│   │   └── email_service_spec.rb
│   ├── wapi/
│   │   └── email_service_spec.rb
│   ├── admin/
│   │   └── email_service_spec.rb
│   └── common/
│       ├── email_service_spec.rb
│       └── notification_service_spec.rb
│
├── 04_infrastructure/
│   ├── sales/
│   │   └── repositories/
│   │       ├── commands/
│   │       │   └── active_record_order_command_repository_spec.rb
│   │       └── queries/
│   │           └── active_record_order_query_repository_spec.rb
│   ├── identity/
│   │   └── repositories/
│   │       ├── commands/
│   │       │   └── active_record_user_command_repository_spec.rb
│   │       └── queries/
│   │           └── active_record_user_query_repository_spec.rb
│   └── external_apis/
│       └── payment_gateway_api_spec.rb
│
└── 05_interfaces/
    ├── requests/
    │   ├── api/
    │   │   └── v1/
    │   │       └── users_controller_request_spec.rb
    │   ├── wapi/
    │   │   └── v1/
    │   │       └── users_controller_request_spec.rb
    │   └── admin/
    │       └── users_controller_request_spec.rb
    ├── system/
    │   └── users/
    │       └── index_spec.rb
    ├── presenters/
    │   └── user_presenter_spec.rb
    └── serializers/
        └── user_serializer_spec.rb
```
### 問題：このツリーは“素のRails”では動かない
  `app/01_domain/...` は2つの理由でそのままだと破綻する：
  1. **`01_domain` は数字始まり** → Rubyのモジュール名にできない（`Domain`
  名前空間が自動で生まれない）
  2. **構造フォルダ（aggregates / value_objects / entities / services / repositories /
  commands / queries）まで名前空間に入ってしまう**
     設定なしだと
     `app/01_domain/identity/aggregates/user/value_objects/user_email.rb`
     は **`Identity::Aggregates::User::ValueObjects::UserEmail`** を要求される（醜い）。
     欲しいのは **`Domain::Identity::User::UserEmail`**
     ＝「**層名(Domain)・文脈(Identity)・集約(User)**

### 解決：Zeitwerkに「名前空間」と「collapse」を教える
```ruby
  # config/initializers/zeitwerk.rb
  module Domain; end
  module UseCases; end
  module AppServices; end
  module Infrastructure; end
  module Interfaces; end

  main = Rails.autoloaders.main

  # ① 数字始まりルート → 層の名前空間
  main.push_dir(Rails.root.join("app/01_domain"),         namespace: Domain)
  main.push_dir(Rails.root.join("app/02_use_cases"),      namespace: UseCases)
  main.push_dir(Rails.root.join("app/03_services"),       namespace: AppServices)
  main.push_dir(Rails.root.join("app/04_infrastructure"), namespace: Infrastructure)
  main.push_dir(Rails.root.join("app/05_interfaces"),     namespace: Interfaces)

  # ② 構造フォルダ
  # domain：  <context>/aggregates/<aggregate>/{構造}
  main.collapse("app/01_domain/*/aggregates")
  main.collapse("app/01_domain/*/aggregates/*/value_objects")
  main.collapse("app/01_domain/*/aggregates/*/entities")
  main.collapse("app/01_domain/*/aggregates/*/services")
  main.collapse("app/01_domain/*/aggregates/*/repositories")
  main.collapse("app/01_domain/*/aggregates/*/repositories/commands")
  main.collapse("app/01_domain/*/aggregates/*/repositories/queries")
  main.collapse("app/01_domain/shared/value_objects")
  main.collapse("app/01_domain/shared/services")

  # infrastructure： <context>/repositories/{commands,queries}
  main.collapse("app/04_infrastructure/*/repositories")
  main.collapse("app/04_infrastructure/*/repositories/commands")
  main.collapse("app/04_infrastructure/*/repositories/queries")
```

## ルール（覚えるのはこれだけ）

> **名前空間に残るのは「層名・文脈名(sales/identity)・集約名・shared・models」だけ。**
> **構造フォルダ（aggregates / value_objects / entities / services / repositories / commands / queries）は collapse して消す。**

## 対応表（A適用・最終形）

| ファイルパス | モジュール／クラス |
|---|---|
| `01_domain/identity/aggregates/user/value_objects/user_email.rb` | `Domain::Identity::User::UserEmail` |
| `01_domain/sales/aggregates/shopping_cart/shopping_cart.rb` | `Domain::Sales::ShoppingCart::ShoppingCart` |
| `01_domain/sales/aggregates/order/entities/order_item.rb` | `Domain::Sales::Order::OrderItem` |
| `01_domain/identity/aggregates/user/repositories/commands/user_command_repository.rb` | `Domain::Identity::User::UserCommandRepository` |
| `01_domain/shared/value_objects/address.rb` | `Domain::Shared::Address` |
| `04_infrastructure/identity/repositories/commands/active_record_user_command_repository.rb` | `Infrastructure::Identity::ActiveRecordUserCommandRepository` |
| `04_infrastructure/identity/models/user.rb` | `Infrastructure::Identity::Models::User` |
| `04_infrastructure/external_apis/payment_gateway_api.rb` | `Infrastructure::ExternalApis::PaymentGatewayApi` |
| `02_use_cases/identity/web/register_user.rb` | `UseCases::Identity::Web::RegisterUser` |

> 🪙 **トレードオフ（正直に）**：この設定は重い。嫌なら**フォルダに数字を付けない／構造を浅くする**手もある。
> 「番号付き・深いネスト」は人間には読みやすいが、**Zeitwerk設定とのセット**で初めて成立する、と覚えておく。
> 以降の全ファイルのモジュール名は、**この設定が入っている前提**で書く。

---

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

### Next.js
（App Routerで。基本バックエンドからapiで情報を受け取るのでドメイン層は必要最低限。 openapi使うともっと楽かも）
（今の所、妄想。実際はインターフェース層ばっかりぶ厚くなるし、Apiから値を受け取るだけで済んでる。）
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
│   ├── app/                           # App Routerディレクトリ（appディレクトリだけルートに移したほうがわかりやすいかもしれない。）
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
│   ├── pages/                         # ページコンポーネント（pagesのところだけ、ルートディレクトリにもっていったほうがわかりやすいかも。）
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


