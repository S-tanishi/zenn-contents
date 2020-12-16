---
title: "Seeder Factory Validation"
emoji: "🐈"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [laravel]
published: false
---
### 違い
Seederはデータベースにデータを（一括）挿入する仕組み。
FactoryはSeederにデータを与える仕組み（通常、Seederの中で呼び出す）。

Factoryが複数件のダミーデータを生成する用に存在することを考えれば、Seeder自体ではテーブルに存在してほしいユーザーや情報を明示的に指定するのが妥当でしょうかね。
### Seeder

### Factory
Fakerが使われる
テスト用データを作成してくれるPHPライブラリ
Fakerは、そのままだと英語っぽい単語などがでてきちゃいますが、設定により日本語対応可能です
config/app.phpに追記
'faker_locale' => env('APP_FAKER_LOCALE', 'ja_JP'),

まずはインストール
```
composer require fzaninotto/faker
```
factoys作成は
```
docker-compose exec app php artisan make:factory --model=model名
```

Laravel8になってから、globalなfactory()関数は削除されました。その為、モデルのFactoryクラスを使います。

1.該当するモデルファイルに、『Illuminate\Database\Eloquent\Factories\HasFactoryがインポートされているかを確認すること。

2.factory側に該当するモデルをインポート、return句を追記

factory(App\models\User::class)
→ User::factory

3 seederに追記
\App\Models\Tweet::factory(10)->create(); //←ここ追記

### error
SQLSTATE[23000]: Integrity constraint violation: 1062
 
 Integrity constraint violation => 整合性規約違反
 
 基本的には定義には当てはめられない挙動を実行しようとした場合に起こるエラー
 
Duplicate entry ‘1’ for key ‘PRIMARY’

「Duplicate entry ‘1’ for key ‘PRIMARY’」エラーは、データベース（テーブル）に入力しようとしたデータの主キー（idフィールドなど）がすでに使われていることを示しています（重複不可のため）。

### バリデーション
```
php artisan make:request Xxx
```
app/Http/Requests/Xxx.php にて作成。また
```php:Xxx
public function authorize()
{
    return false; // trueに変更することで使用可能に
}
```
その他、attributes()
rules(),messages()などのメゾット内を編集

使い方は，使用するコントローラなどで
```php:ExampleController
use App/Http/Requests/TestRequest;

class ExampleController extends Controller
{
    public function testMethod(Xxx $request) {
        // 処理 
    }
}

https://qiita.com/OKCH3COOH/items/53db8780027e5e11be82