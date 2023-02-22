# 26-1-1-26-1-3

1.
--create table gcnt_YYYMM
(policy_no            char(12),
plan_code            char(8),
industry_code        char(1),
cnt                  char(4));


--drop table if exists gcnt_YYYMM;

2.
delete from gcnt_11112  --update
where 1=1;

drop table if exists a1;
drop table if exists a2;
drop table if exists a3;
drop table if exists b1;


select distinct policy_no,client_id
from gico
where co_sts_code in ('42','49')
into temp a1;

select distinct policy_no, ginw_type_cnt
from ginw
where ginw_sts_code in ('42','49')
and expired_date > '111/12/31'  --update
into temp b1;

select 'gico' as type, policy_no,count(client_id) cnt
from a1
group by 1,2
into temp a2;

insert into a2
select 'ginw' as type, policy_no,sum(ginw_type_cnt) cnt
from b1
group by 1,2;

select distinct 
a.policy_no,c.plan_code,
(case when b.industry_code between 1 and 2 then 'A'
when b.industry_code in ('3','03') then 'B'
when b.industry_code between 4 and 21 then 'C'
when b.industry_code ='22' then 'D'
when b.industry_code between 23 and 24 then 'E'
when b.industry_code between 25 and 29 then 'F'
when b.industry_code between 30 and 31 then 'G'
when b.industry_code between 32 and 38 then 'H'
when b.industry_code ='39' then 'I'
when b.industry_code between 40 and 42 then 'J'
when b.industry_code ='43' then 'K'
when b.industry_code ='44' then 'L'
when b.industry_code between 45 and 46 then 'M'
when b.industry_code between 47 and 48 then 'N'
when b.industry_code ='49' then 'O'
when b.industry_code ='50' then 'P'
when b.industry_code ='51' then 'Q'
when b.industry_code ='52' then 'R'
when b.industry_code between 53 and 55 then 'S'
else 'T' end) industry_code,
(case when a.cnt between 0 and 9 then '9'
when a.cnt between 10 and 49 then '49'
when a.cnt between 50 and 99 then '99'
when a.cnt >='100' then '100+' else 'XXX' end) cnt
from a2 a, gipo b,gico c
where a.policy_no=b.policy_no
and a.policy_no=c.policy_no
and a.type = 'gico'
into temp a3;

insert into a3 
select distinct 
a.policy_no,d.plan_code,
(case when b.industry_code between 1 and 2 then 'A'
when b.industry_code in ('3','03') then 'B'
when b.industry_code between 4 and 21 then 'C'
when b.industry_code ='22' then 'D'
when b.industry_code between 23 and 24 then 'E'
when b.industry_code between 25 and 29 then 'F'
when b.industry_code between 30 and 31 then 'G'
when b.industry_code between 32 and 38 then 'H'
when b.industry_code ='39' then 'I'
when b.industry_code between 40 and 42 then 'J'
when b.industry_code ='43' then 'K'
when b.industry_code ='44' then 'L'
when b.industry_code between 45 and 46 then 'M'
when b.industry_code between 47 and 48 then 'N'
when b.industry_code ='49' then 'O'
when b.industry_code ='50' then 'P'
when b.industry_code ='51' then 'Q'
when b.industry_code ='52' then 'R'
when b.industry_code between 53 and 55 then 'S'
else 'T' end) industry_code,
(case when a.cnt between 0 and 9 then '9'
when a.cnt between 10 and 49 then '49'
when a.cnt between 50 and 99 then '99'
when a.cnt >='100' then '100+' else 'XXX' end) cnt
from a2 a, gipo b,ginw c, gipn d
where a.policy_no=b.policy_no
and a.policy_no=b.policy_no
and a.type = 'ginw'
and c.policy_no = d.policy_no
and c.gipn_code = d.gipn_code
and c.gipn_vers = d.gipn_vers
and c.effect_date = d.gipn_issue_date
;

--檢核是否有重複項
select count(*) from gcnt_11112;
select count(distinct policy_no||plan_code) from gcnt_11112;
select policy_no, plan_code, count(*) from gcnt_11112
group by 1,2
order by 3 desc;


insert into gcnt_11112   --update
select * from a3
where 1=1;

3.
select a.policy_no, a.gta_seq, b.names, a.insured_cnt 
from gtcl a, gina b
where a.policy_no||a.gta_seq in (
'R1111120011338','R1116700001821')
and a.policy_no = b.company_id
order by 1

4.
drop table if exists a1;
drop table if exists a2;

select policy_no, plan_code, insurance_type, sum(upr) UPR
from ginw_upr_v1
where value_date = '111/12/31'
and policy_no not in (select policy_no from gp1)
group by 1,2,3
into temp a1;

select a.*, b.industry_code, b.cnt
from a1 a, gcnt_11112 b
where a.policy_no = b.policy_no
and a.plan_code = b.plan_code
into temp a2;

unload to 'ginw_upr_v1_1111231.txt'
select * from a2
order by 3,1

5.
drop table if exists a1;
drop table if exists a2;

select policy_no, plan_code, insurance_type, sum(upr) UPR
from gico_upr
where value_date = '111/12/31'
group by 1,2,3
into temp a1;

select a.*, b.industry_code, b.cnt
from a1 a, gcnt_11112 b
where a.policy_no = b.policy_no
and a.plan_code = b.plan_code
into temp a2;

unload to 'gico_upr_1111231.txt'
select * from a2
order by 3,1

6
unload to 'gipd_11112.txt'
select distinct plan_abbr_code, plan_code, plan_desc, insurance_type
from gipd


