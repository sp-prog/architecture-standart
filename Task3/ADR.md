﻿### <a name="_b7urdng99y53"></a>**Название задачи:** ADR-1. Концептуальная архитектура отркытия депозитов для MVP
 
### <a name="_hjk0fkfyohdk"></a>**Автор:** 
### <a name="_uanumrh8zrui"></a>**Дата:** 
### <a name="_3bfxc9a45514"></a>**Функциональные требования**

|**№**|**Действующие лица или системы**|**Use Case**|**Описание**|
| :-: | :- | :- | :- |
| FT-1   | Клиент банка | Просматривает список депозитов | 1. Клиент банка входит в интернет-банк; 2. Клиента банка открывает список доступных депозитов; |
| FT-2   | Клиент банка | Открывает депозит | 1. Клиента банка просматривает список доступных депозитов с доступными ставками; 2. Клиент открывает депозит, выбрав счёт и введя сумму депозита; 3. Клиент банка подтверждает открывание депозита кодом из SMS-сообщения; 4. Заявка на открытие депозита отправляется в АБС и назначается на сотрудника бэк-офиса |
| FT-3   | Сотрудник бэк офиса | Актуализирует личные ставки по депозитам | 1. Сотрудник бэк-офиса входит в АБС под личной учеткой; 2. Для каждого клиента актуализирует его личные ставки;  |
| FT-4   | Сотрудник бэк офиса | Актуализирует общие ставки по депозитам | 1. Сотрудник бэк-офиса входит в АБС под личной учеткой; 2. Актуализирует общие ставки;  |
| FT-5   | Сотрудник бэк офиса | Обрабатывает заявку на открытие депозита | 1. Сотрудник бэк-офиса входит в АБС под личной учеткой; 2. Открывает список заявок, заначенных на него; 3. Обрабатывает необработанные заявки |

### <a name="_u8xz25hbrgql"></a>**Нефункциональные требования**
|**№**|**Требование**|
| :-: | :- |
|NFT-1. |Дизайн приложения должен быть выдержан в едином стиле корпоративного дизайна|
|NFT-2. |Все сервисы должны работать 24/7 в 99.9% случаев|
|NFT-3. |Необходимо предусмотреть возможность масштабирования вновь созданного функционала|
|NFT-4. |Необходимо предусмотреть возможность балансировки нагрузки между репликами систем банка|
|NFT-5. |У нового функционала должна быть поддержка ручного тестирования|
|NFT-6. |У нового функционала покрытие unit-тестами должно быть не менее 80%|
|NFT-7. |У нового функционала покрытие интеграционными тестами должно быть не менее 80%|
|NFT-8. |У нового функционала покрытие функциональными тестами должно быть не менее 80%|
|NFT-9. |Новый функционал интернет-банка должен разрабатываться на ASP.NET MVC 4.5 с использованием MS SQL|
|NFT-10. |Новый функционал АБС должен разрабатываться на Delphi с использованием СУБД Orcale и хранимых процедур|
|NFT-12. |Для разработки можно использовать новые технологии при наличии обоснования|
|NFT-13. |рассылку SMS желательно реализовать силами разработчиков банка|
|NFT-14. |процесс согласования заявки желательно реализовать в АБС|
|NFT-15. |работу с депозитами желательно реализовать в АБС|
|NFT-16. |Интернет-банк не должен иметь прямую интеграцию с АБС|
|NFT-17. |необходимо добавить шифрование трафика интернет-банка|
|NFT-18. |Невозможно выполнить требования по надежности|

### <a name="_qmphm5d6rvi3"></a>**Решение**

## Диаграмма контекстов

