A.8 Become a Minecraft VJ

# Становимся Minecraft Виджеями

![Screen 0](../images/tutorial/articles/A.08-minecraft-vj/minecraft-vj-0-small.png)

Все вы играли в Minecraft. Все строили удивительные сооружения, конструировали
хитрые ловушки и даже создавали контролируемые транспортные линии. Но, держу
пари, вы не догадывались, что могли бы использовать Minecraft для создания
удивительных визуальных эффектов как профессиональный виджей.

Если вашим единственным способом игры с Minecraft была модификация игрового
мира с помощью мышки, вам наверняка было бы трудно изменять его достаточно
быстро. К счастью, ваш Raspberry Pi поставляется с версией Minecraft,
управляемой с помощью кода. Он также содержит приложение Sonic Pi, которое
делает кодирование Minecraft не только лёгким, но и невероятно весёлым.

В этой статье я покажу вам некоторые советы и трюки, использующиеся в
выступлениях в ночных клубах и концертных площадках по всему миру.

Итак, начнём...

## Приступая К Работе

Давайте начнём с простого разминочного упражнения, дабы освежить в памяти
основы. Во-первых, подключите ваш Raspberry Pi и запустите Sonic Pi вместе с
Minecraft. В Minecraft создайте новый мир, а в Sonic Pi выберите пустой буфер
и наберите там этот код:


    mc_message "Let's get started..."

    
Нажмите кнопку "Выполнить", и вы увидите сообщение в окне Minecraft. Хорошо,
мы готовы начать, давайте повеселимся......

## Песчаные Бури

Когда мы используем Minecraft для создания визуальных эффектов, мы стараемся
думать о том, чтобы это одновременно выглядело интересно и легко генерировалось
с помощью кода. Интересный трюк - создать песчаную бурю, сбрасывая песчаные
блоки с неба. Для этого нам необходимы несколько базовых функций:

- `sleep` - для того, чтобы вставить задержку между действиями 
- `mc_location` - для нахождения нашего текущего местоположения
- `mc_set_block`- чтобы поместить песчаный блок в заданное место
- `rrand` - для генерации случайных чисел в заданном диапазоне 
- `live_loop` - позволяет сделать дождь из песка непрерывным

<!-- Breakout box start --> 

Если вы не знакомы с какой-либо из встроенных функций, например `rrand`, просто
введите её название в свой буфер, кликните на нём, а затем используйте
клавиатурное сочетание `Ctrl-i`, вызвающее встроенную документацию. В качестве
альтернативы вы можете переместиться к вкладке *язык* справочной системы, и
найти нужную функцию прямо там, как и другие интересные вещи.

<!-- Breakout box end -->

Прежде чем высвободить всю мощь бури, сделаем небольшой дождь. Возьмём ваше
текущее местоположение и используем его для создания нескольких песчаных блоков
в небе неподалеку:


    x, y, z = mc_location
    mc_set_block :sand, x, y + 20, z + 5
    sleep 2
    mc_set_block :sand, x, y + 20, z + 6
    sleep 2
    mc_set_block :sand, x, y + 20, z + 7
    sleep 2
    mc_set_block :sand, x, y + 20, z + 8


Когда вы нажмете "Выполнить", вам, возможно, придется немного осмотреться, так
как блоки могут начать падать позади вас в зависимости от того, в каком
направлении вы в настоящее время смотрите. Не волнуйтесь, если вы пропустили
их, просто нажмите "Выполнить" снова, и вы получите ещё одну порцию песчаного
дождя - просто убедитесь, что вы смотрите в правильном направлении!

Давайте быстро посмотрим, что тут у нас происходит. На первой строке мы
получили местоположение Стива в виде координат с помощью функции `mc_location`
и присвоили их переменным `х`, `у`, и `z`. На последующих строках мы
использовали функциию `mc_set_block`, чтобы поместить немного песка в тех же
координатах, что и Стив но с некоторыми изменениями. Мы выбрали ту же
координату `x`, но координата `y` стала на 20 блоков выше, а затем
последовательно увеличивали `z`, чтобы песок падал в линию, удаляющуюся от
Стива.

