## Типы ошибок в паралелизме

### Нежелательное блокирование

1. **Взаимоблокировка (Deadlock):**
   - **Описание:** Несколько потоков блокируют друг друга, ожидая освобождения ресурсов, которые удерживают другие потоки.
   - **Пример:** Поток A удерживает мьютекс M1 и ожидает мьютекс M2, в то время как поток B удерживает мьютекс M2 и ожидает мьютекс M1.

2. **Активная блокировка (Livelock):**
   - **Описание:** Потоки взаимодействуют так, что каждый из них ожидает активности другого, что приводит к постоянной смене состояний без прогресса.
   - **Пример:** Два потока пытаются передать ресурсы друг другу, но из-за определенных условий они постоянно изменяют свое состояние без завершения операции.

3. **Ожидание внешнего источника (Resource Starvation):**
   - **Описание:** Поток ожидает внешний ресурс, например, ввод-вывод, и блокируется, пока ресурс не станет доступным.
   - **Пример:** Поток ожидает данные из сети и блокируется, пока данные не поступят.

### Состояние гонки

1. **Гонка за данными (Race Condition):**
   - **Описание:** Несколько потоков пытаются одновременно изменить общие данные без синхронизации, что может привести к непредсказуемым результатам.
   - **Пример:** Два потока пытаются увеличить значение переменной x, и результат зависит от порядка выполнения операций.

2. **Нарушение инвариантов (Invariant Violation):**
   - **Описание:** Инварианты, предполагаемые для корректности программы, нарушаются при одновременном выполнении нескольких потоков.
   - **Пример:** Предположим, что сумма двух переменных всегда должна быть постоянной. Если два потока изменяют эти переменные, не синхронизируясь, инвариант может быть нарушен.

3. **Проблема времени жизни (Time of Check to Time of Use, TOCTOU):**
   - **Описание:** Ошибка, при которой результат проверки и использования общего ресурса происходят в разные моменты времени, что создает возможность гонки.
   - **Пример:** Поток проверяет наличие файла, а затем читает его. Если между проверкой и чтением файл был изменен другим потоком, это может привести к ошибке.
   
### Методы поиска ошибок
* ревью кода - имеет смысл посмотреть код со стороны, и начинать изучать код с самого начала, попытатся обяснить подозрительные компоненты воображаемым друзям.
имеет смысл задатся такими вопросами при изучении кода:
** Какие данные нуждаются в защите от одновременного доступа?
** Как обеспечивается безопасность данных?
** Какие действия могут выполняться другими потоками в этот момент?
** Какие мьютексы удерживают другие потоки?
** Как ограничивается порядок операций в данном и других потоках, и как гарантируется этот порядок?
** Действительны ли данные, с которыми оперирует текущий поток?
** Мог ли другой поток изменить важные данные?
** Если поток изменил данные, что произойдет, и как гарантировать, чтобы таких ситуаций не возникало?

### Тесты для поиска ошибок
код должен соответствовать:
* Обязаности всех функций и классов четко очерчены
* каждая функция короткая и решает одну задачу
* тесты способны полностю контролировать окружение
* код пишется с расчетом на его тестопригодность

при тестировании
* Можно убрать паралелизм из теста для проверки того что ошибка связана с паралелизм, а это не просто бага
* Стоит перебрать для минимального куска всевозможные комбинации, и сравнить результат с ожидаемым

способы тестирования
* тестирование грубой силой - большая нагрузка на небольшом учатске кода, для выявления опасных ситуаций, на больших частях кода не эфективно, не дает гарантии, так как является случайностю, на другом окружении может изменится результат (на пример при изменении количества ядер)
* комбинаторный вариант - поднимается специальное окружение, которое комбинаторно переберает все возможные варианты работы нескольких потоков, исчерпуещие, дорогое, и изза сложности работы с спец окружением является скорее теоретическим
* спец библиотека - используется специальная библиотека, при тестировании позволяет задать правила по которым потоки должны взаимодействовать, и позволяет детектить нарушения данных правила

Структура теста:
* код общей настройки - (настраиваются все переменные, код подготавлевается к запуску, подготовка в сущьносте которая запустит потоки)
* потоковый код настройки - подготавливает каждый поток к запуску
* содержательный код - то что мы тестируем
* код исполняемый по завершению - тут находятся все проверки которым мы должны соответствовать
