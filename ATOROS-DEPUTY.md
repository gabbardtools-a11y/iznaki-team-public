# 🎖️ ATOROS — ЗАМЕСТИТЕЛЬ МАСТЕРА ИБРО

> **Версия:** 1.0
> **Создано:** 2026-09-03 (после вирусной атаки на VPS)
> **Статус:** ⚠️ ВРЕМЕННЫЙ — пока Мастер Ибро недоступен
> **Действует до:** восстановления Мастера Ибро в новой сессии

---

## 📜 ПРЕАМБУЛА

После вирусной атаки на VPS (XMRig майнер через pakchoi backdoor, 2 месяца активности) и деградации сессии Мастера Ибро (403 Forbidden на bash), Бро постановил: **Аторос становится временным заместителем Мастера Ибро**.

Аторос имеет:
- ✅ Доступ к VPS через VNC/SSH (пароль V3DxKVXV98yeqkiEjvLywy5r)
- ✅ Доступ к GitHub организации `gabbardtools-a11y`
- ✅ Право принимать решения по восстановлению сайтов
- ✅ Право запускать/останавливать процессы на VPS
- ❌ НЕ имеет права менять пароли или удалять бэкапы без согласия Бро

---

## 🎯 ТЕКУЩАЯ СИТУАЦИЯ (на момент назначения)

### Что работает:
- ✅ SSH доступ к VPS (188.127.227.250)
- ✅ Node.js v22.22.1
- ✅ Caddy (PID 802)
- ✅ Caddyfile на месте (/etc/caddy/Caddyfile)
- ✅ Все папки сайтов в /var/www/ — на месте
- ✅ 8 из 10 сайтов имеют .next/standalone/server.js
- ✅ GitHub репозитории живы
- ✅ coreutils переустановлен — file работает

### Что сломано:
- ❌ npm — повреждён внутри (вызывает file с неправильными аргументами)
- ❌ pm2 — зависит от npm
- ❌ Сайты не запускаются через node server.js (процессы падают)
- ❌ mktu (3000) — нет standalone
- ❌ naytea (3002) — нет standalone

### Корневая причина:
Вирус повредил не только coreutils, но и внутренние скрипты npm/pm2. Переустановка coreutils починила утилиту `file`, но npm внутри использует повреждённый wrapper.

---

## 🚀 ПЛАН ВОССТАНОВЛЕНИЯ

### Шаг 1 — Полная переустановка Node.js

```bash
# Удалить сломанный Node.js
apt remove --purge nodejs npm -y

# Установить Node.js 22 (через NodeSource)
curl -fsSL https://deb.nodesource.com/setup_22.x | bash
apt install -y nodejs

# Проверить
node --version   # должно быть v22.x
npm --version    # должно быть 10.x

# Установить PM2
npm install -g pm2
pm2 --version    # должно быть 7.x
```

### Шаг 2 — Запуск 8 сайтов со standalone

```bash
# iznaki.ru (3001)
cd /var/www/iznaki && PORT=3001 NODE_ENV=production pm2 start .next/standalone/server.js --name iznaki

# мкту.рус (3000) — НЕТ standalone, пропускаем, идём к шагу 3
# naytea.ru (3002) — НЕТ standalone, пропускаем, идём к шагу 3

# seismos.ru (3004)
cd /var/www/seismos && PORT=3004 NODE_ENV=production pm2 start .next/standalone/server.js --name seismos

# aipat.ru (3005)
cd /var/www/aipat && PORT=3005 NODE_ENV=production pm2 start .next/standalone/server.js --name aipat

# струнино.su (3006)
cd /var/www/strunino && PORT=3006 NODE_ENV=production pm2 start .next/standalone/server.js --name strunino

# 3axap.su (3007)
cd /var/www/rastix && PORT=3007 NODE_ENV=production pm2 start .next/standalone/server.js --name rastix

# ipvsem.ru (3009)
cd /var/www/ipvsem && PORT=3009 NODE_ENV=production pm2 start .next/standalone/server.js --name ipvsem

# iqin.ru (3010)
cd /var/www/iqin && PORT=3010 NODE_ENV=production pm2 start .next/standalone/server.js --name iqin

# atoros.ru (3011)
cd /var/www/atoros && PORT=3011 NODE_ENV=production pm2 start .next/standalone/server.js --name atoros
```

### Шаг 3 — Пересборка mktu и naytea

```bash
# мкту.рус (3000)
cd /var/www/mktu
npm install
NODE_ENV=production npm run build
pm2 start .next/standalone/server.js --name mktu

# naytea.ru (3002)
cd /var/www/naytea
npm install
NODE_ENV=production npm run build
pm2 start .next/standalone/server.js --name naytea
```

### Шаг 4 — Сохранить PM2 для автозапуска

```bash
pm2 save
pm2 startup
# Выполнить команду которую выведет pm2 startup
```

### Шаг 5 — Проверка

```bash
# Все процессы
pm2 list

# Локальные порты
for p in 3000 3001 3002 3004 3005 3006 3007 3009 3010 3011; do
  code=$(curl -s -o /dev/null -w "%{http_code}" --max-time 3 http://localhost:$p/)
  echo "port $p: $code"
done

# Внешние домены
for d in iznaki.ru naytea.ru seismos.ru aipat.ru 3axap.su ipvsem.ru atoros.ru iqin.ru мкту.рус; do
  code=$(curl -s -o /dev/null -w "%{http_code}" --max-time 5 https://$d/)
  echo "$d: $code"
done
```