Почему бы вам самим не поиграть с этим кодом? Попробуйте добавить больше линий,
изменените время задержки, попробуйте смешать `:sand` с `:gravel` и выбирать
разные координаты. Просто экспериментируйте и веселитесь!

## Раскручиваем Живые Циклы

Хорошо, пришло время получить бушующую бурю, высвободив мощь `live_loop` -
магической способности Sonic Pi, раскрывающей всю силу лайвкодинга - изменения
кода в реальном времени!


    live_loop :sand_storm do
      x, y, z = mc_location
      xd = rrand(-10, 10)
      zd = rrand(-10, 10)
      co = rrand(70, 130)
      synth :cnoise, attack: 0, release: 0.125, cutoff: co
      mc_set_block :sand, x + xd, y + 20, z + zd
      sleep 0.125
    end


Это весело! Мы выполняем круг цикла довольно быстро (8 раз в секунду), и во
время каждого круга мы находим местоположение Стива также, как и раньше, но в
этот раз генерируем 3 случайных значения:

- `xd` - разница для `x`, которая будет находиться между -10 и 10 
- `zd` - разница для `z`, которая также будет между -10 и 10 
- `co` - значение среза фильтра низких частот между 70 и 130

Затем мы используем эти случайные значения в функциях `synth` и `mc_set_block`,
что даёт нам песок, падающий случайным образом вокруг Стива вместе с
перкуссионным звуком, похожим на дождь, от синтезатора `:cnoise`.

Для тех из вас, кто ещё новичок в живых циклах - именно здесь и начинается
самое интересное. Пока код выполняется и песок сыплется вниз, попробуйте
изменить одно из значений. Например, время задержки на `0.25`, или тип блока с
`:sand` на `:gravel`. Теперь нажмите "Выполнить" *снова*. Эй! Всё изменилось
без остановки кода. Это и есть та самая дверь к выступлениям в роли виджея.
Продолжайте тренироваться и что-нибудь изменять. Насколько разнообразными можно
сделать визуальные эффекты, не останавливая кода?

## Эпические Блочные Структуры

![Screen 1](../images/tutorial/articles/A.08-minecraft-vj/minecraft-vj-1-small.png)

Наконец, еще один отличный способ создания интересных визуальных эффектов - 
генерировать огромные повторяющиеся стены, парящие рядом. Для этого эффекта нам
нужно перейти от размещения блоков случайным образом, к размещению их в
упорядоченном виде. Мы можем сделать это с помощью двух вложеных итераций
(нажмите кнопку "Помощь", чтобы перейти в раздел 5.2 учебника "Итерации И
Циклы", чтобы узнать больше об итерациях). Аргумент `|xd|` после `do` означает,
что `xd` будет соответствовать номеру итерации. Поэтому сначала это будет 0,
потом 1, потом 2... и т. д. Совмещая две итерации вместе, мы можем
сгенерировать любые координаты. Затем мы произвольно выбираем типы блоков из
кольцевого списка для создания интересного эффекта:


    x, y, z = mc_location
    bs = (ring :gold, :diamond, :glass)
    10.times do |xd|
      10.times do |yd|
        mc_set_block bs.choose, x + xd, y + yd, z
      end
    end


Довольно аккуратно. В то время как мы здесь веселимся, попробуйте изменить
`bs.choose`, на `bs.tick`, чтобы перейти от случайного выбора к
последовательному. Попробуйте изменять типы блоков более радикально. Вы можете
захотеть делать это с `live_loop`, чтобы узоры продолжали изменяться
автоматически.

Теперь, измените обе итерации с `10.times` на `100.times` и нажмите
"Выполнить". Бабах! Огромные исполинские стены с хаотично расположенными
кирпичами. Представьте сколько времени потребуется вам, чтобы построить их
вручную с помощью мыши! Дважды нажмите пробел, чтобы переключиться в режим
полёта и начните парить для создания замечательных визуальных эффектов. Не
останавливайтесь на этом, используйте своё воображение, чтобы придумать
интересные идеи, а затем, использовав силу кода Sonic Pi, сделайте это
реальностью. Когда вы достаточно попрактикуетесь, приглушите свет и покажите
шоу своим друзьям!
