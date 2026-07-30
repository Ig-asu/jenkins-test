# gitsync — рабочий контур otus-jenkins_test

## 0. Окружение (перед любыми командами)

Нужен **OneScript 1.9.3**, не 2.1.0:

```powershell
$env:Path = "C:\Program Files\OneScript\bin;" + (($env:Path -split ';' | Where-Object { $_ -notmatch 'OneScript-2\.1\.0' }) -join ';')
where.exe oscript
oscript -version
where.exe gitsync
```

Ожидается: `1.9.3.x` и `C:\Program Files\OneScript\bin\gitsync.bat`.

Если Git ругается на `dubious ownership`:

```powershell
git config --global --add safe.directory "E:/Workspace/otus-jenkins_test"
```

`.git` должен быть только в корне `E:\Workspace\otus-jenkins_test`, **не** внутри `src\cf`.

## 1. Плагины (один раз)

`gitsync plugins init` может падать с багом `ПолучитьВыводКоманды` — если плагины уже `[on]`, init не обязателен.

Отключить лишнее (иначе падения EDT / tool1CD / robocopy / конфликты):

```powershell
gitsync plugins disable edtExport
gitsync plugins disable use-ibcmd
gitsync plugins disable tool1CD
gitsync plugins disable drop-config-dump
gitsync plugins disable roboCopy
gitsync plugins list
```

Оставить включёнными минимум: `sync-remote`, `check-authors`, `increment` (по необходимости).

> **tool1CD** на хранилищах новой платформы даёт `Неизвестная версия хранилища` — синхронизация через Конфигуратор (`--v8version`).

## 2. Инициализация (один раз)

Пути с пробелами — **в кавычках**. В конце пути **без** `\` перед закрывающей кавычкой.  
`--v8version` — **глобальная** опция, ставится **до** команды `init`/`sync`.

### Основная конфигурация

```powershell
cd E:\Workspace\otus-jenkins_test

gitsync --v8version 8.3.27.1786 init -u DeployGit -p "1235" "D:\data_1c\Хранилища Конфигураций\ДемонстрационнаяКонфигурация" "E:\Workspace\otus-jenkins_test\src\cf"
```

### Расширение YAXUNIT (если нужно)

```powershell
gitsync --v8version 8.3.27.1786 init -u DeployGit -p "1235" -e YAXUNIT "D:\data_1c\Хранилища Конфигураций\YAXUNIT" "E:\Workspace\otus-jenkins_test\src\cfe\yaxunit"
```

## 3. Синхронизация с хранилищем

### Основная конфигурация

```powershell
cd E:\Workspace\otus-jenkins_test

gitsync --v8version 8.3.27.1786 sync -u DeployGit -p "1235" "D:\data_1c\Хранилища Конфигураций\ДемонстрационнаяКонфигурация" "E:\Workspace\otus-jenkins_test\src\cf"
```

### Расширение YAXUNIT

```powershell
gitsync --v8version 8.3.27.1786 sync -u DeployGit -p "1235" -e YAXUNIT "D:\data_1c\Хранилища Конфигураций\YAXUNIT" "E:\Workspace\otus-jenkins_test\src\cfe\yaxunit"
```

Опционально push в remote (если настроен origin и плагин `sync-remote`):

```powershell
gitsync --v8version 8.3.27.1786 sync -u DeployGit -p "1235" --PS -b master "D:\data_1c\Хранилища Конфигураций\ДемонстрационнаяКонфигурация" "E:\Workspace\otus-jenkins_test\src\cf" "https://github.com/<LOGIN>/<REPO>.git"
```

## 4. Jenkins / Usher (tools)

В `tools/gitsync.json` — строка ИБ без пробела после `/FD:` и с полным путём, например:

```json
"connectionString": "/F\"D:\\data_1c\\1c_V8_Bases\\Demo_Clear\""
```

В `tools/gitsync_conf.json`:
- `path` — **ваше** хранилище (не `tcp://KyralesGun/...`);
- `plugins-config.URL` — **ваш** публичный GitHub-репозиторий (не `Kyrales/otus_JenkinsExample`);
- `storage-user` / пароль — ваши учётные данные хранилища;
- `v8version` — как на агенте.

## 5. Типичные ошибки

| Симптом | Что сделать |
|---------|-------------|
| `Библиотека не найдена: 'edtfind'` | Снова OneScript 2.1.0 в PATH → п.0; либо `opm install edtfind` (админ) |
| `ПолучитьВыводКоманды` на `plugins init` | Игнорировать, если `plugins list` уже `[on]` |
| `Не заполнено имя проекта` | `gitsync plugins disable edtExport` |
| `Неизвестная версия хранилища` / tool1CD | `gitsync plugins disable tool1CD`, sync с `--v8version` |
| `Отказано в доступе ...\src\cf\.git` | `disable roboCopy`, удалить `src\cf\.git` |
| `Ошибка чтения параметров` у sync | `--v8version` до слова `sync` |
| `dubious ownership` | `git config --global --add safe.directory ...` |
