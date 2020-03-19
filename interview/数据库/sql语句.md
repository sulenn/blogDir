[toc]

# sql 语句

## 查询表中重复数据

select * from employee where employeeId in (select employeeId from employee group by employeeId having count(employeeId) > 1)

**参考**:[删除表中重复数据sql语句](https://blog.csdn.net/germoo/article/details/79960480)

---

## 删除表中重复数据

delete from employee where employeeId in (select employeeId from employee group by employeeId having count(employeeId) > 1)
and rowid not in (select min(rowid) from employee group by employeeId having count(employeeId )>1)

**参考**:[删除表中重复数据sql语句](https://blog.csdn.net/germoo/article/details/79960480)

---
