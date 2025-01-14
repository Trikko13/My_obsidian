1. **Изоляция данных**:
    - Airflow хранит в своей базе данных важную информацию о DAG'ах, задачах, логах и расписании. Использование отдельной базы данных помогает избежать смешивания данных из других приложений или проектов.
2. **Простота управления**:
    - Если ты используешь отдельную базу, тебе проще управлять схемой и данными Airflow (например, создавать резервные копии, восстанавливать их или чистить).
3. **Лучшие практики**:
    - В продакшене почти всегда используют отдельную базу для каждого приложения. Это помогает избежать нежелательных зависимостей.

### Если оставить старую базу:

Ты можешь использовать старую базу, но нужно убедиться, что:

1. В ней есть отдельный пользователь и база для Airflow (как в твоем примере).
2. Данные других приложений не будут конфликтовать с данными Airflow.

### Итог:

- **Рекомендация:** Создай новую базу данных (`airflow_metadata`) для Airflow, как указано в инструкции. Это поможет тебе организовать систему правильно.
- Если хочешь использовать старую базу, то создай в ней новую схему или таблицы, чтобы изолировать данные Airflow.

Хочешь, чтобы я показал, как это сделать в существующей базе?