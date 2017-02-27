title: プログラミングElixir 13章 13.10
date: 2016-12-31 13:44:05
tags:
- Elixir
---

プログラミングElixir読んでる

13章のプロジェクトでtest書くところについてちょっとした補足

<!-- more -->

## 13.10 変換: テーブルに整形

`table_formatter_test.exs` のtestだが

最後のテーブルのテストはスペースも挿入しないとテストに通らないので注意
(`test "Output is correct"`のところ)


下記記述をする(見えないが、c4の後に適切な下図のスペースが挿入されている)

```elixir
  test "Output is correct" do
    result = capture_io fn ->
      TF.print_table_for_columns(simple_test_data, headers)
    end
    assert result == """
    c1    | c2     | c4     
    ------+--------+--------
    r1 c1 | r1 c2  | r1+++c4
    r2 c1 | r2 c2  | r2 c4  
    r3 c1 | r3 c2  | r3 c4  
    r4 c1 | r4++c2 | r4 c4  
    """ 
  end
```

正規表現的に(エスケープを使って視覚化して)書くと以下のように書いてもテストは通る


```elixir
  test "Output is correct" do
    result = capture_io fn ->
      TF.print_table_for_columns(simple_test_data, headers)
    end
    assert result == """
    c1\s\s\s\s|\sc2\s\s\s\s\s| c4\s\s\s\s\s
    ------+--------+--------
    r1\sc1\s|\sr1\sc2\s\s|\sr1+++c4
    r2\sc1\s|\sr2\sc2\s\s|\sr2\sc4\s\s
    r3\sc1\s|\sr3\sc2\s\s|\sr3\sc4\s\s
    r4\sc1\s|\sr4++c2\s|\sr4\sc4\s\s
    """ 
  end
```

syntax hightlightが無いとちょっと読みにくくなるので注意
