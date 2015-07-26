title: Sql_fun
date: 2015-07-24 18:38:12
tags: sql
categories:
---

### sql存储函数
 20150601L2sIhL7|XLxG5WuYvu222q6|JU4o4BE10A43070|3iA3O0fGM2u1U8r|
 华南区,河南省,三门峡市,陕县
```sql
DELIMITER $$

USE `b2bbizossscom`$$

DROP FUNCTION IF EXISTS `tranAddr`$$

CREATE DEFINER=`root`@`%` FUNCTION `tranAddr`(inputstring VARCHAR(1000) 
    ) RETURNS CHAR(100) CHARSET utf8
BEGIN
        DECLARE strlen INT DEFAULT LENGTH(inputstring);
        DECLARE last_index INT DEFAULT 0;
        DECLARE cur_index INT DEFAULT 1;
        DECLARE cur_char VARCHAR(200);
        DECLARE len INT;
        DECLARE delim CHAR(1) DEFAULT '|';
        DROP TEMPORARY TABLE IF EXISTS splittable;
        CREATE TEMPORARY TABLE splittable(
        VALUE VARCHAR(20) CHARSET gbk COLLATE gbk_chinese_ci
        ) ;
        WHILE(cur_index<=strlen) DO   
        BEGIN
        IF SUBSTRING(inputstring FROM cur_index FOR 1)=delim OR cur_index=strlen THEN
            SET len=cur_index-last_index-1;
            IF cur_index=strlen THEN
               SET len=len+1;
            END IF;
            INSERT INTO splittable(`value`)VALUES(SUBSTRING(inputstring FROM (last_index+1) FOR len));
            SET last_index=cur_index;
        END IF;
        SET cur_index=cur_index+1;
        END;
        END WHILE;
        RETURN (SELECT GROUP_CONCAT(ta.area_name) AS area_name FROM ts_area ta,splittable s WHERE INSTR(ta.area_id,s.VALUE)>0 ORDER BY s.value);        
    END$$

DELIMITER ;
```
