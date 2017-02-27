title: すごいE本web版第7章 再帰関数と末尾再帰
date: 2016-12-08 11:26:01
tags:
- Erlang
---


すごいE本のweb版があったのでやってみてる

第7章の再帰

<!-- more -->

<br>

# 再帰関数で並び数字のリスト作成関数

引数に与えた数字までの順数のリストを作る関数作ってみた

下記になる、再帰関数版と末尾再帰関数版、さらにリストの逆順を作る4パターン(ファイル名はrecursion.erl)

```erlang recursion.erl
-module(recursion).
-export([make_lists/1, reverse_lists/1, tail_make_lists/1, tail_make_lists/2, reverse_tail_lists/1, reverse_tail_lists/2]).

%%
make_lists(0) -> [];
make_lists(N) when N > 0 -> make_lists(N-1) ++ [N].

%%
reverse_lists(0) -> [];
reverse_lists(N) when N > 0 -> [N] ++ reverse_lists(N-1).

%%
tail_make_lists(N) -> tail_make_lists(N, []).
tail_make_lists(0,List) -> List;
tail_make_lists(N,List) when N > 0 -> tail_make_lists(N-1, [N|List]).

%%
reverse_tail_lists(N) -> reverse_tail_lists(N, []).
reverse_tail_lists(0,List) -> List;
reverse_tail_lists(N,List) when N > 0 -> reverse_tail_lists(N-1,List ++ [N]).
```

使う時はerlで下記のようにする

```
1> c(recursion).
{ok,recursion}
2> recursion:make_lists(10).
[1,2,3,4,5,6,7,8,9,10]
3> recursion:reverse_lists(10).
[10,9,8,7,6,5,4,3,2,1]
4> recursion:tail_make_lists(10).
[1,2,3,4,5,6,7,8,9,10]
5> recursion:reverse_tail_lists(10).
[10,9,8,7,6,5,4,3,2,1]
```

<br>

# lenient_zipの末尾再帰

下記

```erlang
% tail_lenient_zip(X, Y) -> tail_lenient_zip(reverse(X), reverse(Y), []).
tail_lenient_zip(X, Y) -> reverse(tail_lenient_zip(X, Y, [])).

tail_lenient_zip(_, [], SubList) -> SubList;
tail_lenient_zip([], _, SubList) -> SubList;
tail_lenient_zip([X|Xs], [Y|Ys], SubList) -> tail_lenient_zip(Xs, Ys, [{X,Y}|SubList]).
```


もしくはreverseを使わずに下記でもいける

```erlang
tail_lenient_zip(X, Y) -> tail_lenient_zip(X, Y, []).

tail_lenient_zip(_, [], SubList) -> SubList;
tail_lenient_zip([], _, SubList) -> SubList;
tail_lenient_zip([X|Xs], [Y|Ys], SubList) -> tail_lenient_zip(Xs, Ys, SubList ++ [{X,Y}]).
```