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

### Privileges

```sql
grant select on distributionLA to I2A04A;
grant select on operateurLA to I2A04A;
grant select on communeLA to I2A04A;
```

# CÉLA

```sql
grant update(adresse) on distributionLA to I2A04A
```
