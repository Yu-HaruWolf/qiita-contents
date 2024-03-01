---
title: Javaで頻繁に変更される文字列を扱うならStringBuilder
tags:
  - Java
  - StringBuilder
private: false
updated_at: '2024-03-01T18:37:47+09:00'
id: f3b8baed3dd05087cd27
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに
著者は最近leetcodeでプログラミングの問題を解いているのですが、実行時間をしっかりと気にしたほうが良いなと感じてきました。
もちろん問題がクリアできているかで言えばできてはいるのですが、それだと実践では良くないシチュエーションもあると思うので...
一番見落としがちなのがStringの変更だと感じたので今回記事にしました。
記事の中に含まれるコードではメソッドやクラスが省略されています。省略されているものは全部メインメソッドで実行されていると思ってもらって大丈夫です。

# Stringで文字列を更新する
至ってシンプルに書けるので、最初に学ぶ際にはシンプルで分かりやすいですね。
```Java
String s = "Good morning!";
s = "Good afternoon!";
s += " Yu-HaruWolf!";
System.out.println(s); // Good afternoon! Yu-HaruWolf!
```
実際このぐらいの変更しかないのであれば、まぁこれでも大丈夫かなという感じではあると思います。
問題は、大量に変更が加えられることがわかっている場合です。
試しにこんな感じのコードを作って実行してみました。
```Java
long start, end;
{
    start = System.currentTimeMillis();
    String string = "";
    for (int i = 0; i < 100000; i++) {
        string += "a";
    }
    System.out.println(string);
    end = System.currentTimeMillis();
}
System.out.println(end - start + "ms");
System.out.println(Runtime.getRuntime().totalMemory() - Runtime.getRuntime().freeMemory());
```
結果がこんな感じ。
```
aaaaaaaaaa...(省略)
411ms
57363160 (57.3MB)
```
「a」という文字列を10万回くっつける作業をしただけで0.4秒かかってしまいました。文字列をくっつけるのにこれだけかかっていると、ちょっと心配です。メモリも文字列を繋げるだけにしては使いすぎているように感じますね。

# StringBuilderで文字列を更新する
Stringで起きているような問題を解決することができるのが、StringBuilderです。
Stringでやっていたプログラムを再現するのであればこんな感じでしょうか。
Stringの時に比べ、メソッドの使用が発生しているので初めて学ぶ際は少しわかりづらいかもしれませんね。
```Java
StringBuilder sb = new StringBuilder("Good morning!");
sb.replace(5, sb.length(), "afternoon!");
sb.append(" Yu-HaruWolf!");
System.out.println(sb.toString());
```
さて次が本領発揮するところですね。
今度はStringBuilderを使って、こんな感じのコードを作って実行してみました。
```Java
long start, end;
{
    start = System.currentTimeMillis();
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < 100000; i++) {
        sb.append('a');
    }
    System.out.println(sb.toString());
    end = System.currentTimeMillis();
}
System.out.println(end - start + "ms");
System.out.println(Runtime.getRuntime().totalMemory() - Runtime.getRuntime().freeMemory());
```
結果がこんな感じ。
```
aaaaaaaaaa...(省略)
7ms
4867696 (4.86MB)
```
圧倒的に早くなって、メモリの使用量も削減されていますね。
このようなことから、文字列に対して大量の更新がかかることがわかっている場合は、StringBuilderを用いるのが無難そうですね。
なお、今回は比較してわかりやすくなるようにfor文を用いていますが、同じ文字を繰り返すだけであれば、こんな感じでrepeatメソッドを用いることができます。
```Java
s += "a".repeat(100000);
sb.append("a".repeat(100000));
```
なおこの場合はStringを使うほうが実行時間も使用メモリも少なく済みます。ただ同じ文字列を繰り返すだけのパターンは珍しいと思うので、基本的にはStringBuilderを使うのが良いでしょう。

# 原理
StringとStringBuilderの原理を考えるとこの結果になるのですが、その説明はこちらの記事がわかりやすいので是非こちらをご覧ください。

https://qiita.com/atsuk0r0/items/1208b5fead98676bc3c7

# 他にできること
新しいクラスでできることを知るにはJavadocを参照するのが一番確実だと思います。
是非メソッドの項を確認してみてください。

https://docs.oracle.com/javase/jp/8/docs/api/java/lang/StringBuilder.html
