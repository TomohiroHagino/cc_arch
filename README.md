# 理想的だと思うクリーンアーキテクチャを意識したRailsのファイル構成

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
├── presenters/                  # プレゼンター層
│   └── user_presenter.rb        # ユーザー表示フォーマット
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
    └── serializers/
```
