# Основы обработки данных с помощью R и Dplyr
alexkondrol@yandex.ru

## Цель работы

1.  Зекрепить практические навыки использования языка программирования R
    для обработки данных
2.  Закрепить знания основных функций обработки данных экосистемы
    tidyverse языка R
3.  Закрепить навыки исследования метаданных DNS трафика

## Исходные данные

1.  Операционная система GNU/Linux
2.  Интерпретатор языка программирования R
3.  Редактор кода `Positron`
4.  Метаданные о DNS трафике в внутренней сети Доброй Организации

## Задание

Используя программный пакет `dplyr`, освоить анализ DNS логов с помощью
языка программирования R.

## Ход работы

1.  Подготовка данных 1.1. Импортируйте данные DNS –
    https://storage.yandexcloud.net/dataset.ctfsec/dns.zip 1.2. Добавьте
    пропущенные данные о структуре данных (назначении столбцов) 1.3.
    Преобразуйте данные в столбцах в нужный формат 1.4. Просмотрите
    общую структуру данных с помощью функции glimpse()

2.  Анализ 2.1. Сколько участников информационного обмена в сети Доброй
    Организации? 2.2. Какое соотношение участников обмена внутри сети и
    участников обращений к внешним ресурсам? 2.3. Найдите топ-10
    участников сети, проявляющих наибольшую сетевую активность. 2.4.
    Найдите топ-10 доменов, к которым обращаются пользователи сети и
    соответственное количество обращений 2.5. Опеределите базовые
    статистические характеристики (функция summary() ) интервала времени
    между последовательными обращениями к топ-10 доменам. 2.6. Часто
    вредоносное программное обеспечение использует DNS канал в качестве
    канала управления, периодически отправляя запросы на подконтрольный
    злоумышленникам DNS сервер. По периодическим запросам на один и тот
    же домен можно выявить скрытый DNS канал. Есть ли такие IP адреса в
    исследуемом датасете?

