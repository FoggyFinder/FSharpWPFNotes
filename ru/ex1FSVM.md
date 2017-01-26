# Сумма двух чисел

Начнем с самого простого примера - будем выводить сумму двух чисел. Каждое из слагаемых будет вводиться пользователем в соответствующем поле (`TextBox`).
Результат должен оставаться корректным при изменении любого из двух слагаемых.

Начнем с того, как все будет отображаться:

```xml
<Window xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="clr-namespace:ViewModels;assembly=FsEmptyWindowsApp3"
        Title="Сумма двух чисел"
        Width="400"
        Height="200">
    <Window.DataContext>
        <local:MainViewModel />
    </Window.DataContext>
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*"></ColumnDefinition>
            <ColumnDefinition Width="*"></ColumnDefinition>
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"></RowDefinition>
            <RowDefinition Height="Auto"></RowDefinition>
            <RowDefinition Height="Auto"></RowDefinition>
        </Grid.RowDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Margin="10" Text="A:" />
        <TextBox Grid.Row="0" Grid.Column="1" Margin="10" 
                 Text="{Binding A, UpdateSourceTrigger=PropertyChanged}" />
        <TextBlock Grid.Row="1" Grid.Column="0" Margin="10" Text="B:" />
        <TextBox Grid.Row="1" Grid.Column="1" Margin="10" 
                 Text="{Binding B, UpdateSourceTrigger=PropertyChanged}" />
        <TextBlock Grid.Row="2" Grid.Column="0" Margin="10" Text="A + B = " />
        <TextBlock Grid.Row="2" Grid.Column="1" Margin="10" Text="{Binding Sum}" />
    </Grid>
</Window>
```

Так как это не туториал об основах WPF, мы не будем уделять много времени стилям, а оставим базовое отображение.

Теперь перейдем к интересной части - нашей `MainViewModel`.
Для начала наследуем `ViewModelBase`, которая предоставляет несколько очень полезных свойств, с большей частью которых будем знакомиться в ходе туториала.

```fsharp
type MainViewModel() as self = 
    inherit ViewModelBase()
```

Теперь нам нужно задать свойства и поля для первого слагаемого:

```fsharp
    let a = self.Factory.Backing(<@ self.A @>, 0)

    ...
        
    member __.A with get() = a.Value and set v = a.Value <- v

```

Для второго код будет аналогичным.

Функция `self.Factory.Backing` принимает цитируемое (или квотируемое) выражение, начальное значение, а также функцию для валидации в качестве дополнительного необязательного параметра. С валидацией разберемся немного позже,
а сейчас нам важно знать, что эта функция возвращает "наблюдаемое" значение, которое при каждом своем изменении сообщает всем слушателям, что указанное свойсто было изменено (через реализацию интерфейса `INotifyPropertyChanged`).

Нам осталось разобраться только с тем, как обновлять свойство `Sum` каждый раз, когда происходит изменение в любом из свойств `A` и `B`. Тут нам на помощь приходит функция `self.DependencyTracker.AddPropertyDependencies`.
Она принимает два параметра - первый это свойство, которое должно обновляться; второе - список свойств от которых есть зависимость.
Функция доступна в двух видах:

1. С использованием цитируемых выражений

```fsharp
        self.DependencyTracker.AddPropertyDependencies
            (<@ self.Sum @> ,[ <@@ self.A @@>;<@@ self.B @@> ])
```


2. Передачей названий свойств в виде строки

```fsharp
        self.DependencyTracker.AddPropertyDependencies
            ("Sum", ["A" ; "B"]) 
```

Первый вариант предотвращает ошибки связанные с опечатками. 

_Оператор `nameof`, который должен быть знаком C#-разработчикам планируется добавить уже в F# 4 .1_

Собирая все вместе получим следующий код:

```fsharp
type MainViewModel() as self = 
    inherit ViewModelBase()
    
    let a = self.Factory.Backing(<@ self.A @>, 0)
    let b = self.Factory.Backing(<@ self.B @>, 0)
    
    do
        self.DependencyTracker.AddPropertyDependencies
            (<@ self.Sum @> ,[ <@@ self.A @@>;<@@ self.B @@> ])
        
    member __.A with get() = a.Value and set v = a.Value <- v
    member __.B with get() = b.Value and set v = b.Value <- v
    member __.Sum with get() = a.Value + b.Value
```

Синтаксис выглядит довольно необычным, но немного попрактиковавшись вы быстро привыкните к нему. 
(А если нет - не беда, далее мы рассмотрим альтернативу :))

_Упражнение_

----

_Попробуйте добавить возможность выбирать оператор из выпадающего списка (`ComboBox`). Например: [`*`;`+`;`-`;`^`;`/`]_

----