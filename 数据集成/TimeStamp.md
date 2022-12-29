### MySQL

https://dev.mysql.com/doc/refman/5.7/en/datetime.html

> MySQL converts TIMESTAMP values from the current time zone to UTC for storage, and back from UTC to the current time zone for retrieval. (This does not occur for other types such as DATETIME.) By default, the current time zone for each connection is the server's time. The time zone can be set on a per-connection basis. As long as the time zone setting remains constant, you get back the same value you store. If you store a TIMESTAMP value, and then change the time zone and retrieve the value, the retrieved value is different from the value you stored. This occurs because the same time zone was not used for conversion in both directions. The current time zone is available as the value of the time_zone system variable. For more information, see Section 5.1.13, “MySQL Server Time Zone Support”.

### TiDB

https://docs.pingcap.com/zh/tidb/stable/data-type-date-and-time#timestamp-%E7%B1%BB%E5%9E%8B

> TIMESTAMP 类型
> 
> TIMESTAMP 类型是日期和时间的组合，支持的范围是 UTC 时间从 1970-01-01 00:00:01.000000 到 2038-01-19 03:14:07.999999。fsp 参数表示秒精度，取值范围为 0~6，默认值为 0。在 TIMESTAMP 中，不允许零出现在月份部分或日期部分，唯一的例外是零值本身 0000-00-00 00:00:00。
> 
> `TIMESTAMP[(fsp)]`
> 
> 时区处理
> 
> 当存储 TIMESTAMP 时，TiDB 会将当前时区的 TIMESTAMP 值转换为 UTC 时区。当读取 TIMESTAMP 时，TiDB 将存储的 TIMESTAMP 值从 UTC 时区转换为当前时区（注意：DATETIME 不会这样处理）。每次连接的默认时区是服务器的本地时区，可以通过环境变量 time_zone 进行修改。

### Flink

https://nightlies.apache.org/flink/flink-docs-release-1.12/zh/dev/table/types.html#timestamp

> TIMESTAMP
> 
> 不带时区的时间戳数据类型，由 year-month-day hour:minute:second[.fractional] 组成，精度达到纳秒，范围从 0000-01-01 00:00:00.000000000 到 9999-12-31 23:59:59.999999999。
> 
> 与 SQL 标准相比，不支持闰秒（23:59:60 和 23:59:61），语义上更接近于 java.time.LocalDateTime。
> 
> 不支持和 BIGINT（JVM long 类型）互相转换，因为这意味着有时区，然而此类型是无时区的。对于语义上更接近于 java.time.Instant 的需求请使用 TIMESTAMP WITH LOCAL TIME ZONE。

> TIMESTAMP WITH LOCAL TIME ZONE
> 
> 带有本地时区的时间戳数据类型，由 year-month-day hour:minute:second[.fractional] zone 组成，精度达到纳秒，范围从 0000-01-01 00:00:00.000000000 +14:59 到 9999-12-31 23:59:59.999999999 -14:59。
> 
> 不支持闰秒（23:59:60 和 23:59:61），语义上更接近于 java.time.OffsetDateTime。
> 
> 与 TIMESTAMP WITH TIME ZONE 相比，时区偏移信息并非物理存储在每个数据中。相反，此类型在 Table 编程环境的 UTC 时区中采用 java.time.Instant 语义。每个数据都在当前会话中配置的本地时区中进行解释，以便用于计算和可视化。
>
> 此类型允许根据配置的会话时区来解释 UTC 时间戳，从而填补了时区无关和时区相关的时间戳类型之间的鸿沟。

### Spark

https://spark.apache.org/docs/latest/sql-ref-datatypes.html

> Datetime type
> - TimestampType: Represents values comprising values of fields year, month, day, hour, minute, and second, with the session local time-zone. The timestamp value represents an absolute point in time.
> - DateType: Represents values comprising values of fields year, month and day, without a time-zone.

### Iceberg

https://iceberg.apache.org/spec/#schemas-and-data-types

> All time and timestamp values are stored with microsecond precision.
> - Timestamps with time zone represent a point in time: values are stored as UTC and do not retain a source time zone (2017-11-16 17:10:34 PST is stored/retrieved as 2017-11-17 01:10:34 UTC and these values are considered identical).
> - Timestamps without time zone represent a date and time of day regardless of zone: the time value is independent of zone adjustments (2017-11-16 17:10:34 is always retrieved as 2017-11-16 17:10:34). Timestamp values are stored as a long that encodes microseconds from the unix epoch.

### Hive