3.  Обогащение данных 3.1. Определите местоположение (страну, город) и
    организацию-провайдера для топ-10 доменов. Для этого можно
    использовать сторонние сервисы, например http://ip-api.com
    (API-эндпоинт – http://ip-api.com/json).

### Шаг 1 - Подготовка данных

Для начала импортируем наш любимый пакет `dplyr`:

``` r
library(dplyr)
```


    Attaching package: 'dplyr'

    The following objects are masked from 'package:stats':

        filter, lag

    The following objects are masked from 'package:base':

        intersect, setdiff, setequal, union

Считаем наш лог:

``` r
dns_data <- read.table(
  "logs/dns.log",
  sep = "\t",
  header = FALSE,
  fill = TRUE,
  quote = "",
  stringsAsFactors = FALSE
)
```

Добавим названия столбцов, согласно стандартной
[спецификации](https://docs.zeek.org/en/master/logs/dns.html) `zeek`:

``` r
colnames(dns_data) <- c(
  "ts","uid","id.orig_h","id.orig_p","id.resp_h","id.resp_p",
  "proto","trans_id","query","qclass","qclass_name","qtype",
  "qtype_name","rcode","rcode_name","AA","TC","RD","RA","Z",
  "answers","TTLs","rejected"
)[1:ncol(dns_data)]
```

Из [спецификации](https://docs.zeek.org/en/master/logs/dns.html) мы
узнаем значение каждого столбца:

1.  ts – время события (в секундах от эпохи)
2.  uid – уникальный идентификатор соединения
3.  id.orig_h – исходный хост (клиент)
4.  id.orig_p – исходный порт
5.  id.resp_h – сервер (обычно DNS-сервер)
6.  id.resp_p – порт сервера
7.  proto – протокол (udp/tcp)
8.  trans_id – ID DNS-транзакции
9.  query – домен или NetBIOS-имя
10. qclass – числовой код класса (1 — IN)
11. qclass_name – имя класса (обычно C_INTERNET)
12. qtype – числовой код типа запроса (32 = NB)
13. qtype_name – человекопонятное имя (NB, AAAA, A, MX и т. д.)
14. rcode – код ответа (–, если нет)
15. rcode_name – имя кода ответа (–, если нет)
16. AA – authoritative answer (T/F)
17. TC – truncated (T/F)
18. RD – recursion desired (T/F)
19. RA – recursion available (T/F)
20. Z – зарезервировано
21. answers – список ответов (или –)
22. TTLs – список TTL-ов (или –)
23. rejected – флаг отклонения (T/F)

Преобразуем данные в столбцах в нужный формат:

``` r
dns_data$ts <- as.POSIXct(as.numeric(dns_data$ts), origin="1970-01-01", tz="UTC")
dns_data$id.orig_p <- as.integer(dns_data$id.orig_p)
dns_data$id.resp_p <- as.integer(dns_data$id.resp_p)
dns_data$AA <- dns_data$AA == "T"
dns_data$TC <- dns_data$TC == "T"
dns_data$RD <- dns_data$RD == "T"
dns_data$RA <- dns_data$RA == "T"
dns_data$rejected <- dns_data$rejected == "T"
```

Посмотрим общую структуру данных:

``` r
glimpse(dns_data)
```

    Rows: 427,935
    Columns: 23
    $ ts          <dttm> 2012-03-16 12:30:05, 2012-03-16 12:30:15, 2012-03-16 12:3…
    $ uid         <chr> "CWGtK431H9XuaTN4fi", "C36a282Jljz7BsbGH", "C36a282Jljz7Bs…
    $ id.orig_h   <chr> "192.168.202.100", "192.168.202.76", "192.168.202.76", "19…
    $ id.orig_p   <int> 45658, 137, 137, 137, 137, 137, 137, 137, 137, 137, 137, 1…
    $ id.resp_h   <chr> "192.168.27.203", "192.168.202.255", "192.168.202.255", "1…
    $ id.resp_p   <int> 137, 137, 137, 137, 137, 137, 137, 137, 137, 137, 137, 137…
    $ proto       <chr> "udp", "udp", "udp", "udp", "udp", "udp", "udp", "udp", "u…
    $ trans_id    <int> 33008, 57402, 57402, 57402, 57398, 57398, 57398, 62187, 62…
    $ query       <chr> "*\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\…
    $ qclass      <chr> "1", "1", "1", "1", "1", "1", "1", "1", "1", "1", "1", "1"…
    $ qclass_name <chr> "C_INTERNET", "C_INTERNET", "C_INTERNET", "C_INTERNET", "C…
    $ qtype       <chr> "33", "32", "32", "32", "32", "32", "32", "32", "32", "32"…
    $ qtype_name  <chr> "SRV", "NB", "NB", "NB", "NB", "NB", "NB", "NB", "NB", "NB…
    $ rcode       <chr> "0", "-", "-", "-", "-", "-", "-", "-", "-", "-", "-", "-"…
    $ rcode_name  <chr> "NOERROR", "-", "-", "-", "-", "-", "-", "-", "-", "-", "-…
    $ AA          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FA…
    $ TC          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FA…
    $ RD          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FA…
    $ RA          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FA…
    $ Z           <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 1, 1, 1, 1, 0…
    $ answers     <chr> "-", "-", "-", "-", "-", "-", "-", "-", "-", "-", "-", "-"…
    $ TTLs        <chr> "-", "-", "-", "-", "-", "-", "-", "-", "-", "-", "-", "-"…
    $ rejected    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FA…

### Шаг 2 - Анализ

#### Сколько участников информационного обмена в сети Доброй Организации?

Для этого сложим отправителей и получателей в вектор и найдем количество
уникальных элементов (участников):

``` r
participants <- unique(c(dns_data$id.orig_h, dns_data$id.resp_h))
length(participants)
```

    [1] 1359

#### 5. Какое соотношение участников обмена внутри сети и участников обращений к внешним ресурсам?

Как всем известно ip-адреса можно разделить на серые и белые
(публичные), список диапазонов сервых адресов:

-   192.168.0.0/16
-   10.0.0.0/8,
-   172.16.0.0/12

Так вот, если обращение было к адресу из диапазона серых адресов, то
значит от обращался к внутренней сети, если же наоборот, то к внешней.

Не будем изобретать велосипед, а воспользуемся пакетом `ipaddress` с
нужным нам функционалом, но для начала подключим его:

``` r
library(ipaddress)
```

Нам потребуется единственная функция `is_private`, которая позволи
понять, является ли адрес внутренним:

``` r
ip_addresses <- ip_address(dns_data %>% distinct(id.resp_h) %>% pull(id.resp_h))
internal <- sum(is_private(ip_addresses), na.rm = TRUE)
external <- sum(!is_private(ip_addresses), na.rm = TRUE)
internal / (internal + external)
```

    [1] 0.9658537

#### Найдите топ-10 участников сети, проявляющих наибольшую сетевую активность.

``` r
library(dplyr)
top10_participants <- bind_rows(
  dns_data %>%
    select(ip = id.orig_h),
  dns_data %>%
    select(ip = id.resp_h)
) %>%
  filter(!is.na(ip), ip != "-") %>%
  count(ip, name = "events") %>%
  arrange(desc(events)) %>%
  slice_head(n = 10)

top10_participants
```

                    ip events
    1    192.168.207.4 266627
    2    10.10.117.210  75943
    3  192.168.202.255  68720
    4   192.168.202.93  26522
    5     172.19.1.100  25481
    6  192.168.202.103  18121
    7   192.168.202.76  16978
    8   192.168.202.97  16176
    9  192.168.202.141  14976
    10 192.168.202.110  14493

#### Найдите топ-10 доменов, к которым обращаются пользователи сети и соответственное количество обращений

``` r
top10_domains <- dns_data %>%
  filter(!is.na(query), query != "-", query != "") %>%
  count(query, name = "hits") %>%
  arrange(desc(hits)) %>%
  slice_head(n = 10)

top10_domains
```

                                                                         query
    1                                                teredo.ipv6.microsoft.com
    2                                                         tools.google.com
    3                                                            www.apple.com
    4                                                           time.apple.com
    5                                          safebrowsing.clients.google.com
    6  *\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00
    7                                                                     WPAD
    8                                              44.206.168.192.in-addr.arpa
    9                                                                 HPE8AA67
    10                                                                  ISATAP
        hits
    1  39273
    2  14057
    3  13390
    4  13109
    5  11658
    6  10401
    7   9134
    8   7248
    9   6929
    10  6569

#### Опеределите базовые статистические характеристики (функция summary() ) интервала времени между последовательными обращениями к топ-10 доменам.

Сейчас мы берём каждый из топ-10 доменов, вычисляем разницу между
соседними обращениями (diff(ts)), и запускаем summary:

``` r
top10_queries <- top10_domains$query

dns_intervals <- dns_data %>%
  filter(query %in% top10_queries) %>%
  arrange(query, ts) %>%          # сортировка по домену и времени
  group_by(query) %>%
  mutate(
    interval_sec = as.numeric(ts - lag(ts), units = "secs")
  ) %>%
  ungroup()
```

``` r
interval_summary_raw <- dns_intervals %>%
  group_by(query) %>%
  summarise(
    summary_stats = list(summary(interval_sec))
  )

interval_summary_raw$summary_stats[[1]]
```

        Min.  1st Qu.   Median     Mean  3rd Qu.     Max.     NA's 
        0.00     0.15     0.50    11.24     1.50 52723.50        1 

``` r
interval_summary_raw$query[1]
```

    [1] "*\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00"

#### Поиск возможного скрытого DNS-канала

``` r
periodic_suspects <- dns_data %>%
  filter(!is.na(query), query != "-") %>%
  arrange(query, ts) %>%
  group_by(query) %>%
  mutate(
    interval = as.numeric(ts - lag(ts), units = "secs")
  ) %>%
  summarise(
    mean_interval = mean(interval, na.rm = TRUE),
    sd_interval   = sd(interval, na.rm = TRUE),
    n             = sum(!is.na(interval))
  ) %>%
  filter(n > 20, sd_interval < 1)

periodic_suspects
```

    # A tibble: 7 × 4
      query             mean_interval sd_interval     n
      <chr>                     <dbl>       <dbl> <int>
    1 hq.h                    0.00265     0.0515    377
    2 httphq.hec.net          0.00990     0.100     102
    3 input.mozilla.com       5.00        0.00475    31
    4 lifehacker.com          0.00612     0.0171     67
    5 www.ar15.com            0.00435     0.0147     23
    6 www.h                   0.0156      0.125      64
    7 www.hkparts.net         0.00371     0.0124     35

В ходе анализа интервалов между DNS-запросами были обнаружены 7 доменов,
обращения к которым происходят с высокой регулярностью (низкое
стандартное отклонение интервала). Такая периодичность характерна для
автоматических процессов и в ряде случаев — для скрытых каналов
управления вредоносного ПО.

Наиболее подозрительными выглядят домены `hq.h` и `httphq.hec.net`,
генерирующие сотни обращений с интервалами порядка миллисекунд. Домен
`input.mozilla.com` демонстрирует идеальную периодичность в 5 секунд,
что похоже на сбор телеметрии с браузера.

### Обогащение данных

#### Определить местоположение и организацию-провайдера для топ-10 доменов

``` r
library(httr)
library(jsonlite)

resolve_domain_info <- function(domain) {
  url <- paste0("http://ip-api.com/json/", domain)
  response <- httr::GET(url)
  data <- jsonlite::fromJSON(rawToChar(response$content))
  tibble(
    domain = domain,
    country = data$country,
    city = data$city,
    isp = data$isp,
    org = data$org,
    as = data$as
  )
}

enriched_domains <- purrr::map_df(top10_domains$query, resolve_domain_info)

enriched_domains
```

    # A tibble: 10 × 6
       domain                                        country city  isp   org   as   
       <chr>                                         <chr>   <chr> <chr> <chr> <chr>
     1 "teredo.ipv6.microsoft.com"                   <NA>    <NA>  <NA>  <NA>  <NA> 
     2 "tools.google.com"                            United… Los … Goog… Goog… AS15…
     3 "www.apple.com"                               United… El S… Akam… Akam… AS16…
     4 "time.apple.com"                              United… San … Appl… Appl… AS61…
     5 "safebrowsing.clients.google.com"             United… Wash… Goog… Goog… AS15…
     6 "*\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\… <NA>    <NA>  <NA>  <NA>  <NA> 
     7 "WPAD"                                        <NA>    <NA>  <NA>  <NA>  <NA> 
     8 "44.206.168.192.in-addr.arpa"                 <NA>    <NA>  <NA>  <NA>  <NA> 
     9 "HPE8AA67"                                    <NA>    <NA>  <NA>  <NA>  <NA> 
    10 "ISATAP"                                      <NA>    <NA>  <NA>  <NA>  <NA> 

## Вывод

В ходе практической работы я развил навыки работы с языком
программирования R, а также научился анализировать DNS логи
