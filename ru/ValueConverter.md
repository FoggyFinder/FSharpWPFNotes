﻿# Конвертеры значений

Назначение конвертеров значений в принципе достаточно очевидно - 
преобразовывать исходные данные во что-то подходящее для отображения.
Для того, чтобы работала двунаправленная привязки необходимо реализовать
и обратную трансформацию.

В `WPF ` для создания собственного конвертера необходимо реализовать
интерфейс `IValueConverter` . 

Он содержит два метода :

- Convert
- ConvertBack

как следует из названия для прямого и обратного преобразования соответственно.

__Заметка__
> В `Visual Studio` есть возможность упростить себе немного жизнь с помощью 
> быстрого создания скелета через выделение названия интерфейса и нажатием комбинации `Ctrl + .`
> Будет предложено два вариант - легковестный и обычный. Отличаются тем, что во втором случае типы будут указаны явно.

Подробнее читайте тут : 
http://fsprojects.github.io/VisualFSharpPowerTools/implementinterface.html