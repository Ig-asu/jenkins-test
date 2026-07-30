# Цель
Быстрое развертывание сборочной линии если разработка ведется в Конфигураторе 1С (не EDT), с использованием Vanessa Usher для синхронизации хранилища конфигурации с GIT и JenkinsLib для организации тестового контура.

Для EDT использовать другой репозитарий: https://github.com/Kyrales/otus_JenkinsExample_EDT

# Конфигурация
Конфигурация "Демо приложение 1С": https://its.1c.ru/db/metod8dev/content/5028/hdoc

После развертывания можно заменить на свою конфигурацию и продолжить использование.

# Используется сборочная линия для демо базы Otus на основе jenkins-lib
Ci на основе jenkins-lib от https://github.com/firstBitMarksistskaya/jenkins-lib 
Форк библиотеки: https://github.com/Kyrales/jenkins-lib ветка otus
Описание: https://infostart.ru/1c/articles/1681427/

# GitSync
Настроен на основе vanessa-usher https://github.com/silverbulleters/vanessa-usher.
Актуальный репозитарий для использования: https://github.com/Kyrales/vanessa-usher_mod . Ветка my_dev
Путь для подключения библиотеки с названием usher2: 
https://github.com/Kyrales/vanessa-usher_mod.git

Используемые файлы настроек:
Jenkinsfile_gitsync - основной jenkins файл для запуска функций пайплайна
/tools/gitsync.json - базовые настройки для работы с пайплайном
/tools/gitsync_conf.json - настройки хранилищ 1С (конфигурации и расширений) для работы в пакетном режиме 

<img width="1551" height="366" alt="image" src="https://github.com/user-attachments/assets/d8af7541-b2d6-421b-bb49-f0fb6d6e8aa5" />

<img width="1833" height="399" alt="image" src="https://github.com/user-attachments/assets/e216faa3-8f87-4e26-88a3-4dbe05f6abb4" />
