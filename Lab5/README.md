# Исследование информации о состоянии беспроводных сетей
alexkondrol@yandex.ru

## Цель работы

1.  Получить знания о методах исследования радиоэлектронной обстановки.
2.  Составить представление о механизмах работы Wi-Fi сетей на канальном
    и сетевом уровне модели OSI.
3.  Зекрепить практические навыки использования языка программирования R
    для обработки данных
4.  Закрепить знания основных функций обработки данных экосистемы
    tidyverse языка R

## Исходные данные

1.  Операционная система GNU/Linux
2.  Интерпретатор языка программирования R
3.  Редактор кода `Positron`
4.  Журналы программных средств анализа беспроводных сетей – `tcpdump` и
    `airodump-ng`

## Задание

Используя программный пакет `dplyr` языка программирования R провести
анализ журналов и ответить на вопросы

## Ход работы

1.  Подготовка данных 1.1. Импортировать данные
    (https://storage.yandexcloud.net/dataset.ctfsec/P2_wi_data.csv) 1.2.
    Привести датасеты в вид “аккуратных данных”, преобразовать типы
    столбцов в соответствии с типом данных 1.3. Просмотрите общую
    структуру данных с помощью функции `glimpse()`
2.  Анализ 2.1 Анализ точек доступа 2.1.1 Определить небезопасные точки
    доступа (без шифрования – OPN) 2.1.2. Определить производителя для
    каждого обнаруженного устройства 2.1.3. Выявить устройства,
    использующие последнюю версию протокола шифрования WPA3, и названия
    точек доступа, реализованных на этих устройствах 2.1.4.
    Отсортировать точки доступа по интервалу времени, в течение которого
    они находились на связи, по убыванию. 2.1.5. Обнаружить топ-10 самых
    быстрых точек доступа. 2.1.6. Отсортировать точки доступа по частоте
    отправки запросов (beacons) в единицу времени по их убыванию. 2.2
    Анализ клиентов 2.2.1 Определить производителя для каждого
    обнаруженного устройства. 2.2.2 Обнаружить устройства, которые НЕ
    рандомизируют свой MAC адрес 2.2.3. Кластеризовать запросы от
    устройств к точкам доступа по их именам. Определить время появления
    устройства в зоне радиовидимости и время выхода его из нее. 2.2.4.
    Оценить стабильность уровня сигнала внури кластера во времени.
    Выявить наиболее стабильный кластер.
3.  Оформление отчета

### Шаг 1. Подготовка данных

Для начала импортируем необходимые пакеты:

``` r
library(dplyr)
```


    Attaching package: 'dplyr'

    The following objects are masked from 'package:stats':

        filter, lag

    The following objects are masked from 'package:base':

        intersect, setdiff, setequal, union

``` r
library(stringr)
library(tidyr)
library(knitr)
library(readr)
```

Скачаем файл:

``` r
temp_dir <- tempdir()
download.file(
  url = "https://storage.yandexcloud.net/dataset.ctfsec/P2_wifi_data.csv",
  destfile = file.path(temp_dir, "P2_wifi_data.csv"),
  mode = "wb"
)
```

Считаем данные из файла:

``` r
wifi_ap <- read_csv(file.path(temp_dir, "P2_wifi_data.csv"),
                      n_max = 167)
```

    Rows: 167 Columns: 15
    ── Column specification ────────────────────────────────────────────────────────
    Delimiter: ","
    chr  (6): BSSID, Privacy, Cipher, Authentication, LAN IP, ESSID
    dbl  (6): channel, Speed, Power, # beacons, # IV, ID-length
    lgl  (1): Key
    dttm (2): First time seen, Last time seen

    ℹ Use `spec()` to retrieve the full column specification for this data.
    ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
clients <- read_csv(file.path(temp_dir, "P2_wifi_data.csv"),
                      skip = 169)
```

    Warning: One or more parsing issues, call `problems()` on your data frame for details,
    e.g.:
      dat <- vroom(...)
      problems(dat)

    Rows: 12081 Columns: 7
    ── Column specification ────────────────────────────────────────────────────────
    Delimiter: ","
    chr  (3): Station MAC, BSSID, Probed ESSIDs
    dbl  (2): Power, # packets
    dttm (2): First time seen, Last time seen

    ℹ Use `spec()` to retrieve the full column specification for this data.
    ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
head(clients, 2)
```

    # A tibble: 2 × 7
      `Station MAC`  `First time seen`   `Last time seen`    Power `# packets` BSSID
      <chr>          <dttm>              <dttm>              <dbl>       <dbl> <chr>
    1 CA:66:3B:8F:5… 2023-07-28 09:13:03 2023-07-28 10:59:44   -33         858 BE:F…
    2 96:35:2D:3D:8… 2023-07-28 09:13:03 2023-07-28 09:13:03   -65           4 (not…
    # ℹ 1 more variable: `Probed ESSIDs` <chr>

Переименуем названия аттрибутов и приведем аттрибуты к нужному типу
данных для датасета точек доступа:

``` r
names(wifi_ap) <- trimws(names(wifi_ap))

wifi_ap_clean <- wifi_ap %>%
rename(
bssid      = BSSID,
first_seen = `First time seen`,
last_seen  = `Last time seen`,
channel    = channel,
speed      = Speed,
privacy    = Privacy,
cipher     = Cipher,
auth       = Authentication,
power      = Power,
beacons    = `# beacons`,
iv_count   = `# IV`,
lan_ip     = `LAN IP`,
id_length  = `ID-length`,
essid      = ESSID,
key        = Key
) %>%
mutate(across(where(is.character), ~ trimws(.))) %>%
mutate(
first_seen = as.POSIXct(first_seen, format = "%Y-%m-%d %H:%M:%S", tz = "UTC"),
last_seen  = as.POSIXct(last_seen,  format = "%Y-%m-%d %H:%M:%S", tz = "UTC"),
channel    = as.numeric(channel),
speed      = as.numeric(speed),
power      = as.numeric(power),
beacons    = as.numeric(beacons),
iv_count   = as.numeric(iv_count),
id_length  = as.numeric(id_length)
)
```

Проделаем аналагичную операцию с клиентами:

``` r
names(clients) <- trimws(names(clients))

wifi_clients_clean <- clients %>%
rename(
station_mac   = `Station MAC`,
first_seen    = `First time seen`,
last_seen     = `Last time seen`,
power         = Power,
packets       = `# packets`,
bssid         = BSSID,
probed_essids = `Probed ESSIDs`
) %>%
mutate(across(where(is.character), ~ trimws(.))) %>%
mutate(
first_seen = as.POSIXct(first_seen, format = "%Y-%m-%d %H:%M:%S", tz = "UTC"),
last_seen  = as.POSIXct(last_seen,  format = "%Y-%m-%d %H:%M:%S", tz = "UTC"),
power      = as.numeric(power),
packets    = as.numeric(packets),
station_mac = toupper(station_mac),
bssid = case_when(
is.na(bssid) ~ NA_character_,
grepl("not associated", bssid, ignore.case = TRUE) ~ NA_character_,
TRUE ~ toupper(bssid)
)
)
```

Теперь посмотрим общую структуру файлов:

``` r
glimpse(wifi_ap_clean)
```

    Rows: 167
    Columns: 15
    $ bssid      <chr> "BE:F1:71:D5:17:8B", "6E:C7:EC:16:DA:1A", "9A:75:A8:B9:04:1…
    $ first_seen <dttm> 2023-07-28 09:13:03, 2023-07-28 09:13:03, 2023-07-28 09:13…
    $ last_seen  <dttm> 2023-07-28 11:50:50, 2023-07-28 11:55:12, 2023-07-28 11:53…
    $ channel    <dbl> 1, 1, 1, 7, 6, 6, 11, 11, 11, 1, 6, 14, 11, 11, 6, 6, 6, 6,…
    $ speed      <dbl> 195, 130, 360, 360, 130, 130, 195, 130, 130, 195, 180, 65, …
    $ privacy    <chr> "WPA2", "WPA2", "WPA2", "WPA2", "WPA2", "OPN", "WPA2", "WPA…
    $ cipher     <chr> "CCMP", "CCMP", "CCMP", "CCMP", "CCMP", NA, "CCMP", "CCMP",…
    $ auth       <chr> "PSK", "PSK", "PSK", "PSK", "PSK", NA, "PSK", "PSK", "PSK",…
    $ power      <dbl> -30, -30, -68, -37, -57, -63, -27, -38, -38, -66, -42, -62,…
    $ beacons    <dbl> 846, 750, 694, 510, 647, 251, 1647, 1251, 704, 617, 1390, 1…
    $ iv_count   <dbl> 504, 116, 26, 21, 6, 3430, 80, 11, 0, 0, 86, 0, 0, 0, 907, …
    $ lan_ip     <chr> "0.  0.  0.  0", "0.  0.  0.  0", "0.  0.  0.  0", "0.  0. …
    $ id_length  <dbl> 12, 4, 2, 14, 25, 13, 12, 13, 24, 12, 10, 0, 24, 24, 12, 0,…
    $ essid      <chr> "C322U13 3965", "Cnet", "KC", "POCO X5 Pro 5G", NA, "MIREA_…
    $ key        <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…

``` r
glimpse(wifi_clients_clean)
```

    Rows: 12,081
    Columns: 7
    $ station_mac   <chr> "CA:66:3B:8F:56:DD", "96:35:2D:3D:85:E6", "5C:3A:45:9E:1…
    $ first_seen    <dttm> 2023-07-28 09:13:03, 2023-07-28 09:13:03, 2023-07-28 09…
    $ last_seen     <dttm> 2023-07-28 10:59:44, 2023-07-28 09:13:03, 2023-07-28 11…
    $ power         <dbl> -33, -65, -39, -61, -53, -43, -31, -71, -74, -65, -45, -…
    $ packets       <dbl> 858, 4, 432, 958, 1, 344, 163, 3, 115, 437, 265, 77, 7, …
    $ bssid         <chr> "BE:F1:71:D5:17:8B", NA, "BE:F1:71:D6:10:D7", "BE:F1:71:…
    $ probed_essids <chr> "C322U13 3965", "IT2 Wireless", "C322U21 0566", "C322U13…

### Шаг 2. Анализ

#### 1. Определить небезопасные точки доступа (без шифрования – OPN)

``` r
wifi_ap_clean %>% filter(privacy == "OPN")
```

    # A tibble: 42 × 15
       bssid    first_seen          last_seen           channel speed privacy cipher
       <chr>    <dttm>              <dttm>                <dbl> <dbl> <chr>   <chr> 
     1 E8:28:C… 2023-07-28 09:13:03 2023-07-28 11:55:38       6   130 OPN     <NA>  
     2 E8:28:C… 2023-07-28 09:13:06 2023-07-28 11:55:12       6   130 OPN     <NA>  
     3 E8:28:C… 2023-07-28 09:13:06 2023-07-28 11:55:11       6   130 OPN     <NA>  
     4 E8:28:C… 2023-07-28 09:13:06 2023-07-28 11:55:10       6    -1 OPN     <NA>  
     5 00:25:0… 2023-07-28 09:13:06 2023-07-28 11:56:21      44    -1 OPN     <NA>  
     6 E8:28:C… 2023-07-28 09:13:09 2023-07-28 11:56:05      11   130 OPN     <NA>  
     7 E8:28:C… 2023-07-28 09:13:13 2023-07-28 10:27:06       6   130 OPN     <NA>  
     8 E8:28:C… 2023-07-28 09:13:13 2023-07-28 10:39:43       6   130 OPN     <NA>  
     9 E8:28:C… 2023-07-28 09:13:17 2023-07-28 11:52:32       1   130 OPN     <NA>  
    10 E8:28:C… 2023-07-28 09:13:50 2023-07-28 11:43:39      11   130 OPN     <NA>  
    # ℹ 32 more rows
    # ℹ 8 more variables: auth <chr>, power <dbl>, beacons <dbl>, iv_count <dbl>,
    #   lan_ip <chr>, id_length <dbl>, essid <chr>, key <lgl>

### 2. Определить производителя для каждого обнаруженного устройства

Для определения производителя будем использовать `manuf.txt` - это
стандартный справочник производителей, основанный на OUI-таблице
Wireshark. Сделаем из него таблицу префиксов

``` r
temp_dir <- tempdir()

manuf_url  <- "https://raw.githubusercontent.com/observ3r/wobs/master/manuf.txt"
manuf_file <- file.path(temp_dir, "manuf.txt")

download.file(manuf_url, manuf_file, mode = "wb", quiet = TRUE)

manuf_raw <- read.table(
  manuf_file,
  comment.char      = "#",
  stringsAsFactors  = FALSE,
  fill              = TRUE
)

manuf_tbl <- manuf_raw %>%
  transmute(
    prefix6 = toupper(gsub(":", "", V1)), 
    vendor  = V2
  ) %>%
  filter(nchar(prefix6) == 6, vendor != "") %>%
  distinct()
```

Напишем функцию для нормализации MAC-адреса:

``` r
normalize_mac <- function(x) toupper(gsub("[^0-9A-F]", "", x))
```

Добавим новый столбец `prefix6` в котором будет нормализованный MAC
адрес: а потом сделаем left join с нашей таблицей производителей

``` r
wifi_ap_clean <- wifi_ap_clean %>%
  mutate(prefix6 = substr(normalize_mac(bssid), 1, 6)) %>%
  left_join(manuf_tbl, by = "prefix6")
```

Посмотрим какие компании мы смогли определить

``` r
wifi_ap_clean %>%
  select(bssid, essid, vendor) %>%
  filter(!is.na(vendor)) %>%
  head(20)
```

    # A tibble: 20 × 3
       bssid             essid   vendor  
       <chr>             <chr>   <chr>   
     1 1C:7E:E5:8E:B7:DE <NA>    D-LinkIn
     2 00:25:00:FF:94:73 <NA>    Apple   
     3 00:26:99:F2:7A:E2 GIVC    Cisco   
     4 48:5B:39:F9:7A:48 <NA>    AsustekC
     5 00:26:99:F2:7A:E1 IKB     Cisco   
     6 00:26:99:BA:75:80 GIVC    Cisco   
     7 00:23:EB:E3:81:F2 GIVC    Cisco   
     8 00:23:EB:E3:81:F1 IKB     Cisco   
     9 00:26:99:F2:7A:E0 <NA>    Cisco   
    10 00:23:EB:E3:81:FE IKB     Cisco   
    11 00:23:EB:E3:81:FD GIVC    Cisco   
    12 00:26:CB:AA:62:71 IKB     Cisco   
    13 00:03:7A:1A:18:56 <NA>    TaiyoYud
    14 00:09:9A:12:55:04 <NA>    Elmo    
    15 00:23:EB:E3:49:31 <NA>    Cisco   
    16 00:23:EB:E3:44:31 <NA>    Cisco   
    17 00:03:7A:1A:03:56 MT_FREE TaiyoYud
    18 00:26:99:BA:75:8F <NA>    Cisco   
    19 00:03:7F:12:34:56 MT_FREE AtherosC
    20 00:26:99:F1:1A:E1 IKB     Cisco   

#### 3. Выявить устройства, использующие последнюю версию протокола шифрования WPA3, и названия точек доступа, реализованных на этих устройствах?

Нам нужно оставить только строки, где используется WPA3 или SAE (признак
WPA3-Personal),

``` r
wpa3_aps <- wifi_ap_clean %>%
  filter(grepl("WPA3", privacy, ignore.case = TRUE) | grepl("SAE", auth, ignore.case = TRUE)) %>%
  select(bssid, essid, auth)

wpa3_aps
```

    # A tibble: 8 × 3
      bssid             essid                auth   
      <chr>             <chr>                <chr>  
    1 26:20:53:0C:98:E8 <NA>                 SAE PSK
    2 A2:FE:FF:B8:9B:C9 Christie’s           SAE PSK
    3 96:FF:FC:91:EF:64 <NA>                 SAE PSK
    4 CE:48:E7:86:4E:33 iPhone (Анастасия)   SAE PSK
    5 8E:1F:94:96:DA:FD iPhone (Анастасия)   SAE PSK
    6 BE:FD:EF:18:92:44 Димасик              SAE PSK
    7 3A:DA:00:F9:0C:02 iPhone XS Max 🦊🐱🦊 SAE PSK
    8 76:C5:A0:70:08:96 <NA>                 SAE PSK

#### 4. Отсортировать точки доступа по интервалу времени, в течение которого они находились на связи, по убыванию

Склеим сессии и отсортируем их по длительности. Сессии считаются
независимыми, если интервал между окончанием предыдущей и началом
следующей записи по одной точке доступа превышает 45 минут.

``` r
wifi_ap_sessions <- wifi_ap_clean %>%
  arrange(bssid, first_seen) %>%
  group_by(bssid) %>%
  mutate(
    gap_min     = as.numeric(difftime(first_seen, lag(last_seen), units = "mins")),
    new_session = if_else(is.na(gap_min) | gap_min > 45, 1L, 0L),
    session_id  = cumsum(new_session)
  ) %>%
  group_by(bssid, session_id) %>%
  summarise(
    essid      = first(essid),
    vendor     = first(vendor),          # <- vendor, не company
    first_seen = min(first_seen, na.rm = TRUE),
    last_seen  = max(last_seen,  na.rm = TRUE),
    beacons    = sum(beacons,    na.rm = TRUE),
    speed      = max(speed,      na.rm = TRUE),
    mean_power = mean(power,     na.rm = TRUE),
    .groups    = "drop"
  ) %>%
  mutate(
    duration = as.numeric(difftime(last_seen, first_seen, units = "secs")),
    beacon_rate = beacons / duration
  )
```

``` r
wifi_ap_sorted_duration <- wifi_ap_sessions %>%
  arrange(desc(duration)) %>%
  select(bssid, essid, vendor, session_id, duration, first_seen, last_seen)

wifi_ap_sorted_duration
```

    # A tibble: 167 × 7
       bssid             essid        vendor session_id duration first_seen         
       <chr>             <chr>        <chr>       <int>    <dbl> <dttm>             
     1 00:25:00:FF:94:73 <NA>         Apple           1     9795 2023-07-28 09:13:06
     2 E8:28:C1:DD:04:52 MIREA_HOTSP… <NA>            1     9776 2023-07-28 09:13:09
     3 E8:28:C1:DC:B2:52 MIREA_HOTSP… <NA>            1     9755 2023-07-28 09:13:03
     4 08:3A:2F:56:35:FE <NA>         <NA>            1     9746 2023-07-28 09:13:27
     5 6E:C7:EC:16:DA:1A Cnet         <NA>            1     9729 2023-07-28 09:13:03
     6 E8:28:C1:DC:B2:50 MIREA_GUESTS <NA>            1     9726 2023-07-28 09:13:06
     7 48:5B:39:F9:7A:48 <NA>         Asust…          1     9725 2023-07-28 09:13:06
     8 E8:28:C1:DC:B2:51 <NA>         <NA>            1     9725 2023-07-28 09:13:06
     9 E8:28:C1:DC:FF:F2 <NA>         <NA>            1     9724 2023-07-28 09:13:06
    10 8E:55:4A:85:5B:01 Vladimir     <NA>            1     9723 2023-07-28 09:13:06
    # ℹ 157 more rows
    # ℹ 1 more variable: last_seen <dttm>

#### 5. Обнаружить топ-10 самых быстрых точек доступа

``` r
wifi_ap_sorted_speed <- wifi_ap_sessions %>%
arrange(desc(speed)) %>%
slice_head(n = 10) %>%
select(bssid, essid, vendor, session_id, speed, first_seen, last_seen)

wifi_ap_sorted_speed
```

    # A tibble: 10 × 7
       bssid   essid vendor session_id speed first_seen          last_seen          
       <chr>   <chr> <chr>       <int> <dbl> <dttm>              <dttm>             
     1 26:20:… <NA>  <NA>            1   866 2023-07-28 09:15:45 2023-07-28 09:33:10
     2 8E:1F:… iPho… <NA>            1   866 2023-07-28 10:08:32 2023-07-28 10:15:27
     3 96:FF:… <NA>  <NA>            1   866 2023-07-28 09:52:54 2023-07-28 10:25:02
     4 CE:48:… iPho… <NA>            1   866 2023-07-28 09:59:20 2023-07-28 10:04:15
     5 02:B3:… HONO… <NA>            1   360 2023-07-28 10:54:47 2023-07-28 10:54:47
     6 14:EB:… Gnez… <NA>            1   360 2023-07-28 09:25:01 2023-07-28 11:53:36
     7 4A:EC:… POCO… <NA>            1   360 2023-07-28 09:13:03 2023-07-28 11:04:01
     8 56:C5:… OneP… <NA>            1   360 2023-07-28 09:17:49 2023-07-28 10:27:22
     9 9A:75:… KC    <NA>            1   360 2023-07-28 09:13:03 2023-07-28 11:53:31
    10 E8:28:… MIRE… <NA>            1   360 2023-07-28 09:18:16 2023-07-28 11:51:48

#### 6. Отсортировать точки доступа по частоте отправки запросов (beacons) в единицу времени по их убыванию.

``` r
wifi_ap_sorted_beacon_rate <- wifi_ap_sessions %>%
filter(is.finite(beacon_rate)) %>%
arrange(desc(beacon_rate)) %>%
select(bssid, essid, vendor, session_id, beacons, duration, beacon_rate)

wifi_ap_sorted_beacon_rate
```

    # A tibble: 124 × 7
       bssid             essid        vendor session_id beacons duration beacon_rate
       <chr>             <chr>        <chr>       <int>   <dbl>    <dbl>       <dbl>
     1 F2:30:AB:E9:03:ED iPhone (Uli… <NA>            1       6        7       0.857
     2 B2:CF:C0:00:4A:60 Михаил's Ga… <NA>            1       4        5       0.8  
     3 3A:DA:00:F9:0C:02 iPhone XS M… <NA>            1       5        9       0.556
     4 00:3E:1A:5D:14:45 MT_FREE      <NA>            1       1        2       0.5  
     5 02:BC:15:7E:D5:DC MT_FREE      <NA>            1       1        2       0.5  
     6 76:C5:A0:70:08:96 <NA>         <NA>            1       1        2       0.5  
     7 D2:25:91:F6:6C:D8 Саня         <NA>            1       5       13       0.385
     8 BE:F1:71:D6:10:D7 C322U21 0566 <NA>            1    1647     9461       0.174
     9 00:03:7A:1A:03:56 MT_FREE      Taiyo…          1       1        6       0.167
    10 38:1A:52:0D:84:D7 EBFCD57F-EE… <NA>            1     704     4319       0.163
    # ℹ 114 more rows

#### 1. Определить производителя для каждого обнаруженного устройства

Логика аналогичная точкам доступа:

``` r
wifi_clients_clean <- wifi_clients_clean %>%
  mutate(prefix6 = substr(normalize_mac(station_mac), 1, 6)) %>%
  left_join(manuf_tbl, by = "prefix6")
```

``` r
wifi_clients_clean %>%
  select(station_mac, vendor, bssid, power, packets) %>%
  filter(!is.na(vendor))
```

    # A tibble: 8 × 5
      station_mac       vendor   bssid             power packets
      <chr>             <chr>    <chr>             <dbl>   <dbl>
    1 00:95:69:E7:7F:35 LsdScien <NA>                -69    2245
    2 00:95:69:E7:7C:ED LsdScien <NA>                -55    4096
    3 00:95:69:E7:7D:21 LsdScien <NA>                -33    8171
    4 B8:27:EB:59:FA:0E Raspberr 6E:C7:EC:16:DA:1A    -1     405
    5 00:90:4C:E6:54:54 Epigram  <NA>                -65      16
    6 EC:55:F9:A1:4C:6B HonHaiPr 9A:9F:06:44:24:5B    -1       1
    7 00:04:35:22:4F:75 ComptekI 00:AB:0A:00:10:10   -83      20
    8 00:0C:E7:A8:D6:73 Mediatek <NA>                -67       3

``` r
  head(20)
```

    [1] 20

#### 2. Обнаружить устройства, которые НЕ рандомизируют свой MAC адрес

Нерандомизированный MAC - это глобально назначенный адрес (бит LAA = 0).
Проверяем 2 бита первого октета:

0x01 - multicast

0x02 - locally administered (если установлен значит есть рандомизация).

``` r
is_randomized_mac <- function(mac) {
  mac_hex <- toupper(gsub("[^0-9A-F]", "", mac))
  if (nchar(mac_hex) < 2) return(NA)
  
  b1 <- strtoi(substr(mac_hex, 1, 2), base = 16)
  if (is.na(b1)) return(NA)
  
  if (bitwAnd(b1, 0x01) != 0) return(NA)
  
  bitwAnd(b1, 0x02) != 0
}
```

``` r
clients_nr <- wifi_clients_clean %>%
  mutate(is_randomized = vapply(station_mac, is_randomized_mac, logical(1))) %>%
  filter(is_randomized == FALSE) %>%
  select(station_mac, bssid, first_seen, last_seen, power, packets)

clients_nr
```

    # A tibble: 217 × 6
       station_mac       bssid first_seen          last_seen           power packets
       <chr>             <chr> <dttm>              <dttm>              <dbl>   <dbl>
     1 5C:3A:45:9E:1A:7B BE:F… 2023-07-28 09:13:03 2023-07-28 11:51:54   -39     432
     2 C0:E4:34:D8:E7:E5 BE:F… 2023-07-28 09:13:03 2023-07-28 11:53:16   -61     958
     3 10:51:07:CB:33:E7 <NA>  2023-07-28 09:13:05 2023-07-28 11:56:06   -43     344
     4 68:54:5A:40:35:9E 1E:9… 2023-07-28 09:13:06 2023-07-28 11:50:50   -31     163
     5 74:4C:A1:70:CE:F7 E8:2… 2023-07-28 09:13:06 2023-07-28 09:20:01   -71       3
     6 BC:F1:71:D4:DB:04 <NA>  2023-07-28 09:13:07 2023-07-28 10:57:52   -45     265
     7 4C:44:5B:14:76:E3 E8:2… 2023-07-28 09:13:09 2023-07-28 09:47:44    -1      71
     8 A0:E7:0B:AE:D5:44 0A:C… 2023-07-28 09:13:09 2023-07-28 11:34:42   -37     125
     9 00:95:69:E7:7F:35 <NA>  2023-07-28 09:13:11 2023-07-28 11:56:07   -69    2245
    10 00:95:69:E7:7C:ED <NA>  2023-07-28 09:13:11 2023-07-28 11:56:13   -55    4096
    # ℹ 207 more rows

#### Кластеризовать запросы от устройств к точкам доступа по их именам. Определить время появления устройства в зоне радиовидимости и время выхода его из нее

Нормализуем ESSID у точек доступа

``` r
ap_essid <- wifi_ap_clean %>%
  mutate(essid_norm = str_squish(essid)) %>%
  select(bssid, essid_norm)
```

Привязываем ESSID к клиентам и группируем

``` r
device_essid_presence <- wifi_clients_clean %>%
  filter(!is.na(bssid)) %>%
  left_join(ap_essid, by = "bssid") %>%
  filter(!is.na(essid_norm), essid_norm != "") %>%
  group_by(station_mac, essid_norm) %>% 
  summarise(
    first_seen   = min(first_seen, na.rm = TRUE),
    last_seen    = max(last_seen,  na.rm = TRUE),
    duration_sec = as.numeric(difftime(last_seen, first_seen, units = "secs")),
    n_records    = n(),
    .groups      = "drop"
  ) %>%
  arrange(desc(duration_sec), station_mac, essid_norm)

device_essid_presence
```

    # A tibble: 99 × 6
       station_mac   essid_norm first_seen          last_seen           duration_sec
       <chr>         <chr>      <dttm>              <dttm>                     <dbl>
     1 8C:55:4A:DE:… Galaxy A3… 2023-07-28 09:13:17 2023-07-28 11:56:16         9779
     2 CA:54:C4:8B:… GIVC       2023-07-28 09:13:06 2023-07-28 11:55:04         9718
     3 F6:4D:98:98:… GIVC       2023-07-28 09:14:37 2023-07-28 11:55:29         9652
     4 C0:E4:34:D8:… C322U13 3… 2023-07-28 09:13:03 2023-07-28 11:53:16         9613
     5 5C:3A:45:9E:… C322U21 0… 2023-07-28 09:13:03 2023-07-28 11:51:54         9531
     6 28:7F:CF:23:… KC         2023-07-28 09:13:14 2023-07-28 11:51:50         9516
     7 34:E1:2D:3C:… Cnet       2023-07-28 09:13:29 2023-07-28 11:51:50         9501
     8 88:D8:2E:4F:… POCO X5 P… 2023-07-28 09:13:19 2023-07-28 11:51:24         9485
     9 FE:B7:DD:ED:… MIREA_HOT… 2023-07-28 09:13:55 2023-07-28 11:51:47         9472
    10 68:54:5A:40:… Galaxy A71 2023-07-28 09:13:06 2023-07-28 11:50:50         9464
    # ℹ 89 more rows
    # ℹ 1 more variable: n_records <int>

#### Оценить стабильность уровня сигнала внури кластера во времени. Выявить наиболее стабильный кластер.

Для оценки стабильности уровня сигнала кластер определяется как пара
(устройство, сеть). Это позволяет анализировать изменение уровня сигнала
конкретного клиента в пределах конкретной точки доступа. Для каждого
кластера вычислим среднеквадратичное отклонение (т.к. RSSI постоянно
меняется чем менько отклонение, тем меньше “прыгал” сигнал)

``` r
client_clusters <- wifi_clients_clean %>%
  filter(!is.na(bssid)) %>%
  left_join(
    ap_essid,
    by = "bssid"
  ) %>%
  filter(!is.na(essid_norm), essid_norm != "") %>%
  group_by(station_mac, essid_norm)
```

``` r
stability <- client_clusters %>%
  summarise(
    n_obs = n(),
    span_m = as.numeric(difftime(max(last_seen), min(first_seen), units = "mins")),
    sd_rssi   = sd(power,   na.rm = TRUE),
    .groups   = "drop"
  ) %>%
  arrange(sd_rssi)

most_stable <- slice_head(stability, n = 1)

stability
```

    # A tibble: 99 × 5
       station_mac       essid_norm    n_obs  span_m sd_rssi
       <chr>             <chr>         <int>   <dbl>   <dbl>
     1 00:E9:3A:67:93:E9 POCO C40          1  99.9        NA
     2 00:F4:8D:F7:C5:19 Redmi 12          1  58.4        NA
     3 02:69:A5:29:F1:3E Galaxy A71        1  32.3        NA
     4 02:B3:4E:24:2A:00 Димасик           1   0          NA
     5 04:8C:9A:0B:40:EA MIREA_HOTSPOT     1  88.1        NA
     6 06:15:2E:12:C8:A6 MIREA_HOTSPOT     1   2.9        NA
     7 06:7A:BA:E6:9F:FA Vladimir          1  18.1        NA
     8 06:F2:A9:C1:8D:09 MIREA_HOTSPOT     1 139.         NA
     9 0A:AB:49:39:BB:29 MIREA_HOTSPOT     1  67.1        NA
    10 0A:C2:C3:08:9E:F8 MIREA_HOTSPOT     1   0.133      NA
    # ℹ 89 more rows

``` r
most_stable
```

    # A tibble: 1 × 5
      station_mac       essid_norm n_obs span_m sd_rssi
      <chr>             <chr>      <int>  <dbl>   <dbl>
    1 00:E9:3A:67:93:E9 POCO C40       1   99.9      NA

### Шаг 3.

Оформить отчет

## Вывод

В ходе практической работы я развил навыки работы с языком
программирования R, а также научился исследовать информацию о состоянии
беспроводных сетей
