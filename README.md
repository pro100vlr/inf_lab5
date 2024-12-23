# Лабораторная работа 5

Задание 1

1.<img width="425" alt="Снимок экрана 2024-12-04 в 17 11 08" src="https://github.com/user-attachments/assets/a84152be-d735-41a8-88e7-67f3befc9d23">

Перешла на рабочий стол и создала там папку gitproekt

2.<img width="578" alt="Снимок экрана 2024-12-04 в 17 12 45" src="https://github.com/user-attachments/assets/2af74e11-ec27-4504-bdd0-30e1b7c5778d">

Перешла в папку gitproekt. Затем инициализировала работу Git-репозиторий с помощью команды git init.Это создало мне скрытую папку .git . Убедилась, что папка .git/hooks существует с помощью команды ls .git/hooks и увидела файлы с расширением .sample, например, pre-commit.sample. Это примерные хуки, которые можно активировать.

3.<img width="437" alt="Снимок экрана 2024-12-04 в 17 17 03" src="https://github.com/user-attachments/assets/df45cd6a-35c5-4bf0-8a43-537eb0c2a58c">

Перешла в папку .git/hooks.Создала новый файл pre-commit.Сделала файл исполняемым с помощью команды chmod +x pre-commit :
chmod - команда для изменения права доступа к файлу
х - обозначает право на выполнение
Затем открыла файл pre-commit в текстовом редакторе и написала код:

<img width="473" alt="Снимок экрана 2024-12-04 в 23 46 52" src="https://github.com/user-attachments/assets/e464b878-dd0b-44c9-ba49-a220b066d8a6"> 

Расскажу, что там написала:

set -e : Эта команда заставляет скрипт немедленно завершиться, если какая-либо команда вернет ошибку. Это помогает избежать выполнения последующих шагов, если что-то пошло не так.

check_file_format() {
   local file=$1
    if ! grep -q "Автор:" "$file"; then
        echo "Ошибка: В файле '$file' отсутствует подпись автора!"
        return 1
    fi
}

local file=$1: Получает имя файла, переданного в функцию.

grep -q "Автор:" "$file": Проверяет, содержит ли файл строку "Автор:". Опция -q заставляет grep не выводить результат поиска, а только возвращать код: 0 (успех, если строка найдена) или 1 (если строка отсутствует).

Если строка "Автор:" отсутствует:
Выводится сообщение об ошибке.

Возвращается код выхода 1, сигнализируя о проблеме.

files_to_check=$(git diff --cached --name-only --diff-filter=ACM | grep '\.txt$'): эта команда собирает список всех файлов .txt, которые находятся в staging(это промежуточная область в Git, куда мы добавляем изменения перед их фиксацией (коммитом)) и были добавлены, изменены или скопированы.

if [[ -z "$files_to_check" ]]; then
    exit 0
fi

[[ -z "$files_to_check" ]]: Проверяет, пустая ли переменная files_to_check.Если нет .txt файлов для проверки, скрипт завершает выполнение с кодом 0 (успешно).

for file in $files_to_check; do
    if [[ -f $file ]]; then
        check_file_format "$file" || exit 1
    else
        echo "Предупреждение: Файл '$file' не найден, пропуск проверки."
    fi
done

Цикл for: Проходит по каждому имени файла из переменной files_to_check.

[[ -f $file ]]: Проверяет, существует ли файл (на случай, если файл был удален после staging).

Если файл существует:

Вызывается функция check_file_format для проверки содержимого.Если функция возвращает ошибку (код 1), весь скрипт завершает выполнение с ошибкой (exit 1).

Если файл не найден:

Выводится предупреждение и файл пропускается.

echo "Все .txt файлы соответствуют требованиям!"

exit 0

Если все файлы успешно прошли проверку (или их не было), выводится сообщение об успехе.
Скрипт завершает выполнение с кодом 0, сигнализируя об успешной проверке.

4.<img width="576" alt="Снимок экрана 2024-12-04 в 17 25 36" src="https://github.com/user-attachments/assets/8841dd79-c898-4fb8-9d89-7fbfc48f94f1">

С помощью cd .. вышла из hooks и перешла в gitproekt. 

Потом использовала команду: echo -e "Тестовый файл\nПользователь: Валерия" > test.txt , функциональность команды по отдельности:

echo – выводит текст.

-e – позволяет использовать специальные символы, такие как \n (перевод строки).

"Тестовый файл\nПользователь: Валерия" – это текст, который будет записан в файл. В данном случае:

Первая строка: "Тестовый файл".

Вторая строка: "Пользователь: Валерия".

">" – записывает текст в файл. Проверила, что файл создан, используя команду: cat test.txt .Эта команда выведет содержимое файла test.txt, что я и вижу в терминале.


5.<img width="578" alt="Снимок экрана 2024-12-04 в 17 37 21" src="https://github.com/user-attachments/assets/198e0487-bc8a-41f0-b7e1-3c40beebd7ad">

Теперь файл test.txt добавила в индекс Git, чтобы он был подготовлен для коммита: git add test.txt
Дальше я создала коммит и получила ошибку, потому что содержимое файла не прошло проверку, следовательно коммит не прошел.
Потом поменяла пользователь на автор и перезаписала содержимое файла test.txt . Видно, что проверка прошла и не возникло ошибок, поэтому коммит записался.

