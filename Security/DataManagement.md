# 機密情報管理

基本的な事項として、パスワードのような機密情報を平文で扱うことはNG。
常になんらかの変換処理を行った上で扱うようにすること。

## パスワード変換処理

パスワード変換処理は主に以下の3つとなる。
どれも平文よりははるかに優れていると言える。

- Encoding（機密情報には使用しない）
  - あるデータから別のデータへと安全に変換するものであるが、セキュリティとは関係なし
  - エンコードされた文字を簡単にデコード可能（つまり暗号化メカニズムではない）
  - 例えば以下
    - ASCII
    - BASE64
    - UNICODE
- Encryption（暗号化に使用するものだが、パスワード管理には推奨されない）
  - あるデータから別のデータへと安全に変換するもの。エンコードとは異なり、機密性を保持している。
  - 一般的には公開されないキーを使用して復号を行う
  - 機密情報とキーの双方が取得されない限り復号化はできない仕組みとなっている
    - つまりどちらかが漏洩した時点で危険度が上がってしまう
  - Webアプリケーション内で暗号化が必要な場合などに使用される
- Hashing（パスワード管理に推奨される）
  - いくつかのハッシュアルゴリズム関数を使用して元データをハッシュ値に変換する
  - ハッシュ化されたデータには可逆性がなく、基本的には復号不可能
  - 外部から渡されたデータを同様のアルゴリズムでハッシュ化し、DBのハッシュデータと比較して認証を行う