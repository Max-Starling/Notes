
# Основные понятия
#
![image](https://user-images.githubusercontent.com/22237384/220296705-a06e8dca-9103-4e90-9bce-48ea758d6815.png)

## Data Model

**Модель данных** (англ. `Data Model`) в Sisense представляет собой ничто иное, как схему базы данных, то есть множество таблиц, отображённых в виде вершин графа и рёбер между вершинами, которые показывают связи между таблицами.

Каждая таблица имеет набор столбцов, каждый из которых имеет определённый тип данных из списка поддерживаемых в Sisense типов.

Строки таблиц можно получить

Data Model может быть двух типов:
* `live` - делает запросы напрямую к сторонней базе данных, используя строку подключения (англ. `connection string`), переданный при инициализации.
* `elastic cube` - данные клонируются со сторонней базы данных в Sisense, далее запросы отправляются непосредственно к скопированным данным.

Смешивать эти типы невозможно.

## Dataset

Чтобы получить строки таблиц, необходимо отправить запрос. Одной таблице в модели данных может соответствовать несколько источников данных.

**Набор данных** (англ. `Dataset`) представляет собой один из источников данных, используемых текущей моделью данных. Например, если модель данных содержит данные из трёх CSV-файлов и двух баз данных SQL, то всего у нас 5 источников, а значит 5 наборов данных.

Например, `connection string` может быть
```
Server=ServerName;Database=DatabaseName;User Id=UserName;Password=UserPassword;
Server=localhost;Database=SisenseNotes;User Id=SuperAdmin;Password=qwerty;
```

Каждый набор данных содержит информацию о подключении к своему источнику (например, строку подключения к SQL-базе или путь к CSV-файлу)


## Table