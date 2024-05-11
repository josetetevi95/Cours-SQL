## Exercice :

Base de données Karaoké
Le propriétaire d’un bar-karaoké fait appel à vous pour l’étude des usages de ses clients (en matière de karaoké). Cette étude lui permettra de mieux comprendre leurs habitudes.
Afin de réaliser cette étude, vous disposez de la base de données ci-dessous, qui contient 3 tables :

```sql
- Interprétation (nInter, date, heureDébut, nCli, nChanson)

- Personne (nPers, nom, prénom, dateNaiss, langue)

- Chanson (nChanson, titreChanson, typeMusique, nPro, durée)

```

Les clés primaires sont soulignées d’un trait plein, les clés étrangères sont soulignées en pointillés.
La table Interprétation enregistre chaque interprétation réalisée par un client individuel (les chansons chantées en groupe ne sont pas enregistrées dans la base de données). On connaît la date et l’heure de l’interprétation, ainsi que l’identifiant du client et celui de la chanson.
L’attribut nCli est une clé étrangère dans la table Interprétation, qui fait référence à l’attribut nPers, clé primaire de la table Personne. On connaît le nom, le prénom, la date de naissance et la langue maternelle de chaque personne.

Enfin, des détails sont également stockés concernant les chansons disponibles sur le catalogue du karaoké. Chaque chanson est identifiées par un numéro, et on connaît son titre (par exemple « Imagine », « Happy », ...), son type de musique (par exemple soul, rap, disco, rock,...), sa durée, ainsi que l’attribut nPro, qui est le numéro de la personne correspondant au chanteur professionnel qui en est l’interprète original. nPro est une clé étrangère qui fait référence à l’attribut nPers de la table Personne, ce qui signifie que l’on a également des informations sur les chanteurs professionnels dans la base de données (nom, prénom, date de naissance et langue maternelle).
Attention donc à ne pas confondre les attributs nCli et nPro :

- nCli est l’identifiant de la personne qui interprète une chanson en tant que client durant le karaoké
- nPro est l’identifiant d’une personne qui est chanteur professionnel, interprète original d’une chanson

## Corrections

1. 

2. Q : Prénom et titre des chansons chantées par les chanteurs amateurs qui ne chantent jamais après 21h en karaoké

__R : Pour obtenir les prénoms et titres des chansons chantées par les chanteurs amateurs qui ne chantent jamais après 21h en karaoké, vous pouvez utiliser la requête suivante__

```sql
SELECT DISTINCT p.prenom, c.titreChanson
FROM Personne p, Interprétation i, Chanson c
WHERE p.nPers = i.nCli
AND i.nChanson = c.nChanson
AND p.nPers NOT IN (
    SELECT i2.nCli
    FROM Interprétation i2
    WHERE i2.heureDébut >= '21:00:00'
)

```
Cette requête effectue les étapes suivantes :

- Sélectionne les prénoms et les titres de chansons dans les tables "Personne" et "Chanson".

- Effectue une jointure avec la table "Interprétation" pour récupérer les interprétations.

- Filtre les résultats pour exclure les personnes qui chantent après 21h.

- Utilise DISTINCT pour éliminer les doublons dans les résultats, car une personne peut avoir chanté plusieurs chansons.

Cette requête sélectionnera les prénoms et les titres des chansons chantées par des chanteurs amateurs qui ne chantent jamais après 21h en karaoké.

3. Q : Prénom des chanteurs amateurs anglophones qui ont chanté des chansons de David Bowie le 25/03/2020

__R :
Pour récupérer les prénoms des chanteurs amateurs anglophones qui ont chanté des chansons de David Bowie le 25/03/2020, vous pouvez utiliser la requête suivante :__

```sql
SELECT DISTINCT p.prenom
FROM Personne p, Interprétation i, Chanson c, Personne p_pro
WHERE p.nPers = i.nCli
AND i.nChanson = c.nChanson
AND c.nPro = p_pro.nPers
AND p_pro.nom = 'Bowie'
AND p_pro.prenom = 'David'
AND p.langue = 'anglais'
AND i.date = '2020-03-25'

```

