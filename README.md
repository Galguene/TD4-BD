# Compte-rendu BD

## Code

### Intro

```sql
create table communeLA as select * 
from BASETD.COMMUNE c 
where c.NOMDEP = 'Loire-Atlantique';

create table distributionLA as select *
from BASETD.DISTRIBUTION di
where di.CODE_INSEE >= 44000
and di.CODE_INSEE < 45000;

create table operateurLA as select distinct o.NUMFO, o.NOMFO, o.GENERATION, o.TECHNOLOGIE
from BASETD.OPERATEUR o, distributionLA d
where o.NUMFO = d.NUMFO;
```
```sql
CREATE TABLE COMMUNEVE AS SELECT DISTINCT *
FROM BASETD.COMMUNE CO
WHERE CO.NOMDEP = 'Vendée';

CREATE TABLE DISTRIBUTIONVE AS SELECT DISTINCT *
FROM BASETD.DISTRIBUTION DI
WHERE CODE_INSEE IN (SELECT CODE_INSEE
FROM COMMUNEVE CO
WHERE DI.CODE_INSEE = CO.CODE_INSEE);

CREATE TABLE OPERATEURVE AS SELECT DISTINCT *
FROM BASETD.OPERATEUR OP
WHERE NUMFO IN (SELECT NUMFO
FROM DISTRIBUTIONVE DI
WHERE OP.NUMFO = DI.NUMFO);
```

### Question 1

```sql
--1
grant select on distributionLA to I2A04A;
grant select on operateurLA to I2A04A;
grant select on communeLA to I2A04A;
--2
grant update(adresse) on distributionLA to I2A04A;
--3
grant insert on distributionLA to I2A04A;
--4
revoke select on distributionLA from I2A04A;
revoke select on operateurLA from I2A04A;
revoke select on communeLA from I2A04A;
revoke update on distributionLA from I2A04A;
revoke insert on distributionLA from I2A04A;
--5
create or replace view q5 as select id, numfo, code_insee, adresse,status
from distributionLA;
grant select on q5 to I2A04A;
--6
create or replace view nb4gLA as select c.NOM_COMMUNE, count(*) as NB_ANT_4G
from distributionLA d, communeLA c, operateurLA o
where c.CODE_INSEE = d.CODE_INSEE
and o.NUMFO = d.NUMFO
and o.GENERATION = '4G'
group by c.NOM_COMMUNE;

create or replace view nb5gLA as select c.NOM_COMMUNE, count(*) as NB_ANT_5G
from distributionLA d, communeLA c, operateurLA o
where c.CODE_INSEE = d.CODE_INSEE
and o.NUMFO = d.NUMFO
and o.GENERATION = '5G'
group by c.NOM_COMMUNE;

grant select on nb4gLA to I2A04A;
grant select on nb5gLA to I2A04A;

--7
create or replace view nbopLA as select c.NOM_COMMUNE, o.NOMFO, count(*) as NB_ANTENNES
from distributionLA d, communeLA c, operateurLA o
where c.CODE_INSEE = d.CODE_INSEE
and o.NUMFO = d.NUMFO
and (o.GENERATION = '4G' or o.GENERATION = '5G')
group by c.NOM_COMMUNE, o.NOMFO
order by 1, 2;

grant select on nbopLA to I2A04A;

--8
create or replace view nbtechLA as select c.NOM_COMMUNE, o.TECHNOLOGIE, count(*) as NB_ANTENNES
from distributionLA d, communeLA c, operateurLA o
where c.CODE_INSEE = d.CODE_INSEE
and o.NUMFO = d.NUMFO
and (o.GENERATION = '4G' or o.GENERATION = '5G')
group by c.NOM_COMMUNE, o.TECHNOLOGIE
order by 1, 2;

grant select on nbtechLA to I2A04A;
```
```sql
-- 1
GRANT SELECT ON COMMUNEVE TO I2A02B;
GRANT SELECT ON DISTRIBUTIONVE TO I2A02B;
GRANT SELECT ON OPERATEURVE TO I2A02B;
-- 2
GRANT UPDATE(ADRESSE) ON DISTRIBUTIONVE TO I2A02B;
-- 3
GRANT INSERT ON DISTRIBUTIONVE TO I2A02B;
-- 4
REVOKE SELECT ON COMMUNEVE FROM I2A02B;
REVOKE SELECT ON DISTRIBUTIONVE FROM I2A02B;
REVOKE SELECT ON OPERATEURVE FROM I2A02B;
REVOKE UPDATE ON DISTRIBUTIONVE FROM I2A02B;
REVOKE INSERT ON DISTRIBUTIONVE FROM I2A02B;
-- 5
CREATE OR REPLACE VIEW Q5 AS SELECT ID,NUMFO,CODE_INSEE,ADRESSE,STATUT
FROM DISTRIBUTIONVE;
GRANT SELECT ON Q5 TO I2A02B;
-- 6
CREATE OR REPLACE VIEW NB4GVE AS SELECT C.NOM_COMMUNE, COUNT(*) NB_ANT_4G
FROM DISTRIBUTIONVE D, COMMUNEVE C, OPERATEURVE O
WHERE C.CODE_INSEE = D.CODE_INSEE
AND O.NUMFO = D.NUMFO
AND O.GENERATION = '4G'
GROUP BY C.NOM_COMMUNE;

CREATE OR REPLACE VIEW NB5GVE AS SELECT C.NOM_COMMUNE, COUNT(*) NB_ANT_5G
FROM DISTRIBUTIONVE D, COMMUNEVE C, OPERATEURVE O
WHERE C.CODE_INSEE = D.CODE_INSEE
AND O.NUMFO = D.NUMFO
AND O.GENERATION = '5G'
GROUP BY C.NOM_COMMUNE;

GRANT SELECT ON NB4GVE TO I2A02B;
GRANT SELECT ON NB5GVE TO I2A02B;
```

### Question 2

```sql
```
```sql
```
