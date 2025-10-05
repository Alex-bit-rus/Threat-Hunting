# Основы обработки данных с помощью R и Dplyr

alexkondrol\@yandex.ru

# Обработка данных с помощью R и dply

## Цель

1.  Развить практические навыки использования языка программирования R для обработки данных
2.  Закрепить знания базовых типов данных языка R
3.  Развить практические навыки использования функций обработки данных пакета `dplyr` – функции `select()`, `filter()`, `mutate()`, `arrange()`, `group_by()`

## Исходные данные

1.  Операционная система GNU/Linux, дистрибутив ALT
2.  Редактор кода Positron
3.  Интерпретатор R
4.  Пакет `dplyr` с встроенными функциями и набором данных `starwars`

## План

Проанализировать встроенный в пакет `dplyr` набор данных `starwars` с помощью языка R и ответить на вопросы.

## Описание шагов

### 1. Подключение пакета `dplyr`

Импортируем пакет `dplyr`, чтобы использовать набор данных `starwars` из него не обращаясь к нему каждый раз

``` r
library("dplyr")
```

### 2. Базовое ознакомление с набором данных `starwars`

Для выведем датафрейм `starwars`:

``` r
starwars
```

<img src="img/1.png" width="671"/>

Как видно из изображение выше, в датафрейме `starwars` перечислены персонажи из франшизы “Звездные войны”, а также сведения о них.

### Ответы на вопросы

#### 1. Сколько строк в датафрейме?

``` r
starwars %>% nrow()
```

**Вывод:** 87

#### 2. Сколько столбцов в датафрейме?

``` r
starwars %>% ncol()
```

**Вывод:** 14

#### 3. Как просмотреть примерный вид датафрейма?

``` r
starwars %>% glimpse()
```

**Вывод:**

<img src="img/2.png" width="618"/>

#### 4. Сколько уникальных рас персонажей (species) представлено в данных?

``` r
starwars["species"] %>% unique() %>% nrow() 
```

**Вывод:** 38

#### 5. Найти самого высокого персонажа

``` r
starwars %>% filter(height == max(height, na.rm = TRUE)) %>% 
    select(name, height, species)
```

**Вывод:**

+-------------+--------+----------+
| name        | height | species  |
+:============+=======:+:=========+
| Yarael Poof | 264    | Quermian |
+-------------+--------+----------+

#### 6. Найти всех персонажей ниже 170

``` r
starwars %>% filter(height < 170) %>% select(name, height)
```

**Вывод:**

+-----------------------+--------+
| name                  | height |
+:======================+=======:+
| C-3PO                 | 167    |
+-----------------------+--------+
| R2-D2                 | 96     |
+-----------------------+--------+
| Leia Organa           | 150    |
+-----------------------+--------+
| Beru Whitesun Lars    | 165    |
+-----------------------+--------+
| R5-D4                 | 97     |
+-----------------------+--------+
| Yoda                  | 66     |
+-----------------------+--------+
| Mon Mothma            | 150    |
+-----------------------+--------+
| Wicket Systri Warrick | 88     |
+-----------------------+--------+
| Nien Nunb             | 160    |
+-----------------------+--------+
| Watto                 | 137    |
+-----------------------+--------+
| Sebulba               | 112    |
+-----------------------+--------+
| Shmi Skywalker        | 163    |
+-----------------------+--------+
| Ratts Tyerel          | 79     |
+-----------------------+--------+
| Dud Bolt              | 94     |
+-----------------------+--------+
| Gasgano               | 122    |
+-----------------------+--------+
| Ben Quadinaros        | 163    |
+-----------------------+--------+
| Cordé                 | 157    |
+-----------------------+--------+
| Barriss Offee         | 166    |
+-----------------------+--------+
| Dormé                 | 165    |
+-----------------------+--------+
| Zam Wesell            | 168    |
+-----------------------+--------+
| Jocasta Nu            | 167    |
+-----------------------+--------+
| R4-P17                | 96     |
+-----------------------+--------+

#### 7. Подсчитать ИМТ (индекс массы тела) для всех персонажей. ИМТ подсчитать по формуле

``` r
starwars <- starwars %>% mutate(IMT = mass / ( (height / 100) ^ 2 ))
starwars 
```

**Вывод:**

