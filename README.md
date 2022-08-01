# Тестовое задание на позицию Java Junior developer

## Постановка задачи
Нужно реализовать анализатор XML файла, который собирает статистику использования XML тегов в файле и сохраняет ее в БД.
Алгоритм работы анализатора:

**Шаг 1**: При запуске программы в параметре командной строки передается URL XML файла для анализа. Для примера можно взять [файл с описанием XML формата интеграции](http://integration.modeus-pages.custis.ru/docs/in/employees/groups.ru.files/groups.xsd) или [пример XML сообщения](http://integration.modeus-pages.custis.ru/docs/out/planning/course-unit-realization-change.ru.files/course-unit-realization-new.xml).

**Шаг 2**: Анализатор скачивает файл, разбирает его и собирает статистику по использованию тегов: сколько раз каждый тег встречается в этом файле. Например, для XML:
```xml
    <simpleType name="ТипСобытия">
        <annotation>
            <documentation>Событие, которое произошло с объектом: создание, обновление, удаление</documentation>
        </annotation>
        <restriction base="string">
            <enumeration value="Создание"/>
            <enumeration value="Обновление"/>
            <enumeration value="Удаление"/>
        </restriction>
    </simpleType>
``` 
статистика будет следующая: 
   1. `simlpeType` = 1
   1. `restriction` = 1
   1. `annotation` = 1 
   1. `documentation` = 1 
   1. `enumeration` = 3 


**Шаг 3**: Далее анализатор сохраняет результат обработки файла и статистику тегов этого файла в БД в таблицах вида
```sql
-- информация о разобранном файле
CREATE TABLE parsed_file
(
    file_id       BIGINT NOT NULL GENERATED ALWAYS AS IDENTITY,
    file_url      VARCHAR(255),
    parsed_at     TIMESTAMP, -- дата и время обработки файла
    file_status   VARCHAR(255), -- статус обработки файла – SUCCESS или FAILURE
    error_message VARCHAR(255), -- сообщение об ошибке (для статуса = FAILURE)
    CONSTRAINT pk_parsed_file PRIMARY KEY (file_id)
);

-- статистика по тегам
CREATE TABLE tag_record
(
    record_id    BIGINT NOT NULL GENERATED ALWAYS AS IDENTITY,
    tag_name     VARCHAR(255), -- имя тега в файле
    frequency    INT, -- частота появления тега в файле
    parsed_file_id BIGINT, -- ссылка на файл
    CONSTRAINT pk_tag_record PRIMARY KEY (record_id)
);

ALTER TABLE tag_record
    ADD CONSTRAINT FK_TAG_RECORD_ON_FILE_FILE FOREIGN KEY (parsed_file_id) REFERENCES parsed_file (file_id);

```
 - Структура таблиц не окончательная, можно расширить ее полями на свое усмотрение
 - Сохранение в БД желательно выполнить средствами ORM (например, Hibernate), но финальное решение по реализации остается за разработчиком. 
 - В качестве БД можно использовать DB H2, Postgres или любую другую доступную БД. Желательно при этом, чтобы результаты работы можно было проверить внешним Database клиентом.

 **Дополнительное необязательное условие**: при желании можно реализовать одновременную загрузку и анализ нескольких файлов, указанных в параметрах командной строки  

## Как выполнять задание
Сообщите, пожалуйста, сроки выполнения после того, как ознакомитесь с заданием.

Написанный код необходимо выложить на [Github](https://github.com) или [Gitlab](https://gitlab.com) и прислать ссылку на репозиторий с кодом.

Задание будет проверяться на Java 11 с параметрами JVM `-Xmx512` и `-Xmx1g`, если только вы не укажете другие параметры.
