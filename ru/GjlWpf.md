# Использование Gjallarhorn вместе с WPF

Одним из основных классов в `Gjallarhorn` при работе с `WPF`
является `BindingSource`, экземпляр которого создается при помощи одной из функций
модуля `Binding`. Это один из основных модулей, поэтому будет уместно
привести сигнатуры и краткие описание для его функций.

#### *Функции модуля Binding*


-------

Функция |Сигнатура 
--------|------------------
|componentToView | componentToView: source : BindingSource -> name : string -> comp : Component<'TModel, 'TMessage> -> signal : ISignal<'TModel> -> IObservable<'TMessage>
| | Привязывает компонент к данному имени
|constantToView | constantToView: name : string -> value : 'a -> source : BindingSource -> unit
| | Добавляет константное значение с заданным именем к источнику привязки 
|createCommand | createCommand: name : string -> source : BindingSource -> ITrackingCommand<DateTime>
| | Для источника привязки создает комманду с заданным именем 
|createCommandChecked|createCommandChecked: name : string -> canExecute : ISignal<bool> -> source : BindingSource -> ITrackingCommand<DateTime>
| | Создает комманду с возможностью управлять её доступностью через специальный сигнал
|createCommandParam |createCommandParam: name : string -> source : BindingSource -> ITrackingCommand<'a>
| | Создает комманду с параметром 
|createCommandParamChecked|	createCommandParamChecked: name : string -> canExecute : ISignal<bool> -> source : BindingSource -> ITrackingCommand<'a>
| | Служит для создания комманды с параметром, возможность выполнения которой зависит от передаваемого сигнала
|createMessage| createMessage: name : string -> message : 'a -> source : BindingSource -> IObservable<'a>
| | Создает комманду для источника привязки которая выводит определенное сообщение
|createMessageChecked|createMessageChecked: name : string -> canExecute : ISignal<bool> -> message : 'b -> source : BindingSource -> IObservable<'b>
| | 
|createMessageParam|createMessageParam: name : string -> message : ('a -> 'b) -> source : BindingSource -> IObservable<'b>
| |
|createMessageParamChecked|createMessageParamChecked: name : string -> canExecute : ISignal<bool> -> message : ('a -> 'b) -> source : BindingSource -> IObservable<'b>
| |
|createObservableSource |createObservableSource: unit -> ObservableBindingSource<'a> 
| | Создает наблюдаемый источник привязки
|createSource |createSource: unit -> BindingSource
| | Создает источник привязки
|memberToFromView |memberToFromView: source : BindingSource -> expr : Expr -> validation : Validation<'a,'a> -> signal : ISignal<'b> -> IValidatedSignal<'a,'a>
| |
|mutateToFromView|mutateToFromView: source : BindingSource -> name : string -> mutatable : IMutatable<'a> -> unit
| | Добавляет изменяемое значение (двухсторонняя привязка) к источнику привязки
|mutateToFromViewConverted |mutateToFromViewConverted: source : BindingSource -> name : string -> converter : ('a -> 'b) -> validator : Validation<'b,'a> -> mutatable : IMutatable<'a> -> unit
| | Создает двухстороннюю привязку для заданного источника по заданному имени. Передаваемое значение будет преобразовано указанным конвертером (`converter`) и проверенно на правильность с помощью валидатора (`validator`)
|mutateToFromViewValidated |mutateToFromViewValidated: source : BindingSource -> name : string -> validator : Validation<'b,'a> -> mutatable : IMutatable<'a> -> unit
| | Добавляет изменяемое значение с проверкой корректности данных через валиадатор к источнику привязки
|toFromView |toFromView: source : BindingSource -> name : string -> signal : ISignal<'a> -> ISignal<'a>
| | Добавляет заданный сигнал к источнику привязки и возвращает новый сигнал при изменении значения у целевого объекта 
|toFromViewConvertedValidated |toFromViewConvertedValidated: source : BindingSource -> name : string -> converter : ('a -> 'b) -> validator : Validation<'b,'c> -> signal : ISignal<'a> -> IValidatedSignal<'b,'c>
| | Позволяет передавать и принимать значения в виде сигнала к целевому объекту. Конвертер позволяет преобразовывать значение прежде чем оно поступит к цели, тогда как валидатор проверяет возвращаемое значение
|toFromViewValidated |toFromViewValidated: source : BindingSource -> name : string -> validator : Validation<'a,'b> -> signal : ISignal<'a> -> IValidatedSignal<'a,'b>
| | Добавляет заданный сигнал к источнику привязки и возвращает новый сигнал, значение которого проверяется с помощью валидатора
|toView|toView: source : BindingSource -> name : string -> signal : ISignal<'a> -> unit
| | Добавляет наблюдаемый сигнал (односторонняя привязка) к источнику привязки
|toViewValidated |toViewValidated: source : BindingSource -> name : string -> -> validator : Validation<'b,'b> -> signal : ISignal<'b> -> unit
| | Добавляет наблюдаемый сигнал (односторонняя привязка) к источнику привязки с учетом указанного валидатора

