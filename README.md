# Домашнее задание к занятию `"13.Системы мониторинга"` - `Сулименков Алексей`

## Обязательные задания

1. Вас пригласили настроить мониторинг на проект. На онбординге вам рассказали, что проект представляет из себя
   платформу для вычислений с выдачей текстовых отчетов, которые сохраняются на диск. Взаимодействие с платформой
   осуществляется по протоколу http. Также вам отметили, что вычисления загружают ЦПУ. Какой минимальный набор метрик вы
   выведите в мониторинг и почему?

---

## Ответ

1.  Загрузка CPU
2.  Использование RAM
3.  Свободное место на диске
4.  Дисковая активность I/O
5.  Количество inode
6.  Загрузка сети
7.  Количество активных соединений
8.  Доступность сервисов
9.  LA

---

#

2. Менеджер продукта посмотрев на ваши метрики сказал, что ему непонятно что такое RAM/inodes/CPUla. Также он сказал,
   что хочет понимать, насколько мы выполняем свои обязанности перед клиентами и какое качество обслуживания. Что вы
   можете ему предложить?

---

## Ответ

1. RAM - объем оперативной памяти, который всего/занято/свободно
2. inode - это структура данных файловой системы Unix-подобных операционных систем, содержащая важную метаинформацию о файле или директории, такую как права доступа, владелец файла, размер, временные отметки изменения и модификации, а также адрес физического расположения данных на носителе. В большинстве файловых систем есть ограничение на максимальное количество inodes, доступных для хранения объектов. Когда лимит достигнут, невозможно создать новые файлы или каталоги, несмотря на оставшееся свободное место на диске.
3. LA (Load Average) — это метрика, отображающая среднюю нагрузку на систему за определенный промежуток времени. Складывется из нескольких параметров загрузка CPU, количество операций I/O диска, загрузка сетевого интерфейса.

Предложить менеджеру внедрить следующие показатели:

1. SLA - это соглашение об уровне обслуживания, документально закрепленное обязательство поставщика услуги обеспечить определенное качество предоставления услуг клиенту. Такие соглашения помогают формализовать ожидания обеих сторон и установить чёткие критерии оценки выполнения обязательств.

2. SLO (Service Level Objective) — это конкретные цели, устанавливаемые внутри организации или соглашения между сторонами, направленные на достижение определенного уровня качества обслуживания. Они представляют собой численные индикаторы, которые компания обязуется соблюдать в рамках своей работы или предоставляемых услуг.

3. SLI (Service Level Indicator) — это показатель, используемый для определения текущего состояния качества обслуживания, предоставляемого сервисом или продуктом.
   Latency (задержка): Измеряет время, затраченное на получение ответа от сервиса. Цель может заключаться в обеспечении задержки менее определенной величины (например, 50 мс).
   Error rate (частота ошибок): Определяет процент неудачно завершившихся запросов. Желаемое значение может быть установлено как максимум 0,1% ошибок.
   Throughput (пропускная способность): Оценивает количество запросов, обслуживаемых системой за единицу времени.Например, цель может звучать как обслуживание 10 тысяч запросов в минуту.
   Availability (доступность): Фиксирует продолжительность времени, в течение которого сервис функционирует должным образом. Обычно указывается как процент времени бесперебойной работы (например, 99,0%).

---

#

3. Вашей DevOps команде в этом году не выделили финансирование на построение системы сбора логов. Разработчики в свою
   очередь хотят видеть все ошибки, которые выдают их приложения. Какое решение вы можете предпринять в этой ситуации,
   чтобы разработчики получали ошибки приложения?

---

## Ответ

Необходимо настроить сбор и анализ логов на open source решениях(ELK, greylog, rsyslog + bash скрипты и т.д.). Для этого необходим, что бы разработка включила логирование в приложении стандартный вывод (stdout или stderr), с уровнем логирования INFO.

---

#

4. Вы, как опытный SRE, сделали мониторинг, куда вывели отображения выполнения SLA=99% по http кодам ответов.
   Вычисляете этот параметр по следующей формуле: summ_2xx_requests/summ_all_requests. Данный параметр не поднимается выше
   70%, но при этом в вашей системе нет кодов ответа 5xx и 4xx. Где у вас ошибка?

---

## Ответ

В формуле отсутствуют 3ХХ коды ответов, а так же не учтены сетевые проблемы(connection timeout).

(summ_2xx_requests + summ_3xx_redirect)/(summ_all_requests - sum_connection_timeout)

Для connection timeout необходимо сделать отдельный мониторинг.

---

#

5. Опишите основные плюсы и минусы pull и push систем мониторинга.

---

## Ответ

<details><summary>Pull</summary>
Плюсы:

Контроль и безопасность: Сервер контролирует весь процесс мониторинга, гарантируя полноту и регулярность проверок.
Масштабируемость: Легче реализовать масштабирование при росте числа устройств, так как сервер собирает данные самостоятельно.
Стандартизированный интерфейс: Большинство стандартных протоколов мониторинга (SNMP, SSH, RESTful API) работают в режиме pull.
Независимость от агента: Даже если агент выйдет из строя, сервер продолжит попытки опроса устройства.

