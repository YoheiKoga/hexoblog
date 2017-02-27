title: Elm練習してみる4
date: 2016-10-05 18:16:44
tags:
- Elm
---

引き続きElmの練習

[Elmの練習](https://yoheikoga.github.io/2016/10/05/elm-training/)

[Elm練習してみる2](https://yoheikoga.github.io/2016/10/05/elm-training-100502/)

[Elm練習してみる3](https://yoheikoga.github.io/2016/10/05/elm-training-100503/)

公式の [radio-buttons](http://elm-lang.org/examples/radio-buttons) に少し手を加える

<!-- more -->


<br>

# legendとfont-familyを加えてみる

公式のradio-buttonsは、ラジオボタンの選択によって文字の大きさが変わるやつなので

せっかくなのでfontSizeのところをタプルにしてfont-familyも足してやった

こうすることで、文字の大きさとともにfont-familyも変化させることができる

似たようなことをすれば、ボタンによってビジュアルを簡単に変えることができそう

コードは以下

```hs
import Html exposing (Html, Attribute, div, fieldset, input, label, text, legend)
import Html.App as App
import Html.Attributes exposing (name, style, type')
import Html.Events exposing (onClick)
import Markdown


main =
  App.beginnerProgram { model = chapter1, update = update, view = view}


-- Model

type alias Model = {fontSize: FontSize, content: String}
type FontSize = Small | Medium | Large

chapter1 : Model
chapter1 =
  Model Medium intro

intro : String
intro = """

# Hello World

## Chapter 1

Happy Birthday to me

"""

-- Update

type Msg = SwitchTo FontSize

update : Msg -> Model -> Model
update msg model =
  case msg of 
    SwitchTo newFontSize ->
      { model | fontSize = newFontSize}



-- View

view : Model -> Html Msg
view model =
  div []
    [fieldset []
      [ legend [][text "hello this is legend"]
      , radio "Small" (SwitchTo Small)
      , radio "Medium" (SwitchTo Medium)
      , radio "Large" (SwitchTo Large)
      ]
    , Markdown.toHtml[ sizeToStyle model.fontSize] model.content
    ]


radio : String -> msg -> Html msg
radio value msg =
  label
    [style[("padding", "20px")]
    ]
    [input[type' "radio", name "font-size", onClick msg][]
    , text value
    ]

sizeToStyle : FontSize -> Attribute msg
sizeToStyle fontSize =
  let 
    (size, fontFamily) =
      case fontSize of  
        Small ->
          ("1.2em", "Impact")

        Medium ->
          ("1.8em", "Meyryo")

        Large ->
          ("2.4em", "Courier New")


  in
    style [("font-size", size)
          , ("font-family", fontFamily)
          ]
```



今回は以上、少しづつなれてきた

ただElmって同じような関数名や型名を使うのでキャメルケースだと書きにくいようなきがする
(今回のだと、fontSizeとFontSizeとか)