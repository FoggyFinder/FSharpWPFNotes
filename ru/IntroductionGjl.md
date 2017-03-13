# Базовые сведения о Gjallarhorn

Эта часть представляет собой обзор базовых особенностей библиотеки [Gjallarhorn](http://github.com/ReedCopsey/Gjallarhorn). 

Основными в библиотеке являются два типа - изменяемые переменные (`Mutable`) и сигналы (`Signal`).

`Mutable` представляют собой изменяемые значение. Для работы с ними используется одноименный модуль, который предоставляет функции для удобной работы. Для начала кратко опишем их назначение а затем рассмотрим небольшой пример

Функция                   | Сигнатура  / Назначение
--------------------------|-----------
|create           |<pre lang="fsharp">create : value : 'a -> IMutatable<'a> </pre>
| |Создает изменяемую переменную на основе переданного значения
|createAsync      |<pre lang="fsharp">createAsync : value : 'a -> IAsyncMutatable<'a> </pre>
| |Создает асинхронную изменяемую переменную на основе переданного значения
|createThreadsafe |<pre lang="fsharp">createThreadsafe : value : 'a -> IAtomicMutatable<'a></pre>
| |Создает изменяемую переменную на основе переданного значения для работы в многопоточном режиме
|get              |<pre lang="fsharp">get : mutatable : IMutatable<'a> -> 'a</pre>
| |Получает значение ассоциированное с изменяемой переменной
|set              |<pre lang="fsharp">set : mutatable : IMutatable<'a> -> value: 'a -> unit</pre>
| |Задает новое значение для изменяемой переменной 
|step             |<pre lang="fsharp">step : f : ('a -> 'a) -> mutatable : IMutatable<'a> -> unit</pre>
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

Функция                   | Сигнатура  
--------------------------|-----------
|both|<pre class="fsharp">both : a : ISignal<bool> -> b : ISignal<bool> -> ISignal<bool> </pre> 
|cache|<pre class="fsharp">cache : provider : ISignal<'a> -> ISignal<'a> </pre> 
|choose|<pre class="fsharp">choose : fn('a -> 'b option) -> defaultValue : 'b -> provider : ISignal<'a> -> ISignal<'b> </pre>
|constant|<pre class="fsharp">constant : value : 'a -> ISignal<'a> </pre>
|either|<pre class="fsharp">either : a : ISignal<bool> -> b : ISignal<bool> -> ISignal<bool> </pre>
|equal |<pre class="fsharp">equal : a : ISignal<'b> -> b : ISignal<'b> -> ISignal<bool> </pre> 
|filter|<pre class="fsharp">filter : predicate : ('a -> bool) -> defaultValue : 'a -> provider : ISignal<'a> -> ISignal<'a> </pre> 
|filterBy|<pre class="fsharp">filterBy : condition : ISignal<bool> -> defaultValue : 'b  -> input : ISignal<'b> -> ISignal<'b> </pre>
|fromObservable|<pre class="fsharp">fromObservable : initialValue : 'a -> observable : IObservable<'a> -> ISignal<'a> </pre>
|get|<pre class="fsharp">get : signal : ISignal<'a> -> 'a </pre>
|iter|<pre class="fsharp">iter : f('a -> unit) -> ISignal<'a> -> unit </pre>
|map|<pre class="fsharp">map : mapping : ('a -> 'b) -> provider : ISignal<'a> -> ISignal<'b> </pre>
|mapAsync|<pre class="fsharp">mapAsync : mapping : ('a -> Async<'b>) -> initialValue : 'b -> provider : ISignal<'a> -> ISignal<'b> </pre>
|mapAsyncTracked|<pre class="fsharp">mapAsyncTracked : mapping : ('a -> Async<'b>) -> initialValue : 'b -> tracker : IdleTracker -> provider : ISignal<'a> -> ISignal<'b> </pre>
|mapFunction|<pre class="fsharp">mapFunction : f : ('c -> 'a -> 'b) -> v : ISignal<'c> -> : ('a -> 'b) </pre>
|mapOption2 |<pre class="fsharp">mapOption2 : mapping : ('a -> 'b -> 'c) -> provider1 : ISignal<'a option> -> provider2 : ISignal<'b option> -> ISignal<'c option> </pre>
|merge|<pre class="fsharp">merge : a : ISignal<'b> -> b : ISignal<'b> -> ISignal<'b> </pre>
|not|<pre class="fsharp">not : a : ISignal<bool> -> ISignal<bool> </pre>
|notEqual|<pre class="fsharp">notEqual : a : ISignal<'b> -> b : ISignal<'b> -> ISignal<bool> </pre>
|observeOn|<pre class="fsharp">observeOn : ctx : SynchronizationContext -> signal : ISignal<'a> -> ISignal<'a> ? </pre>
|toFunction|<pre class="fsharp">toFunction : fs : ISignal<('a -> 'b)> -> ('a -> 'b) </pre>
|validate | <pre class="fsharp">validate : validator : (ValidationCollector<'a> -> ValidationCollector<'b>) -> ISignal<'a> -> IValidatedSignal<'a, 'b> </pre>