```plantuml

@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml

LAYOUT_WITH_LEGEND()

title Диаграмма контекстов банка Стандарт

Person_Ext(customer, "Клиент банка")
System_Ext(telecom_operator, "Телеком оператор")

Enterprise_Boundary(c0, "Банк Стандарт") {
    Person(employee, "Сотрудник бэк-офиса")
    System(abs, "Автоматизированная банковская система (АБС)")
    System(internet_bank, "Интернет-банк")
    System(sms_gate, "СМС-шлюз телеком-оператора")
}

Rel(customer, internet_bank, "1. Просматривает список депозитов")

Rel(customer, internet_bank, "2.1. Открывает депозит")
Rel(internet_bank, sms_gate, "2.2. Запрашивает отправку SMS-код")
Rel(customer, internet_bank, "2.4. Подтверждение открытия депозита")
Rel(internet_bank, abs, "2.5. Передача заявки на открытие депозита")

Rel(employee, abs, "3. Актуализирует личные ставки по депозитам")

Rel(employee, abs, "4. Актуализирует общие ставки по депозитам")

Rel(employee, abs, "5.1. Обрабатывает заявку на открытие депозита")
Rel(internet_bank, sms_gate, "5.2. Отправка SMS об открытии депозита")

Rel(sms_gate, telecom_operator, "Отправка SMS")
Rel(telecom_operator, customer, "Доставка SMS")

@enduml

```

## Схема контейнеров

```plantuml

@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml?a

title Диаграмма контейнеров банка Стандарт

Person_Ext(customer, "Клиент банка")
System_Ext(telecom_operator, "Телеком оператор")

Person(employee, "Сотрудник бэк-офиса")
Boundary(b0, "AБС") {
    Container(abs, "Автоматизированная банковская система (АБС)")
    ContainerDb(db_abs, "База данных", "Oracle", "Хранит данные АБС")
}

Boundary(b1, "Интернет-банк") {
    Container(internet_bank, "Основной сервис интернет-банк")
    Container(internet_bank_bg, "Вспомогательный сервис интернет-банка")
    ContainerDb(db_internet_bank, "База данных", "MS SQL Server", "Хранит данные интернет-банка")
}
Container(sms_gate, "СМС-шлюз телеком-оператора")

Boundary(b2, "Kafka") {
    ContainerQueue(queue_requests, "Канал обмена заявками на депозиты")
    ContainerQueue(queue_deposits, "Канал обмена ставками на депозиты")
}

Rel(customer, internet_bank, "1.1. Просматривает список депозитов", "https")

Rel(customer, internet_bank, "2.1. Открывает депозит", "https")
Rel(internet_bank, sms_gate, "2.2. Запрашивает отправку SMS-код", "https")
Rel(customer, internet_bank, "2.3. Подтверждает открытия депозита", "https")
Rel(internet_bank, db_internet_bank, "2.4. Сохранение заявки", "TLS")
Rel(db_internet_bank, internet_bank_bg, "2.6. Получение новых заявок для отправки", "TLS")
Rel(internet_bank_bg, queue_requests, "2.6. Отправка новых заявок", "TLS")
Rel(queue_requests, abs, "2.7. Получение новых заявок", "TLS")
Rel(abs, db_abs, "2.8. Сохранение заявок", "TLS")

Rel(employee, abs, "3.1. Актуализирует личные ставки по депозитам", "https")
Rel(abs, db_abs, "3.2.1 Сохранение ставок в БД", "TLS")
Rel(abs, queue_deposits, "3.2.2 Отправка ставок подписчикам", "TLS")

Rel(employee, abs, "4.1. Актуализирует общие ставки по депозитам", "https")
Rel(abs, db_abs, "4.2.1 Сохранение ставок в БД", "TLS")
Rel(abs, queue_deposits, "4.2.2 Отправка ставок подписчикам", "TLS")

Rel(queue_deposits, internet_bank_bg, "3.2.3, 4.2.3. Прием ставок по депозитам", "TLS")
Rel(internet_bank_bg, db_internet_bank, "3.2.4, 4.2.4. Обновление ставок по депозитам", "TLS")

Rel(employee, abs, "5.1. Обрабатывает заявку на открытие депозита", "https")
Rel(abs, db_abs, "5.2. Сохранение результата", "TLS")
Rel(abs, queue_requests, "5.3. Отправка результата подписчикам", "TLS")
Rel(queue_requests, internet_bank_bg, "5.4. Доставка изменений в заявке", "TLS")
Rel(internet_bank_bg, db_internet_bank, "5.5. Изменения статуса заявки", "TLS")
Rel(internet_bank_bg, sms_gate, "5.6. Отправка SMS при необходимости", "TLS")

Rel(sms_gate, telecom_operator, "Отправка SMS", "TLS")
Rel(telecom_operator, customer, "Доставка SMS", "Защита на стороне оператора связи")

SHOW_LEGEND()
@enduml

```

