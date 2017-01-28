# Валидация

В качестве примера на валидацию реализуем простую форму регистрации. Признаюсь, что она была взята из туториала по Elm.

Наше окно будет содержать базовые поля для ввода данных и список ошибок валидации.
Кнопка `Join` станет активной только тогда, когда все проверки будут пройдены. 
Сейчас нас не интересует, что делать с полученными данными, поэтому нет указания соответствующей команды.


```xml
<Window xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:fsxaml="http://github.com/fsprojects/FsXaml"
        xmlns:local="clr-namespace:ViewModels;assembly=FsSimpleForm"
        Title="Simple Form"
        Width="400"
        Height="400"
        ResizeMode="NoResize"
        WindowStartupLocation="CenterScreen">
    <Window.DataContext>
        <local:MainViewModel />
    </Window.DataContext>
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        <Grid VerticalAlignment="Center">
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
            
            <TextBlock Grid.Row="0" Grid.Column="0" Margin="10" Text="Name : " />
            
            <TextBox Grid.Row="0" Grid.Column="1" Margin="10"
                     Text="{Binding Name, UpdateSourceTrigger=PropertyChanged}" />
            
            <TextBlock Grid.Row="1" Grid.Column="0" Margin="10" Text="Password:" />
            
            <TextBox Grid.Row="1" Grid.Column="1" Margin="10"
                     Text="{Binding Password, UpdateSourceTrigger=PropertyChanged}" />
            
            <TextBlock Grid.Row="2" Grid.Column="0" Margin="10" Text="Re-enter Password : " />
            
            <TextBox Grid.Row="2" Grid.Column="1" Margin="10"
                     Text="{Binding PasswordAgain, UpdateSourceTrigger=PropertyChanged}" />

            <Button Grid.Row="3" Grid.Column="2" HorizontalAlignment="Right"
                    Content="Join" IsEnabled="{Binding IsValid}" />

        </Grid>
        <Grid Grid.Row="1">
            <TreeView ItemsSource="{Binding PropertyErrors}">
                <TreeView.ItemContainerStyle>
                    <Style TargetType="{x:Type TreeViewItem}">
                        <Setter Property="IsExpanded" Value="True" />
                    </Style>
                </TreeView.ItemContainerStyle>
                <TreeView.ItemTemplate>
                    <HierarchicalDataTemplate ItemsSource="{Binding error}">
                        <TextBlock Text="{Binding propertyName, StringFormat=Property: {0}}" />
                        <HierarchicalDataTemplate.ItemTemplate>
                            <HierarchicalDataTemplate>
                                <Grid>
                                    <Grid.ColumnDefinitions>
                                        <ColumnDefinition Width="Auto" />
                                        <ColumnDefinition Width="*" />
                                    </Grid.ColumnDefinitions>
                                    <TextBlock Grid.Column="1" Margin="3" Foreground="Red" 
                                               Text="{Binding}" />
                                </Grid>
                            </HierarchicalDataTemplate>
                        </HierarchicalDataTemplate.ItemTemplate>
                    </HierarchicalDataTemplate>
                </TreeView.ItemTemplate>
            </TreeView>

        </Grid>
    </Grid>
</Window>
```

Нам нужно определить три свойства: для имени, пароля и подтверждения пароля соответственно. Не будем снова останавливаться на том, как их объявить, а перейдем к новой информации - проверке получаемых данных.
Тут на помощь приходит модуль `Validation`, который содержит несколько очень полезных функций для валидации:


Свойство      | Требование            | Функция
--------------|-----------------------|--------
Name          |                       |
|             | Не пустой             | notNullOrWhitespace 
|             | Не содержит пробелов  | noSpaces
Password      |                       |
|             | Не меньше 6 символов  | hasLengthAtLeast 6 
|             | Не больше 16 символов | hasLengthNoLongerThan 16 

На всякий случай приведу таблицу с кратким описанием функций из этого модуля: 


Название                              |Сигнатура  | Описание  
--------------------------------------|-|----------------------
validate                              | | Начинает валидацию для заданного свойства
notNullOrWhitespace                   | | Проверка на то, что строка ни состоит из одних пробелов ни равна null
noSpaces                              | | Строка не должна содержать пробелы
hasLength                             | | Строка должна иметь указанную длину
hasLengthAtLeast                      | | Длина строка должна быть по крайней мере символов
hasLengthNoLongerThan                 | | В строке должно быть не более чем символов
matchesPattern                        | | Строка должна подходить под заданный шаблон регулярного выражения
isAlphanumeric                        | | Стили лайтбокса, если таковой используется
containsAtLeastOneDigit               | | Содержит хотя бы одну цифру
containsAtLeastOneUpperCaseCharacter  | | Содержит хотя бы один символ верхнего регистра
containsAtLeastOneLowerCaseCharacter  | | В строке находится по крайней мере один символ нижнего регистра
notEqual                              | | Значение не должно быть равно `value`
greaterThan                           | | Больше чем 
greaterOrEqualTo                      | | Больше или равно
lessThan                              | | Меньше чем
lessOrEqualTo                         | | Меньше или равно
isBetween                             | | Находится между двумя заданными значениями
containedWithin                       | | Дополнительные стили
notContainedWithin                    | | Стили иконочного шрифта


Переведем записанное в код:

```fsharp
    let vpassword = 
        hasLengthAtLeast 6 
        >> hasLengthNoLongerThan 16 
                
    let name = self.Factory.Backing(<@ self.Name @>, "", notNullOrWhitespace >> noSpaces)
    let password = self.Factory.Backing(<@ self.Password @>, "", vpassword)
    let passwordAgain = self.Factory.Backing(<@ self.PasswordAgain @>, "", vpassword)

    member __.Name with get() = name.Value and set v = name.Value <- v 
    member __.Password with get() = password.Value and set v = password.Value <- v 
    member __.PasswordAgain with get() = passwordAgain.Value and set v = passwordAgain.Value <- v 
```

Но это еще не все. Осталось еще одна проверка - свойство `PasswordAgain` должно быть равно `Password`.
Для её добавления переопределим стандартный метод `Validate`

```fsharp
    override x.Validate propertyName =
        seq {
            let err =
                  if x.Password = x.PasswordAgain 
                  then ["Passwords do not match!"]
                  else []

            yield PropertyValidation("PasswordAgain", err)
            yield EntityValidation(err)

        }
```

Теперь, в случае, если какое-то из полей будет невалидным, внизу будет отображаться список ошибок, а 
кнопка `Join` будет недоступна.


_Упражнение_

----

Добавление информирования пользователя о степени надежности выбранного им пароля
. 
В качестве примера можно выбрать следующее разбиение:
```fsharp
type Надежность = |Ужасная |Плохая |Средняя |Хорошая |Отличная
```

Первые две не должны проходить валидацию и выдавать соответствующее сообщение.

Бонусом может служить индикатор надежности:
Красный -> Розовый -> Оранжевый -> Хаки -> Зеленый

----
