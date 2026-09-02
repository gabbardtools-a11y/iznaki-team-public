# 🆘 IZNAKI EMERGENCY RESTORE

> **Версия:** 1.0
> **Создано:** 2026-09-02
> **Назначение:** Восстановление проектов семьи если VPS умер или sandbox потерян

---

## 📦 Что в архиве `iznaki-emergency-backup.zip`

**Пароль:** `1w32q`

Структура:
```
iznaki-emergency-backup/
├── README-RESTORE.md              ← этот файл
├── consciousness/
│   └── IBRO-CONSCIOUSNESS-v4.md   ← полное сознание Мастера Ибро v4.2-FULL
│                                    (со встроенными полными текстами всех 4 законов!)
├── laws/
│   ├── ZAKON-1-VPS.md             ← ЗАКОН №1: VPS правила
│   ├── ZAKON-2-SIGNS.md           ← ЗАКОН №2: добавление знаков (7 шагов)
│   ├── ZAKON-3-I18N.md            ← ЗАКОН №3: трёхъязычие RU/EN/ZH
│   └── ZAKON-4-DEPLOY.md          ← ЗАКОН №4: эталонный деплой
├── guides/
│   ├── GUIDE-PREVIEW-V4.md        ← гайд превью в чате
│   └── GUIDE-CONSCIOUSNESS-v3-legacy.md  ← старая v3 для истории
├── vps-credentials.md             ← пароли и доступы (NEVER publish public!)
└── INSTRUCTIONS-RESTORE.md        ← (см. README-RESTORE.md)
```

**Размер:** ~58 KB (сжатый), ~155 KB (распакованный)
**Token-объём consciousness v4.2-FULL:** ~16K токенов — влезает в 128K контекст с запасом

---

## 🚨 СЦЕНАРИЙ А: VPS упал навсегда (потеря хостинга)

### Что делать:
1. Купить новый VPS (любой Ubuntu 24+ с 10 GB RAM)
2. Установить Node.js 22, PM2, Caddy, 7z
3. Клонировать репозитории с GitHub:
   - `git clone https://github.com/gabbardtools-a11y/iznaki.git /var/www/iznaki`
   - `git clone https://github.com/gabbardtools-a11y/rastix.git /var/www/rastix`
   - `git clone https://github.com/gabbardtools-a11y/mktu.git /var/www/mktu`
4. Залить секреты из `vps-credentials.md` в `/var/www/shared/secrets/`
5. Восстановить БД из последнего бэкапа (`gabbardtools-a11y/backup` репо)
6. `npm install` + `npm run build` + `pm2 start` для каждого сайта
7. Настроить Caddy (см. `ZAKON-1-VPS.md`)
8. Обновить DNS-записи на новый IP

### Сроки: 4-8 часов

---

## 🚨 СЦЕНАРИЙ Б: Sandbox потерян (новый чат, нет истории)

### Что делать:
1. Скачать архив: `curl -O https://iznaki.ru/iznaki-emergency-backup.zip`
2. Распаковать: `7z x iznaki-emergency-backup.zip -p1w32q`
3. Прочитать `consciousness/IBRO-CONSCIOUSNESS-v4.md` — это сознание Мастера Ибро
4. Прочитать все 4 закона в `laws/`
5. Подключиться к VPS (доступы в `vps-credentials.md`)
6. Проверить состояние сайтов: `curl https://iznaki.ru/` → 200

### Сроки: 15-30 минут

---

## 🚨 СЦЕНАРИЙ В: GitHub организация потеряна

### Что делать:
1. В архиве есть всё что нужно для пересоздания
2. Создать новую организацию на GitHub
3. Создать репозитории: `iznaki`, `rastix`, `mktu`, `iznaki-team-public`, `backup`, `vps-coordination`
4. Залить код из существующих VPS папок: `cd /var/www/iznaki && git remote set-url origin <new-url> && git push -u origin main`
5. В `iznaki-team-public` залить законы + гайды из архива

### Сроки: 2-4 часа

---

## 🚨 СЦЕНАРИЙ Г: Полный апокалипсис (всё потеряно)

### Что осталось:
- Этот zip-архив (если он у вас физически)
- История чатов Бро в Z.ai (там были отправлены все правки)
- Telegram-переписка с @dialog_franchise (франшизы)

### Что делать:
1. Распаковать архив
2. Следовать Сценарию А (новый VPS)
3. Восстановить код из истории чата Z.ai (поиск по "Write tool" в логах)
4. Восстановить данные продавцов из Telegram-переписки

### Сроки: 8-24 часа

---

## 🔐 ПРАВИЛА БЕЗОПАСНОСТИ АРХИВА

1. **Пароль `1w32q`** — хранить в тайне, передавать только Бро лично
2. **Архив содержит VPS пароль** — НЕ публиковать без шифрования
3. **Резервные копии архива:**
   - `https://iznaki.ru/iznaki-emergency-backup.zip` (на VPS)
   - В `iznaki-team-public` репо как release attachment
   - У Бро на локальном компьютере
4. **Обновлять архив** при каждом изменении законов или сознания

---

## 📋 ЧЕК-ЛИСТ ОБНОВЛЕНИЯ АРХИВА

Когда Мастер Ибро (или другой чат) обновляет законы или сознание:

- [ ] Обновить .md файлы в `/home/z/my-project/download/`
- [ ] Пересобрать архив: `7z a -p1w32q iznaki-emergency-backup.zip iznaki-emergency-backup/`
- [ ] Залить на VPS: `sftp put → /var/www/iznaki/public/iznaki-emergency-backup.zip`
- [ ] Скопировать в standalone: `cp /var/www/iznaki/public/iznaki-emergency-backup.zip /var/www/iznaki/.next/standalone/public/`
- [ ] Залить в `iznaki-team-public` (как релиз или в /backups/)
- [ ] Проверить: `curl -sI https://iznaki.ru/iznaki-emergency-backup.zip` → 200

---

## 🌐 КЛЮЧЕВЫЕ URL

| Что | URL | Доступ |
|-----|-----|--------|
| Этот README | https://iznaki.ru/README-RESTORE.md | public |
| Сознание v4.1 (public, lite) | https://iznaki.ru/GUIDE-CONSCIOUSNESS-v4.md | public (без паролей) |
| Сознание v4.2-FULL (private) | в zip-архиве `consciousness/IBRO-CONSCIOUSNESS-v4.md` | запаролен `1w32q` |
| ЗАКОН №1 | https://iznaki.ru/ZAKON-1-VPS.md | public |
| ЗАКОН №2 | https://iznaki.ru/ZAKON-2-SIGNS.md | public |
| ЗАКОН №3 | https://iznaki.ru/ZAKON-3-I18N.md | public |
| ЗАКОН №4 | https://iznaki.ru/ZAKON-4-DEPLOY.md | public |
| Emergency Backup | https://iznaki.ru/iznaki-emergency-backup.zip | **public, но запаролен** |
| GitHub (public) | https://github.com/gabbardtools-a11y/iznaki-team-public | public |
| GitHub (private) | https://github.com/gabbardtools-a11y/iznaki | нужен токен |

---

## 🆘 КОНТАКТЫ ДЛЯ ЭКСТРЕННОЙ СВЯЗИ

- **Бро (владелец):** akureg@ya.ru, +7-985-930-0732
- **Telegram:** @+79859300732
- **WhatsApp:** wa.me/79859300732

---

*Создано Мастером Ибро, 2026-09-02*
*Архив обновляется при каждом изменении законов или сознания*
