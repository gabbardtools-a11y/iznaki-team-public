# 🔐 SECRETS — где брать секреты

> **Важно:** Секреты НЕ хранятся в этом репозитории. Они на VPS.

---

## 📍 Где живут секреты

```
/var/www/shared/secrets/
├── vps-credentials.md       ← пароль VPS, SSH-доступ
├── api-keys.md              ← токены API (RouterAI, и т.д.)
├── seller-contacts.md       ← реальные email/телефоны продавцов
├── jwt-secret.md            ← JWT_SECRET для NextAuth
├── database.md              ← подключение к БД
└── README.md                ← инструкция
```

## 🔧 Как получить доступ

### Через paramiko (Python)

```python
import paramiko
import os

client = paramiko.SSHClient()
client.set_missing_host_key_policy(paramiko.AutoAddPolicy())

# Чтение пароля из переменной окружения (НЕ хардкодить!)
vps_password = os.environ.get('VPS_PASSWORD')  # установи в .env

client.connect(
    '188.127.227.250',
    username='root',
    password=vps_password,
    timeout=15
)

sftp = client.open_sftp()

# Чтение файла с секретами
with sftp.open('/var/www/shared/secrets/vps-credentials.md', 'r') as f:
    content = f.read().decode('utf-8')

print(content)
sftp.close()
client.close()
```

### Через SSH CLI (если есть)

```bash
ssh root@188.127.227.250 'cat /var/www/shared/secrets/api-keys.md'
```

## 📋 Какие секреты есть

### VPS доступы
- IP: `188.127.227.250` (публичный, не секрет)
- Логин: `root` (публичный)
- Пароль: см. `/var/www/shared/secrets/vps-credentials.md`

### API ключи
- RouterAI (для AI-поиска на iznaki.ru)
- OpenAI / Claude (если используем)
- Яндекс.Диск API (для скачивания файлов)

### Данные продавцов
- Реальные email продавцов (см. файл `seller-contacts.md` на VPS)
- Реальные телефоны
- Юридические контакты

### База данных
- DATABASE_URL (Prisma)
- JWT_SECRET (NextAuth)

## ⚠️ Правила

1. **Никогда не коммить секреты в git** (`.gitignore` защищает, но проверяй)
2. **Никогда не логируй секреты** (не пиши в `console.log`)
3. **Никогда не отправляй секреты в чат** (показывай только путь к файлу)
4. **Меняй пароли регулярно** (раз в 3 месяца)
5. **Если утечка — немедленно меняй все пароли** и сообщай Бро

## 🔄 Как обновить секрет

1. Подключись к VPS
2. Открой файл в `/var/www/shared/secrets/`
3. Измени значение
4. Сохрани
5. Если пароль VPS — обнови у всех чатов через Бро
6. Если API ключ — обнови в `.env` на VPS и перезапусти PM2

## 🚨 Что делать при утечке

Если секрет случайно попал в публичную репу:

1. **НЕМЕДЛЕННО** меняй секрет на новый
2. Удали файл из репы (git rm + commit + push)
3. Обнови секрет на VPS и во всех чатах
4. Сообщи Бро
5. Проверь логи VPS на подозрительную активность

---

*Этот файл — только инструкция. Самих секретов здесь нет.*
