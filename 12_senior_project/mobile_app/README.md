# Исследование аудитории приложения «Ненужные вещи» 

В приложении «Ненужные вещи» пользователи продают свои ненужные вещи, размещая их на доске объявлений.
Необходимо провести исследование аудитории сервиса по логам пользовательских событий в приложении, совершенных после 07.10.2019.

**Цель исследования** - на основе сегментации пользователей (заданной и выделенной самостоятельно) получить гипотезы о том как можно было бы улучшить приложение с точки зрения клиентского опыта. Заданная сегментация пользователей - по источникам скачиванaия приложения.

**Задачи исследования:**
1. Выделить сегменты пользователей для создния второй пары тестируемых групп.
2. Исследовать, какие пользователи склонны чаще возвращаться в мобильное приложение.
2. Исследовать, какие пользователи чаще совершают целевое событие (конверсия в целевое действие).
3. Определить, как различается время между распространенными событиями пользователей из разных групп.
4. Подготовить презентацию с результатами исследования. 

**Источник данных** - 2 датасета: один с действиями пользователей в приложении, второй с данными об источниках скачивания приложения.

**Навыки и инструменты** - python, pandas, numpy, math, datetime, scipy.stats, matplotlib, seaborn, plotly,  событийная аналитика, продуктовые метрики, проверка статистических гипотез. 

# Резюме

Обобщая полученные данные, можно с достаточной степенью уверенности сказать, что:
1. Пользователи группы google и пользователи с короткими сессиями чаще возвращаются в приложение.
2. Среди пар сравниваемых групп конверсия находится примерно на одном уровне: среди групп yandex и google конверсия равна 25,28% против 24,11%, а среди групп с короткими и длинными сессиями - 17,97% против 16,55%, соответственно. Разница составляет 0,17% между группами по источникам и 1,42% между группами по длительности сессий.
3. Среди пользователей групп google и yandex время, проводимое в приложении, практически не отличается: 10:12 против 10:43, а среди групп с короткими и длительными сессиями разница в 14,4 раза: 1:28 минут против 18:45 минут соответственно.
1. Несмотря на численное превосходство группы yandex и её незначительно более высокую конверсию, обе группы (google и yandex) приносят приложению пользователей одинакового качества.
2. Несмотря на достаточно интенсивную активность пользователей с короткими сессиями и незначительно более высокую конверсию среди них, пользователи с длинными сессиями значимо не отличаются от них, то есть для приложения пользователи обоих групп качественно идентичны.

Для повышения конверсии в целевое действие среди пользователей группы yandex необходимо улучшить их удержание, а для группы google стоит сосредоточиться на возможности повышения активности пользователей.

Для увеличения конверсии среди групп с короткими сессиями необходимо улучшить их удержание и сосредоточиться на возможности повышения активности пользователей, а для улучшения конверссии среди пользователей с длинными сессиями
необходимо повышать их удержание.

**Презентация**

https://disk.yandex.ru/i/3z6VxbSu-YTFLQ

**Дашборд**

https://public.tableau.com/app/profile/anna3994/viz/Nenuzhnye_veshi_manuilova_2/Dashboard1?publish=yes

# Краткое содержание работы

**В 1-ом разделе выполнялось первичное изучение данных.** 
Здесь было произведено ознакомление с данными методом `info`, а также проводилось сравнение уникальных пользователей между датасетами методом `nunique`. В данные были выявлены аспекты, требующие исправления на следующем этапе.

**Во 2-м разделе проводилась предобработка данных.**

На этом шаге наименование столбцов обоих датасетов было приведено к единому стилю написания методом `rename`, проверены и устранены смысловые дубликаты методом `value_counts` (в отношении действий пользователей), столбец с датами событий приведен к соответсвующему формату при помощи `to_datetime`.

**В 3-м разделе проводился исследовательсктй анализ данных.**

На этом этапе исходные датасеты о событиях и логах сессий были объединены в один общий `df` для удобства работы. Затем, опираясь на  результаты независимого [исследования](https://traff.ink/articles/trendy-mobilnyh-prilozhenij-2021-2022/#timesess), которые можно принять за стандарт, были выделены сессии пользователей и сформирован датасет для поиска аномалий - `df_20`.

При проверке данных на выбросы было проанализировано 3 параметра: пользователи, сессии и даты событий. В каждом из них были обнаружены выбросы, совокупный объем которых составил 37,5%.

С учетом ограниченности исходных данных и невозможности получения доп.информации, было принято решение очистить датасет только по показателю средней продолжительности одной сессии, так как этот параметр использовался для сегментации пользователей на группы.

После очистки датасета от выбросов пользователи были распределены на группы:
 - по медианному показателю средней продолжительности одной сессии `df_duration`: короткие (А) и длинные (В),
 - по источникам скачивания приложения `df_source`: google и yandex.

**В 4-ом разделе проводился непосредственно анализ данных.**

В данном разделе были проанализированы все группы пользователей по таким метрикам как: 
- удержание, 
- время, проведенное в приложение,
- частота действий,
- конверсия в целевое действие - просмотр контактов.

**В 5-ом резделе проводилось тестирование гипотез.**

На основе проведенного анализа и выдвинутых ранее предположений были сформулированы следующие гипотезы:

1. Гипотезы для групп, разделенных по признаку источника скачивания приложения:
    - Нулевая гипотеза: Группа пользователей, установившая приложение по ссылке из yandex, и группа пользователей, установившая приложение поссылке из - google, демонстрируют одинаковую конверсию в просмотры контактов.
    - Альтернативная гипотеза: Группа пользователей, установившая приложение по ссылке из yandex, и группа пользователей, установившая приложение поссылке из - google, демонстрируют разную конверсию в просмотры контактов.
    
2. Гипотезы для групп, разделенных по признаку средней продолжительности одной сессии:
     - Нулевая гипотеза: Пользователи группы с короткими сессиями и пользователи с длинными сессиями демонстрируют одинаковую конверсию в целевое действие.
     - Альтернативная гипотеза: Пользователи группы с короткими сессиями демонстрируют более высокую конверсию в целевое действие, чем пользователи с длинными сессиями.

Для тестирования гипотез был проведен z-тест сравнения конверсий в целевое действие по долям и в обоих случаях был получен отрицательный результат: нулевую гипотезу о равенстве конверсии между каждой парой групп отвергнуть нельзя.

Результаты анализа говорят о том, что в обоих случаях конверсия каждой пары групп не идентична, но разница составляет 0,17% среди групп по источникам скачивания и 1,42% среди групп по длительности сессий. Таким образом, можно сделать вывод о том, что разница в общей конверсии между группами в относительных величинах не является статистически значимой при тестировании гипотез.
