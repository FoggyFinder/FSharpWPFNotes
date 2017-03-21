# Использование Gjallarhorn вместе с WPF

Для начала нужно вызвать функцию `Platform.install` передав
ему `true`, которая вернет контекст синхронизации.


```fsharp
[<STAThread>]
[<EntryPoint>]
let main argv =
    Gjallarhorn.Wpf.Platform.install true |> ignore
```

Одним из основных классов в `Gjallarhorn` при работе с `WPF`
чвляется `BindingSource`, экземпляр которого создается при помощи одной из функций
модуля `Binding`. Это один из основных модулей, поэтому будет уместно
привести сигнатуры и краткие описание для его функций

#### *Функции модуля Binding*


-------

Функция |Сигнатура 
--------|------------------
|componentToView | componentToView: source : BindingSource -> name : string -> comp : Component<'TModel, 'TMessage> -> signal : ISignal<'TModel> -> IObservable<'TMessage>
| |
|constantToView | constantToView: name : string -> value : 'a -> source : BindingSource -> unit
| |
|createCommand | createCommand: name : string -> source : BindingSource -> ITrackingCommand<DateTime>
| |
|createCommandChecked|createCommandChecked: name : string -> canExecute : ISignal<bool> -> source : BindingSource -> ITrackingCommand<DateTime>
| |
|createCommandParam |createCommandParam: name : string -> source : BindingSource -> ITrackingCommand<'a>
| |
|createCommandParamChecked|	createCommandParamChecked: name : string -> canExecute : ISignal<bool> -> source : BindingSource -> ITrackingCommand<'a>
| |
|createMessage| createMessage: name : string -> message : 'a -> source : BindingSource -> IObservable<'a>
| |
|createMessageChecked|createMessageChecked: name : string -> canExecute : ISignal<bool> -> message : 'b -> source : BindingSource -> IObservable<'b>
| |
|createMessageParam|createMessageParam: name : string -> message : ('a -> 'b) -> source : BindingSource -> IObservable<'b>
| |
|createMessageParamChecked|createMessageParamChecked: name : string -> canExecute : ISignal<bool> -> message : ('a -> 'b) -> source : BindingSource -> IObservable<'b>
| |
|createObservableSource |createObservableSource: unit -> ObservableBindingSource<'a> 
| |
|createSource |createSource: unit -> BindingSource
| |
|memberToFromView |memberToFromView: source : BindingSource -> expr : Expr -> validation : Validation<'a,'a> -> signal : ISignal<'b> -> IValidatedSignal<'a,'a>
| |
|mutateToFromView|mutateToFromView: source : BindingSource -> name : string -> mutatable : IMutatable<'a> -> unit
| |
|mutateToFromViewConverted |mutateToFromViewConverted: source : BindingSource -> name : string -> converter : ('a -> 'b) -> validator : Validation<'b,'a> -> mutatable : IMutatable<'a> -> unit
| |
|mutateToFromViewValidated |mutateToFromViewValidated: source : BindingSource -> name : string -> validator : Validation<'b,'a> -> mutatable : IMutatable<'a> -> unit
| |
|toFromView |toFromView: source : BindingSource -> name : string -> signal : ISignal<'a> -> ISignal<'a>
| |
|toFromViewConvertedValidated |toFromViewConvertedValidated: source : BindingSource -> name : string -> converter : ('a -> 'b) -> validator : Validation<'b,'c> -> signal : ISignal<'a> -> IValidatedSignal<'b,'c>
| |
|toFromViewValidated |toFromViewValidated: source : BindingSource -> name : string -> validator : Validation<'a,'b> -> signal : ISignal<'a> -> IValidatedSignal<'a,'b>
| |
|toView|toView: source : BindingSource -> name : string -> signal : ISignal<'a> -> unit
| |
|toViewValidated |toViewValidated: source : BindingSource -> name : string -> -> validator : Validation<'b,'b> -> signal : ISignal<'b> -> unit
| |