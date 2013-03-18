SELECT FOUND_ROWS()返回前一次查询的记录总数
比如：
执行 SELECT * FROM a 或者SELECT * FROM a LIMIT 10
再执行SELECT FOUND_ROWS()

如果想返回查询的总记录数不受limit影响，查询语句增加 SQL_CALC_FOUND_ROWS 关键字
如：
执行 SELECT SQL_CALC_FOUND_ROWS * FROM a LIMIT 10
SELECT FOUND_ROWS()返回的结果数是不受limit影响的
