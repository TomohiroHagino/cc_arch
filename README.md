# クリーンアーキテクチャを意識したRailsのファイル構成
#### こんな感じになったらとてもスッキリする
```
app/
├── domain/                      # ドメイン層
│   ├── entities/                # エンティティ
│   │   └── user.rb
│   └── repositories/            # リポジトリ
│       └── user_repository.rb
│
├── use_cases/                   # ユースケース層
│   ├── users/
│   │   ├── create_user.rb           # ユースケース: ユーザー作成
│   │   ├── update_user.rb           # ユースケース: ユーザー更新
│   │   ├── delete_user.rb           # ユースケース: ユーザー削除
│   │   └── get_user.rb              # ユースケース: ユーザー取得
│   └── services/                    # サービス層 (汎用的な処理)
│　　     ├── api/
│　　     │    └── ...
│　　     ├── wapi/
│　　     │    └── ...
│　　     ├── admin/
│　　     │    └── email_service.rb         # （管理者が使用するための）メール送信サービス
│　　     └── share
│            　├── email_service.rb         # メール送信サービス
│            　├── payment_service.rb       # 支払いサービス
│            　└── notification_service.rb  # 通知サービス
│
├── infrastructure/              # フレームワーク & ドライバ層
│   ├── repositories/            # リポジトリ実装
│   │   └── active_record_user_repository.rb
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
│       └── user_repository_spec.rb
│
├── use_cases/                # ユースケース層のテスト
│   └── users/
│       └── create_user_spec.rb
│
├── infrastructure/           # インフラストラクチャ層のテスト
│   ├── repositories/         # リポジトリ実装のテスト (ActiveRecordなど)
│   │   └── active_record_user_repository_spec.rb
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
│       │       ├── user_service.py         # ユーザー関連のドメインサービス
│       │       ├── product_service.py      # 商品関連のドメインサービス
│       │       ├── order_service.py        # 注文関連のドメインサービス
│       │       └── __init__.py
│       ├── infrastructure/
│       │   ├── models.py
│       │   ├── serializers.py
│       │   ├── views.py
│       │   └── admin.py
│       ├── application/
│       │   ├── use_cases/
│       │   │   ├── create_something.py
│       │   │   ├── update_something.py
│       │   │   └── ...
│       │   ├── services/
│       │   │   ├── notification_service.py  # 通知に関するアプリケーションサービス
│       │   │   ├── report_service.py        # レポート生成サービス
│       │   │   ├── logging_service.py       # ロギングサービス
│       │   │   └── __init__.py
│       │   └── __init__.py
│       ├── presentation/
│       │   ├── controllers/                 # api
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
│       └── tests/
│           ├── domain/
│           ├── infrastructure/
│           ├── application/
│           └── presentation/
├── project/
│   ├── settings.py
│   ├── urls.py
│   ├── wsgi.py
│   └── asgi.py
└── manage.py
```
