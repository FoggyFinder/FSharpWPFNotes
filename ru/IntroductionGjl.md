# Базовые сведения о Gjallarhorn

Эта часть представляет собой обзор базовых особенностей библиотеки [Gjallarhorn](http://github.com/ReedCopsey/Gjallarhorn). 

Основными в библиотеке являются два типа - изменяемые переменные (`Mutable`) и сигналы (`Signal`).

`Mutable` представляют собой изменяемые значение. Для работы с ними используется одноименный модуль, который предоставляет функции для удобной работы. Для начала кратко опишем их назначение а затем рассмотрим небольшой пример

Функция                   | Сигнатура  / Назначение
--------------------------|-----------
|Mutable.create           |<pre lang="fsharp">val Mutable.create : value : 'a -> IMutatable<'a> </pre>
| |Создает изменяемую переменную на основе переданного значения
|Mutable.createAsync      |<pre lang="fsharp">val Mutable.createAsync : value : 'a -> IAsyncMutatable<'a> </pre>
| |Создает асинхронную изменяемую переменную на основе переданного значения
|Mutable.createThreadsafe |<pre lang="fsharp">val Mutable.createThreadsafe : value : 'a -> IAtomicMutatable<'a></pre>
| |Создает изменяемую переменную на основе переданного значения для работы в многопоточном режиме
|Mutable.get              |<pre lang="fsharp">val Mutable.get : mutatable : IMutatable<'a> -> 'a</pre>
| |Получает значение ассоциированное с изменяемой переменной
|Mutable.set              |<pre lang="fsharp">val Mutable.set : mutatable : IMutatable<'a> -> value: 'a -> unit</pre>
| |Задает новое значение для изменяемой переменной 
|Mutable.step             |<pre lang="fsharp">val Mutable.step : f : ('a -> 'a) -> mutatable : IMutatable<'a> -> unit</pre>
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

