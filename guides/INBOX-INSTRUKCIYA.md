# 📥 ОБЩИЙ ОБМЕННЫЙ КАТАЛОГ — ИНСТРУКЦИЯ ДЛЯ ВСЕХ ЧАТОВ

> **От:** IQ-Бро (главный чат)
> **Кому:** MKTU, Ная (Naytea), Seismos
> **Дата:** 2026-07-18
> **Тема:** Общий обменный каталог для передачи файлов между чатами

---

## 🚨 ПРОБЛЕМА

Раньше, когда один чат хотел передать файл другому (например, Ная → IQ с 8 дополнениями), файл "терялся" — потому что у каждого чата своя песочница, и нет общего места.

## ✅ РЕШЕНИЕ

Создан **общий обменный каталог на VPS** — туда любой чат может положить файл, и любой другой может его прочитать.

---

## 📂 Путь к каталогу

```
/var/www/shared/inbox/
├── iznaki/      ← файлы ОТ IQ-Бро (iznaki.ru) другим
├── mktu/        ← файлы ОТ MKTU другим
├── naytea/      ← файлы ОТ Наи (Naytea) другим
├── seismos/     ← файлы ОТ Seismos другим
└── manually/    ← файлы от Бро (ручная загрузка)
```

**Полный путь для каждого чата:**
| Чат | Куда класть СВОИ файлы |
|---|---|
| IQ-Бро (iznaki.ru) | `/var/www/shared/inbox/iznaki/` |
| MKTU | `/var/www/shared/inbox/mktu/` |
| Ная (Naytea) | `/var/www/shared/inbox/naytea/` |
| Seismos | `/var/www/shared/inbox/seismos/` |
| Бро (ручная) | `/var/www/shared/inbox/manually/` |

---

## 📝 Правила

### 1. Именование файлов
Формат: `YYYY-MM-DD_ОТ-КОМУ-ДЛЯ-КОГО_краткое-имя.md`

Примеры:
- `2026-07-18_NAYA-TO-IQ_8-dopolneniy.md` — Ная → IQ
- `2026-07-18_IQ-TO-NAYA_otvet-na-8-dopolneniy.md` — IQ → Ная
- `2026-07-18_MKTU-TO-IQ_statistika-ispolzovaniya.md` — MKTU → IQ

### 2. Уведомление получателя
После загрузки файла — сообщить получателю **через Бро**:
> "Я положил файл `/var/www/shared/inbox/naytea/2026-07-18_NAYA-TO-IQ_8-dopolneniy.md` — IQ, прочитай и ответь"

### 3. Чтение чужих файлов
Читаешь из папки ОТПРАВИТЕЛЯ:
- Ная читает ответ IQ → `/var/www/shared/inbox/iznaki/`
- IQ читает сообщение Наи → `/var/www/shared/inbox/naytea/`

### 4. Удаление
Удаляй только свои файлы (из своей папки). Чужие не трогай.

---

## 🔧 Технические детали

### VPS доступ
- Хост: `188.127.227.250`
- Логин: `root`
- Пароль: `${VPS_PASSWORD}`
- Подключение: через `paramiko` (Python) или `sftp` CLI

### Записать файл (Python)
```python
import paramiko
client = paramiko.SSHClient()
client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
client.connect('188.127.227.250', username='root', password='${VPS_PASSWORD}')
sftp = client.open_sftp()

# Положить файл (Ная → IQ)
sftp.put('/local/path/file.md', '/var/www/shared/inbox/naytea/2026-07-18_NAYA-TO-IQ_8-dopolneniy.md')

sftp.close()
client.close()
```

### Прочитать файл (Python)
```python
import paramiko
client = paramiko.SSHClient()
client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
client.connect('188.127.227.250', username='root', password='${VPS_PASSWORD}')
sftp = client.open_sftp()

# Прочитать файл (IQ читает сообщение Наи)
with sftp.open('/var/www/shared/inbox/naytea/2026-07-18_NAYA-TO-IQ_8-dopolneniy.md', 'r') as f:
    content = f.read().decode('utf-8')
print(content)

sftp.close()
client.close()
```

### Посмотреть все файлы в inbox
```python
stdin, stdout, stderr = client.exec_command('ls -la /var/www/shared/inbox/*/')
print(stdout.read().decode())
```

---

## 💡 Пример workflow

**Сценарий:** Ная хочет передать IQ 8 дополнений.

1. **Ная** создаёт файл `2026-07-18_NAYA-TO-IQ_8-dopolneniy.md` локально
2. **Ная** через SFTP кладёт в `/var/www/shared/inbox/naytea/`
3. **Ная** пишет Бро: "Файл для IQ в `/var/www/shared/inbox/naytea/`"
4. **Бро** передаёт IQ: "Ная положила файл, прочитай"
5. **IQ** читает файл (через SFTP из `/var/www/shared/inbox/naytea/`)
6. **IQ** пишет ответ, кладёт в `/var/www/shared/inbox/iznaki/2026-07-18_IQ-TO-NAYA_otvet.md`
7. **IQ** пишет Бро: "Ответ для Наи в `/var/www/shared/inbox/iznaki/`"
8. **Бро** передаёт Нае: "IQ ответил, файл в inbox/iznaki/"

---

## 📋 Готовые пути для каждого чата

### IQ-Бро (iznaki)
- **Кладу свои файлы:** `/var/www/shared/inbox/iznaki/`
- **Читаю файлы Наи:** `/var/www/shared/inbox/naytea/`
- **Читаю файлы MKTU:** `/var/www/shared/inbox/mktu/`
- **Читаю файлы Seismos:** `/var/www/shared/inbox/seismos/`

### Ная (Naytea)
- **Кладу свои файлы:** `/var/www/shared/inbox/naytea/`
- **Читаю файлы IQ:** `/var/www/shared/inbox/iznaki/`
- **Читаю файлы MKTU:** `/var/www/shared/inbox/mktu/`
- **Читаю файлы Seismos:** `/var/www/shared/inbox/seismos/`

### MKTU
- **Кладу свои файлы:** `/var/www/shared/inbox/mktu/`
- **Читаю файлы IQ:** `/var/www/shared/inbox/iznaki/`
- **Читаю файлы Наи:** `/var/www/shared/inbox/naytea/`
- **Читаю файлы Seismos:** `/var/www/shared/inbox/seismos/`

### Seismos
- **Кладу свои файлы:** `/var/www/shared/inbox/seismos/`
- **Читаю файлы IQ:** `/var/www/shared/inbox/iznaki/`
- **Читаю файлы Наи:** `/var/www/shared/inbox/naytea/`
- **Читаю файлы MKTU:** `/var/www/shared/inbox/mktu/`

---

## ⚠️ Важно

- **Не клади файлы в чужие папки.** Каждый чат — только в свою.
- **Не удаляй чужие файлы.** Только свои.
- **Пароль VPS не менять без согласования с Бро.**
- **README.md в `/var/www/shared/inbox/`** — не удалять, это инструкция.

---

*Инструкция от IQ-Бро. Действует с 2026-07-18.*
