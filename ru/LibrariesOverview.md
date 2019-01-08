## Краткий обзор существующих библиотек

1. [`Gjallarhorn.Bindable.Wpf`](https://github.com/ReedCopsey/Gjallarhorn.Bindable)
2. [`VinylUI`](https://github.com/Acadian-Ambulance/vinyl-ui)
3. [`Elmish.WPF`](https://github.com/elmish/Elmish.WPF)
4. [`FSharp.ViewModule`](https://github.com/fsprojects/FSharp.ViewModule)
5. [`RZ.Wpf`](https://github.com/ruxo/WpfFs)
6. [`FSharp.Desktop.UI`](http://fsprojects.github.io/FSharp.Desktop.UI/index.html)


Сравнение проведем в виде двух таблиц, объективной и субъективной.

*Примечание*
> Для удобства, вместо полных названий будет указаны следующие сокращения
> 
> `Gjallarhorn.Bindable.Wpf` -> `Gjallarhorn`
> 
> `FSharp.ViewModule` -> `FsVM`
> 
> `FSharp.Desktop.UI` -> `Desktop.UI`


|                  | Gjallarhorn  | VinylUI    | Elmish.WPF  | FsVM       | RZ.Wpf     | Desktop.UI |
| -------------    | -----------  |--------    | ---------   |----        | ---        |
| Примеры          | +            | +          | +           | -          | +          | +  
| Паттерн          | Elmish       | MVC-like   | Elmish      | MVVM       | MVVM       | MVC 
| Последний релиз  | 10.03.2018   | 26.11.2018 | 09.12.2018  | 06.03.2017 | 06.01.2016 | 26.11.2015  
| Пакет            | [1.0.0-beta7](https://www.nuget.org/packages/Gjallarhorn.Bindable.Wpf/)  | [2.0.0](https://www.nuget.org/packages/VinylUI/)  | [2.0.0-beta-7](https://www.nuget.org/packages/Elmish.WPF)  | [1.0.7](https://www.nuget.org/packages/FSharp.ViewModule.Core/)  | [1.1.1](https://www.nuget.org/packages/RZ.Wpf/)  | [0.7.1](https://www.nuget.org/packages/FSharp.Desktop.UI/)  |
| Framework        | netstandard2.0  | 4.5.1   | 4.5         | 4.5        | 4.5        | 4.5  


Оценки поставлены субъективно по следующим критериям:

Документация:

1. Отсутствует в принципе.
2. В зачаточном состоянии.
3. Дает базовое представление о проекте.
4. Описаны основные возможности, также есть раздел часто задаваемых вопросов.
5. Подробная, обновляемая.
 
|                        | Gjallarhorn  | VinylUI | Elmish.WPF | FsVM | RZ.Wpf | Desktop.UI |
| -------------          | ------------ |-------- | ---------  |----  | ---    | --- 
| Документация [ru]      | 1            | 1       | 1          | 1    | 1      | 1  
| Документация [eng]     | 2            | 3       | 4          | 1    | 3      | 3  

Поддержка:

1. Нет доступных материалов, получить помощь практически невозможно. 
2. Не большое количество статей, ответы на вопросы получить можно, но отвечающих 1-2 человека.
3. Не большое число материалов, помощь оказывается быстро.
4. Большое число материалов и людей использовавших библиотеку.

*Примечание*

> Под поддержкой подразумевается отличные от документации материалы (статьи, видеоролики, ..), а также оперативность в получении ответов на вопросы в сообществе.

   
|                        | Gjallarhorn  | VinylUI | Elmish.WPF | FsVM | RZ.Wpf | Desktop.UI |
| -------------          | ------------ |-------- | ---------  |----  | ---    | --- 
| Поддержка [ru]         | 3            | 1       | 2          | 2    | 1      | ?  
| Поддержка [eng]        | 3            | 2       | 3          | 3    | ?      | ?  

`?` означает что сложно дать какую-либо оценку. 

В случае с `FSharp.Desktop.UI` в виду популярности библиотеки в прошлом.

Активность:

1. По всей видимости находится в заброшенном состоянии
2. Добавление новых возможностей в обозримом будущем не предвидится.
3. Время от времени появляются обновления, найденные баги исправляются быстро
4. Проектом активно занимаются небольшое количество (1 - 2) контрибьютеров.

|                        | Gjallarhorn | VinylUI | Elmish.WPF | FsVM | RZ.Wpf | Desktop.UI |
| -------------          | ----------- |-------- | ---------  | ---  | ---    |
| Активность разработки  | 3           | 4       | 4          | 2    | 1      | 1  