основные способы синхронизации паралельных операций:
* condition_variable используется для ожидания определенного условия и уведомления других потоков, когда это условие становится истинным.
condition_variable может использоватся для блокировки потока при помощи мютекса (wait), фактически condition_variable управляет std::unique_lock который в свою очередь управляет мютексом
condition_variable так же может оповещять свой инстанс в других потоках о изменении состояния, разблакируя мютексы в этих потоках, сработать это может в двух режимах notify_one оповестит один поток, и notify_all оповестит все потоки
так как condition_variable имеет переодически срабатывание без выполнения условий снятия блокировки, при выполнении ожидания необходимо добавить короткоий чистый придекат который проверит условие чтоб обезапасить себя.
* ожидание одноразовое событие
выполняется за счет так называемых будуещих результатов (std::future), срабатывает гарантировано но только один раз, способен возвращять как значения так и исключения.
std::async - использует будуещие результаты по дефолту, и возврящет future при создании, возвращяемое значение функции будет установленно как возвращяемое значение std::future.
std::packaged_task - позволяет обернуть любую функцию в специальный обект, данны обект может быть передан другому потоку, или выполнен в другом потоке, так же packaged_task может при вызове get_future вернуть std::future который позволит получить результат выполнения задачи
std::promise - позволяет в потоке установить значение, которое мы сможем затем получить при помощи соответствуещего std::future который был создан из данного промиса при помощи метода get_future
при необходимости получить результат работы в нескольких разных местах (разных потоках) следует использовать std::shared_future создать можно при помощи get_future().share() однако так как обект уже не владеет данным фючерсом то из обекта промиса мы его больше получить не сможем

отложенное ожидание
* мы можем ожидать промежуток времени при помощи std::future::wait_for/std::condition_variable::wait_for и передав время ожидания при помощи обектов std::chrono
* мы можем ожидать определенного времени при помощи std::future::wait_until/std::condition_variable::wait_until и передав время ожидания при помощи обектов std::chrono

при использовании только чистых функций нет необходимости в синхронизации так как результаты разных потоков не зависят друг от друга, по тому при написании програмы по возможности стоит попытатся обратить внимание на это
CSP потоки общяются через специальные места обмениваясь сообщениями, допустим в существующие потоки мы присылаем задания при помощи очереди, в таком случае синхронизацию будет проще держать под контролем
так же можно привести потоки к конечнм автоматам, где поток меняет свое состояние под влеянием внешних потоков перетикая из состояние в состояние, это в совмещении с прошлым вариантом позволит держать логику синхронизации и взаимодействия потоков под контролем