Минусы:

Высокая нагрузка на сеть: Постоянные опросы увеличивают сетевую нагрузку, особенно при большом числе узлов.
Большое потребление ресурсов на стороне сервера: Один центральный узел отвечает за опрос множества источников, что увеличивает нагрузку на сервер мониторинга.
Задержки данных: Между двумя последовательными опросами может пройти некоторое время, что вызывает временную потерю актуальной информации.
Потеря контрольных точек: Если сервер мониторинга выходит из строя, информация о состоянии узлов теряется до восстановления.

</details>

<details><summary>Push</summary>
Плюсы:

Реальность данных: Информация отправляется немедленно, что делает её актуальной и своевременной.
Низкая нагрузка на сеть: Агент отправляет данные только тогда, когда произошли изменения, снижая сетевой трафик.
Равномерное распределение нагрузки: Нет постоянного активного опроса большого числа устройств, что уменьшает нагрузку на центральный сервер.
Лучшая реакция на события: Push позволяет мгновенно получать уведомления о любых изменениях, уменьшая время обнаружения аномалий.

Минусы:

Требования к ресурсам на стороне агента: Необходимо поддерживать активное соединение и постоянно отправлять обновления, что повышает нагрузку на каждый контролируемый узел.
Зависимость от агента: Выход агента из строя прекращает передачу данных, делая невозможным дальнейший мониторинг узла.
Необходимость централизованного протокола: Требуется стандартизация формата сообщений и совместимости различных версий агентов.
Безопасность: Возможность злоупотребления каналом передачи данных злоумышленниками (DoS-атаки, подделывание пакетов).

</details>

---

#

6. Какие из ниже перечисленных систем относятся к push модели, а какие к pull? А может есть гибридные?

   - Prometheus
   - TICK
   - Zabbix
   - VictoriaMetrics
   - Nagios

---

## Ответ

Pull-модель: Prometheus, Nagios

Push-модель: TICK stack

Гибридная модель: Zabbix, VictoriaMetrics

---

#

7. Склонируйте себе [репозиторий](https://github.com/influxdata/sandbox/tree/master) и запустите TICK-стэк,
   используя технологии docker и docker-compose.

В виде решения на это упражнение приведите скриншот веб-интерфейса ПО chronograf (`http://localhost:8888`).

P.S.: если при запуске некоторые контейнеры будут падать с ошибкой - проставьте им режим `Z`, например
`./data:/var/lib:Z`

---

## Ответ

</details>

<details> <summary>Веб-интерфейса ПО chronograf</summary>

![run](https://github.com/biparasite/10-monitoring-02-systems/blob/main/task_7.1.png "run")
![web](https://github.com/biparasite/10-monitoring-02-systems/blob/main/task_7.2.png "web")

</details>

---

#

8. Перейдите в веб-интерфейс Chronograf (http://localhost:8888) и откройте вкладку Data explorer.
   - Нажмите на кнопку Add a query
   - Изучите вывод интерфейса и выберите БД telegraf.autogen
   - В `measurments` выберите cpu->host->telegraf-getting-started, а в `fields` выберите usage_system. Внизу появится график утилизации cpu.
   - Вверху вы можете увидеть запрос, аналогичный SQL-синтаксису. Поэкспериментируйте с запросом, попробуйте изменить группировку и интервал наблюдений.

Для выполнения задания приведите скриншот с отображением метрик утилизации cpu из веб-интерфейса.

---

## Ответ

<details> <summary>Chronograf</summary>

![web](https://github.com/biparasite/10-monitoring-02-systems/blob/main/task_8.jpg "web")

</details>

---

#

9. Изучите список [telegraf inputs](https://github.com/influxdata/telegraf/tree/master/plugins/inputs).
   Добавьте в конфигурацию telegraf следующий плагин - [docker](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/docker):

```
[[inputs.docker]]
  endpoint = "unix:///var/run/docker.sock"
```

Дополнительно вам может потребоваться донастройка контейнера telegraf в `docker-compose.yml` дополнительного volume и
режима privileged:

```
  telegraf:
    image: telegraf:1.4.0
    privileged: true
    volumes:
      - ./etc/telegraf.conf:/etc/telegraf/telegraf.conf:Z
      - /var/run/docker.sock:/var/run/docker.sock:Z
    links:
      - influxdb
    ports:
      - "8092:8092/udp"
      - "8094:8094"
      - "8125:8125/udp"
```

После настройке перезапустите telegraf, обновите веб интерфейс и приведите скриншотом список `measurments` в
веб-интерфейсе базы telegraf.autogen . Там должны появиться метрики, связанные с docker.

Факультативно можете изучить какие метрики собирает telegraf после выполнения данного задания.

---

## Ответ

<details> <summary>Chronograf</summary>

![web](https://github.com/biparasite/10-monitoring-02-systems/blob/main/task_8.jpg "web")

</details>

---