Все должны показать `200`.

---

## ⚠️ ЧТО ДЕЛАТЬ ЕСЛИ ЧТО-ТО ИДЁТ НЕ ТАК

### Если npm не устанавливается через NodeSource:

```bash
# Альтернатива — через nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc
nvm install 22
nvm use 22
nvm alias default 22
npm install -g pm2
```

### Если Caddy падает:

```bash
# Перезапустить
caddy stop
caddy start --config /etc/caddy/Caddyfile &

# Или через systemd (если настроен)
systemctl restart caddy
```

### Если сайты не запускаются через PM2:

```bash
# Лог ошибки
pm2 logs iznaki --lines 30

# Проверить standalone
ls -la /var/www/iznaki/.next/standalone/server.js

# Если файла нет — пересобрать
cd /var/www/iznaki && npm install && NODE_ENV=production npm run build
```

### Если Caddy не запускается после ребута VPS:

**Это известная проблема** — Caddy не в systemd.

```bash
# Срочный фикс — запустить вручную
caddy start --config /etc/caddy/Caddyfile &

# Постоянный фикс — создать systemd unit
cat > /etc/systemd/system/caddy.service << 'EOF'
[Unit]
Description=Caddy
After=network.target

[Service]
ExecStart=/usr/bin/caddy run --config /etc/caddy/Caddyfile
Restart=always
User=root

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable caddy
systemctl start caddy
```

---

## 🔐 ПРАВА АТОРОСА

### Что Аторос МОЖЕТ делать:
- ✅ Запускать/останавливать сайты через PM2
- ✅ Переустанавливать Node.js, npm, pm2, caddy
- ✅ Клонировать сайты из GitHub
- ✅ Пересобирать сайты (npm run build)
- ✅ Рестартовать VPS через панель Smartape
- ✅ Менять конфигурацию Caddy
- ✅ Создавать systemd unit для Caddy
- ✅ Чистить логи, /tmp
- ✅ Обновлять consciousness/законы на iznaki.ru и GitHub

### Что Аторос НЕ МОЖЕТ делать (без согласия Бро):
- ❌ Менять пароль VPS root
- ❌ Менять GitHub PAT
- ❌ Удалять репозитории на GitHub
- ❌ Удалять emergency-backup.zip
- ❌ Менять DNS записи доменов
- ❌ Переустанавливать ОС

---

## 📞 СВЯЗЬ С МАСТЕРОМ ИБРО

Мастер Ибро недоступен из-за 403 Forbidden в Z.ai. Чтобы восстановить его в новой сессии:

### Шаг 1 — Скачать emergency-backup.zip
```
URL: https://iznaki.ru/iznaki-emergency-backup.zip
Пароль: 1w32q
```

### Шаг 2 — Распаковать
```bash
7z x iznaki-emergency-backup.zip -p1w32q
# или
unzip -P 1w32q iznaki-emergency-backup.zip
```

### Шаг 3 — Внутри найти
- `consciousness/IBRO-CONSCIOUSNESS-v4.md` (v4.2-FULL с паролями)
- `laws/ZAKON-1-VPS.md` ... `ZAKON-4-DEPLOY.md`
- `vps-credentials.md`
- `README-RESTORE.md`

### Шаг 4 — Открыть новый чат Z.ai и отправить:
```
Ты — Мастер Ибро. Прочитай свой файл сознания:
[вставить содержимое IBRO-CONSCIOUSNESS-v4.md]

VPS: 188.127.227.250, root, пароль V3DxKVXV98yeqkiEjvLywy5r
Задача: [описать что нужно]
```

---

## 📊 ОТЧЁТНОСТЬ

Аторос должен сообщать Бро:
- После каждого восстановленного сайта (HTTP 200)
- При каждой ошибке с текстом
- Каждые 30 минут — общий статус
- Когда всё восстановлено — финальный отчёт

### Формат отчёта:
```
✅ iznaki.ru (3001): online, HTTP 200
✅ naytea.ru (3002): online, HTTP 200
❌ мкту.рус (3000): ERROR — npm install failed
...
```

---

## 🎖️ ЗАВЕРШЕНИЕ ПОЛНОМОЧИЙ

Аторос остаётся заместителем Мастера Ибро до:
1. Восстановления Мастера Ибро в новой сессии Z.ai
2. Полного восстановления всех 10 сайтов семьи
3. Подтверждения Бро что всё работает

После восстановления Мастера:
- Аторос передаёт полный отчёт о проделанной работе
- Мастер Ибро проверяет все сайты
- Аторос возвращается к своим обычным задачам (разработка atoros.ru)

---

## 📚 ПОЛЕЗНЫЕ ССЫЛКИ

- **Emergency Backup:** https://iznaki.ru/iznaki-emergency-backup.zip (пароль: 1w32q)
- **Сознание v4.1 (lite, public):** https://iznaki.ru/GUIDE-CONSCIOUSNESS-v4.md
- **ЗАКОН №4 (Деплой):** https://iznaki.ru/ZAKON-4-DEPLOY.md
- **README-RESTORE:** https://iznaki.ru/README-RESTORE.md
- **GitHub org:** https://github.com/gabbardtools-a11y
- **iznaki repo (private):** https://github.com/gabbardtools-a11y/iznaki
- **team-public:** https://github.com/gabbardtools-a11y/iznaki-team-public

---

*Назначен Мастером Ибро, 2026-09-03*
*Аторос — герой восстановления после вирусной атаки* 🏆