Знаний о функциях из этого модуля будет вполне достаточно для написания простых приложений, поэтому будет уместно перейти к практической части 

----

В качестве примера рассмотрим нахождение интерполяционного многочлена Лагранжа по заданным точкам.
Поскольку это демонстрационный пример, то для сокращения кода обработка возможных ошибок проводиться не будет.
Для начала нужно определиться с условиям. 
Предположим, что значения в узловых точках будет храниться в файле, в качестве символа разделителя между 
координатой (`X`, `Y`) возьмем точку с запятой (`;`). Таким образом координатная точка будет записываться

`-1.5;-14.1014`

Путь к файлу будет задаваться в текстовом поле (`Path`) и по нажатию на кнопку (`ReadData`) данные будут загружаться
и отображаться в `DataGridView`. За расчет и отображение интерполяционного многочлена отвечает кнопка (``) и текстовое поле (`Function`).

После такого описания мы готовы заполнить наш `xaml` - файл:

```xml
        <DataGrid Grid.Row="1"
                  AutoGenerateColumns="False"
                  CanUserAddRows="False"
                  ItemsSource="{Binding Data}">
            <DataGrid.Columns>
                <DataGridTextColumn Width="*"
                                    Binding="{Binding X}"
                                    Header="X" />
                <DataGridTextColumn Width="*"
                                    Binding="{Binding Y}"
                                    Header="Y" />
            </DataGrid.Columns>
        </DataGrid>
        <Button Grid.Row="2"
                HorizontalAlignment="Right"
                Command="{Binding Calculate}"
                Content="Calculate" />
        <TextBlock Grid.Row="3" Text="{Binding Function}" />
```
         
Часть кода с разметкой для строк и столбцов `Grid` сознательно не приводится.

Модуль `Data` содержит функции необходимые для нахождения полинома и вспомогательные функции.
Для того, чтобы сократить сгенерированное выражение используются функции из библиотеки `MathNet.Symbolics`.

```fsharp
module Data

open MathNet.Symbolics
open System.Collections.Generic
open System.Globalization

let private culture = CultureInfo.InvariantCulture

let generate n = 
    let y = sprintf "y%i"
    let l i n = seq {
        for j in 0..n - 1 do
            if j <> i then
                yield sprintf "(x - x%i)/(x%i - x%i)" j i j
        }
    let p i = 
        l i n
        |> String.concat "*"
        |> sprintf "%s*%s" <| y i

    Array.init n p 
    |> String.concat "+"

let calculate n map = 
    map
    |> Map.fold
        (fun (acc : string) key (value:float) -> 
            acc.Replace(key, value.ToString culture))
        (generate n)
    |> Infix.parseOrUndefined
    |> Algebraic.expand

let format map = 
    let n = ((map |> Seq.length) - 1) / 2
    calculate n map
    |> Infix.format
```

Модуль `Context` содержит функции для взаимодействия с нашим представлением а также
тип `Point` и функции для чтения их из файла:

```fsharp
module Context
open Gjallarhorn.Bindable
open Gjallarhorn
open System

type Point = {X:float; Y:float}

let pointToMap points = 
    [for i, point in points |> Seq.indexed do
        yield sprintf "x%i" i, point.X
        yield sprintf "y%i" i, point.Y ] 
    |> Map.ofList

let split (arr : char[]) (str : string) = 
    str.Split(arr, StringSplitOptions.RemoveEmptyEntries)

let readPoints = 
    System.IO.File.ReadLines
    >> Seq.map(split [|';'|])
    >> Seq.map(Array.map float)
    >> Seq.map(fun arr -> {X = arr.[0]; Y = arr.[1]})
    >> Seq.toArray

let example() = 
    let source = Binding.createSource()
    
    let path = Mutable.create ""
    let data = Mutable.create [||]
    
    let create() = 
        readPoints path.Value
        |> Mutable.set data

    Binding.toView source "Data" data
    Binding.mutateToFromView source "Input" path

    Binding.createCommand "ReadData" source
    |> Observable.subscribe (ignore >> create)
    |> source.AddDisposable

    Binding.createCommand "Calculate" source
    |> Observable.map(fun _ -> pointToMap data.Value)
    |> Observable.map(Data.format)
    |> Signal.fromObservable "Unknown"
    |> Binding.toView source "Function"

    source
```

и последний модуль `main`, который связывает все воедино

```fsharp
module main

open System
open System.Windows
open FsXaml

type MainWin = XAML<"MainWindow.xaml">

[<STAThread>]
[<EntryPoint>]
let main argv =
    Gjallarhorn.Wpf.Platform.install true |> ignore

    let app = Application()
    let win = MainWin(DataContext = Context.example())

    app.Run win
```

Тут есть функция, которая не описывалась раньше, а именно - `Wpf.Platform.install`.
Эта функция возвращает (а также может устанавливает, если передать значение `true`) контекст
синхронизации для `WPF`. Поскольку в нашем примере он не используется явным образом, можно отбросить возвращаемое значение.


Больше примеров вы можете найти в папке [samples](https://github.com/ReedCopsey/Gjallarhorn/tree/master/samples) проекта на `GitHub`.