### 分配 ###
### 分配不发触发器 ###
<pre>
create or replace trigger tr_gq_allocations
before update of allqty_0 on sorderq
for each row

declare
totalRow integer;
toldelqty number(12,4);
dme integer;
msg varchar2(1000);
hd  integer;

begin
   if :new.allqty_0 = :old.allqty_0 then
     return;
   end if;

   select count(*) into totalRow from facgroup f where f.cpy_0='Z01' and fcy_0=:new.stofcy_0;
   --select count(*) into totalRow from dual where :new.stofcy_0 in ('F11','H11','I11','R11','W11');
   if totalRow = 0 then
     return;
   end if;
   --dbms_output.put_line('11111111111111');
   select count(sohnum_0) into hd from sorder where ZXBPCORD_0 in (select zbpnum_0 from zhdbpc) and sohnum_0 = :new.sohnum_0;
   if hd > 0 then
     :new.zfpbf_0 := 2;
   else
     select dme_0 into dme from sorder where sohnum_0 = :new.sohnum_0;
     if dme = 1 then
       if :new.stofcy_0 = 'G11' then
          :new.zfpbf_0 := 1;
       else
         --dbms_output.put_line('222222222222222222222');
          --select sum(odlqty_0+dlvqty_0) into toldelqty from sorderq where sohnum_0 = :new.sohnum_0;
          select dlvsta_0 into toldelqty from sorder where sohnum_0 = :new.sohnum_0;
          --dbms_output.put_line('33333333333333333333');
          if toldelqty > 1 then
            :new.zfpbf_0 := 2;
          else
            :new.zfpbf_0 := 1;
          end if;
       end if;
     elsif dme = 2 then
       if (:new.allqty_0 + :new.zsoqqtya_0) = :new.qtystu_0 then
          :new.zfpbf_0 := 1;
       else
          :new.zfpbf_0 := 2;
       end if;
     elsif dme = 3 then
          :new.zfpbf_0 := 2;
     end if;
   end if;
   
exception
when others then
        msg:=sqlerrm;
     insert into zcpfp(sohnum,ms)values(:new.sohnum_0,msg);
end;
</pre>