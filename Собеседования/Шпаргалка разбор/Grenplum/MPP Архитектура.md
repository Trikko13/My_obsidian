### **Зачем нужна MPP-архитектура?**

**MPP (Massively Parallel Processing)** — это архитектура, в которой задачи обрабатываются одновременно (параллельно) на множестве узлов. Каждый узел имеет собственные процессоры, память и диски, что делает такую архитектуру эффективной для больших объёмов данных.

#### Задачи, решаемые MPP-архитектурой:

1. **Обработка больших данных**:
    
    - Традиционные реляционные базы данных с централизованным сервером (SMP, Symmetric Multiprocessing) сталкиваются с ограничением вычислительных и хранилищных ресурсов. MPP позволяет распределять данные и нагрузку между узлами, что делает возможной обработку петабайт данных.
2. **Высокая производительность**:
    
    - Каждый узел обрабатывает свою часть данных параллельно, что значительно ускоряет выполнение запросов. Например, вычисления, такие как `JOIN` или `GROUP BY`, выполняются на каждом узле локально, а затем объединяются.
3. **Масштабируемость**:
    
    - Легко добавлять новые узлы в кластер, чтобы увеличить вычислительную мощность и объём хранимых данных. Это важно для компаний, которые работают с быстрорастущими объёмами данных.
4. **Гибкость аналитики**:
    
    - MPP идеально подходит для выполнения сложных аналитических запросов, которые требуют агрегаций, фильтрации, работы с временными рядами или больших `JOIN`.

---

### **Основные преимущества Greenplum**

Greenplum — одна из наиболее зрелых MPP-платформ, и она обладает рядом преимуществ благодаря своей архитектуре:

#### Плюсы:

1. **Масштабируемость**:
    
    - Можно добавлять узлы, увеличивая объём доступного хранилища и вычислительных ресурсов. Это делает Greenplum гибким для масштабирования от малых кластеров до огромных установок.
2. **Производительность**:
    
    - Задачи распределяются между сегментами, что позволяет обрабатывать запросы значительно быстрее, особенно для аналитических и агрегационных операций.
3. **Совместимость с PostgreSQL**:
    
    - Greenplum основан на PostgreSQL, что делает его понятным для пользователей этой СУБД. Большинство стандартных SQL-запросов и инструментов, работающих с PostgreSQL, совместимы с Greenplum.
4. **Аналитические функции**:
    
    - Greenplum оптимизирован для аналитики: он поддерживает сложные запросы, оконные функции, `CUBE`, `ROLLUP` и другие аналитические инструменты.
5. **Поддержка гибридной архитектуры**:
    
    - Greenplum может работать с внешними таблицами, подключая данные из других систем, таких как Hadoop, S3, или файловые системы.
6. **Массовая загрузка данных**:
    
    - Благодаря встроенным утилитам, таким как `gpfdist`, Greenplum эффективно загружает данные в распределённую среду.

---

#### Минусы:

1. **Сложность администрирования**:
    
    - Поддержка и настройка распределённых систем требуют более глубоких знаний и навыков, чем работа с обычными реляционными базами данных.
2. **Зависимость от сети**:
    
    - Производительность сильно зависит от качества и пропускной способности сети между узлами, так как interconnect играет ключевую роль. Узкие места в сети могут стать причиной замедления.
3. **Чувствительность к распределению данных**:
    
    - Если данные неравномерно распределены между сегментами (например, из-за плохого выбора ключа распределения), это может привести к дисбалансу нагрузки и снижению производительности.
4. **Ограниченная OLTP-производительность**:
    
    - Greenplum предназначен для аналитики (OLAP) и плохо подходит для высокочастотных транзакций (OLTP). Для OLTP-сценариев лучше использовать PostgreSQL или другие специализированные системы.
5. **Лицензирование и ресурсоёмкость**:
    
    - В коммерческих версиях Greenplum есть затраты на лицензии. Также система требует значительных аппаратных ресурсов для поддержания высокой производительности.

---

### Сравнение: MPP против SMP

|**Критерий**|**MPP (Greenplum)**|**SMP (например, PostgreSQL)**|
|---|---|---|
|**Архитектура**|Узлы с собственной памятью, диском, CPU|Единый сервер с общей памятью и ресурсами|
|**Масштабируемость**|Горизонтальная, добавление новых узлов|Вертикальная, ограничена ресурсами сервера|
|**Производительность**|Высокая для аналитики|Хорошая для транзакций (OLTP)|
|**Управление**|Сложное|Простое|
|**Стоимость**|Высокая (особенно при больших объёмах)|Низкая|

---

### Итог:

**Зачем Greenplum и MPP?**

- Если вы работаете с огромными объёмами данных и выполняете сложную аналитику, MPP-архитектура Greenplum идеально подходит.
- Плюсы: масштабируемость, высокая производительность, мощные аналитические функции.
- Минусы: сложность администрирования, зависимость от качества сети, чувствительность к дисбалансу данных.

Greenplum выбирают тогда, когда нужно быстро обрабатывать терабайты или петабайты данных, и система должна масштабироваться без значительных изменений архитектуры.