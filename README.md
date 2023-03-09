# Домашнее задание к занятию 12.8. «Резервное копирование баз данных» - `Мальцев Виктор`

---

Задание 1. Резервное копирование
Кейс

Финансовая компания решила увеличить надёжность работы баз данных и их резервного копирования.

Необходимо описать, какие варианты резервного копирования подходят в случаях:

1.1. Необходимо восстанавливать данные в полном объёме за предыдущий день.

1.2. Необходимо восстанавливать данные за час до предполагаемой поломки.

1.3.* Возможен ли кейс, когда при поломке базы происходило моментальное переключение на работающую или починенную базу данных.

Приведите ответ в свободной форме.

Ответ:

1.1. Необходимо восстанавливать данные в полном объёме за предыдущий день.
Полное резервное копирование (Full backup), ежедневно ночью.

1.2. Необходимо восстанавливать данные за час до предполагаемой поломки.
Дифференциальный бэкап (differential backup), выполняется один раз в час.

1.3.* Возможен ли кейс, когда при поломке базы происходило моментальное переключение на работающую или починенную базу данных.
При использовании репликации в один момент времени будет несколько актуальных копий БД на разных серверах.
При настройке master-slave возможно переключение на slave, который становится master.
---

Задание 2. PostgreSQL

2.1. С помощью официальной документации приведите пример команды резервирования данных и восстановления БД (pgdump/pgrestore).

2.1.* Возможно ли автоматизировать этот процесс? Если да, то как?

Приведите ответ в свободной форме.

Ответ:

На примере БД test
pg_dump -U username -W -F p test > test.sql
pg_restore -U username -W -d test < test.sql

Эти процессы можно автоматизировать с помощью скриптов, например backup.sh.
#!/bin/bash
pg_dump -U username -W -F p test > test.sql
Затем это можно добавить в cron.

---

Задание 3. MySQL

3.1. С помощью официальной документации приведите пример команды инкрементного резервного копирования базы данных MySQL.

3.1.* В каких случаях использование реплики будет давать преимущество по сравнению с обычным резервным копированием?

Приведите ответ в свободной форме.

Ответ:

Например, в понедельник делается full backup с помощью утилиты mysqldump.
mysqldump -u username -p --single-transaction testdb > testdb_full.sql

Затем когда нужно сделать инкрементный бэкап можно использовать утилиту mysqlbinlog, которая скопирует
только изменения с момента полного бэкапа.

mysqlbinlog --start-datetime='20230306 00:00:00' /var/log/mysql-bin.* > testdb_incremental.sql

Для восстановления сначала используется полный бэкап, затем инкрементый.

В случае если, необходимо оперативное восстановление работоспособности приложения, то репликация лучше.
Но в случае поломки структуры master, то эти поломки перейдут и на slave.




