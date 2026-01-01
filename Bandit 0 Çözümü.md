# Bandit Level 0 -> 0

## Amaç (Objective)

Bandit sunucusuna SSH üzerinden bağlantı sağlamak.

## المنهجية (Methodology)

للدخول على أي سيرفر بعيد (Remote Server) بنستخدم بروتوكول الـ SSH. في المستوى ده بنتعلم إزاي نستخدم الأمر `ssh` مع تحديد المستخدم، الهوست، والمنفذ (Port).

## Step-by-Step

### التحضير (Preparation)

- **Host**: `bandit.labs.overthewire.org`
- **Port**: `2220`
- **User**: `bandit0`
- **Password**: `bandit0`

### الأمر (Command)

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

### شرح الأجزاء (Explanation)

- `ssh`: البرنامج اللي بيعمل الاتصال.
- `bandit0@...`: اسم المستخدم اللي داخلين بيه.
- `-p 2220`: تحديد بورت الاتصال (لأن البورت الافتراضي 22 وهنا بنستخدم 2220).

## Öğrenilenler (Key Takeaways)

- **SSH Basics**: Uzak sunuculara güvenli bağlantı kurmayı öğrendik.
- **Port Specification**: `-p` bayrağı ile varsayılan dışındaki portlara bağlanmayı keşfettik.
- **Authentication**: İlk defa bir "wargame" dünyasına giriş yaptık!