#### Шифрование
Для шифрования данных, передаваемых между сервисами, необходимо использовать HTTPS-протокол. Данное решение необходимо согласовать с отделом ИТ-безопасности банка. За неимением оной можно обратиться к организации, специализирующейся на предоставлении услуг ИТ-безопасности. Но т.к. в задании не указаны ограничения на выбор способа шифрования, то выбраны стандартные на всех уровнях по принципу "не доверяй никому"

#### Контроль нагрузки
В задании упоминается перегруженность сервиса АБС. Для предотвращения дополнительной нагрузки со стороны интернет-банка добавлена интеграция с брокером сообщений. Это позволит АБС выбирать данные без потерь с учетом нагрузки. В тоже время в задании указано, что интернет-банк невозможно интегрировать с kafka. 

#### Вспомогательный сервис
Вместо kafka можно попробовать использовать другой брокер сообщений, например, RabbitMQ, однако, есть рекомендация использовать именно Kafka. В связи с чем для работы с ней был создан отдельный вспомогательный сервис.

Вспомогательный сервис и интернет-банк используют часть базы данных интернет-банка в качестве общей предметной области - заявок на депозиты. Т.к. в задании упоминается о несовместимости "платформы", то новый сервис необходимо разрабатывать отдельно. Возможно на Java или Delphi, если C# уж прям совсем не совместим, что выглядит достаточно странным. В первых двух случаях код, отвечающий за работу с общей предметной областью, придется дублировать. В третьем - можно выделить в отдельную библиотеку или общий сабмодуль

Общая предметная область в БД позволяет быстро с учетом требования к масштабируемости без создания зависимостей между сервисами интернет-банка работать с необходимыми обоим сервисам данными. 

Функционал интеграции СМС-шлюзом можно реализовать в виде отдельного компонента, который будет подключен в вспомогательному сервису. В последствии компонент можно выделить в отдельный сервис при необходимости. Необходимость может возникнуть, если СМС-шлюз невозможно будет масштабировать и потребуется контроль нагрузки

### <a name="_bjrr7veeh80c"></a>**Альтернативы**
Вместо HTTPS протокола можно использовать продукт собственной разработки, но, обычно, такие продукты тяжелы для разработки. В описании задания отсутствует упоминание о наличии экспертов, но присутствует запрет на новые технологии без наличия экспертизы. Поэтому данное решение не подходит

Вместо решения общей предметной области БД сервисов интернет-банка можно было бы реализовать в вспомогательном сервисе АПИ для работы с таблицей, которое можно было бы вызывать в интернет-банке, но такое решение усложнило бы применение транзакций, в которых учувствовала бы таблица заявок, сильно связало бы сервисы через синхронное АПИ, усложнило бы поставку сервисов, снизило бы скорость работы сервиса интернет-банка с таблицей заявок.

**Недостатки, ограничения, риски**

Решение по созданию общей предметной области сервисов интернет-банка потребует применения дополнительные меры по недопущению изменений схемы данных БД, которые могут нарушить работу одного из сервисов, и дополнительных затрат на тестирование. Конфликты изменения записей в таблице возникнуть в данном решении не должно, т.к. со стороны АПИ интернет-банка изменения в таблицу вносятся только на этапе создания записи. 


