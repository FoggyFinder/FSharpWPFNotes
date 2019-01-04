# Конвертеры значений

Назначение конвертеров значений в принципе достаточно очевидно - 
преобразовывать исходные данные во что-то подходящее для отображения.
Для того, чтобы работала двунаправленная привязки необходимо реализовать
и обратную трансформацию.

В `WPF` для создания собственного конвертера нужно реализовать
интерфейс [`IValueConverter`](https://docs.microsoft.com/ru-ru/dotnet/api/system.windows.data.ivalueconverter?view=netframework-4.7.2) 
или [`IMultiValueConverter`](https://docs.microsoft.com/ru-ru/dotnet/api/system.windows.data.imultivalueconverter?view=netframework-4.7.2)
из пространства имен `System.Windows.Data`.

Оба содержат два метода :

- Convert
- ConvertBack

как следует из названия для прямого и обратного преобразования соответственно.

`IMultiValueConverter` используется при множественной привязке (`MultiBinding`). 
В этом случае при прямом преобразовании передается не один объект, а массив объектов, а при обратном возвращается массив объектов, с учетом типов исходных данных.


__Заметка__
> В `Visual Studio` есть возможность немного упростить себе жизнь с помощью 
> быстрого создания скелета через выделение названия интерфейса и нажатия комбинации клавиш `Ctrl + .`
> Будет предложено два варианта - с и без аннотации типов.

В`FsXaml` есть вспомогательный класс `ConverterBase`, у которого есть два конструктора.
Первый принимает только функцию для преобразования, предоставляя базовую реализацию для обратного, что удобно при односторонней привязке данных.
Второй требует функции для преобразований в обоих направлениях.
 
То есть вместо реализации интерфейса `IValueConverter` можно унаследоваться от класса `ConverterBase`. 

Для примера возьмем конвертер из тестового задания, в котором требуется правильно отображать возраст студента.

```fsharp
type AgeToStringConverter() =
     inherit ConverterBase
        (fun value _ _ _ ->
             match value with
             | :? int ->
                value 
                |> unbox
                |> AgeToStringConverter.ageToStr
                |> box                    
             | _ -> null )

     static member ageToStr age = 
           if age >= 11 && age <= 14 then 
               sprintf "%A лет" age
           else
               match age % 10 with
               | 1 -> sprintf "%A год" age
               | m when m = 0 || m >= 5 
                  -> sprintf "%A лет" age
               | _ -> sprintf "%A года" age
```

Затем, в `xaml` указать пространство имен:

```xml
xmlns:conv="clr-namespace:Converters;assembly=StudentsTask"
```

Создать в ресурсах экземпляр класса:

```xml
<UserControl.Resources>
    <conv:AgeToStringConverter x:Key="ageConverter" />
</UserControl.Resources>
```

и в привязке данных указать конвертер в параметре `Converter` :

```xml
DisplayMemberBinding="{Binding Age, Converter={StaticResource ageConverter}}"
```
