## Заметки (возможно с ошибками)

### Что я вроде как понял
#### Основы
  Есть классификация и есть регрессия. Классификация — это когда по вектору признаков объекта нужно 
  предсказать к какому классу относится объект (типа понять по картинке хот-дог или не хот-дог), 
  а регрессия — когда нужно предсказать вещественное число (какая будет температура, если у нас есть какая-то история).
  
  Можно попробовать применить линейную модель, то есть подобрать для каждого признака объекта вес (+1 свободный член) 
  — то есть гиперплоскость (прямая, если признак один), которая в случае классификации разделяет два (больше линейная 
  разделить не сможет) класса, а в случае регрессии — гиперплоскость прямо и апроксимирует нужные значения.
  
  Для подбора параметров решаем задачу минимизации ошибки. В регрессии можно было бы взять модуль разницы предсказания модели 
  и реального значения из данных, но обычно берут среднеквадратичную ошибку MSE тупо потому что у нее один минимум и есть 
  производная во всех точках (выпуклая и гладкая) — тогда можно применить метод градиентного спуска. 
  В классификации сложнее — там функция, сообщающая долю ошибок негладкая и вместо нее оптимизируют другую функцию, которая ограничивает ее сверху (и вообще-то необязательно имеет минимум там же)

#### Переобучение
  Бывает недообучение и переобучение. Недообучение — когда алгоритм (типа линейного) не может описать более сложную зависимость, а переобучение — когда алгоритм дико подогнался под обучающие данные, а если пытаться им предсказать другую выборку — получится плохой результат. Тут много решений:
     * посмотрим на полученные коэффициенты модели и если они конские, то это может быть симпотомом переобучения — можно добавить к функции ошибки регуляризатор — например сумму квадратов весов — и тогда веса получатся меньше
     * отложим, скажем, 25% данных для обучения и потом проверим на них — должна получиться примерно такая же как и на первых 75% данных ошибка, иначе есть переобучение
     * в предыдущем варианте можно неудачно разбить (особые объекты будут в одной выборки и не будет в другой) — есть обобщение — кросс-валидация — выборка разбивается на тренировочную и тестовую k раз, а потом берется средняя оценка ошибки

#### Метрики качества обучения
  Дополнительные метрики качества алгоритма. В регрессии это в основном про интерпретацию MSE. В классификации — это точность и полнота. Точность — какая доля правильных ответов среди всех объектов, которые //модель отнесла// к классу +1. Полнота — какая доля правильных ответов по отношению ко всем объектам, которые //действительно относятся// к классу +1.

#### Нюансы
  Признаки нужно масштабировать (вычесть матожидание и поделить на стандартное отклонение, либо смаппить на [0, 1], вычислив min/max), просто потому что градиентный спуск лучше работает на функциях, похожих на круги, а не овалы.
  
  Можно брать квадраты, кубы и тд от признаков и добавлять их в множество признаков — возможно получится модель получше.
  
  Если в классификации объектов разных классов неравномерно, то нужно балансировать:
     * выкидывать лишнее
     * дублировать маленькие данные
     * стратификация при кросс-валидации

#### Решающие деревья
   По значениям признаков строим деревья, которые в узлах проверяют скажем больше или меньше один из признаков какого-нибудь порога. В итоге группы объектов рассыпаются по листьям дерева и дальше дерево можно использовать для прогнозирования. Если делать слишком глубоким — обязательно переобучится. Есть разные способы выбора какой признак брать и с каким порогом сравнивать в данном узле — оптимизируем некую функцию от этих параметров (критерий информативности). 

  Более крутая штука — Случайный лес — рандомно строят много неглубоких деревьев и потом берут класс, за который голосует большинство деревьев. Рандомизировать можно строя деревья на разных подвыборках обучающей выборки или разном подмножестве признаков. Они не переобучаются и их можно обучать параллельно. Есть проблема — обучать много деревьев вычислительно сложно, если они глубокие, а если неглубокие — будут плохо работать
  
  Вышеописанные проблемы решает градиентный бустинг — идея в том, чтобы получить алгоритм-композицию простых, последовательно обучая простые алгоритм и добавление каждого следующего должно минимизровать общую ошибку. Но тут опять возвращается переобучение, с которым нужно бороться.
  
  
#### Нейронные сети
   В нейронных сетях тоже подбираем веса для признаков, но помимо этого подбираем функции и веса для внутрнних слоев нейронной сети. Если взять однойслойную сеть и функцию-активации id — то получится линейный регрессор.
  
#### knn
   Можно просто запомнить всю выборку, а потом при прогнозе выдавать тот класс, который стоит у ближайших соседей объекта (или среднее, если речь о регрессии). Можно предсказывать оценки для фильмов, если найти k ближайших похожих пользователей или k ближайших похожих фильмов.

### Что я не понял
  * все, что связано с Байесом/распределениями — фундаментально не понимаю ничего в тервере и не хватает интуиции
  * а также метод максимального правдоподобия
  * до сих пор не научился нормально манипулировать многомерными numpy-массивами, также как и pandas-датафреймами.
