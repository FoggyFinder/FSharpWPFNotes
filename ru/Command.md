# Команды


`FSharp.ViewModule` поддерживает два вида команд:

- синхронные
- асинхронные

Для каждого из них есть набор полезных функций, в зависимости от
того, с чем вы хотите работать.

- CommandAsync
- CommandSync
- CommandAsyncChecked
- CommandSyncChecked
- CommandAsyncParam
- CommandSyncParam
- CommandSyncParamChecked
- CommandAsyncParamChecked


В качестве примера рассмотрим простой таймер обратного отсчета.

Нам понадобится `TextBox` для ввода начального значения,
`TextBlock` для вывода текущего значения и `Button` для запуска. Для того чтобы не блокировать основное окно во время выполнения будет использовать `CommandAsync`.


XAML:

```xml
    <Grid VerticalAlignment="Center">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid Grid.Row="0">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Column="0" Margin="10" Text="Count: " />
            <TextBox Grid.Column="1" Margin="10" Text="{Binding Count}" />
            <Button Grid.Column="2" Margin="10" HorizontalAlignment="Center"
                    Command="{Binding StartCommand}" Content="Start" />
        </Grid>

        <TextBlock Grid.Row="1" Margin="10"
                   HorizontalAlignment="Center" FontSize="20"
                   Text="{Binding Current}" />

    </Grid>
```

F#:

```fsharp
type MainViewModel() as self = 
    inherit ViewModelBase()    

    let count = self.Factory.Backing(<@ self.Count @>, 10)
    let current = self.Factory.Backing(<@ self.Current @>, 0)

    let start _ =
        current.Value <- count.Value
        async{ 
            while current.Value <> 0 do
                do! Async.Sleep 1000
                current.Value <- current.Value - 1  
        }

    let startcommand = self.Factory.CommandAsync(start)
    
    member __.StartCommand with get() = startcommand
    member __.Count with get() = count.Value and set v = count.Value <- v
    member __.Current with get() = current.Value and set v = current.Value <- v
 ```

 Отметим, что пока работает таймер, запускающая кнопка будет отключена.
