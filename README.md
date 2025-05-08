My Sudoku Solver in C
======
Это мой собственный проект — решатель судоку на языке C с возможностью оценки сложности задач по количеству откатов (backtracks), необходимых для нахождения решения.

Программа не только находит решения для головоломок судоку, но и позволяет анализировать их сложность. Сложность измеряется количеством откатов, которые алгоритм выполняет для нахождения правильного ответа. Это полезно для статистического анализа и визуализации сложности задач.

Проект полностью написан на языке C и не использует сторонних библиотек.

Требования
------
- C-компилятор (например, `gcc`)
- Утилита `gnuplot` для построения графиков
- Утилита `ImageMagick` для рендеринга изображений
- Unix-совместимая оболочка (`bash`)

Компиляция
------

> gcc solver.c -o solver
 
Использование
------


Программа читает входные данные из стандартного ввода и выводит решение в стандартный вывод. В командной строке можно указать формат ввода.

 1. Grid:

``` 
 53__7____
 6__195___
 _98____6_
 8___6___3
 4__8_3__1
 7___2___6
 _6____28_
 ___419__5
 ____8__79
```

 2. Linear:

``` 
 530070000600195000098000060800060003400803001700020006060000280000419005000080079
```

Пример запуска:

> ./solver 2 < puzzle.txt


Анализ сложности
======

Программа оценивает сложность задачи, подсчитывая количество откатов, которые алгоритм должен выполнить, чтобы найти решение. Для этого необходимо раскомментировать соответствующую строку в функции main:

```C
print(&s, 1);    // prints the solution
// printf("%d\n", s.nbacktracks);  // prints how many times it had to backtrack
```

Для анализа сложности можно использовать набор из ~50 000 задач:

> wget http://school.maths.uwa.edu.au/~gordon/sudoku17 -O puzzles.txt

Для подсчёта откатов для всех задач из файла выполните:

> ./solve 1 < puzzles.txt > nbacktracks.txt

Для построения гистограммы количества откатов используйте скрипт make_histograms.sh:

> cat nbacktracks.txt | sort -n | awk -F" " '{print exp(int(log($1))) }' | uniq -c > histogram.txt

> echo "set xlabel '#backtracks to solve (log-scale)';"               > plot.gnu

> echo "set ylabel 'Frequency';"                             >> plot.gnu

> echo "set output 'histogram.png'"                     >> plot.gnu

> echo "set term png"                                        >> plot.gnu

> echo "set logscale x;"                        >> plot.gnu

> echo "plot 'histogram.txt' u 2:1 w boxes t 'Sudoku 17-puzzles' ;"  >> plot.gnu


Запуск:
 
> ./make_histograms.sh

> gnuplot plot.gnu

![ScreenShot](https://raw.github.com/hpenedones/sudoku/master/analysis/histogram.png)


Для вывода списка 10 самых сложных судоку (по количеству откатов) используйте команду:


> paste nbacktracks.txt puzzles.txt | sort -nr | head -n 10

```
1165175 200500080001020000000000000070008000003000020000070600600200001040000700000300000
937856 004036000100000500000000000062000000000050700000800200000002004700000030050700000
669800 000870600200000000000100000060054000000000021400000000070000050000200300500001000
583255 000100038200005000000000000050000400400030000000700006001000050000060200060004000
452647 010050300000800000070000000020400700600280000300000100900000020000001000000000080
448038 200500070060030000000000000000040501004008000050000020100700000003000400000200000
436722 010050300000200000080000000020400800700620000300000100500000020000001000000000060
429469 010050300000600000080000000060400800700260000300000100900000020000001000000000060
427221 010600420000800000000050000005000030700400000000001000200030700000000504040000000
419682 200100600004000500000000000030040000800000009000050700050700010000800030007000000
```
Итак, теперь нам нужно отобразить эти головоломки судоку в удобном для пользователя виде.
Мы сохраняем 10 лучших судоку в файле (исключаем количество обратных ходов).:

> paste nbacktracks.txt puzzles.txt | sort -nr | head -n 10 | cut -f 2 > top10.txt

Загрузите изображение пустой доски для судоку откуда-нибудь из Интернета (поиск в Google images по запросу "sudoku empty").:

> wget http://www.scouk.net/entertainment/sudoku/blank_grid.gif


Rendering Sudoku puzzles
------

Затем используйте следующий скрипт для создания изображений:

> i=1; cat top10.txt | while read line;  do echo $line | fold -w 9 | tr 0 " " | head -c 89 | convert  -pointsize 100 -font Courier -size 531x721 label:@- puzzle.png ; convert puzzle.png -border 10x10 -splice 0x10 -resize 328x328\! puzzle.png; convert blank_grid.gif puzzle.png -average hardest_sudoku_$i.png; i=`expr $i + 1`; done

Готово!

Теперь мы можем отобразить 10 самых сложных головоломок судоку (по версии моего решателя).:

![ScreenShot](https://raw.github.com/hpenedones/sudoku/master/analysis/hardest_sudoku_1.png)

![ScreenShot](https://raw.github.com/hpenedones/sudoku/master/analysis/hardest_sudoku_2.png)

![ScreenShot](https://raw.github.com/hpenedones/sudoku/master/analysis/hardest_sudoku_3.png)

![ScreenShot](https://raw.github.com/hpenedones/sudoku/master/analysis/hardest_sudoku_4.png)

![ScreenShot](https://raw.github.com/hpenedones/sudoku/master/analysis/hardest_sudoku_5.png)

![ScreenShot](https://raw.github.com/hpenedones/sudoku/master/analysis/hardest_sudoku_6.png)

![ScreenShot](https://raw.github.com/hpenedones/sudoku/master/analysis/hardest_sudoku_7.png)

![ScreenShot](https://raw.github.com/hpenedones/sudoku/master/analysis/hardest_sudoku_8.png)

![ScreenShot](https://raw.github.com/hpenedones/sudoku/master/analysis/hardest_sudoku_9.png)

![ScreenShot](https://raw.github.com/hpenedones/sudoku/master/analysis/hardest_sudoku_10.png)
