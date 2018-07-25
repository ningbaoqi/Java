### Java8新增的日期、时间包

+ java8新增了java.time包；该包中的常用类如下：

|类|说明|
|------|-------|
|Clock|用于获取指定时区的当前时间、日期、该类可取代System类的currentTimeMillis()方法，而且提供了更多方法来获取当前时间和日期；该类提供了大量的静态方法获取Clock类|
|Duration|该类代表持续时间，该类可以非常方便的获取一段时间|
|Instant|代表一个具体的时刻，可以精确到纳秒|
|LocalDate|该类代表不带时区的时间|
|LocalTime|该类代表不带时区的时间|
|LocalDateTime|该类代表不带时区的日期、时间|
|MonthDay|该类仅代表月日|
|Year|该类仅代表年|
|YearMonth|该类仅代表年月|
|ZonedDateTime|该类代表一个时区化的日期，时间|
|ZoneId|该类代表一个时区|
|DayOfWeek|该类是一个枚举类，定义了周日到周六的枚举值|
|Month|该类也是一个枚举类，定义了一月到十二月的枚举值|



