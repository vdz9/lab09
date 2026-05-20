[![CI](https://github.com/vdz9/lab06/actions/workflows/ci.yml/badge.svg)](https://github.com/vdz9/lab06/actions/workflows/ci.yml)
### Tasks: Изучение систем автоматизации развёртывания и управления приложениями на примере Docker

1. Создать публичный репозиторий с названием **lab09** на сервисе **GitHub**
2. Ознакомиться со ссылками учебного материала
3. Получить токен для доступа к репозиториям сервиса GitHub
4. Выполнить инструкцию учебного материала

### 1. Настройка переменных окружения

```bash
export GITHUB_USERNAME=vdz9
export GITHUB_TOKEN=<сохраненный_токен>
source scripts/activate

cd ${GITHUB_USERNAME}/workspace
pushd .
source scripts/activate

git clone https://github.com/${GITHUB_USERNAME}/lab08.git projects/lab08
cd projects/lab09
git remote remove origin
git remote add origin https://github.com/${GITHUB_USERNAME}/lab09.git
```

Результат: Копирование репозитория из предыдущей лабораторной работы в текущую и последующая его привязка к новому репозиторию
### 2. Установка GPG и создание ключа

```bash
sudo apt install -y gpg
gpg --full-generate-key
```

Результат: Создание GPG-ключ для подписи тегов

### 3. Получение ID ключа и настройка Git

```bash
gpg --list-secret-keys --keyid-format LONG
GPG_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep ssb | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')
GPG_SEC_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep sec | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')
git config user.signingkey ${GPG_SEC_KEY_ID}
git config gpg.program gpg
```
Результат: Git настроен для подписи тегов GPG-ключом

### 4. Экспорт публичного ключа

```bash
gpg --armor --export ${GPG_KEY_ID} > gpg_public.key
cat gpg_public.key
```
Результат: Публичный GPG-ключ добавлен на GitHub

### 5. Настройка GPG_TTY

```bash
test -r ~/.bash_profile && echo 'export GPG_TTY=$(tty)' >> ~/.bash_profile
echo 'export GPG_TTY=$(tty)' >> ~/.profile
```

Результат: Добавление GPG_TTY в профили для корректной работы GPG в терминале
### 6. Авторизация в GitHub CLI

```bash
gh auth login

#Авторизация была проведина непосредственно через сайт
#Выбрано -> GitHub.com ->  HTTPS -> Login with a web browser
```

Результат: Авторизация GitHub CLI для работы с репозиторием
### 7. Создание подписанного тега

```bash
rm -f .git/TAG_EDITMSG
git tag -s v0.1.0.0 -m "first release"
git tag -v v0.1.0.0
git show v0.1.0.0
git push origin master --tags
```

Результат: Создание подписанного GPG-тега v0.1.0.0

### 8. Загрузка артефакта в релиз

```bash
export PACKAGE_OS=`uname -s` PACKAGE_ARCH=`uname -m`
export PACKAGE_FILENAME=print-${PACKAGE_OS}-${PACKAGE_ARCH}.tar.gz
gh release upload v0.1.0.0 _build/*.tar.gz --clobber
```

Результат: Артефакт print-Linux-x86_64.tar.gz загружен в релиз v0.1.0.0

