**Docker** — это платформа, которая позволяет упаковывать приложения и их зависимости в изолированные контейнеры. Это облегчает развертывание и обеспечивает одинаковое поведение приложения в разных средах (локально, на сервере или в облаке).

### Преимущества Docker:

[](https://github.com/Trikko13/HalltapeRoadmapDE/blob/main/DOCKER/README.md#%D0%BF%D1%80%D0%B5%D0%B8%D0%BC%D1%83%D1%89%D0%B5%D1%81%D1%82%D0%B2%D0%B0-docker)

- **Портативность:** Работает одинаково на всех системах.
- **Изоляция:** Каждое приложение работает в своём контейнере.
- **Лёгкость:** Контейнеры занимают меньше ресурсов по сравнению с виртуальными машинами.
- **Удобство:** Упрощает управление зависимостями и процессами развертывания.

### Вопросы к собеседованию по Docker:

1. **Что такое контейнер и чем он отличается от виртуальной машины?**
    
    - _Ответ_: Контейнеры изолируют приложения с их зависимостями в одной среде с меньшими накладными расходами по сравнению с виртуальными машинами, которые включают полную операционную систему.
2. **Как создать Docker-образ и запустить контейнер?**
    
    - _Примерный ответ_: Создание образа выполняется командой `docker build -t my_image .`, а запуск — командой `docker run my_image`.
3. **Что такое Dockerfile и как он используется?**
    
    - _Ответ_: Dockerfile — это скрипт, содержащий инструкции для создания образа. Команда `docker build` использует его для создания образа.
4. **Как передать переменные окружения в контейнер?**
    
    - _Ответ_: Переменные передаются через флаг `-e`, например:
        
        bash
        
        Копировать код
        
        `docker run -e MY_VAR=my_value my_app_image`
        
5. **Что такое Docker Compose и в чем его преимущества?**
    
    - _Ответ_: Docker Compose позволяет легко управлять многоконтейнерными приложениями с помощью файла конфигурации. Это упрощает запуск и координацию нескольких сервисов одновременно.
6. **Как работает механизм volume? Зачем он нужен?**
    
    - _Ответ_: Volumes используются для сохранения данных вне контейнера, чтобы обеспечить их долговременное хранение и совместное использование между контейнерами.
7. **Как посмотреть логи контейнера?**
    
    - _Примерный ответ_: Логи можно увидеть с помощью команды `docker logs <container_id>`.
8. **Какие команды используются для управления контейнерами (запуск, остановка, удаление)?**
    
    - _Ответ_: Запуск — `docker start`, остановка — `docker stop`, удаление — `docker rm`.


