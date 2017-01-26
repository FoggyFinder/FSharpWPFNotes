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

https://github.com/ruxo/WpfFs