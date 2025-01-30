# olimpit-moodle
### Recommended Specs
```txt
--- Ideal ---
CPU: 4 Core
RAM: 8 GB
Storage: 30 GB
Versi OS:  Ubuntu 20.04
```

## Setup VPS dan Docker:
install docker https://docs.docker.com/engine/install/ubuntu/ 

berikan hak pada docker ```usermod -aG docker $USER```

## Setup Moodle
- Clone repo
```bash
git clone https://github.com/ARenewalAgent-ITS/moodle-docker
cd moodle-docker
```
- Ubah .env
```bash
cp .env.example .env
```
- Run Docker
```bash
docker compose up -d
```

# Setup Web
masuk ke http://IP-VPS ikuti langkah ini di web
- language "Next"
- Confirm paths "Next"
- Choose database driver "MariaDB"
- Database settings
    - Database host moodle_db
    - Database name moodle
    - Database user ${MOODLE_DATABASE_USER}
    - Database Password ${MOODLE_DATABASE_PASSWORD}
- Installation - Moodle 4.5.1+ (Build: 20250124) "Continue aja"
- Nunggu instalasi (agak lama)
- Masukin user admin dan moodle sesuai kebutuhan
