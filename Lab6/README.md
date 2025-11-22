# Исследование информации о состоянии беспроводных сетей
alexkondrol@yandex.ru

## Цель работы

1.  Закрепить навыки исследования данных журнала Windows Active
    Directory
2.  Изучить структуру журнала системы Windows Active Directory
3.  Зекрепить практические навыки использования языка программирования R
    для обработки данных
4.  Закрепить знания основных функций обработки данных экосистемы
    `tidyverse` языка R

## Исходные данные

1.  Операционная система GNU/Linux
2.  Интерпретатор языка программирования R
3.  Редактор кода `Positron`
4.  Выгрузки данных журнала Windows Active Directory из системы SIEM

## Задание

Используя программный пакет `dplyr` языка программирования R провести
анализ журналов и ответить на вопросы.

## Ход работы

1.  Подготовка данных 1.1. Импортировать данные
    (https://storage.yandexcloud.net/iamcth-data/dataset.tar.gz) 1.2.
    Привести датасеты в вид “аккуратных данных”, преобразовать типы
    столбцов в соответствии с типом данных 1.3. Просмотрите общую
    структуру данных с помощью функции `glimpse()`
2.  Анализ 2.1. Раскройте датафрейм избавившись от вложенных
    датафреймов. Для обнаружения таких можно использовать функцию
    `dplyr::glimpse()` , а для раскрытия вложенности – `tidyr::unnest()`
    . Обратите внимание, что при раскрытии теряются внешние названия
    колонок – это можно предотвратить если использовать параметр
    `tidyr::unnest(..., names_sep = )` . 2.2. Минимизируйте количество
    колонок в датафрейме – уберите колоки с единственным значением
    параметра. 2.3. Какое количество хостов представлено в данном
    датасете? 2.4. Подготовьте датафрейм с расшифровкой Windows
    Event_ID, приведите типы данных к типу их значений. 2.5. Есть ли в
    логе события с высоким и средним уровнем значимости? Сколько их?
3.  Оформление отчета

### Шаг 1. Подготовка данных

Для начала импортируем необходимые пакеты:

Скачаем файл и сделаем из него датафрейм:

``` r
temp_dir <- tempdir()

tar_path <- file.path(temp_dir, "dataset.tar.gz")

download.file(
  url = "https://storage.yandexcloud.net/iamcth-data/dataset.tar.gz",
  destfile = tar_path,
  mode = "wb"
)

untar(
  tarfile = tar_path,
  exdir = temp_dir
)

json_path <- file.path(temp_dir, "caldera_attack_evals_round1_day1_2019-10-20201108.json")

ad_data_raw <- stream_in(file(json_path, open = "r"), verbose = FALSE)

glimpse(ad_data_raw)
```

    Rows: 101,904
    Columns: 9
    $ `@timestamp` <chr> "2019-10-20T20:11:06.937Z", "2019-10-20T20:11:07.101Z", "…
    $ `@metadata`  <df[,4]> <data.frame[26 x 4]>
    $ event        <df[,4]> <data.frame[26 x 4]>
    $ log          <df[,1]> <data.frame[26 x 1]>
    $ message      <chr> "A token right was adjusted.\n\nSubject:\n\tSecurity I…
    $ winlog       <df[,16]> <data.frame[26 x 16]>
    $ ecs          <df[,1]> <data.frame[26 x 1]>
    $ host         <df[,1]> <data.frame[26 x 1]>
    $ agent        <df[,5]> <data.frame[26 x 5]>

### Шаг 2. Анализ

#### Раскройте датафрейм избавившись от вложенных датафреймов. Для обнаружения таких можно использовать функцию dplyr::glimpse() , а для раскрытия вложенности – tidyr::unnest() . Обратите внимание, что при раскрытии теряются внешние названия колонок – это можно предотвратить если использовать параметр tidyr::unnest(…, names_sep = ).

Мы раскроем вложенные структуры в датафрейме, чтобы получить плоские,
удобные для анализа данные. Для этого последовательно проверяем, есть ли
внутри колонок списки или датафреймы, и с помощью unnest() разворачиваем
их в отдельные столбцы, сохраняя исходные названия через параметр
names_sep. В итоге получаем датафрейм без вложенностей, где каждая
переменная представлена отдельной колонкой.

``` r
ad_data <- ad_data_raw

is_nested_df <- function(x) is.data.frame(x) || is.list(x)

repeat {
  nested_cols <- names(ad_data)[sapply(ad_data, is_nested_df)]
  if (length(nested_cols) == 0) break
  for (col in nested_cols) {
    ad_data <- tidyr::unnest(
      ad_data,
      cols = dplyr::all_of(col),
      names_sep = paste0(col, "_"),
      keep_empty = TRUE
    )
  }
}

ad_data %>%
  select(
    `@timestamp`,
    winlogwinlog_computer_name,
    eventevent_code,
    eventevent_action,
    starts_with("winlogwinlog_event_data_")
  ) %>%
  glimpse()
```

    Rows: 101,904
    Columns: 4
    $ `@timestamp`               <chr> "2019-10-20T20:11:06.937Z", "2019-10-20T20:…
    $ winlogwinlog_computer_name <chr> "HR001.shire.com", "HFDC01.shire.com", "IT0…
    $ eventevent_code            <int> 4703, 4673, 10, 10, 10, 10, 11, 10, 10, 10,…
    $ eventevent_action          <chr> "Token Right Adjusted Events", "Sensitive P…

``` r
ncol(ad_data)
```

    [1] 300

#### 2. Минимизируйте количество колонок в датафрейме – уберите колоки с единственным значением параметра.

``` r
ad_data <- ad_data %>%
  select(where(~ n_distinct(.) > 1))

ncol(ad_data)
```

    [1] 287

#### 3. Какое количество хостов представлено в данном датасете?

``` r
ad_data %>% select(winlogwinlog_computer_name) %>% distinct() %>% count()
```

    # A tibble: 1 × 1
          n
      <int>
    1     5

#### 4. Подготовьте датафрейм с расшифровкой Windows Event_ID, приведите типы данных к типу их значений

``` r
win_events <- ad_data %>% select(eventevent_code, eventevent_action) %>% distinct() %>% rename(
    event_id     = eventevent_code,
    task         = eventevent_action
) %>% type_convert()

win_events
```

    # A tibble: 119 × 2
       event_id task                                       
          <int> <chr>                                      
     1     4703 Token Right Adjusted Events                
     2     4673 Sensitive Privilege Use                    
     3       10 Process accessed (rule: ProcessAccess)     
     4       11 File created (rule: FileCreate)            
     5        7 Image loaded (rule: ImageLoad)             
     6     4689 Process Termination                        
     7        5 Process terminated (rule: ProcessTerminate)
     8     5158 Filtering Platform Connection              
     9     5156 Filtering Platform Connection              
    10     4672 Special Logon                              
    # ℹ 109 more rows

#### 5. Есть ли в логе события с высоким и средним уровнем значимости? Сколько их?

Создадим датафрейм с критичностью событий уровня Medium и High:

``` r
event_severity <- tribble(
  ~event_id, ~severity,
  4618L, "High",
  4649L, "High",
  4719L, "High",
  4765L, "High",
  4766L, "High",
  4794L, "High",
  4897L, "High",
  4964L, "High",
  5124L, "High",
  1102L, "Medium to High",
  4621L, "Medium",
  4675L, "Medium",
  4692L, "Medium",
  4693L, "Medium",
  4706L, "Medium",
  4713L, "Medium",
  4714L, "Medium",
  4715L, "Medium",
  4716L, "Medium",
  4724L, "Medium",
  4727L, "Medium",
  4735L, "Medium",
  4737L, "Medium",
  4739L, "Medium",
  4754L, "Medium",
  4755L, "Medium",
  4764L, "Medium",
  4780L, "Medium",
  4816L, "Medium",
  4865L, "Medium",
  4866L, "Medium",
  4867L, "Medium",
  4868L, "Medium",
  4870L, "Medium",
  4882L, "Medium",
  4885L, "Medium",
  4890L, "Medium",
  4892L, "Medium",
  4896L, "Medium",
  4906L, "Medium",
  4907L, "Medium",
  4908L, "Medium",
  4912L, "Medium",
  4960L, "Medium",
  4961L, "Medium",
  4962L, "Medium",
  4963L, "Medium",
  4965L, "Medium",
  4976L, "Medium",
  4977L, "Medium",
  4978L, "Medium",
  4983L, "Medium",
  4984L, "Medium",
  5027L, "Medium",
  5028L, "Medium",
  5029L, "Medium",
  5030L, "Medium",
  5035L, "Medium",
  5037L, "Medium",
  5038L, "Medium",
  5120L, "Medium",
  5121L, "Medium",
  5122L, "Medium",
  5123L, "Medium",
  5376L, "Medium",
  5377L, "Medium",
  5453L, "Medium",
  5480L, "Medium",
  5483L, "Medium",
  5484L, "Medium",
  5485L, "Medium",
  5827L, "Medium",
  5828L, "Medium",
  6145L, "Medium",
  6273L, "Medium",
  6274L, "Medium",
  6275L, "Medium",
  6276L, "Medium",
  6277L, "Medium",
  6278L, "Medium",
  6279L, "Medium",
  6280L, "Medium",
  24586L, "Medium",
  24592L, "Medium",
  24593L, "Medium",
  24594L, "Medium"
) %>% arrange(event_id)

event_severity
```

    # A tibble: 86 × 2
       event_id severity      
          <int> <chr>         
     1     1102 Medium to High
     2     4618 High          
     3     4621 Medium        
     4     4649 High          
     5     4675 Medium        
     6     4692 Medium        
     7     4693 Medium        
     8     4706 Medium        
     9     4713 Medium        
    10     4714 Medium        
    # ℹ 76 more rows

Затем соединим критичность с event_id из наших логов, если совпадения
нет, запишем Low:

``` r
win_events_with_severity <- win_events %>% left_join(event_severity, by = "event_id") %>%
  mutate(severity = coalesce(severity, "Low"))  
```

И оставим события с критичностью не равной Low:

``` r
not_low_severity <- win_events_with_severity %>% filter(severity != 'Low')
not_low_severity %>% count()
```

    # A tibble: 1 × 1
          n
      <int>
    1     0

Как оказалось событий с критичностью Medium/High в наших событиях нет

## Вывод

В ходе практической работы я развил навыки работы с языком
программирования R, а также научился исследовать вредоносную активность
в домене Windows