6. Затем я еще прописала несколько тестовых варриантов файлов, чтобы показать, что все работает исправно.

   <img width="576" alt="Снимок экрана 2024-12-04 в 17 47 57" src="https://github.com/user-attachments/assets/68d035d2-f6cb-49e9-b62f-bfaee2361a2c">


Задание 2

1.<img width="571" alt="Снимок экрана 2024-12-04 в 17 51 00" src="https://github.com/user-attachments/assets/0631f5d2-74fd-46c0-a62c-4f2ec9bd06ed">

Перешла на рабочий стол и создала там папку gitproekt2task. Перешла в эту папку и инициализировала Git-репозиторий.

2.<img width="571" alt="Снимок экрана 2024-12-04 в 17 53 40" src="https://github.com/user-attachments/assets/1e33d7b4-f3e6-4c87-aeff-f62ca24a093c">

Здесь я связываю локальный репозиторий с удаленным, используя URL-ключ.Проверила командой git remote -v на то, что удаленный репозиторий подключился.Как видно, успешно. Инициализация Git Flow показала мне, что он у меня не установлен.

3.<img width="572" alt="Снимок экрана 2024-12-04 в 17 57 52" src="https://github.com/user-attachments/assets/1e7b7191-be0d-4d87-9dcf-b520655ed0c8">

Решила его установить и столкнулась с проблемой, к сожалению, эта команда у меня не сработала. Для macOS или других систем, отличных от Ubuntu/Debian, нужно установить Git Flow другим способом. Я выбрала способ установить Homebrew, через который установлю Git Flow (Homebrew популярный менеджер пакетов для macOS). Для этого использовала команду: /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
После чего пошла установка - виднно на скриншоте.


4.<img width="571" alt="Снимок экрана 2024-12-04 в 18 05 21" src="https://github.com/user-attachments/assets/d8e4e950-bba0-44ff-8554-9d1c496ad7a7">

После я применила комаду : brew install git-flow, но не получилось ничего установить.Нашла решение проблемы, а именно необходимо было обновить PATH(переменная среда, которая определяет, где искать исполнимые файлы). То есть Homebrew был установлен, но его путь не добавлен в систему. Применила команду : eval "$(/opt/homebrew/bin/brew shellenv)" и посмотрела версию - все сработало. Опять применила команду: brew install git-flow , после чего пошла установка.


5.<img width="697" alt="Снимок экрана 2024-12-05 в 00 27 29" src="https://github.com/user-attachments/assets/c545f76e-da8e-4a4e-a0db-d9cb2182cb4b">

Инициализировала Git Flow, после чего Git Flow задает вопросы о конфигурации веток, оставила настройки по умолчанию. git flow feature start task-management - создает ветку для разработки функциональности (например, "task-management"). Git Flow автоматически создает ветку feature/task-management и переключил меня на нее.С помощью nano создада и открыла файл и записала текст, который предлогается в задании. Следующая команда: git add task_manager.py.

6.<img width="694" alt="Снимок экрана 2024-12-05 в 00 34 16" src="https://github.com/user-attachments/assets/d34ca125-544f-4c29-8b3f-fcaaafa4f640">

Сделала коммит,после завершения работы над функционалом завершила ветку.Git Flow автоматически:
переключил на ветку develop, объединил ветку feature/task-management с develop, удалил локальную ветку feature/task-management.Убедилась, что нахожусь в ветке develop: git checkout develop

7.<img width="689" alt="Снимок экрана 2024-12-05 в 00 39 25" src="https://github.com/user-attachments/assets/8691f490-e58e-4e28-82e3-e05259cafe49">

Начала создание релиза, указав его версию.Внесла изменения, связанные с релизом(обновила файл версии).Зафиксировала изменения(git add/git commit).


<img width="548" alt="Снимок экрана 2024-12-05 в 00 43 46" src="https://github.com/user-attachments/assets/5569fdd7-9363-4ea2-a060-b51cc0daf113">

Завершила релиз.Git Flow автоматически: переключился на ветку main, объединил релиз с ветками main и develop, создал тег, ветку объединил в develop и потом удалил её.

8. Хотя у меня не возникло не одной ошибки, я создала ветку hotfix:

<img width="695" alt="Снимок экрана 2024-12-05 в 00 50 37" src="https://github.com/user-attachments/assets/fb4ca4cb-6ed2-470e-abcd-1e623f159e16">

Активировала работу ветки, создала файл и записала текст(самостоятельно смоделировала файл "с ошибкой", чтобы сделать коммит), сделала коммит и завершила работу ветки.


9.<img width="497" alt="Снимок экрана 2024-12-05 в 00 55 06" src="https://github.com/user-attachments/assets/cb00af71-04dc-466d-8278-67fb40d55479">

Отправила изменения из веток main и develop в удаленный репозиторий. На моем гитхабе все, что я отправила из локального отображается 

<img width="746" alt="Снимок экрана 2024-12-05 в 00 57 18" src="https://github.com/user-attachments/assets/e3f2eb5a-bcc1-4324-bffa-f14a8d6694d1">

Вывод: Данная лабораторная работа полностью реализована. Я познакомилась с оновными командами Git, Git Hooks, Git Flow и отработала навыки по их применению. Несмотря на то, что я выполнила задачи, которые передо мной поставила лабораторная работа, еще установила менеджер пакетов и обновила PATH, следовательно, получила дополнительные знания.

