# クリーンアーキテクチャを意識したRailsのファイル構成
#### こんな感じになったらとてもスッキリする
```
app/
├── domain/                      # ドメイン層
│   ├── entities/                # エンティティ
│   │   └── user.rb
│   └── repositories/            # リポジトリ
│       └── user_repository.rb
├── use_cases/                   # ユースケース層
│   ├── users/
│   │   ├── create_user.rb           # ユースケース: ユーザー作成
│   │   ├── update_user.rb           # ユースケース: ユーザー更新
│   │   ├── delete_user.rb           # ユースケース: ユーザー削除
│   │   └── get_user.rb              # ユースケース: ユーザー取得
│   └── services/                    # サービス層 (汎用的な処理)
│       ├── email_service.rb         # メール送信サービス
│       ├── payment_service.rb       # 支払いサービス
│       └── notification_service.rb  # 通知サービス
├── infrastructure/              # フレームワーク & ドライバ層
│   ├── repositories/            # リポジトリ実装
│   │   └── active_record_user_repository.rb
│   └── external_apis/           # 外部APIとの連携
│       └── payment_gateway_api.rb
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
    └── serializers/
```

```
spec/
├── domain/                   # ドメイン層に関するテスト
│   ├── entities/             # ドメインエンティティのテスト
│   │   └── user_spec.rb
│   └── repositories/         # リポジトリインターフェースのテスト
│       └── user_repository_spec.rb
├── use_cases/                # ユースケース層のテスト
│   └── users/
│       └── create_user_spec.rb
├── infrastructure/           # インフラストラクチャ層のテスト
│   ├── repositories/         # リポジトリ実装のテスト (ActiveRecordなど)
│   │   └── active_record_user_repository_spec.rb
│   └── external_apis/        # 外部API連携のテスト
│       └── payment_gateway_api_spec.rb
├── interfaces/               # インターフェース層のテスト
│   ├── requests/          # コントローラのリクエストスペック
│   │   ├── api/
│   │   │   └── users_controller_request_spec.rb
│   │   ├── admin/
│   │   │   └── users_controller_request_spec.rb
│   │   └── wapi/
│   │       └── users_controller_request_spec.rb
│   └── serializers/          # シリアライザのテスト
│       └── user_serializer_spec.rb
└── support/                  # テストのヘルパーモジュールや共通設定
    ├── factories/            # FactoryBotのファクトリ
    │   └── users.rb
    ├── shared_examples/      # 共通のテストケース
    │   └── user_repository_examples.rb
    └── helpers/              # カスタムヘルパー
        └── api_helper.rb
```
