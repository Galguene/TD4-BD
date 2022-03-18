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
create or replace view opnb4gLA as select c.code_insee, c.nom_commune, o.nomfo, count(*) nb_ant_4g
from distributionLA d, communeLA c, operateurLA o
where c.code_insee = d.code_insee
and o.numfo = d.numfo
and o.generation = '4G'
group by c.code_insee, c.nom_commune,o.nomfo;

create or replace view opnb5gLA as select c.code_insee, c.nom_commune, o.nomfo, count(*) nb_ant_5g
from distributionLA d, communeLA c, operateurLA o
where c.code_insee = d.code_insee
and o.numfo = d.numfo
and o.generation = '5G'
group by c.code_insee, c.nom_commune,o.nomfo;

create or replace view opnbLA as select c.nom_commune, opnb4gLA.nomfo, nb_ant_4g, nb_ant_5g
from communeLA c, opnb4gLA, opnb5gLA
where c.code_insee = opnb4gLA.code_insee
and c.code_insee = opnb5gLA.code_insee
and opnb4gLA.nomfo = opnb5gLA.nomfo
order by 1,2;

grant select on opnbLA to I2A04A;
--8
--9
create or replace view nb5g4gorangeLA as select l.nom_commune, l.nb_ant_5g, o.nb_ant_4g as nb_ant_4g_orange
from (
    select nom_commune, sum(nb_ant_5g) nb_ant_5g
    from opnbLA
    group by nom_commune
    order by 1
    ) l,
     (
        select *
        from opnb4gLA
        where nomfo = 'ORANGE'
        ) o
where o.nom_commune = l.nom_commune
order by 2 desc;

grant select on nb5g4gorangeLA to I2A04A;

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
-- 7
CREATE OR REPLACE VIEW OPNB4GVE AS SELECT C.CODE_INSEE, C.NOM_COMMUNE, O.NOMFO, COUNT(*) NB_ANT_4G
FROM DISTRIBUTIONVE D, COMMUNEVE C, OPERATEURVE O
WHERE C.CODE_INSEE = D.CODE_INSEE
AND O.NUMFO = D.NUMFO
AND O.GENERATION = '4G'
GROUP BY C.CODE_INSEE, C.NOM_COMMUNE,O.NOMFO;

CREATE OR REPLACE VIEW OPNB5GVE AS SELECT C.CODE_INSEE, C.NOM_COMMUNE, O.NOMFO, COUNT(*) NB_ANT_5G
FROM DISTRIBUTIONVE D, COMMUNEVE C, OPERATEURVE O
WHERE C.CODE_INSEE = D.CODE_INSEE
AND O.NUMFO = D.NUMFO
AND O.GENERATION = '5G'
GROUP BY C.CODE_INSEE, C.NOM_COMMUNE,O.NOMFO;

CREATE OR REPLACE VIEW OPNBVE AS SELECT C.NOM_COMMUNE, OPNB4GVE.NOMFO, NB_ANT_4G, NB_ANT_5G
FROM COMMUNEVE C, OPNB4GVE, OPNB5GVE
WHERE C.CODE_INSEE = OPNB4GVE.CODE_INSEE
AND C.CODE_INSEE = OPNB5GVE.CODE_INSEE
AND OPNB4GVE.NOMFO = OPNB5GVE.NOMFO
ORDER BY 1,2;
```

### Question 2

```sql
--1
create role i2a02bmon_ami;
set role i2a02bmon_ami;
--2
grant select on distributionLA to i2a02bmon_ami;
grant select on operateurLA to i2a02bmon_ami;
grant select on communeLA to i2a02bmon_ami;
grant update(adresse) on distributionLA to i2a02bmon_ami;
grant insert on distributionLA to i2a02bmon_ami;
set role i2a02bmon_ami;
--3
grant i2a02bmon_ami to I2A04A;
--4
revoke select on distributionLA from i2a02bmon_ami;
revoke select on operateurLA from i2a02bmon_ami;
revoke select on communeLA from i2a02bmon_ami;
set role i2a02bmon_ami;
--5
revoke i2a02bmon_ami from I2A04A;
--6
create role i2a02bvoir_mes_tables;
grant select on distributionLA to i2a02bvoir_mes_tables;
grant select on operateurLA to i2a02bvoir_mes_tables;
grant select on communeLA to i2a02bvoir_mes_tables
set role i2a02bvoir_mes_tables;
create role i2a02bmaj_mes_tables;
grant update(adresse) on distributionLA to i2a02bmaj_mes_tables;
grant insert on distributionLA to i2a02bmaj_mes_tables;
set role i2a02bmaj_mes_tables;
--7
create role i2a02bvoir_update;
grant i2a02bmaj_mes_tables to i2a02bvoir_update;
grant i2a02bvoir_mes_tables to i2a02bvoir_update;
set role i2a02bvoir_update;
--8
grant i2a02bvoir_update to I2A04A;
--9
revoke i2a02bmaj_mes_tables from i2a02bvoir_update;
set role i2a02bvoir_update;
```
```sql
```
