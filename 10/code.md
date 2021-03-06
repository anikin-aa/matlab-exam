# Одномерная интерполяция. Реализация в среде MATLAB

__Интерполяция__ – определение промежуточных значений функции по известному дискретному набору значений функции.

Пусть про некоторую функцию `g(x)` известно, что она в точках `x1,x2,...,xn` (`x1<x2<...<xn`) принимает значения `y1,y2,...,yn` соответственно. Задача интерполяции заключается в построении такой функции `f(x)` из известного класса, которая в точках
`xi` принимает теже значения `yi`, что и функция `g(x)`. Функцию `f(x)` часто называют __интерполянтом__, точки `xi` - __узлами интерполяции__, велечины `yi` - __значениями интерполяции__
Выделим тот случай, когда `f(x)` - многочлен. Он называется __интерполяционным__. Известно, что для произвольной таблицы интерполяции

| x1  |  x2 |  xn |
| --- | --- | --- |
| y1  |  y2 | yn  |

  интерполяционный многочлен `f(x)` степени меньше `n` существует и единственен.

## Полиномиальная интерполяция

В среде Matlab коэффициенты интерполирующего многочлена можно найти с помощью функции `polyfit`

Если `x` - массив, содержащий `n` узлов, а `y` - массив, содержащий `n` значений, то

```matlab
polyfit( x, y, n-1 ); % вернёт вектор коэффициентов интерполяционного многочлена
```

Рассмотрим пример. Функцию `y = ln (x)` будем интерполировать кубическим полиномом по точкам `[0.4, 0.5, 0.7, 0.8]`:

```matlab
x = [0.4, 0.5, 0.7, 0.8];
y = log(x);
f = polyfit(x,y,3);
```

Получим:

```
f =

    1.6836   -4.5239    5.2760   -2.4106
```

Таким образом, интерполяционный полином имеет вид:

```matlab
f(x) = 1.6836 * x^3 - 4.5239 * x^2 + 5.2760 * x - 2.4106
```

Для того, чтобы подсчитать значение полинома в заданной точке, можно воспользоваться функцией `polyval`:

```matlab
polyval(f, 0.6)
```

Даёт значение:

```
ans =

   -0.5100
```

Фактическая абсолютная ошибка:

```matlab
abs(polyval(f, 0.6) - log(0.6))
```

```
ans =

   8.4992e-04
```

Более реальный пример:

```matlab
X = 0.5:0.5:10;
Y = log(X);

F = polyfit(X, Y, numel(X) - 1);

XX = 0.5:0.25:10;
YY = polyval(F, XX);

axis equal;
hold on;
plot(X, Y, '-g');
plot(XX, YY, '-r');
hold off;
```

Красным нарисована исходная функция, зелёным - результат интерполяции.

![Результат](/10/img3.png)

Погрешность интерполяции:

```matlab
plot(XX, abs(YY-log(XX)), '-r');
```

![Погрешность интерполяции](/10/img4.png)

## Кусочно-полиномиальная интерполяция

Функция называется кусочно-полиномиальной, если ее область определения раз-бивается на отрезки, на каждом из которых функция представляет собой многочлен.

Кусочно-полиномиальная интерполяция заключается в построении такой кусочно-полиномиальной функции, которая в заданных точках `x1, x2, ..., xn` принимает заданные значения `y1, y2, ..., yn` соответственно.

Для осуществления кусочно-полиномиальной интерполяции в MATLAB существует функция `interp1`:

```matlab
interp1(x, y, xx, type);
```

Где

* `x` - узлы интерполяции
* `y` - значения функции в узлах интерполяции
* `xx` - узлы, для которых нужно посчитать интерполированное значение
* `type` - метод интерполяции. Может быть `linear` (по-умолчанию), `nearest`, `spline`, `pchip`, `cubic`

Пример интерполяции функции `sin(x)`:

```matlab
x = 0:10;
y = sin(x);
xx = linspace (0, 10);
yy = sin(xx);
yn = interp1 (x, y, xx, 'nearest');
yl = interp1 (x, y, xx, 'linear');
yc = interp1 (x, y, xx, 'cubic');
ys = interp1 (x, y, xx, 'spline');

grid;
hold on;
plot(x, y, 'o');
plot(xx, yy, 'r');
plot(xx, yn, 'g');
plot(xx, yl, 'y');
plot(xx, yc, 'b');
plot(xx, ys, 'm');
hold off;
legend ('data', ...
    'function', ...
    'nearest', ...
    'linear', ...
    'cubic', ...
    'spline', 3);
```

![Результат](/10/img5.png)