<table style="width:94%;">
<colgroup>
<col style="width: 6%" />
<col style="width: 6%" />
<col style="width: 6%" />
<col style="width: 6%" />
<col style="width: 6%" />
<col style="width: 6%" />
<col style="width: 6%" />
<col style="width: 6%" />
<col style="width: 6%" />
<col style="width: 6%" />
<col style="width: 6%" />
<col style="width: 6%" />
<col style="width: 6%" />
<col style="width: 6%" />
<col style="width: 6%" />
</colgroup>
<thead>
<tr>
<th style="text-align: left;"><p>name</p></th>
<th style="text-align: right;"><p>height</p></th>
<th style="text-align: right;"><p>mass</p></th>
<th style="text-align: left;"><p>hair_color</p></th>
<th style="text-align: left;"><p>skin_color</p></th>
<th style="text-align: left;"><p>eye_color</p></th>
<th style="text-align: right;"><p>birth_year</p></th>
<th style="text-align: left;"><p>sex</p></th>
<th style="text-align: left;"><p>gender</p></th>
<th style="text-align: left;"><p>homeworld</p></th>
<th style="text-align: left;"><p>species</p></th>
<th style="text-align: left;"><p>films</p></th>
<th style="text-align: left;"><p>vehicles</p></th>
<th style="text-align: left;"><p>starships</p></th>
<th style="text-align: right;"><p>IMT</p></th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align: left;"><p>Luke Skywalker</p></td>
<td style="text-align: right;"><p>172</p></td>
<td style="text-align: right;"><p>77.0</p></td>
<td style="text-align: left;"><p>blond</p></td>
<td style="text-align: left;"><p>fair</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>19.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Tatooine</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>A New Hope , The Empire Strikes Back, Return of the Jedi , Revenge of the Sith , The Force Awakens</p></td>
<td style="text-align: left;"><p>Snowspeeder , Imperial Speeder Bike</p></td>
<td style="text-align: left;"><p>X-wing , Imperial shuttle</p></td>
<td style="text-align: right;"><p>26.02758</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>C-3PO</p></td>
<td style="text-align: right;"><p>167</p></td>
<td style="text-align: right;"><p>75.0</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>gold</p></td>
<td style="text-align: left;"><p>yellow</p></td>
<td style="text-align: right;"><p>112.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Tatooine</p></td>
<td style="text-align: left;"><p>Droid</p></td>
<td style="text-align: left;"><p>A New Hope , The Empire Strikes Back, Return of the Jedi , The Phantom Menace , Attack of the Clones , Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>26.89232</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>R2-D2</p></td>
<td style="text-align: right;"><p>96</p></td>
<td style="text-align: right;"><p>32.0</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>white, blue</p></td>
<td style="text-align: left;"><p>red</p></td>
<td style="text-align: right;"><p>33.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Naboo</p></td>
<td style="text-align: left;"><p>Droid</p></td>
<td style="text-align: left;"><p>A New Hope , The Empire Strikes Back, Return of the Jedi , The Phantom Menace , Attack of the Clones , Revenge of the Sith , The Force Awakens</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>34.72222</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Darth Vader</p></td>
<td style="text-align: right;"><p>202</p></td>
<td style="text-align: right;"><p>136.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>white</p></td>
<td style="text-align: left;"><p>yellow</p></td>
<td style="text-align: right;"><p>41.9</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Tatooine</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>A New Hope , The Empire Strikes Back, Return of the Jedi , Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"><p>TIE Advanced x1</p></td>
<td style="text-align: right;"><p>33.33007</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Leia Organa</p></td>
<td style="text-align: right;"><p>150</p></td>
<td style="text-align: right;"><p>49.0</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>light</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>19.0</p></td>
<td style="text-align: left;"><p>female</p></td>
<td style="text-align: left;"><p>feminine</p></td>
<td style="text-align: left;"><p>Alderaan</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>A New Hope , The Empire Strikes Back, Return of the Jedi , Revenge of the Sith , The Force Awakens</p></td>
<td style="text-align: left;"><p>Imperial Speeder Bike</p></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>21.77778</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Owen Lars</p></td>
<td style="text-align: right;"><p>178</p></td>
<td style="text-align: right;"><p>120.0</p></td>
<td style="text-align: left;"><p>brown, grey</p></td>
<td style="text-align: left;"><p>light</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>52.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Tatooine</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>A New Hope , Attack of the Clones, Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>37.87401</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Beru Whitesun Lars</p></td>
<td style="text-align: right;"><p>165</p></td>
<td style="text-align: right;"><p>75.0</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>light</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>47.0</p></td>
<td style="text-align: left;"><p>female</p></td>
<td style="text-align: left;"><p>feminine</p></td>
<td style="text-align: left;"><p>Tatooine</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>A New Hope , Attack of the Clones, Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>27.54821</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>R5-D4</p></td>
<td style="text-align: right;"><p>97</p></td>
<td style="text-align: right;"><p>32.0</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>white, red</p></td>
<td style="text-align: left;"><p>red</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Tatooine</p></td>
<td style="text-align: left;"><p>Droid</p></td>
<td style="text-align: left;"><p>A New Hope</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>34.00999</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Biggs Darklighter</p></td>
<td style="text-align: right;"><p>183</p></td>
<td style="text-align: right;"><p>84.0</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: left;"><p>light</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>24.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Tatooine</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>A New Hope</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"><p>X-wing</p></td>
<td style="text-align: right;"><p>25.08286</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Obi-Wan Kenobi</p></td>
<td style="text-align: right;"><p>182</p></td>
<td style="text-align: right;"><p>77.0</p></td>
<td style="text-align: left;"><p>auburn, white</p></td>
<td style="text-align: left;"><p>fair</p></td>
<td style="text-align: left;"><p>blue-gray</p></td>
<td style="text-align: right;"><p>57.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Stewjon</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>A New Hope , The Empire Strikes Back, Return of the Jedi , The Phantom Menace , Attack of the Clones , Revenge of the Sith</p></td>
<td style="text-align: left;"><p>Tribubble bongo</p></td>
<td style="text-align: left;"><p>Jedi starfighter , Trade Federation cruiser, Naboo star skiff , Jedi Interceptor , Belbullab-22 starfighter</p></td>
<td style="text-align: right;"><p>23.24598</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Anakin Skywalker</p></td>
<td style="text-align: right;"><p>188</p></td>
<td style="text-align: right;"><p>84.0</p></td>
<td style="text-align: left;"><p>blond</p></td>
<td style="text-align: left;"><p>fair</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>41.9</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Tatooine</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>The Phantom Menace , Attack of the Clones, Revenge of the Sith</p></td>
<td style="text-align: left;"><p>Zephyr-G swoop bike, XJ-6 airspeeder</p></td>
<td style="text-align: left;"><p>Naboo fighter , Trade Federation cruiser, Jedi Interceptor</p></td>
<td style="text-align: right;"><p>23.76641</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Wilhuff Tarkin</p></td>
<td style="text-align: right;"><p>180</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>auburn, grey</p></td>
<td style="text-align: left;"><p>fair</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>64.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Eriadu</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>A New Hope , Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Chewbacca</p></td>
<td style="text-align: right;"><p>228</p></td>
<td style="text-align: right;"><p>112.0</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>unknown</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>200.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Kashyyyk</p></td>
<td style="text-align: left;"><p>Wookiee</p></td>
<td style="text-align: left;"><p>A New Hope , The Empire Strikes Back, Return of the Jedi , Revenge of the Sith , The Force Awakens</p></td>
<td style="text-align: left;"><p>AT-ST</p></td>
<td style="text-align: left;"><p>Millennium Falcon, Imperial shuttle</p></td>
<td style="text-align: right;"><p>21.54509</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Han Solo</p></td>
<td style="text-align: right;"><p>180</p></td>
<td style="text-align: right;"><p>80.0</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>fair</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>29.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Corellia</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>A New Hope , The Empire Strikes Back, Return of the Jedi , The Force Awakens</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"><p>Millennium Falcon, Imperial shuttle</p></td>
<td style="text-align: right;"><p>24.69136</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Greedo</p></td>
<td style="text-align: right;"><p>173</p></td>
<td style="text-align: right;"><p>74.0</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>green</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: right;"><p>44.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Rodia</p></td>
<td style="text-align: left;"><p>Rodian</p></td>
<td style="text-align: left;"><p>A New Hope</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>24.72518</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Jabba Desilijic Tiure</p></td>
<td style="text-align: right;"><p>175</p></td>
<td style="text-align: right;"><p>1358.0</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>green-tan, brown</p></td>
<td style="text-align: left;"><p>orange</p></td>
<td style="text-align: right;"><p>600.0</p></td>
<td style="text-align: left;"><p>hermaphroditic</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Nal Hutta</p></td>
<td style="text-align: left;"><p>Hutt</p></td>
<td style="text-align: left;"><p>A New Hope , Return of the Jedi, The Phantom Menace</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>443.42857</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Wedge Antilles</p></td>
<td style="text-align: right;"><p>170</p></td>
<td style="text-align: right;"><p>77.0</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>fair</p></td>
<td style="text-align: left;"><p>hazel</p></td>
<td style="text-align: right;"><p>21.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Corellia</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>A New Hope , The Empire Strikes Back, Return of the Jedi</p></td>
<td style="text-align: left;"><p>Snowspeeder</p></td>
<td style="text-align: left;"><p>X-wing</p></td>
<td style="text-align: right;"><p>26.64360</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Jek Tono Porkins</p></td>
<td style="text-align: right;"><p>180</p></td>
<td style="text-align: right;"><p>110.0</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>fair</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>Bestine IV</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>A New Hope</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"><p>X-wing</p></td>
<td style="text-align: right;"><p>33.95062</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Yoda</p></td>
<td style="text-align: right;"><p>66</p></td>
<td style="text-align: right;"><p>17.0</p></td>
<td style="text-align: left;"><p>white</p></td>
<td style="text-align: left;"><p>green</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>896.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>Yoda’s species</p></td>
<td style="text-align: left;"><p>The Empire Strikes Back, Return of the Jedi , The Phantom Menace , Attack of the Clones , Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>39.02663</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Palpatine</p></td>
<td style="text-align: right;"><p>170</p></td>
<td style="text-align: right;"><p>75.0</p></td>
<td style="text-align: left;"><p>grey</p></td>
<td style="text-align: left;"><p>pale</p></td>
<td style="text-align: left;"><p>yellow</p></td>
<td style="text-align: right;"><p>82.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Naboo</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>The Empire Strikes Back, Return of the Jedi , The Phantom Menace , Attack of the Clones , Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>25.95156</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Boba Fett</p></td>
<td style="text-align: right;"><p>183</p></td>
<td style="text-align: right;"><p>78.2</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: left;"><p>fair</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>31.5</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Kamino</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>The Empire Strikes Back, Return of the Jedi , Attack of the Clones</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"><p>Slave 1</p></td>
<td style="text-align: right;"><p>23.35095</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>IG-88</p></td>
<td style="text-align: right;"><p>200</p></td>
<td style="text-align: right;"><p>140.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>metal</p></td>
<td style="text-align: left;"><p>red</p></td>
<td style="text-align: right;"><p>15.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>Droid</p></td>
<td style="text-align: left;"><p>The Empire Strikes Back</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>35.00000</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Bossk</p></td>
<td style="text-align: right;"><p>190</p></td>
<td style="text-align: right;"><p>113.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>green</p></td>
<td style="text-align: left;"><p>red</p></td>
<td style="text-align: right;"><p>53.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Trandosha</p></td>
<td style="text-align: left;"><p>Trandoshan</p></td>
<td style="text-align: left;"><p>The Empire Strikes Back</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>31.30194</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Lando Calrissian</p></td>
<td style="text-align: right;"><p>177</p></td>
<td style="text-align: right;"><p>79.0</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: left;"><p>dark</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>31.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Socorro</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>The Empire Strikes Back, Return of the Jedi</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"><p>Millennium Falcon</p></td>
<td style="text-align: right;"><p>25.21625</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Lobot</p></td>
<td style="text-align: right;"><p>175</p></td>
<td style="text-align: right;"><p>79.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>light</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>37.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Bespin</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>The Empire Strikes Back</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>25.79592</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Ackbar</p></td>
<td style="text-align: right;"><p>180</p></td>
<td style="text-align: right;"><p>83.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>brown mottle</p></td>
<td style="text-align: left;"><p>orange</p></td>
<td style="text-align: right;"><p>41.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Mon Cala</p></td>
<td style="text-align: left;"><p>Mon Calamari</p></td>
<td style="text-align: left;"><p>Return of the Jedi, The Force Awakens</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>25.61728</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Mon Mothma</p></td>
<td style="text-align: right;"><p>150</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>auburn</p></td>
<td style="text-align: left;"><p>fair</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>48.0</p></td>
<td style="text-align: left;"><p>female</p></td>
<td style="text-align: left;"><p>feminine</p></td>
<td style="text-align: left;"><p>Chandrila</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>Return of the Jedi</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Arvel Crynyd</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>fair</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>Return of the Jedi</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"><p>A-wing</p></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Wicket Systri Warrick</p></td>
<td style="text-align: right;"><p>88</p></td>
<td style="text-align: right;"><p>20.0</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>8.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Endor</p></td>
<td style="text-align: left;"><p>Ewok</p></td>
<td style="text-align: left;"><p>Return of the Jedi</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>25.82645</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Nien Nunb</p></td>
<td style="text-align: right;"><p>160</p></td>
<td style="text-align: right;"><p>68.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>grey</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Sullust</p></td>
<td style="text-align: left;"><p>Sullustan</p></td>
<td style="text-align: left;"><p>Return of the Jedi</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"><p>Millennium Falcon</p></td>
<td style="text-align: right;"><p>26.56250</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Qui-Gon Jinn</p></td>
<td style="text-align: right;"><p>193</p></td>
<td style="text-align: right;"><p>89.0</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>fair</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>92.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>The Phantom Menace</p></td>
<td style="text-align: left;"><p>Tribubble bongo</p></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>23.89326</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Nute Gunray</p></td>
<td style="text-align: right;"><p>191</p></td>
<td style="text-align: right;"><p>90.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>mottled green</p></td>
<td style="text-align: left;"><p>red</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Cato Neimoidia</p></td>
<td style="text-align: left;"><p>Neimodian</p></td>
<td style="text-align: left;"><p>The Phantom Menace , Attack of the Clones, Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>24.67038</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Finis Valorum</p></td>
<td style="text-align: right;"><p>170</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>blond</p></td>
<td style="text-align: left;"><p>fair</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>91.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Coruscant</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>The Phantom Menace</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Padmé Amidala</p></td>
<td style="text-align: right;"><p>185</p></td>
<td style="text-align: right;"><p>45.0</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>light</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>46.0</p></td>
<td style="text-align: left;"><p>female</p></td>
<td style="text-align: left;"><p>feminine</p></td>
<td style="text-align: left;"><p>Naboo</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>The Phantom Menace , Attack of the Clones, Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"><p>Naboo fighter , H-type Nubian yacht, Naboo star skiff</p></td>
<td style="text-align: right;"><p>13.14828</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Jar Jar Binks</p></td>
<td style="text-align: right;"><p>196</p></td>
<td style="text-align: right;"><p>66.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>orange</p></td>
<td style="text-align: left;"><p>orange</p></td>
<td style="text-align: right;"><p>52.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Naboo</p></td>
<td style="text-align: left;"><p>Gungan</p></td>
<td style="text-align: left;"><p>The Phantom Menace , Attack of the Clones</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>17.18034</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Roos Tarpals</p></td>
<td style="text-align: right;"><p>224</p></td>
<td style="text-align: right;"><p>82.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>grey</p></td>
<td style="text-align: left;"><p>orange</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Naboo</p></td>
<td style="text-align: left;"><p>Gungan</p></td>
<td style="text-align: left;"><p>The Phantom Menace</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>16.34247</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Rugor Nass</p></td>
<td style="text-align: right;"><p>206</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>green</p></td>
<td style="text-align: left;"><p>orange</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Naboo</p></td>
<td style="text-align: left;"><p>Gungan</p></td>
<td style="text-align: left;"><p>The Phantom Menace</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Ric Olié</p></td>
<td style="text-align: right;"><p>183</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>fair</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Naboo</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>The Phantom Menace</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"><p>Naboo Royal Starship</p></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Watto</p></td>
<td style="text-align: right;"><p>137</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: left;"><p>blue, grey</p></td>
<td style="text-align: left;"><p>yellow</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Toydaria</p></td>
<td style="text-align: left;"><p>Toydarian</p></td>
<td style="text-align: left;"><p>The Phantom Menace , Attack of the Clones</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Sebulba</p></td>
<td style="text-align: right;"><p>112</p></td>
<td style="text-align: right;"><p>40.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>grey, red</p></td>
<td style="text-align: left;"><p>orange</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Malastare</p></td>
<td style="text-align: left;"><p>Dug</p></td>
<td style="text-align: left;"><p>The Phantom Menace</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>31.88776</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Quarsh Panaka</p></td>
<td style="text-align: right;"><p>183</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: left;"><p>dark</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>62.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Naboo</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>The Phantom Menace</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Shmi Skywalker</p></td>
<td style="text-align: right;"><p>163</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: left;"><p>fair</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>72.0</p></td>
<td style="text-align: left;"><p>female</p></td>
<td style="text-align: left;"><p>feminine</p></td>
<td style="text-align: left;"><p>Tatooine</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>The Phantom Menace , Attack of the Clones</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Darth Maul</p></td>
<td style="text-align: right;"><p>175</p></td>
<td style="text-align: right;"><p>80.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>red</p></td>
<td style="text-align: left;"><p>yellow</p></td>
<td style="text-align: right;"><p>54.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Dathomir</p></td>
<td style="text-align: left;"><p>Zabrak</p></td>
<td style="text-align: left;"><p>The Phantom Menace</p></td>
<td style="text-align: left;"><p>Sith speeder</p></td>
<td style="text-align: left;"><p>Scimitar</p></td>
<td style="text-align: right;"><p>26.12245</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Bib Fortuna</p></td>
<td style="text-align: right;"><p>180</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>pale</p></td>
<td style="text-align: left;"><p>pink</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Ryloth</p></td>
<td style="text-align: left;"><p>Twi’lek</p></td>
<td style="text-align: left;"><p>Return of the Jedi</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Ayla Secura</p></td>
<td style="text-align: right;"><p>178</p></td>
<td style="text-align: right;"><p>55.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: left;"><p>hazel</p></td>
<td style="text-align: right;"><p>48.0</p></td>
<td style="text-align: left;"><p>female</p></td>
<td style="text-align: left;"><p>feminine</p></td>
<td style="text-align: left;"><p>Ryloth</p></td>
<td style="text-align: left;"><p>Twi’lek</p></td>
<td style="text-align: left;"><p>The Phantom Menace , Attack of the Clones, Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>17.35892</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Ratts Tyerel</p></td>
<td style="text-align: right;"><p>79</p></td>
<td style="text-align: right;"><p>15.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>grey, blue</p></td>
<td style="text-align: left;"><p>unknown</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Aleen Minor</p></td>
<td style="text-align: left;"><p>Aleena</p></td>
<td style="text-align: left;"><p>The Phantom Menace</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>24.03461</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Dud Bolt</p></td>
<td style="text-align: right;"><p>94</p></td>
<td style="text-align: right;"><p>45.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>blue, grey</p></td>
<td style="text-align: left;"><p>yellow</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Vulpter</p></td>
<td style="text-align: left;"><p>Vulptereen</p></td>
<td style="text-align: left;"><p>The Phantom Menace</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>50.92802</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Gasgano</p></td>
<td style="text-align: right;"><p>122</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>white, blue</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Troiken</p></td>
<td style="text-align: left;"><p>Xexto</p></td>
<td style="text-align: left;"><p>The Phantom Menace</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Ben Quadinaros</p></td>
<td style="text-align: right;"><p>163</p></td>
<td style="text-align: right;"><p>65.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>grey, green, yellow</p></td>
<td style="text-align: left;"><p>orange</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Tund</p></td>
<td style="text-align: left;"><p>Toong</p></td>
<td style="text-align: left;"><p>The Phantom Menace</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>24.46460</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Mace Windu</p></td>
<td style="text-align: right;"><p>188</p></td>
<td style="text-align: right;"><p>84.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>dark</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>72.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Haruun Kal</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>The Phantom Menace , Attack of the Clones, Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>23.76641</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Ki-Adi-Mundi</p></td>
<td style="text-align: right;"><p>198</p></td>
<td style="text-align: right;"><p>82.0</p></td>
<td style="text-align: left;"><p>white</p></td>
<td style="text-align: left;"><p>pale</p></td>
<td style="text-align: left;"><p>yellow</p></td>
<td style="text-align: right;"><p>92.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Cerea</p></td>
<td style="text-align: left;"><p>Cerean</p></td>
<td style="text-align: left;"><p>The Phantom Menace , Attack of the Clones, Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>20.91623</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Kit Fisto</p></td>
<td style="text-align: right;"><p>196</p></td>
<td style="text-align: right;"><p>87.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>green</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Glee Anselm</p></td>
<td style="text-align: left;"><p>Nautolan</p></td>
<td style="text-align: left;"><p>The Phantom Menace , Attack of the Clones, Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>22.64681</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Eeth Koth</p></td>
<td style="text-align: right;"><p>171</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Iridonia</p></td>
<td style="text-align: left;"><p>Zabrak</p></td>
<td style="text-align: left;"><p>The Phantom Menace , Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Adi Gallia</p></td>
<td style="text-align: right;"><p>184</p></td>
<td style="text-align: right;"><p>50.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>dark</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>female</p></td>
<td style="text-align: left;"><p>feminine</p></td>
<td style="text-align: left;"><p>Coruscant</p></td>
<td style="text-align: left;"><p>Tholothian</p></td>
<td style="text-align: left;"><p>The Phantom Menace , Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>14.76843</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Saesee Tiin</p></td>
<td style="text-align: right;"><p>188</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>pale</p></td>
<td style="text-align: left;"><p>orange</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Iktotch</p></td>
<td style="text-align: left;"><p>Iktotchi</p></td>
<td style="text-align: left;"><p>The Phantom Menace , Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Yarael Poof</p></td>
<td style="text-align: right;"><p>264</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>white</p></td>
<td style="text-align: left;"><p>yellow</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Quermia</p></td>
<td style="text-align: left;"><p>Quermian</p></td>
<td style="text-align: left;"><p>The Phantom Menace</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Plo Koon</p></td>
<td style="text-align: right;"><p>188</p></td>
<td style="text-align: right;"><p>80.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>orange</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: right;"><p>22.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Dorin</p></td>
<td style="text-align: left;"><p>Kel Dor</p></td>
<td style="text-align: left;"><p>The Phantom Menace , Attack of the Clones, Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"><p>Jedi starfighter</p></td>
<td style="text-align: right;"><p>22.63468</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Mas Amedda</p></td>
<td style="text-align: right;"><p>196</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Champala</p></td>
<td style="text-align: left;"><p>Chagrian</p></td>
<td style="text-align: left;"><p>The Phantom Menace , Attack of the Clones</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Gregar Typho</p></td>
<td style="text-align: right;"><p>185</p></td>
<td style="text-align: right;"><p>85.0</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: left;"><p>dark</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>Naboo</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>Attack of the Clones</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"><p>Naboo fighter</p></td>
<td style="text-align: right;"><p>24.83565</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Cordé</p></td>
<td style="text-align: right;"><p>157</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>light</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>Naboo</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>Attack of the Clones</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Cliegg Lars</p></td>
<td style="text-align: right;"><p>183</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>fair</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>82.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Tatooine</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>Attack of the Clones</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Poggle the Lesser</p></td>
<td style="text-align: right;"><p>183</p></td>
<td style="text-align: right;"><p>80.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>green</p></td>
<td style="text-align: left;"><p>yellow</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Geonosis</p></td>
<td style="text-align: left;"><p>Geonosian</p></td>
<td style="text-align: left;"><p>Attack of the Clones, Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>23.88844</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Luminara Unduli</p></td>
<td style="text-align: right;"><p>170</p></td>
<td style="text-align: right;"><p>56.2</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: left;"><p>yellow</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>58.0</p></td>
<td style="text-align: left;"><p>female</p></td>
<td style="text-align: left;"><p>feminine</p></td>
<td style="text-align: left;"><p>Mirial</p></td>
<td style="text-align: left;"><p>Mirialan</p></td>
<td style="text-align: left;"><p>Attack of the Clones, Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>19.44637</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Barriss Offee</p></td>
<td style="text-align: right;"><p>166</p></td>
<td style="text-align: right;"><p>50.0</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: left;"><p>yellow</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>40.0</p></td>
<td style="text-align: left;"><p>female</p></td>
<td style="text-align: left;"><p>feminine</p></td>
<td style="text-align: left;"><p>Mirial</p></td>
<td style="text-align: left;"><p>Mirialan</p></td>
<td style="text-align: left;"><p>Attack of the Clones</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>18.14487</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Dormé</p></td>
<td style="text-align: right;"><p>165</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>light</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>female</p></td>
<td style="text-align: left;"><p>feminine</p></td>
<td style="text-align: left;"><p>Naboo</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>Attack of the Clones</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Dooku</p></td>
<td style="text-align: right;"><p>193</p></td>
<td style="text-align: right;"><p>80.0</p></td>
<td style="text-align: left;"><p>white</p></td>
<td style="text-align: left;"><p>fair</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>102.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Serenno</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>Attack of the Clones, Revenge of the Sith</p></td>
<td style="text-align: left;"><p>Flitknot speeder</p></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>21.47709</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Bail Prestor Organa</p></td>
<td style="text-align: right;"><p>191</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: left;"><p>tan</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>67.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Alderaan</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>Attack of the Clones, Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Jango Fett</p></td>
<td style="text-align: right;"><p>183</p></td>
<td style="text-align: right;"><p>79.0</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: left;"><p>tan</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>66.0</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Concord Dawn</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>Attack of the Clones</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>23.58984</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Zam Wesell</p></td>
<td style="text-align: right;"><p>168</p></td>
<td style="text-align: right;"><p>55.0</p></td>
<td style="text-align: left;"><p>blonde</p></td>
<td style="text-align: left;"><p>fair, green, yellow</p></td>
<td style="text-align: left;"><p>yellow</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>female</p></td>
<td style="text-align: left;"><p>feminine</p></td>
<td style="text-align: left;"><p>Zolan</p></td>
<td style="text-align: left;"><p>Clawdite</p></td>
<td style="text-align: left;"><p>Attack of the Clones</p></td>
<td style="text-align: left;"><p>Koro-2 Exodrive airspeeder</p></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>19.48696</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Dexter Jettster</p></td>
<td style="text-align: right;"><p>198</p></td>
<td style="text-align: right;"><p>102.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>yellow</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Ojom</p></td>
<td style="text-align: left;"><p>Besalisk</p></td>
<td style="text-align: left;"><p>Attack of the Clones</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>26.01775</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Lama Su</p></td>
<td style="text-align: right;"><p>229</p></td>
<td style="text-align: right;"><p>88.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>grey</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Kamino</p></td>
<td style="text-align: left;"><p>Kaminoan</p></td>
<td style="text-align: left;"><p>Attack of the Clones</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>16.78076</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Taun We</p></td>
<td style="text-align: right;"><p>213</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>grey</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>female</p></td>
<td style="text-align: left;"><p>feminine</p></td>
<td style="text-align: left;"><p>Kamino</p></td>
<td style="text-align: left;"><p>Kaminoan</p></td>
<td style="text-align: left;"><p>Attack of the Clones</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Jocasta Nu</p></td>
<td style="text-align: right;"><p>167</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>white</p></td>
<td style="text-align: left;"><p>fair</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>female</p></td>
<td style="text-align: left;"><p>feminine</p></td>
<td style="text-align: left;"><p>Coruscant</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>Attack of the Clones</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>R4-P17</p></td>
<td style="text-align: right;"><p>96</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>silver, red</p></td>
<td style="text-align: left;"><p>red, blue</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>feminine</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>Droid</p></td>
<td style="text-align: left;"><p>Attack of the Clones, Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Wat Tambor</p></td>
<td style="text-align: right;"><p>193</p></td>
<td style="text-align: right;"><p>48.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>green, grey</p></td>
<td style="text-align: left;"><p>unknown</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Skako</p></td>
<td style="text-align: left;"><p>Skakoan</p></td>
<td style="text-align: left;"><p>Attack of the Clones</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>12.88625</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>San Hill</p></td>
<td style="text-align: right;"><p>191</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>grey</p></td>
<td style="text-align: left;"><p>gold</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Muunilinst</p></td>
<td style="text-align: left;"><p>Muun</p></td>
<td style="text-align: left;"><p>Attack of the Clones</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Shaak Ti</p></td>
<td style="text-align: right;"><p>178</p></td>
<td style="text-align: right;"><p>57.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>red, blue, white</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>female</p></td>
<td style="text-align: left;"><p>feminine</p></td>
<td style="text-align: left;"><p>Shili</p></td>
<td style="text-align: left;"><p>Togruta</p></td>
<td style="text-align: left;"><p>Attack of the Clones, Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>17.99015</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Grievous</p></td>
<td style="text-align: right;"><p>216</p></td>
<td style="text-align: right;"><p>159.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>brown, white</p></td>
<td style="text-align: left;"><p>green, yellow</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Kalee</p></td>
<td style="text-align: left;"><p>Kaleesh</p></td>
<td style="text-align: left;"><p>Revenge of the Sith</p></td>
<td style="text-align: left;"><p>Tsmeu-6 personal wheel bike</p></td>
<td style="text-align: left;"><p>Belbullab-22 starfighter</p></td>
<td style="text-align: right;"><p>34.07922</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Tarfful</p></td>
<td style="text-align: right;"><p>234</p></td>
<td style="text-align: right;"><p>136.0</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>blue</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Kashyyyk</p></td>
<td style="text-align: left;"><p>Wookiee</p></td>
<td style="text-align: left;"><p>Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>24.83746</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Raymus Antilles</p></td>
<td style="text-align: right;"><p>188</p></td>
<td style="text-align: right;"><p>79.0</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>light</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Alderaan</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>A New Hope , Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>22.35174</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Sly Moore</p></td>
<td style="text-align: right;"><p>178</p></td>
<td style="text-align: right;"><p>48.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>pale</p></td>
<td style="text-align: left;"><p>white</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>Umbara</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>Attack of the Clones, Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>15.14960</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Tion Medon</p></td>
<td style="text-align: right;"><p>206</p></td>
<td style="text-align: right;"><p>80.0</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>grey</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>Utapau</p></td>
<td style="text-align: left;"><p>Pau’an</p></td>
<td style="text-align: left;"><p>Revenge of the Sith</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>18.85192</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Finn</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: left;"><p>dark</p></td>
<td style="text-align: left;"><p>dark</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>The Force Awakens</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Rey</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>light</p></td>
<td style="text-align: left;"><p>hazel</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>female</p></td>
<td style="text-align: left;"><p>feminine</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>The Force Awakens</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Poe Dameron</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: left;"><p>light</p></td>
<td style="text-align: left;"><p>brown</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>male</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>The Force Awakens</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"><p>X-wing</p></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>BB8</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>black</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>masculine</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>Droid</p></td>
<td style="text-align: left;"><p>The Force Awakens</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
<tr>
<td style="text-align: left;"><p>Captain Phasma</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>none</p></td>
<td style="text-align: left;"><p>unknown</p></td>
<td style="text-align: right;"><p>NA</p></td>
<td style="text-align: left;"><p>female</p></td>
<td style="text-align: left;"><p>feminine</p></td>
<td style="text-align: left;"><p>NA</p></td>
<td style="text-align: left;"><p>Human</p></td>
<td style="text-align: left;"><p>The Force Awakens</p></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><p>NA</p></td>
</tr>
</tbody>
</table>

