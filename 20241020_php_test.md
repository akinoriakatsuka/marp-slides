---
marp: true
theme: gaia
class: invert
---
# <br>PHPでパッケージを使わずに<br>自動テストをするためのアイデア

あかつか
CI／CD・自動化勉強会 in 神戸  2024/10/20 

`#関ガレ`

---
## 自己紹介

- 赤塚
- 神戸のPHPer
- 最近はスクラムに興味があります

---
## おことわり

- composerが使えるならPHPUnitやPestを使うことをおすすめします
  - `composer require --dev` でインストールしましょう
  - プロダクション環境には入れないように！

---
## モチベーション

- composerがない環境でも自動テストが書きたい
  - 色々あってパッケージ入れらない環境…あるよね
    - （心の声：あってたまるか！）
- とはいえ、制約のある中で実現できる方法を考えることは、<br>本質の理解につながる
  - 本質：何が重要であって、何がそうでないのか

---
## サンプルプロジェクト

```
.
├── app
│   └── Models
│       └── Post.php
├── lib
│   └── functions.php
└── public
     └── index.php
```

- クラスっぽいものがあったり、関数の集まりのようなファイルがあったり、生のPHPで書かれたファイルがあったり
  
---
## 自動テストで大事なこと

- 簡単に実行できる
- 結果が一目でわかる

それ以外は、ぶっちゃけ後でもよい
自動テストを実行できる環境ができれば、新規に開発する機能でテスト駆動開発を始めることもできる。

- とはいえ、テストは書き溜めると負債にもなる
- PHPUnitなどのスタンダードなものに移行できるなら早い方が良い

---
## テストコードその１（テスト対象）

```php
<?php

/**
 * FizzBuzz function
 * @param int $number
 * @return string
 */
function fizzBuzz($number)
{
    return ($number % 3 === 0 ? 'Fizz' : '') . ($number % 5 === 0 ? 'Buzz' : '') ?: (string) $number;
}
```

---
## テストコードその１（テスト）
```php
<?php

require __DIR__ . '/../lib/functions.php';

$expected = 'FizzBuzz'; // OK
// $expected = 'Fizz'; // NG
$actual = fizzBuzz(15);

try {
    assert($actual === $expected, 'Expected: ' . $expected . ' but got: ' . $actual . "\n");
    echo "\033[32mOK!\n\033[0m";
} catch (AssertionError $e) {
    echo "\033[31mNG!\n\033[0m";
    echo $e->getMessage();
    exit(1);
}
```

---
## テストコードその２（テスト対象）

```php
<?php

class Post
{
    public function __construct(
        private int $id,
        private string $title,
        private string $body
    ) {}

    public function getId(): int
    {
        return $this->id;
    }
}
```

---
## テストコードその２（テスト）
```php
<?php

require __DIR__ . '/../app/Models/Post.php';

$post = new Post(1, 'First post', 'This is the first post.');

$expected = 1; // OK
// $expected = 2; // NG
$actual = $post->getId();

try {
    assert($actual === $expected, 'Expected: ' . $expected . ' but got: ' . $actual . "\n");
    echo "\033[32mOK!\n\033[0m";
} catch (AssertionError $e) {
    echo "\033[31mNG!\n\033[0m";
    echo $e->getMessage();
    exit(1);
}
```
---
## テストコードその３（テスト対象）

```php
<html>

<?php
echo 'Hello, World!';
?>

</html>
```

古き良き埋め込みPHP

---
## テストコードその３（テスト）
```php
<?php

$expected = 'Hello, World!';

ob_start();
require __DIR__ . '/../../public/index.php';
$actual = ob_get_contents();
ob_end_clean();

try {
    // Hello, World! を含んでいることをassertする
    assert(str_contains($actual, $expected), "Expected: " . $actual . ' contains: ' . $expected . "\n");
    echo "\033[32mOK!\n\033[0m";
} catch (AssertionError $e) {
    echo "\033[31mNG!\n\033[0m";
    echo $e->getMessage();
}
```

（流石にちょっと厳しい。普通にplaywrightとか使った方が良いかも）

---
## まとめと所感

- composerが使えなくてもテストコードは書ける
- テストコードの本質は、早く実行できて、結果が一目でわかること
  - たしか、Martin Fowlerもそう言ってた
- 疎結合でないとテストが書きにくいのは、PHPUnitでも手作りテストでも同じ

---
## 時間余ったらデモします

---
## ご清聴ありがとうございました