https://cwiki.apache.org/confluence/display/hive/languagemanual+types#LanguageManualTypes-TimestampstimestampTimestamps

> Timestamps
> 
> Supports traditional UNIX timestamp with optional nanosecond precision.
> 
> Supported conversions:
> - Integer numeric types: Interpreted as UNIX timestamp in seconds
> - Floating point numeric types: Interpreted as UNIX timestamp in seconds with decimal precision
> - Strings: JDBC compliant java.sql.Timestamp format "YYYY-MM-DD HH:MM:SS.fffffffff" (9 decimal place precision)
>
> Timestamps are interpreted to be timezoneless and stored as an offset from the UNIX epoch. Convenience UDFs for conversion to and from timezones are provided (to_utc_timestamp, from_utc_timestamp).
> All existing datetime UDFs (month, day, year, hour, etc.) work with the TIMESTAMP data type.
> 
> Timestamps in text files have to use the format yyyy-mm-dd hh:mm:ss[.f...]. If they are in another format, declare them as the appropriate type (INT, FLOAT, STRING, etc.) and use a UDF to convert them to timestamps.
>
> Timestamps in Parquet files may be stored as int64 (as opposed to int96) by setting hive.parquet.write.int64.timestamp=true and hive.parquet.timestamp.time.unit to a default storage time unit. ("nanos", "micros", "millis"; default: "micros"). Note that because only 64 bits are stored, int64 timestamps stored as "nanos" will be stored as NULL if outside the range of 1677-09-21T00:12:43.15 and 2262-04-11T23:47:16.8.
>
> On the table level, alternative timestamp formats can be supported by providing the format to the SerDe property "timestamp.formats" (as of release 1.2.0 with HIVE-9298). For example, yyyy-MM-dd'T'HH:mm:ss.SSS,yyyy-MM-dd'T'HH:mm:ss.

### Parquet

https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#local-semantics-timestamps-not-normalized-to-utc

TimeStamp(isAdjustedToUTC=false): 对应 Flink 的 TimeStamp，不带时区信息的时间类型，无论本地时区如何设置，总是展示为同样的时间值
TimeStamp(isAdjustedToUTC=true): 对应 Flink 的 TimeStamp_LTZ，按设置的本地时区信息动态改变展示的时间值

> **Instant semantics (timestamps normalized to UTC)**
>
> A TIMESTAMP with isAdjustedToUTC=true is defined as the number of milliseconds, microseconds or nanoseconds (depending on the unit parameter being MILLIS, MICROS or NANOS, respectively) elapsed since the Unix epoch, 1970-01-01 00:00:00 UTC. Each such value unambiguously identifies a single instant on the time-line.
>
> For example, in a TIMESTAMP(isAdjustedToUTC=true, unit=MILLIS), the number 172800000 corresponds to 1970-01-03 00:00:00 UTC, because it is equal to 2 * 24 * 60 * 60 * 1000, therefore it is exactly two days from the reference point, the Unix epoch. In Java, this calculation can be achieved by calling Instant.ofEpochMilli(172800000).
>
> As a slightly more complicated example, if one wants to store 1970-01-03 00:00:00 (UTC+01:00) as a TIMESTAMP(isAdjustedToUTC=true, unit=MILLIS), first the time zone offset has to be dealt with. By normalizing the timestamp to UTC, we calculate what time in UTC corresponds to the same instant: 1970-01-02 23:00:00 UTC. This is 1 day and 23 hours after the epoch, therefore it can be encoded as the number (24 + 23) * 60 * 60 * 1000 = 169200000.
>
> Please note that time zone information gets lost in this process. Upon reading a value back, we can only reconstruct the instant, but not the original representation. In practice, such timestamps are typically displayed to users in their local time zones, therefore they may be displayed differently depending on the execution environment.
>
> **Local semantics (timestamps not normalized to UTC)**
>
> A TIMESTAMP with isAdjustedToUTC=false represents year, month, day, hour, minute, second and subsecond fields in a local timezone, regardless of what specific time zone is considered local. This means that such timestamps should always be displayed the same way, regardless of the local time zone in effect. On the other hand, without additional information such as an offset or time-zone, these values do not identify instants on the time-line unambiguously, because the corresponding instants would depend on the local time zone.
> 
> Using a single number to represent a local timestamp is a lot less intuitive than for instants. One must use a local timestamp as the reference point and calculate the elapsed time between the actual timestamp and the reference point. The problem is that the result may depend on the local time zone, for example because there may have been a daylight saving time change between the two timestamps.