#### 8. Найти 10 самых “вытянутых” персонажей. “Вытянутость” оценить по отношению массы (mass) к росту (height) персонажей

``` r
top10_stretched <- starwars %>%
  mutate(stretch = mass / (height / 100)) %>% 
  filter(is.finite(stretch)) %>% 
  arrange(desc(stretch)) %>%
  select(name, species, height, mass, stretch) %>%
  slice_head(n = 10)

top10_stretched
```

**Вывод:**

+-----------------------+------------+--------+------+-----------+
| name                  | species    | height | mass | stretch   |
+:======================+:===========+=======:+=====:+==========:+
| Jabba Desilijic Tiure | Hutt       | 175    | 1358 | 776.00000 |
+-----------------------+------------+--------+------+-----------+
| Grievous              | Kaleesh    | 216    | 159  | 73.61111  |
+-----------------------+------------+--------+------+-----------+
| IG-88                 | Droid      | 200    | 140  | 70.00000  |
+-----------------------+------------+--------+------+-----------+
| Owen Lars             | Human      | 178    | 120  | 67.41573  |
+-----------------------+------------+--------+------+-----------+
| Darth Vader           | Human      | 202    | 136  | 67.32673  |
+-----------------------+------------+--------+------+-----------+
| Jek Tono Porkins      | NA         | 180    | 110  | 61.11111  |
+-----------------------+------------+--------+------+-----------+
| Bossk                 | Trandoshan | 190    | 113  | 59.47368  |
+-----------------------+------------+--------+------+-----------+
| Tarfful               | Wookiee    | 234    | 136  | 58.11966  |
+-----------------------+------------+--------+------+-----------+
| Dexter Jettster       | Besalisk   | 198    | 102  | 51.51515  |
+-----------------------+------------+--------+------+-----------+
| Chewbacca             | Wookiee    | 228    | 112  | 49.12281  |
+-----------------------+------------+--------+------+-----------+

