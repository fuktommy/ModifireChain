ModifireChain
=============

はじめに
--------
[Smarty](http://www.smarty.net/manual/ja/) では

1. 文字列を先頭から100文字にカットする
2. 英数字を半角にする
3. HTMLエスケープ処理をする
4. 改行コードをbrタグに変換する
5. 表示

(この例は [エクスギア技術系サイト PHP5限定　CakePHPのView内の関数処理を綺麗に記述する（邪道でしょうか？）](http://www.exgear.jp/tech/doc/detail/85) から引用しました)を

    {$text|mb_substr:0:100|mb_convert_kana:"a"|escape|nl2br}

のように書くことができます。 これを素のPHPでやると、

    <?php echo nl2br(htmlspecialchars(mb_convert_kana(mb_substr($text, 0, 100), 'a'))); ?>

となります(このコードも引用しました)。
Smartyやそのほかのテンプレートエンジンを使うほど大袈裟じゃないけど、
こんなことがしたいな、というときに使えるものを作りました。
ModifireChain では

    <?php $chain->text->mb_substr(0, 100)->mb_convert_kana('a')->escape()->nl2br()->p(); ?>

と書くことができます。

使い方
------

    // PHPロジック部分
    require_once 'ModifireChain.php';
    $chain = ModifireChain::factory();
    
    // 任意のフィールドに値を設定できます。
    $chain->text = 'abc';
    $chain->array = array('a', 'b', 'c');
    $chain->assoc = array('a' => array('b' => 'c'));
    
    $obj = new StdClass();
    $obj->foo = 'bar';
    $chain->obj = $obj;
    
    // テンプレート部分
    $chain->text->e();       // HTMLエスケープして表示
    $chain->text->e('url');  // URLエンコードして表示
    $chain->text->escape()->nl2br()->p();  // p()はエスケープなしの表示
    $chain->text->stringFormat('%10s')->e();  // Smarty風のメソッドがいくつかあります。

    // 配列も扱えます。
    // ただしキーは個々にpack()する必要があります。
    <?php foreach ($chain->array as $k => $v): ?>
      <li><?php $chain->pack($k)->e(); ?>: <?php $v->e(); ?></li>
    <?php endforeach; ?>
    
    // 配列のキーを指定する方法
    $chain->assoc->get('a')->get('b')->e();
    $chain->assoc->a->b->e();
    
    // オブジェクトの場合
    $chain->obj->prop('foo')->e();
    $chain->obj->foo->e();
    
    // 中身を取り出すとき
    echo $chain->unpack();
