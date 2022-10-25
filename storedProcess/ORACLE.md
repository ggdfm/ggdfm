---
layout: default
title: StoredProcess--ORACLE
nav_order: 2
parent: StoredProcess
---

## ORACLE
{: .no_toc }

## oracle 处理乱码 --全表查找乱码，并将乱码所处表与列标记在临时表中。
````oracle
create or replace procedure check_zh_cn is
    v_table_name  varchar2(50) ;
    v_table_column  varchar2(50) ;
    v_sql  varchar2(1000) ;
    v_count  int(10) := 0;
    v_index  int(10) := 0;
    -- 查询所有的表
    cursor v_table_names is
        SELECT * FROM USER_TABLES U;
begin
    for v_table_data in v_table_names loop
      begin
        v_table_name := v_table_data.TABLE_NAME ;
        -- 否则查询出表的列数据
        for v_table_column_data in (select * from user_tab_columns where Table_Name=v_table_name ORDER BY COLUMN_ID ASC ) loop

            begin
            v_table_column := v_table_column_data.COLUMN_NAME ;
            if  instr(v_table_column_data.DATA_TYPE, 'VARCHAR') > 0 then
                -- 判断乱码
                v_sql := 'select count(1) from '|| v_table_name || ' where asciistr('|| v_table_column ||') like ''%??%'' or asciistr('|| v_table_column ||') like ''%\FFFD%'' ';
                execute immediate v_sql into v_count;
                if v_count >0 then
                   v_index := v_index+1;
                   DBMS_OUTPUT.PUT_LINE(v_index ||' table.column is ' || v_table_name || '.' || v_table_column);
                end if;
            end if;
            end;
        end loop;
      end;
    end loop ;
end;
````