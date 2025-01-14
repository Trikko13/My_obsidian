**DAG — это ориентированный ациклический граф**, концептуальное представление серии действий или, другими словами, математическая абстракция конвейера данных (data pipeline).

Хотя оба термина, DAG и data pipeline, используются в разных сферах, они представляют собой почти идентичный механизм. В двух словах, DAG (или конвейер) определяет последовательность этапов выполнения в любом неповторяющемся алгоритме.

**1)**                **Какие операторы использовал?**

**Операторы** являются основными строительными блоками DAG Airflow. Это классы, которые содержат логику выполнения единичной работы.

Вы можете использовать операторы в Airflow, создав их экземпляры в задачах. Задача определяет работу, выполняемую оператором в контексте DAG.

Хуки (Hooks) являются одним из основных строительных блоков Airflow. На высоком уровне хук - это абстракция определенного API, который позволяет Airflow взаимодействовать с внешней системой. Хуки встроены во многие операторы, но их также можно использовать непосредственно в коде DAG.

**BashOperator** используется для запуска простых bash комманд.

**PythonOperator** вызывает функцию python, определенную ранее в нашем коде.

**PostgresOperator** выдает инструкцию SQL для базы данных Postgres. Учетные данные для базы данных хранятся в соединении Airflow с именем my_postgres_connection. Если вы посмотрите на код [оператора Postgres](https://registry.astronomer.io/providers/postgres/modules/postgresoperator), он использует [PostgresHook](https://registry.astronomer.io/providers/postgres/modules/postgreshook) для взаимодействия с базой данных.

## 2)                Приходилось ли использовать сенсоры?

**Сенсоры** - это особый вид операторов. Когда они выполняются, они проверяют, выполняется ли определенный критерий, прежде чем разрешить выполнение последующих задач. Это отличный способ заставить ожидать часть задач вашего DAG завершения какого-либо внешнего процесса или выполнения условия.

·                     **DateTimeSensor** ожидает прохождения указанной даты и времени. Полезно, когда нужно выполнять задачи из одного DAG в разное время;

·                     **HttpSensor** ожидает доступности API;

·                     **SqlSensor** ожидает появления данных в таблице SQL. Пригодится, если нужно, чтобы DAG обрабатывал данные по мере их поступления в базу данных.

**3)**    **Как тестировал даги?**

Для тестирования дагов в Apache Airflow можно использовать модуль **unittest** в сочетании с классом **DagBag**. Вот пример того, как вы можете написать тест для вашего дага:

**Создайте новый файл** с расширением .py для вашего теста дага.

**Импортируйте** необходимые модули:

import **unittest**

from airflow.models import **DagBag**

**Создайте** **класс** **теста**, который наследуется от **unittest.TestCase**:

class TestMyDag(unittest.TestCase):

    def setUp(self):

        self.dagbag = DagBag()

    def test_dag_loaded(self):

        dag_id = 'my_dag_id'

        dag = self.dagbag.get_dag(dag_id)

        self.assertIsNotNone(dag)

В методе **setUp** инициализируйте экземпляр **DagBag**, который будет загружать все даги из вашего проекта.

В методе **test_dag_loaded** проверьте, что ваш даг был успешно загружен из **DagBag**.

**Запустите** ваш тест с помощью команды **python -m unittest <имя_файла_теста>**. Например, **python -m unittest test_my_dag.py**.

При запуске теста, он загрузит все даги из вашего проекта и проверит, что ваш даг был успешно загружен. Вы также можете добавить дополнительные тесты для проверки других аспектов вашего дага, таких как наличие операторов, правильность зависимостей и т.д.