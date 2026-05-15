# Домашка: мониторинг под Windows

## Что запускаем
- InfluxDB, Prometheus, Grafana — через Docker Compose.
- Telegraf — нативно в Windows как служба, чтобы собирать реальные метрики Windows.
- Для Prometheus под Windows технически правильный экспортёр — windows_exporter. Если преподаватель требует именно node-exporter, его можно запустить в WSL2, но он будет показывать метрики Linux/WSL, а не Windows.

## Запуск Docker-части
Открыть PowerShell в этой папке:

```powershell
docker compose up -d
```

Проверка:

```powershell
docker ps
```

Открыть:
- Grafana: http://localhost:3000
- Prometheus: http://localhost:9090
- InfluxDB: http://localhost:8086

Grafana login/password по умолчанию: admin/admin.

## Telegraf
1. Скачать Telegraf для Windows с сайта InfluxData/GitHub.
2. Положить telegraf.exe и telegraf.conf в `C:\Program Files\Telegraf`.
3. Запустить PowerShell от администратора:

```powershell
cd "C:\Program Files\Telegraf"
.\telegraf.exe --config "C:\Program Files\Telegraf\telegraf.conf" --test
.\telegraf.exe service install --config "C:\Program Files\Telegraf\telegraf.conf"
Start-Service telegraf
Get-Service telegraf
```

## Windows exporter
1. Скачать windows_exporter MSI с GitHub prometheus-community/windows_exporter.
2. Установить.
3. Проверить в браузере:

http://localhost:9182/metrics

4. В Prometheus открыть:

http://localhost:9090/targets

Target должен быть UP.

## Grafana
Подключить два Data source:

### Prometheus
URL:

```text
http://prometheus:9090
```

### InfluxDB
Query language: InfluxQL
URL:

```text
http://influxdb:8086
```

Database:

```text
telegraf
```

User/password:

```text
admin / admin123
```

## Скриншоты для сдачи
Поставить диапазон времени в Grafana: Last 15 minutes.

Сделать скрины:
1. `docker ps` — контейнеры запущены.
2. Prometheus targets — windows_exporter UP.
3. Grafana dashboard по Prometheus.
4. Grafana dashboard по InfluxDB/Telegraf.
5. telegraf.conf — видно interval = 60s и плагины mem/swap/disk/net.
6. prometheus.yml — видно scrape_interval = 36s.

## Текст в отчёт
Стенд мониторинга был развёрнут под Windows. InfluxDB, Prometheus и Grafana запущены через Docker Compose. Telegraf установлен как Windows-служба, чтобы собирать аппаратные метрики компьютера: CPU, оперативная память, swap, диск, дисковые операции и сеть. В конфигурации Telegraf задан интервал сбора и отправки метрик 60 секунд. Для Prometheus использован windows_exporter, так как node-exporter предназначен для Linux-систем. В prometheus.yml задан scrape_interval 36 секунд. В Grafana подключены источники данных InfluxDB и Prometheus, после чего были сняты скриншоты метрик системы в покое за последние 15 минут.
