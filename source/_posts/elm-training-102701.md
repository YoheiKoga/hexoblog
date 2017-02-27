title: Elm練習10
date: 2016-10-27 09:38:44
tags:
- Elm
---

引き続きElm練習

[Elm練習 archives](https://yoheikoga.github.io/tags/Elm/)

[exampleのDrag](http://elm-lang.org/examples/drag)をいじって練習してみる

<!-- more -->

<br>

---
# updateHelp
---

updateのところ、関数が２つに分解されてて下記のように`update`と`updateHelp`がある

```elm
-- UPDATE

update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
  (updateHelp msg model, Cmd.none)

updateHelp : Msg -> Model -> Model
updateHelp msg ({position, drag} as model) =
  case msg of
    DragStart xy ->
      Model position (Just (Drag xy xy))

    DragAt xy ->
      Model position (Maybe.map(\{start} -> Drag start xy) drag)

    DragEnd _ ->
      Model (getPosition model) Nothing
```

updateだけでコードを書くとごちゃごちゃしてしまうので、分解できるよって例を示してくれてるのだと思う

これぐらいだと分割しなくても特にややこしくは無さそうだけど(Cmd.noneを書く回数が増えるだけ)

もっと大きなコードになったらこういうやり方が役に立つのだと思われる

とりあえず`update`と`updateHelp`を合成すると下記になる

```elm
update : Msg -> Model -> (Model, Cmd Msg)
update msg ({position, drag} as model) =
  case msg of
    DragStart xy ->
      (Model position (Just (Drag xy xy)), Cmd.none)

    DragAt xy ->
      (Model position (Maybe.map(\{start} -> Drag start xy) drag), Cmd.none)

    DragEnd _ ->
      (Model (getPosition model) Nothing, Cmd.none)
```


<br>

ついでに、画面にpositionの状態を表示できるようにviewも下記のように改変してみた

```elm
view : Model -> Html Msg
view model =
  let
    realPosition =
      getPosition model

  in
    div []
      [
      div
        [ onMouseDown
        , style
          [ "background-color" => "#3c8CF2"
          , "cursor" => "move"

          , "width" => "100px"
          , "height" => "100px"
          , "border-radius" => "4px"
          , "position" => "absolute"
          , "left" => px realPosition.x
          , "top" => px realPosition.y

          , "color" => "white"
          , "display" => "flex"
          , "align-items" => "center"
          , "justify-content" => "center"
          ]
        ]
        [ div[]
            [text "Drag me"
            ]
        ]
      , div []
          [ text ("position.x: " ++ px realPosition.x)
          , br [][]
          , text ("position.y: " ++ px realPosition.y)
          , br [][]
          -- position, drag, currentを表示させる
          , text ("model: " ++ toString (model))          
          ]
      ]
```

Elm、まだまだ自分で好きなようにかけるような気がしなくて難しい・・・