Cette requête sélectionne les prénoms des chanteurs amateurs anglophones qui ont chanté des chansons de David Bowie le 25/03/2020 en reliant les tables via les conditions dans la clause WHERE, sans utiliser explicitement la clause JOIN.


4. Q : Titre et type des chansons interprétées par le chanteur professionnel le plus jeune.

__R : Pour récupérer le titre et le type des chansons interprétées par le chanteur professionnel le plus jeune, vous pouvez utiliser la requête suivante :__

```sql
SELECT titreChanson, typeMusique
FROM Chanson
WHERE nPro = (
    SELECT nPers
    FROM Personne
    WHERE dateNaiss = (
        SELECT MIN(dateNaiss)
        FROM Personne
        WHERE nPers IN (
            SELECT nPro
            FROM Chanson
        )
    )
)
```

Cette requête sélectionne le titre et le type des chansons en se basant sur les conditions suivantes :

- Utilise une sous-requête pour trouver la date de naissance du chanteur professionnel le plus jeune.

- Associe cette date de naissance à la table Chanson pour récupérer les chansons associées à ce chanteur professionnel.

- Utilise la clause JOIN pour joindre les tables Chanson et Personne pour obtenir les détails des chansons et des chanteurs professionnels.

- Sélectionne les titres et les types des chansons.

5. Q: Date de naissance des chanteurs professionnels qui sont les interprètes originaux de chansons de type disco ou rap.

__R : Pour obtenir la date de naissance des chanteurs professionnels qui sont les interprètes originaux de chansons de type disco ou rap sans utiliser la clause JOIN, vous pouvez utiliser des sous-requêtes imbriquées. Voici une approche possible :__

```sql
SELECT dateNaiss
FROM Personne
WHERE nPers IN (
    SELECT DISTINCT nPro
    FROM Chanson
    WHERE typeMusique IN ('disco', 'rap')
)
```
Dans cette requête :

- La sous-requête interne sélectionne tous les identifiants de chanteurs professionnels associés à des chansons de type disco ou rap.

- La requête principale sélectionne les dates de naissance des personnes dont l'identifiant est associé à la sous-requête interne, ce qui signifie qu'ils sont les interprètes originaux de chansons de type disco ou rap.


6. Q : Langue maternelle des chanteurs amateurs qui ont chanté la chanson intitulée « Imagine » le 1er janvier 2020 et le 1er janvier 2019.

__R: Pour obtenir la langue maternelle des chanteurs amateurs qui ont chanté la chanson intitulée "Imagine" le 1er janvier 2020 et le 1er janvier 2019 sans utiliser explicitement JOIN, vous pouvez utiliser des sous-requêtes imbriquées. Voici comment vous pourriez le faire :__

```sql
SELECT DISTINCT langue
FROM Personne
WHERE nPers IN (
    SELECT DISTINCT nCli
    FROM Interprétation
    WHERE nChanson IN (
        SELECT nChanson
        FROM Chanson
        WHERE titreChanson = 'Imagine'
    )
    AND (date = '2020-01-01' OR date = '2019-01-01')
)
AND nPers NOT IN (
    SELECT DISTINCT nPro
    FROM Chanson
)

```
Dans cette requête :

- La sous-requête interne sélectionne les identifiants des chanteurs amateurs qui ont interprété la chanson "Imagine" le 1er janvier 2020 et le 1er janvier 2019.

- La sous-requête la plus interne sélectionne les identifiants des chansons avec le titre "Imagine".

- La requête principale sélectionne les langues maternelles des chanteurs amateurs associées aux identifiants obtenus dans la première sous-requête, en excluant ceux qui sont également des chanteurs professionnels.

7. Q: Nombre de chansons associées à chaque chanteur professionnel (uniquement pour ceux qui possèdent plus de 10 chansons dans leur répertoire)

