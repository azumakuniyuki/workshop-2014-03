# リファレンス

## リファレンスとは
リファレンスとは参照です。
リファレンスを使うと複雑なデータ構造を表現出来たりします。

## どういうこと ?
例えば、リファレンスを使うとハッシュの中にハッシュを格納し、更にそのハッシュにハッシュを格納する……みたいな複雑なデータ構造を表現出来ます。

## 具体例 (1/3)
         |     0    |     1    |
    -----+----------+----------+
      0  |  nqounet | boolfool |
    -----+----------+----------+
      1  |  macopy  |  papix   |
    -----+----------+----------+

テーブルのようなデータ構造を表現したいとき

## 具体例 (2/3)
    my @osaka = ('nqounet', 'boolfool');
    my @tokyo = ('macopy', 'papix');
    my @perl_entrance = (@osaka, @tokyo);
    print $perl_entrance[1][1]; # => これはおかしい。エラーになる。

今までの知識であれば、配列を用いて<b>行</b>単位で表現できるが、`n 行 m 列目は papix` という様に表現したいですね。

## 具体例 (3/3)
    my $osaka = ['nqounet', 'boolfool'];
    my $tokyo = ['macopy', 'papix'];
    my @perl_entrance = ($osaka, $tokyo);
    print $perl_entrance[1][1]; # => papix

このように <b>リファレンス</b> を用いることで、複雑なデータ構造を表現することが容易になります。

## あとは
関数の引数に複数の配列を渡す必要がある場合などに使います。
また、関数に引数として配列やハッシュを渡すとそれらをコピー (クローン) してしまうので、非効率になる場合があります。
リファレンスはコピーするのではなく参照を渡すだけなので、その問題を回避することができます。
(これらはまた後で出てきます)

## リファレンスの例
    my $scalar     = 'scalar';
    my $scalar_ref = \$scalar   # スカラーのリファレンス;
    my @array      = ( 'this', 'is', 'array' );
    my $array_ref  = \@array;   # 配列のリファレンス
    my %hash       = ( this_is => 'hash' );
    my $hash_ref   = \%hash;    # ハッシュのリファレンス

それぞれのシジルの前にバックスラッシュ (\\) を置くことによってそれぞれリファレンスになります

## 簡単な書き方 (配列)
    my $array_ref = ['This', 'is', 'array-ref'];

普通のかっこ () ではなく角かっこ [] によって要素をくくってやることで、配列のリファレンスになります

## 簡単な書き方 (ハッシュ)
    my $hash_ref = {
        this_is => 'hash_ref'
    };

普通のかっこ () ではなく波括弧 {} によってくくってやることで、ハッシュのリファレンスになります

## さて、ここで print してみましょう
    my $array_ref = ['This', 'is', 'array-ref'];
    print $array_ref;

`ARRAY(0x7fd8938060b8)` みたいな出力が得られると思います (括弧の中身は実行環境によって異なります)
スカラーリファレンスの場合はSCALAR()、ハッシュリファレンスの場合はHASH() とそれぞれ表示されます

## これはなに
最初に言ったように、リファレンスは参照です。
SCALAR() や ARRAY()、HASH() の括弧の中身に書いてあるのはその実体が格納されているアドレスです。

## アドレスではなく中身が欲しいんだけど ?
そこで出てくるのが“デリファレンス”です

## デリファレンスとは
デリファレンスから、元のスカラーや配列、ハッシュを得るための操作です。

## デリファレンスの例
    my $scalar     = 'scalar';
    my $scalar_ref = \$scalar;
    print $$scalar_ref; #<= アドレスではなく“scalar”が表示されます
    my $array_ref  = ["I'm", "scalar-ref"];
    print @$array_ref;  #<= アドレスではなく配列の中身が展開されます
    my $hash_ref   = { this_is => 'hash-ref' };
    print %$hash_ref;

## デリファレンス (スカラー)
    my $scalar     = 'scalar';
    my $scalar_ref = \$scalar;
    print $$scalar;

スカラーをデリファレンスするためには、そのリファレンスの前にスカラーのシジル ($) を置きます。

## デリファレンス (配列)
    my $array_ref = ['zero', 'one', 'two'];
    print @$array_ref;      # <= 配列の中身が展開されます (zeroonetwo)
    print ${$array_ref}[1]; # <= 'one'
    print $array_ref->[1];  # <= 上と同じ。こう書くのがベター

配列をデリファレンスするためには、そのリファレンスの前に配列のシジル (@) を置きます。
配列の要素に対してデリファレンスしたい場合 (配列リファレンスから要素を取り出したい場合)、
配列リファレンスをスカラーとしてデリファレンスして波括弧でくくり、通常の配列のようにアクセスすると内容を得られます。
また、中括弧と$を取り除き、その代わりにアロー (->) によりアクセスする事もできます。

## デリファレンス (ハッシュ)
    my $hash_ref = { key => 'value' };
    print %$hash_ref;        # <= ハッシュの中身が展開される (keyvalue)
    print ${$hash_ref}{key}; # <= 'value'
    print $hash_ref->{key};  # <= 上と同じ。こう書くのがベター

ハッシュをデリファレンスするためには、そのリファレンスの前にハッシュのシジル (%) を置きます。
ハッシュの要素に対してデリファレンスしたい場合、ハッシュリファレンスをスカラーとして
デリファレンスして波括弧でくくり、通常のハッシュのようにアクセスすると内容を得られます。
また配列同様、中括弧と$を取り除きその代わりにアロー (->) によりアクセスする事もできます。

## ダンプ再び
    use Data::Dumper;
    my $hash_ref = { key => 'value' };
    print $hash_ref; # => HASH(0x7f88d08060b8)
    print Dumper($hash_ref);
    # $VAR1 = {
    #           'key' => 'value'
    #         };

中身を確認するのにデリファレンスをするのは面倒なので、`Data::Dumper` を用いてダンプすると良いでしょう。

## 練習問題
    my $papix = {
        name    => 'papix',
        address => 'Tokyo',
        age     => 25,
    };

上記のような名前・住所・年齢といった要素を持つリファレンスを作成してみよう。さらに `Data::Dumper` を用いて、リファレンスを出力してみよう。

## リファレンスのはまりどころ
    my $scalar     = 'scalar';
    my $scalar_ref = \$scalar;
    print $scalar; # => 'scalar'
    $$scalar_ref = 'changed!';
    print $scalar; # => 'changed!'

リファレンスは参照なので、デリファレンスして更にその中身を変更すると、参照元が破壊されます

## 練習問題
[score.pl](https://github.com/perl-entrance-org/workshop-2014-03/blob/master/code/score.pl) には上記のようなハッシュリファレンスがいくつか宣言してあります。

1. それぞれの人物の`perl`、`ruby`、`python` ... といった分野毎の合計値をそれぞれのリファレンスに追加しましょう
2. `@people` や `@languages` と言った変数に各々のリファレンスや言語などを格納しておくとやりやすいでしょう

## 練習問題
[practice.md](https://github.com/perl-entrance-org/workshop-2014-03/blob/master/practice.md) には練習問題の続きがあるので、チャレンジしてみて下さい。