#### 9. Найти средний возраст персонажей каждой расы вселенной Звездных войн.

``` r
starwars %>%
  filter(!is.na(species), !is.na(birth_year)) %>%
  group_by(species) %>%
  summarise(avg_age = mean(birth_year, na.rm = TRUE)) %>%
  arrange(desc(avg_age)) 
```

**Вывод:**

+----------------+-----------+
| species        | avg_age   |
+:===============+==========:+
| Yoda’s species | 896.00000 |
+----------------+-----------+
| Hutt           | 600.00000 |
+----------------+-----------+
| Wookiee        | 200.00000 |
+----------------+-----------+
| Cerean         | 92.00000  |
+----------------+-----------+
| Zabrak         | 54.00000  |
+----------------+-----------+
| Human          | 53.74231  |
+----------------+-----------+
| Droid          | 53.33333  |
+----------------+-----------+
| Trandoshan     | 53.00000  |
+----------------+-----------+
| Gungan         | 52.00000  |
+----------------+-----------+
| Mirialan       | 49.00000  |
+----------------+-----------+
| Twi’lek        | 48.00000  |
+----------------+-----------+
| Rodian         | 44.00000  |
+----------------+-----------+
| Mon Calamari   | 41.00000  |
+----------------+-----------+
| Kel Dor        | 22.00000  |
+----------------+-----------+
| Ewok           | 8.00000   |
+----------------+-----------+