__R: Pour obtenir le nombre de chansons associées à chaque chanteur professionnel, mais uniquement pour ceux qui possèdent plus de 10 chansons dans leur répertoire, vous pouvez utiliser une requête avec une clause GROUP BY et une condition HAVING. Voici comment vous pourriez le faire :__


```sql
SELECT p_pro.nom, p_pro.prenom, COUNT(c.nChanson) AS nombre_de_chansons
FROM Personne p_pro, Chanson c
WHERE p_pro.nPers = c.nPro
GROUP BY p_pro.nPers, p_pro.nom, p_pro.prenom
HAVING COUNT(c.nChanson) > 10

```

Dans cette requête :

- Nous sélectionnons les noms et prénoms des chanteurs professionnels ainsi que le nombre de chansons associées à chacun d'eux.

- Nous faisons une jointure implicite entre les tables Personne (pour les chanteurs professionnels) et Chanson en utilisant les identifiants respectifs.

- Nous utilisons la clause GROUP BY pour regrouper les résultats par chanteur professionnel.

- Nous utilisons la clause HAVING pour filtrer les résultats et ne montrer que les chanteurs professionnels qui ont plus de 10 chansons dans leur répertoire.

8. Q: Prénom, date de naissance et langue maternelle des chanteurs amateurs qui ont chanté en 2019 une chanson dont le titre finit par « forever », triés du plus jeune au plus âgé.

__R: Pour obtenir le prénom, la date de naissance et la langue maternelle des chanteurs amateurs qui ont chanté en 2019 une chanson dont le titre se termine par "forever", triés du plus jeune au plus âgé, vous pouvez utiliser la requête suivante :__

```sql 
SELECT DISTINCT p.prenom, p.dateNaiss, p.langue
FROM Personne p, Interprétation i, Chanson c
WHERE p.nPers = i.nCli
AND i.nChanson = c.nChanson
AND p.nPers NOT IN (
    SELECT DISTINCT nPro
    FROM Chanson
)
AND c.titreChanson LIKE '%forever'
AND EXTRACT(YEAR FROM i.date) = 2019
ORDER BY p.dateNaiss ASC

```
Dans cette requête :

- Nous sélectionnons les prénoms, les dates de naissance et les langues maternelles des chanteurs amateurs.

- Nous utilisons une jointure implicite entre les tables Personne (pour les chanteurs amateurs), Interprétation et Chanson en utilisant les identifiants respectifs.

- Nous excluons les chanteurs professionnels en vérifiant s'ils ne sont pas présents dans la table Chanson.

- Nous filtrons les chansons dont le titre se termine par "forever" en utilisant la clause LIKE.
Nous filtrons les interprétations qui ont eu lieu en 2019 en extrayant l'année de la date.

- Enfin, nous ordonnons les résultats du plus jeune au plus âgé en utilisant la clause ORDER BY sur la date de naissance.

9. Nombre de chansons chantées en karaoké par le chanteur amateur Jean Dupont à chacun de ses karaokés. On indiquera, pour chaque date où il a participé à un karaoké, le nombre de chansons interprétées.

__R: Pour obtenir le nombre de chansons chantées en karaoké par le chanteur amateur Jean Dupont à chacun de ses karaokés, ainsi que le nombre de chansons interprétées pour chaque date, vous pouvez utiliser la requête suivante :__

```sql 
SELECT i.date, COUNT(*) AS nombre_de_chansons
FROM Interprétation i, Personne p
WHERE i.nCli = p.nPers
AND p.nom = 'Dupont'
AND p.prenom = 'Jean'
GROUP BY i.date

```
Dans cette requête :

- Nous sélectionnons les dates des interprétations et comptons le nombre de chansons interprétées à chaque date.

- Nous utilisons une jointure implicite entre les tables Interprétation et Personne (pour les clients) en utilisant les identifiants respectifs.

- Nous filtrons les résultats pour ne sélectionner que les interprétations où le nom est "Dupont" et le prénom est "Jean".

- Nous utilisons la clause GROUP BY pour regrouper les résultats par date d'interprétation.

