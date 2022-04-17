Описание

Решение для соревнования, который дается в конце курса "Введение в Data Science и машинное обучение" на stepik. На соревнование я не успел, оно прошло 3 года назад, но выполнить все равно было интересно.

Файлы:

inputs.rar - архив со входными параметрами
Stepik_MLcontest_cleancode.ipynb - решение
test_result.csv - результат решения (файл с пронозом, уйдет юзер с курса или останется).


Постановка задачи
Предсказать, завершит ли юзер онлайн курс Анализ данных в R на основании логов первых двух дней обучения. Считается, что пользователь успешно закончил курс, если он правильно решил больше 40 практических заданий.

В данных:

submission_data_test.csv
events_data_test.csv
хранится информация о решениях и действиях для 6184 студентов за первые два дня прохождения курса. Это 6184 студентов, которые проходили курс в период с мая 2018 по январь 2019.

Используя данные о первых двух днях активности на курсе вам нужно предсказать, наберет ли пользователь более 40 баллов на курсе или нет.

В этих данных, вам доступны только первые дня активности студентов для того, чтобы сделать предсказание. На самом деле, используя эти данные, вы уже можете сделать прогноз. Например, если пользователь за первые два дня набрал 40 баллов, скорее всего он наберет более 40 баллов в дальнейшем. Чтобы подкрепить такие гипотезы, вы можете использовать данные, на которые мы исследовали в первых двух модулях курса, где для всех пользователей представлены все данные об их активности на курсе.

Оформление результатов
Итогом вашей работы должен стать csv файл c предсказанием для каждого студента из тестовых данных. Пример предсказания выглядит следующим образом.

Чтобы узнать точность ваших предсказаний, в качестве решения этого шага отпраьте файл с предсказаниями для каждого студента в указанном выше формате.

Убедитесь, что вы сформировали файл с предсказаними для всех 6184 студентов, для каждого студента должна быть предсказана вероятность, что он наберет более 40 баллов за курс. У вас есть 20 попыток засабмитить решения, в зачет пойдет наилучший вариант.

Результатом проверки этого задания будет значение ROC AUC score, именно по этому показателю мы и отберем победителей, успехов!

Завершение соревнования - 1 июня.

Описание данных
events_train.csv - данные о действиях, которые совершают студенты со стэпами

step_id - id стэпа
user_id - анонимизированный id юзера
timestamp - время наступления события в формате unix date
action - событие, возможные значения:
discovered - пользователь перешел на стэп
viewed - просмотр шага,
started_attempt - начало попытки решить шаг, ранее нужно было явно нажать на кнопку - начать решение, перед тем как приступить к решению практического шага
passed - удачное решение практического шага


submissions_train.csv - данные о времени и статусах сабмитов к практическим заданиям

step_id - id стэпа
timestamp - время отправки решения в формате unix date
submission_status - статус решения
user_id - анонимизированный id юзера


Логика решения
1. Создание единого датафрейма. Объединение submissions и events в единый датафрейм - total.
2. Создание фич, необходимых для анализа, внутри total датафрейма: определение, кто закончил курс; определение, кто еще учится; исключение данных за дни после второго дня обучения для каждого из юзеров
3. Создание юзерского датафрейма, добавление в него фич, которые используются в рамках обучения: среднее время выполнения практического задания; число учебных сессий за первые два дня; относительная успешность решений; агрегированные показатели за первый день учебы; время между первым и последним действием за первые два дня; число степов, по которым к концу второго дня есть wrong, но нет correct
Для каждой фичи - отдельная функция. Функции укрупнены через агрегированные функции. Итоговый юзерский датафрейм создается одной функцией.

4. Перебор различных констант, определение оптимальной. Долгая функция, при каждом пользовании перебирать нецелесообразно.
5. Перебор различных комбинаций фич, определение оптимальной. Долгая функция, при каждом пользовании перебирать нецелесообразно.
6. Формирование случайного леса по выбранным фичам с выбранными константами.
