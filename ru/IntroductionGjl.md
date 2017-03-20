# Базовые сведения о Gjallarhorn

Эта часть представляет собой обзор базовых особенностей библиотеки [Gjallarhorn](http://github.com/ReedCopsey/Gjallarhorn). 

Основными в библиотеке являются два типа - изменяемые переменные (`Mutable`) и сигналы (`Signal`).

`Mutable` представляют собой изменяемые значение. Для работы с ними используется одноименный модуль, который предоставляет функции для удобной работы. Для начала кратко опишем их назначение а затем рассмотрим небольшой пример

Функция                   | Сигнатура  / Назначение
--------------------------|-----------
|create           |`create : value : 'a -> IMutatable<'a> `
| |Создает изменяемую переменную на основе переданного значения
|createAsync    | `createAsync : value : 'a -> IAsyncMutatable<'a> `
| |Создает асинхронную изменяемую переменную на основе переданного значения
|createThreadsafe |`createThreadsafe : value : 'a -> IAtomicMutatable<'a> `
| |Создает изменяемую переменную на основе переданного значения для работы в многопоточном режиме
|get              |`get : mutatable : IMutatable<'a> -> 'a `
| |Получает значение ассоциированное с изменяемой переменной
|set              |`set : mutatable : IMutatable<'a> -> value: 'a -> unit `
| |Задает новое значение для изменяемой переменной 
|step             |`step : f : ('a -> 'a) -> mutatable : IMutatable<'a> -> unit `
| |Обновляет значение изменяемой переменной с помощью функции которая принимает исходное значение и возвращает новое
 
Кроме описанных выше функций изменяемая переменная непосредственно содержит несколько полезных свойств и функций.

Пример

```fsharp
open Gjallarhorn

// Создаем изменяемую переменную
let var = Mutable.create "Some"
// Получаем значение и выводим на консоль
var |> Mutable.get |> printfn "%s" //=> Some
// Устаналиваем новое
"Hello" |> Mutable.set var
//Используем свойство для получения значения
var.Value |> printfn "%s" //=> Hello
```

`Signal` представляют значения которые могут изменятся с течением времени
со следующими свойствами:

- текущее значение, которое всегда может быть извлечено
- механизм оповещения для подписчиков о том, что текущее значение было изменено

Для работы с сигналами используется модуль `Signal`, вот некоторые функции

Функция                   | Сигнатура  / Назначение
--------------------------|-----------
|Signal.both|`both : a : ISignal<bool> -> b : ISignal<bool> -> ISignal<bool>` 
| |Создает новый сигнал на основании двух сигналов, который будет равным `true` только в том случае, если значение каждого из двух сигнала будет равняться `true`
|Signal.cache|`cache : provider : ISignal<'a> -> ISignal<'a>` 
| |Создает кэшированный сигнал
|Signal.choose|`choose : fn('a -> 'b option) -> defaultValue : 'b -> provider : ISignal<'a> -> ISignal<'b>`
| |Возвращает сигнал, который является проекцией входного сигнала к значению которого применяется заданнаю функция. Все наблюдения, для которых функция вернет Some будут отображены в новое значение. Значение по умолчанию будет использовано, если первоначальое значение входного сигнала будет равняться None
|Signal.constant|`constant : value : 'a -> ISignal<'a>`
| |Создает неизменяемый сигнал для заданного значения
|Signal.either|`either : a : ISignal<bool> -> b : ISignal<bool> -> ISignal<bool>`
| |Возвращает сигнал, который будет равен `true` в случае если значение хотя бы у одного из двух сигналов будет равняться `true` 
|Signal.equal |`equal : a : ISignal<'b> -> b : ISignal<'b> -> ISignal<bool>` 
| |Создает сигнал который будет равен `true` в том случае, когда оба значения равны между собой
|Signal.filter|`filter : predicate : ('a -> bool) -> defaultValue : 'a -> provider : ISignal<'a> -> ISignal<'a>` 
| |Фильтрует сигнал, кешируя и передавая только значения соответствующие предикату. В том случае если базовое значение не проходят фильтр будет установлено значение по умолчанию.
|Signal.filterBy|`filterBy : condition : ISignal<bool> -> defaultValue : 'b  -> input : ISignal<'b> -> ISignal<'b>`
| |Фильтрует сигнал на основании отдельного сигнала (`condition`). Если первоначальное значение сигнала будет равнятья `false`, то будет установлено значение по умолчанию (`defaultValue`)
|Signal.fromObservable|`fromObservable : initialValue : 'a -> observable : IObservable<'a> -> ISignal<'a>`
| |Создает сигнал на основании наблюдаемого (IObservable). Поскольку у ISignal всегда есть текущее значения необходимо предоставлять начальное значение (`initialValue`)
|Signal.get|`get : signal : ISignal<'a> -> 'a`
| |Получает текущее значение ассоциированное с сигналом
|Signal.iter|`iter : f('a -> unit) -> ISignal<'a> -> unit`
| |Выполняет функцию для значения сигнала
|Signal.map|`map : mapping : ('a -> 'b) -> provider : ISignal<'a> -> ISignal<'b>`
| |Преобразует значение сигнала путем приминения к нему специальной функции (`mapping`). Аналогичные функции имеются и для двух (`map2`), трех (`map3`) и так до десяти (`map10`) сигналов.
|Signal.mapAsync|`mapAsync : mapping : ('a -> Async<'b>) -> initialValue : 'b -> provider : ISignal<'a> -> ISignal<'b>`
| |Асинхронно изменяет значение сигнала с помощью функции преобразования
|Signal.mapAsyncTracked|`mapAsyncTracked : mapping : ('a -> Async<'b>) -> initialValue : 'b -> tracker : IdleTracker -> provider : ISignal<'a> -> ISignal<'b>`
| |Также как и `Signal.mapAsync` асинхронно изменяет сигнал с помощью указанной функции преобразования. Отличие состоит в отслеживании статус выполнения, который сообщается через указанный `IdleTracker`.
|Signal.mapFunction|`mapFunction : f : ('c -> 'a -> 'b) -> v : ISignal<'c> -> : ('a -> 'b)`
| |...
|Signal.mapOption2 |`mapOption2 : mapping : ('a -> 'b -> 'c) -> provider1 : ISignal<'a option> -> provider2 : ISignal<'b option> -> ISignal<'c option>`
| |Применяет преобразующую функцию (`mapping`) в том случае, если значение каждого сигнала равняеться `Some`. Существуют также функции `mapOption3`, `mapOption4`,...`mapOption10`. 
|Signal.merge|`merge : a : ISignal<'b> -> b : ISignal<'b> -> ISignal<'b>`
| |Объединяет два сигнала в один. Значение второго сигнала становится стартовым значением для нового сигнала
|Signal.not|`not : a : ISignal<bool> -> ISignal<bool>`
| |Создает сигнал котороq инвертирует входное значение типа bool 
|Signal.notEqual|`notEqual : a : ISignal<'b> -> b : ISignal<'b> -> ISignal<bool>`
| |Создает сигнал который будет равен `true` в случае, если значения различны
|Signal.observeOn|`observeOn : ctx : SynchronizationContext -> signal : ISignal<'a> -> ISignal<'a> ?`
| |Создает сигнал на основании наблюдаемого (IObservable). Поскольку у ISignal всегда есть текущее значения необходимо предоставлять начальное значение (`initialValue`)
|Signal.toFunction|`toFunction : fs : ISignal<('a -> 'b)> -> ('a -> 'b)`
| |Создает функцию из сигнала, содержащего функцию, где значение выбирается при выполнении
|Signal.validate | `validate : validator : (ValidationCollector<'a> -> ValidationCollector<'b>) -> ISignal<'a> -> IValidatedSignal<'a, 'b>`
| |Проверяет сигнал с помощью цепочки валидаторов

Пример

```fsharp
let pairToOption (b, v) = if b then Some v else None
let parse = Int32.TryParse >> pairToOption
    
//создаем изменяемую переменную
let input = Mutable.create ""
//значение по умолчанию
let defValue = 0
//результирующий сигнал
let output = 
    input 
    |> Signal.choose parse defValue 
    |> Signal.filter (fun v -> v < 10) defValue
    
input.Value <- "test"
output.Value |> printfn "%i" //0

input.Value <- "3"
output.Value |> printfn "%i" //3

input.Value <- "12"
output.Value |> printfn "%i" //3
```