#### 10. Найти самый распространенный цвет глаз персонажей вселенной Звездных войн.

``` r
starwars %>%
  count(eye_color, sort = TRUE) %>%
  slice_head(n = 1) |> knitr::kable(format='markdown')
```

**Вывод:**

+-----------+----+
| eye_color | n  |
+:==========+===:+
| brown     | 21 |
+-----------+----+

#### 11. Подсчитать среднюю длину имени в каждой расе вселенной Звездных войн

``` r
starwars %>%
  filter(!is.na(species), !is.na(name)) %>%
  mutate(name_length = nchar(name)) %>%
  group_by(species) %>%
  summarise(
    avg_name_length = round(mean(name_length), 1),
    n = n()
  ) %>%
  arrange(desc(avg_name_length)) 
```

**Вывод:**

+----------------+-----------------+----+
| species        | avg_name_length | n  |
+:===============+================:+===:+
| Ewok           | 21.0            | 1  |
+----------------+-----------------+----+
| Hutt           | 21.0            | 1  |
+----------------+-----------------+----+
| Geonosian      | 17.0            | 1  |
+----------------+-----------------+----+
| Besalisk       | 15.0            | 1  |
+----------------+-----------------+----+
| Mirialan       | 14.0            | 2  |
+----------------+-----------------+----+
| Toong          | 14.0            | 1  |
+----------------+-----------------+----+
| Aleena         | 12.0            | 1  |
+----------------+-----------------+----+
| Cerean         | 12.0            | 1  |
+----------------+-----------------+----+
| Gungan         | 11.7            | 3  |
+----------------+-----------------+----+
| Human          | 11.3            | 35 |
+----------------+-----------------+----+
| Iktotchi       | 11.0            | 1  |
+----------------+-----------------+----+
| Neimodian      | 11.0            | 1  |
+----------------+-----------------+----+
| Quermian       | 11.0            | 1  |
+----------------+-----------------+----+
| Twi’lek        | 11.0            | 2  |
+----------------+-----------------+----+
| Chagrian       | 10.0            | 1  |
+----------------+-----------------+----+
| Clawdite       | 10.0            | 1  |
+----------------+-----------------+----+
| Pau’an         | 10.0            | 1  |
+----------------+-----------------+----+
| Skakoan        | 10.0            | 1  |
+----------------+-----------------+----+
| Tholothian     | 10.0            | 1  |
+----------------+-----------------+----+
| Zabrak         | 9.5             | 2  |
+----------------+-----------------+----+
| Nautolan       | 9.0             | 1  |
+----------------+-----------------+----+
| Sullustan      | 9.0             | 1  |
+----------------+-----------------+----+
| Kaleesh        | 8.0             | 1  |
+----------------+-----------------+----+
| Kel Dor        | 8.0             | 1  |
+----------------+-----------------+----+
| Muun           | 8.0             | 1  |
+----------------+-----------------+----+
| Togruta        | 8.0             | 1  |
+----------------+-----------------+----+
| Vulptereen     | 8.0             | 1  |
+----------------+-----------------+----+
| Wookiee        | 8.0             | 2  |
+----------------+-----------------+----+
| Dug            | 7.0             | 1  |
+----------------+-----------------+----+
| Kaminoan       | 7.0             | 2  |
+----------------+-----------------+----+
| Xexto          | 7.0             | 1  |
+----------------+-----------------+----+
| Mon Calamari   | 6.0             | 1  |
+----------------+-----------------+----+
| Rodian         | 6.0             | 1  |
+----------------+-----------------+----+
| Toydarian      | 5.0             | 1  |
+----------------+-----------------+----+
| Trandoshan     | 5.0             | 1  |
+----------------+-----------------+----+
| Droid          | 4.8             | 6  |
+----------------+-----------------+----+
| Yoda’s species | 4.0             | 1  |
+----------------+-----------------+----+

## Оценка результатов

Задача решена с использованием языка программирования R и пакета `dplyr`. Я научился использовать эти два инструмента для анализа данных.

## Вывод

В данной работе я ответил на вопросы путем анализа и обработки датафрейма `starwars`