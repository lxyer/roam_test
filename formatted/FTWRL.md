
# Backlinks
## [MySQL](<MySQL.md>)
- MySQL提供了一个加全局读锁的方法，命令是 Flush tables with read lock ([FTWRL](<FTWRL.md>))

- single-transaction方法只适用于所有的表使用事务引擎的库。如果有的表使用了不支持事务的引擎，那么备份就只能通过[FTWRL](<FTWRL.md>)方

