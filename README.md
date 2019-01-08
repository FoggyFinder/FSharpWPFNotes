## Содержание  ##

1. [Введение](#Введение)
2. [Обзор библиотек](ru/LibrariesOverview.md)
2. [Конвертеры значений](ru/ValueConverter.md)
4. [FsVM - свойства](ru/ex1FSVM.md)
5. [FsVM - команды](ru/Command.md)
6. [FsVM - валидация](ru/Validation.md)
7. [Gjallarhorn - базовые сведения](ru/IntroductionGjl.md)
8. [Использование Gjallarhorn вместе с WPF](ru/GjlWpf.md)

---------

#### Введение

Для написания полноценных `WPF` приложений на `F#` существует несколько подходов:

1. Не использовать разметку `xaml`, а создавать графический интерфейс полностью на F#.
2. Использовать `XamlReader` для разбора файлов разметки.
3. Использовать `FsXaml` для работы с `.xaml` файлами.
 
И, конечно, есть еще вариант писать UI часть на C#, но здесь его в расчет брать не будем. 

В качестве иллюстрации напишем стандартный `Hello World`, для этого создадим простое консольное приложение.

*Примечание*
> Если вы создали консольное приложение в `VisualStudio`, то не забудьте изменить в настройках приложения тип выходных данных с консольного на Windows

Добавим ссылки на следующие сборки:

- WindowsBase.dll
- PresentationCore.dll
- PresentationFramework.dll

Теперь используем базовые `WPF` элементы `Label` для отображения приветственной надписи и `Window` для окна вывода. 

В результате код может выглядеть подобным образом (хотя здесь он и намеренно упрощен):

```fsharp
open System
open System.Windows
open System.Windows.Controls

let label = Label(FontSize=30.0, Content="Привет, МИР!")
let win = Window(Height=100.0, Width=240.0, Content=label)

[<STAThread>]
Application().Run win
|> ignore
```

Для второго способа усложним пример так, чтобы классическое сообщение выводилось при нажатии на кнопку.

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

[<STAThread>]
Application().Run win
|> ignore
```

Подробнее о возможностях `XamlReader` можно узнать в [документации](https://docs.microsoft.com/ru-ru/dotnet/api/system.windows.markup.xamlreader?view=netframework-4.7.2).

Теперь о последнем из перечисленных подходов. [`FsXaml`](https://github.com/fsprojects/FsXaml) это поставщик типов (type provider) который служит для генерации F# - типов для наших XAML - файлов.

Для добавления в проект через NuGet

```powershell
Install-Package FsXaml.Wpf
```

или, если предпочитаете `paket`:

```powershell
paket add FsXaml.Wpf --version 3.1.6
```

*Примечание*
> В этом случае предварительный шаг делать не нужно, так как все требуемые ссылки на сборки будут автоматически добавлены при установке пакета, 

Пример:

```fsharp
open FsXaml
open System
open System.Windows

type MainWindowBase = XAML<"MainWindow.xaml">

[<STAThread>]
MainWindowBase() 
|> Application().Run
|> ignore
```

Здесь `MainWindow.xaml` файл с разметкой, который должен быть отмечен как ресурс.

О преимуществах использования `FsXaml` над `XamlReader` можно прочитать [`тут`](http://stackoverflow.com/questions/30859148/advantage-of-fsxaml-type-provider-over-xamlreader)


У каждого из описанных выше подходов есть свои сильные стороны, но для относительно небольших приложений скорее всего удобнее будет использовать именно `FsXaml`. 


-------

## Используемые версии библиотек ##

- FsXaml.Wpf - **3.1.16**
- FSharp.ViewModule - **1.0.5**
- Gjallarhorn - **Gjallarhorn 0.0.9 (beta)**