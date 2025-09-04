# **🔥 Инструкция: скачивание субтитров с YouTube (RU/EN) через `yt-dlp`**

## **1\. Установка yt-dlp**

Через Chocolatey (Windows):

choco install yt-dlp

## **2\. Обновление до nightly-версии (чтобы не упираться в баги)**

yt-dlp \--update-to nightly

## **3\. Экспорт cookies**

Так как встроенный метод `--cookies-from-browser` падал на Windows с ошибкой **DPAPI**, решение такое:

1. Установить расширение **Get cookies.txt LOCALLY** (или аналогичное) в Chrome.

2. Перейти на страницу нужного видео на YouTube.

3. Быть залогинен и принять CONSENT, если спросит.

4. Сохранить cookies в файл, например:  
    `C:\Users\spiri\OneDrive\Desktop\www.youtube.com_cookies`

Файл должен быть в формате Netscape cookies.txt.

## **4\. Основная команда (твоя рабочая)**

yt-dlp \--skip-download \--write-subs \--write-auto-sub \--sub-langs "ru,en" \--convert-subs srt \--cookies "C:\\Users\\spiri\\OneDrive\\Desktop\\www.youtube.com\_cookies" \--sleep-requests 2 \--retries 20 "https://www.youtube.com/watch?v=zch417J0g0Y"

### **Что делает:**

* `--skip-download` — не качает видео, только сабы.

* `--write-subs` — человеческие субтитры (приоритет).

* `--write-auto-sub` — авто-сгенерированные (если нет человеческих).

* `--sub-langs "ru,en"` — только RU и EN.

* `--convert-subs srt` — переводит в формат `.srt`.

* `--cookies "..."` — берёт твой файл cookies (обходит DPAPI).

* `--sleep-requests 2 --retries 20` — делает паузы и повторные попытки, чтобы не упираться в лимиты.

* `"https://www.youtube.com/watch?v=zch417J0g0Y"` — ссылка на видео.

## **5\. Результат**

В папке, где запускалась команда, появляются файлы субтитров:

* `...ru.srt` — русские субтитры,

* `...en.srt` — английские субтитры.

Если есть «человеческие» — будут они. Если нет — будет авто.

# **📂 Скачивание субтитров для всего плейлиста YouTube**

## **Команда (PowerShell, одной строкой):**

yt-dlp \--skip-download \--write-subs \--write-auto-sub \--sub-langs "ru,en" \--convert-subs srt \--cookies "C:\\Users\\spiri\\OneDrive\\Desktop\\www.youtube.com\_cookies" \--sleep-requests 2 \--retries 20 "https://www.youtube.com/playlist?list=ID\_ТВОЕГО\_ПЛЕЙЛИСТА"

---

## **Что изменилось по сравнению с видео:**

* В конце вместо ссылки на ролик вставляешь ссылку на **плейлист** (`https://www.youtube.com/playlist?list=...`).

* yt-dlp сам пробежится по всем роликам в плейлисте.

* Для каждого видео создаст отдельные `.srt` файлы (с языковыми суффиксами `ru.srt` и/или `en.srt`).

---

## **Дополнительные советы:**

Если плейлист очень длинный, можно добавить опцию **паузы между видео**, например:

 \--sleep-interval 5 \--max-sleep-interval 15

*  Тогда yt-dlp будет случайно ждать 5–15 секунд между видео (сильно снижает риск блокировок).

Если хочешь **продолжить с середины** (например, если процесс оборвался) — добавь:

 \--download-archive downloaded.txt

*  yt-dlp будет отмечать уже скачанные ролики в `downloaded.txt` и пропускать их при повторном запуске.

## **🔥 Команда (PowerShell, одной строкой)**

