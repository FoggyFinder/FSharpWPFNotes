
Следующей в нашем списке идет библиотека `RZ.Wpf`

Мне не приходилось её использовать, поэтому говорить о преимуществах или недостатках не могу. Подробнее о ней можно прочитать на странице проекта в [github](https://github.com/ruxo/WpfFs)

Для установки через NuGet :

`Install-Package RZ.Wpf`


Что касается [FSharp.Desktop.UI](http://fsprojects.github.io/FSharp.Desktop.UI/index.html) то по ней есть хороший [справочный материал](https://github.com/dmitry-a-morozov/fsharp-wpf-mvc-series) на английском языке. 

Как и в случае с `WpfFs` ограничусь только ссылкой на пакет

`Install-Package FSharp.Desktop.UI -Pre`

Это связано с тем, что с их возможностями знаком слабо так как 
абсолютно доволен тем, что предоставляет `FsXAML` вместе с `FsVM` или `Gjalarhorn`. Вот именно на них мы и остановимся подробнее.
  
[`FSharp.ViewModule`](https://github.com/fsprojects/FSharp.ViewModule) - библиотека, которая предоставляет поддержку 
MVVM для F# проектов. Начиная с версии `1.0.0` добавлено полноценное `C#-API`.

Для того, чтобы установить последнюю версию:

`Install-Package FSharp.ViewModule.Core`

Существует даже шаблон проекта для `VisualStudio` [F# Empty Windows App](https://marketplace.visualstudio.com/items?itemName=DanielMohl.FEmptyWindowsAppWPF)
но в нем используются более старые версии библиотек.

[`Gjallarhorn`](https://github.com/ReedCopsey/Gjallarhorn) - разработана для гибкого контроля над изменяемыми состояниям.
Возможности библиотеки не ограничиваются только работой с `WPF`, но хорошая [документация](http://reedcopsey.github.io/Gjallarhorn/) поможет разобраться в том как правильно с ней работать. 

`Install-Package Gjallarhorn -Pre`

На это краткий обзор заканчивается, а нас ждет детальное знакомство с `FSharp.ViewModule`.
