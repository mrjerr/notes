tmux
=

### Создаем сессию для быстрого запуска нужного количества окон и панелей
```
> vim .tmux/session1
neww
split-window -v -p 85  
split-window -v -p 80 'ipython'
split-window -h -p 30
select-pane -t 3
split-window -h -p 50
```

```
     100% ширины окна
----------------
|     15%(1)    | 15% высоты окна
----------------
|     20%(2)    | 17% высоты окна
----------------
| 50%| 50%| 30%|  68% высоты окна
| (3)| (5)| (4)|
----------------
 35%   35%  30%  ширины окна
```
Логика деления такая.
1) neww - создается окно
2) split-window -v -p 85 - создаем вторую панель, раслогая ее под первой, вторая панель будет занимать 85% высоты, первой панель остается 15%
3) split-window -v -p 80 'ipython' - из второй панели создаем 3-ю панель, которая будет занимать 80% размера 2-й панели, таким образом для 2-й панели останется 20% высоты ее предыдущего размера и 17% высоты от размера окна в целом. Так же в этой панели выполняется команда *ipython*.
Для 3-е панели остается 68% высоты экрана.
4) split-window -h -p 30 из третьей панели создаем 4-ю панель 30% ширины
при создании панели, мы переключаеся на нее, поэтому что бы разделить другую панель, сначала нужно ее выбрать.
5) select-pane -t 3 = выбирам 3-ю панель
6) Делим 3-ю панель пополам, создая таким образом 5-ю, с шириной 50% от текущего размера панели 3. По факту получаем три панели с ширино1 35%, 35% и 30%