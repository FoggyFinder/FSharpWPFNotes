#Введение



Существует несколько библиотек которые позволяют писать полноценные WPF - приложения полностью на F#. В этом туториале будут рассмотрены основные возможности.

Существуют несколько подходов:

1. FsXAML + FsVM/Gjalarhorn/..whatever
2. FSharp.Desktop.UI
3. Другие библиотеки (например, RZ.Wpf)
3. Не использовать сторонние библиотеки 

Мне больше всего нравится первый подход, поэтому основная часть будет посвящена именно ему. Но, чтобы обзор был максимально полным бегло рассмотрим и два последних подхода. 

Один из основных недостатков последнего подхода является тот факт, что вам придется написать интерфейс F# - кодом.
Таким образом вы не сможете воспользоваться преимуществом описания пользовательского интерфейса через XAML - разметку.

В качестве иллюстрации напишем простое приложение `Hello World`.

Для начала нам нужно добавить ссылки на следующие сборки:

- WindowsBase.dll
- PresentationCore.dll
- PresentationFramework.dll

Если вы создали консольное приложение в `VisualStudio` то ваш код может выглядеть подобным образом (не забудьте изменить в настройках приложения тип выходных данных с консольного на Windows)

```fsharp
open System
open System.Windows
open System.Windows.Controls

let win = new Window(Height = 100.0, Width = 240.0)
let label = new Label(FontSize = 30.0, Content = "Привет, МИР!")

win.Content <- label

let app = new Application()

[<STAThread>]
app.Run win
|> ignore
```

Кроме этого вы можете читать ваши XAML - файлы через [`XamlReader`](https://msdn.microsoft.com/ru-ru/library/system.windows.markup.xamlreader(v=vs.110).aspx) и разбирать их используя метод [`Parse`](https://msdn.microsoft.com/ru-ru/library/cc663033(v=vs.110).aspx). Пример:

```fsharp
open System
open System.Windows
open System.Windows.Controls
open System.Windows.Markup

let windowXaml = """
<Window
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    Height = "57" Width = "100">
    <StackPanel>
        <Button Name="HelloWorldButton">Тыц</Button>
    </StackPanel>
</Window>"""

let win = XamlReader.Parse windowXaml :?> Window
let button = win.FindName "HelloWorldButton" :?> Button

button.Click.Add
    (fun _ -> MessageBox.Show "Привет, МИР!" |> ignore)

let app = new Application()

[<STAThread>]
app.Run win
|> ignore
```

Если все прошло хорошо, вы увидите маленькое окошко с кнопкой, нажав на которую получите информационное сообщение с классическим приветствием.

Следующей в нашем списке идет библиотека `FSharp XAML processor library`

Мне не приходилось её использовать, поэтому говорить о преимуществах или недостатках не могу. Подробнее о ней можно прочитать на странице проекта в [github](https://github.com/ruxo/WpfFs)

Для установки через NuGet :

`Install-Package RZ.Wpf`


Что касается [FSharp.Desktop.UI](http://fsprojects.github.io/FSharp.Desktop.UI/index.html) то по ней есть хороший [справочный материал](https://github.com/dmitry-a-morozov/fsharp-wpf-mvc-series) на английском языке. 

Как и в случае с `WpfFs` ограничусь только ссылкой на пакет

`Install-Package FSharp.Desktop.UI -Pre`

Это связано с тем, что с их возможностями знаком слабо так как 
абсолютно доволен тем, что предоставляет `FsXAML` вместе с `FsVM` или `Gjalarhorn`. Вот именно на них мы и остановимся подробнее.
  
[`FsXAML`](https://github.com/fsprojects/FsXaml) это поставщик типов (type provider) который служит для генерации F# - типов для наших XAML - файлов.

Для добавления в проект через NuGet

`Install-Package FsXaml.Wpf`

О преимуществах использования `FsXAML` над `XamlReader` можно прочитать [`тут`](http://stackoverflow.com/questions/30859148/advantage-of-fsxaml-type-provider-over-xamlreader)

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