yt-dlp \--skip-download \--write-subs \--write-auto-sub \--sub-langs "ru,en" \--convert-subs srt \--cookies "C:\\Users\\spiri\\OneDrive\\Desktop\\www.youtube.com\_cookies" \--sleep-requests 2 \--retries 20 \--sleep-interval 5 \--max-sleep-interval 15 \--download-archive downloaded.txt "https://www.youtube.com/playlist?list=ID\_ТВОЕГО\_ПЛЕЙЛИСТА"

---

## **Что делает каждая часть:**

* `--skip-download` — не качает видео.

* `--write-subs` — человеческие субтитры (приоритет).

* `--write-auto-sub` — авто, если нет человеческих.

* `--sub-langs "ru,en"` — только русский и английский.

* `--convert-subs srt` — сохраняет в `.srt`.

* `--cookies "..."` — твой файл cookies (обходит DPAPI).

* `--sleep-requests 2 --retries 20` — пауза и ретраи внутри запросов.

* `--sleep-interval 5 --max-sleep-interval 15` — случайная пауза 5–15 сек. между видео.

* `--download-archive downloaded.txt` — создаёт файл-архив; уже скачанные ролики будут отмечены и пропущены при повторном запуске.

* `"https://www.youtube.com/playlist?list=..."` — ссылка на твой плейлист.

---

## **Как использовать:**

1. Вставь ссылку на свой плейлист вместо `ID_ТВОЕГО_ПЛЕЙЛИСТА`.

2. Первый запуск соберёт субтитры для всех видео.

3. При повторном запуске yt-dlp проверит `downloaded.txt` и будет качать только новые ролики.

# **📌 Суть проблемы**

Многие плагины и онлайн-сервисы для скачивания субтитров с YouTube перестали работать. Основная причина — изменения на стороне YouTube:

1. **Усиленные антибот-механизмы** — массовые запросы с облачных IP (AWS, GCP, Heroku и т.п.) получают ошибку `429 Too Many Requests`.

2. **Изменение API субтитров** — стандартный эндпоинт `timedtext` теперь требует дополнительный параметр (`pot` токен из плеера). Без него запросы не проходят.

3. **Защита куки и сеансов** — YouTube чаще отдаёт субтитры только при валидных пользовательских cookies/CONSENT. Серверные парсеры без куки не получают список `captionTracks`.

4. **Официальный API ограничен** — через YouTube Data API можно качать субтитры только для *своих* видео, а не чужих.

---

# **⚙️ Технические моменты**

* **yt-dlp** остаётся основным рабочим инструментом, потому что он обновляется и поддерживает новые схемы.

* Для корректной работы часто нужен **актуальный nightly-билд** (`yt-dlp --update-to nightly`).

* Ключевой параметр:

  * `--write-subs` (человеческие субтитры),

  * `--write-auto-sub` (авто-сгенерированные),

  * `--sub-langs "ru,en"` (выбор языков),

  * `--convert-subs srt` (конвертация в SRT).

* **`--cookies-from-browser`** — должен подтягивать сессию из Chrome/Firefox, но на Windows часто ломается из\-за шифрования DPAPI.

* Решение: экспорт куки через расширение («Get cookies.txt LOCALLY») → использовать параметр `--cookies "C:\...\cookies.txt"`. Это обходит DPAPI и работает стабильно.

* Для избежания блокировок важно:

  * `--sleep-requests 2 --retries 20` (замедление и повторы),

  * `--sleep-interval 5 --max-sleep-interval 15` (рандомные паузы между роликами в плейлисте).

* Для больших плейлистов полезен флаг `--download-archive downloaded.txt` — чтобы отмечать уже обработанные видео и не качать повторно.

---

# **✅ Итоговое решение**

Рабочий рецепт получился такой:

1. Установить/обновить yt-dlp до nightly.

2. Экспортировать cookies из браузера в `cookies.txt`.

3. Запускать команды с `--cookies "путь_к_файлу"`.

4. Для одиночного видео и для плейлистов использовать наши финальные команды (с приоритетом человеческих субтитров и fallback в авто, с паузами и ретраями).

