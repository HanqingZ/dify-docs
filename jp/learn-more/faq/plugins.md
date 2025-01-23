# プラグイン

> 以下の問題と解決方法は、`1.0.0` コミュニティエディションに適用されます。

### プラグインインストール時のエラーの対処方法

**問題**: `plugin verification has been enabled, and the plugin you want to install has a bad signature` というエラーメッセージが表示された場合、どのように対処すればよいですか？

**解決方法**: `.env` 設定ファイルの末尾に以下の行を追加してください：  
`FORCE_VERIFYING_SIGNATURE=false`

このフィールドを追加すると、Dify プラットフォームは Dify Marketplace にリストされていない（つまり、未検証の）すべてのプラグインのインストールを許可します。

ただし、安全性を考慮して、未知のソースから提供されるプラグインは、テスト環境またはサンドボックス環境でまずインストールし、安全性を確認した後、本番環境にデプロイしてください。