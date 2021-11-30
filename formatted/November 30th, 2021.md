- 数据库问题 [R2M](<R2M.md>)
    - r2m_3c_client 表created_time [datetime](<datetime.md>)类型,但是modified_time 使用了[timestamp](<timestamp.md>)类型
    - r2m_3c_config_type表中
- VMware Fusion Player – Personal UseH108L-8H2EN-N8TJC-0T9H6-2DYQN
- — via [MySQL 中 [datetime](<datetime.md>) 和 [timestamp](<timestamp.md>) 的区别与选择 - SegmentFault 思否](https://segmentfault.com/a/1190000017393602) [+Roam](<+Roam.md>)
    - 占用空间
        - [datetime](<datetime.md>) 8 字节
        - [timestamp](<timestamp.md>) 4 字节
    - 表示范围
        - [datetime](<datetime.md>) '1000-01-01 00:00:00.000000' to '9999-12-31 23:59:59.999999'
        - [timestamp](<timestamp.md>) '1970-01-01 00:00:01.000000' to '2038-01-19 03:14:07.999999'
- 时区
    - [timestamp](<timestamp.md>) 只占 4 个字节，而且是以utc的格式储存， 它会自动检索当前时区并进行转换。
    - [datetime](<datetime.md>)以 8 个字节储存，不会进行时区的检索.
    - 也就是说，对于[timestamp](<timestamp.md>)来说，如果储存时的时区和检索时的时区不一样，那么拿出来的数据也不一样。对于[datetime](<datetime.md>)来说，存什么拿到的就是什么。
    - 还有一个区别就是如果存进去的是NULL，[timestamp](<timestamp.md>)会自动储存当前时间，而 [datetime](<datetime.md>)会储存 NULL。
- 如何选择
    - 如果在时间上要超过Linux时间的，或者服务器时区不一样的就建议选择 [datetime](<datetime.md>)。
    - 如果是想要使用自动插入时间或者自动更新时间功能的，可以使用[timestamp](<timestamp.md>)。
    - 如果只是想表示年、日期、时间的还可以使用 year、 date、 time，它们分别占据 1、3、3 字节，而[datetime](<datetime.md>)就是它们的集合。