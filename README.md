# Resultat des TP de Marie
```Exercice invitation des stars
```

-- creation de la table
CREATE TABLE IF NOT EXISTS `inv_personne` (
  `id` int NOT NULL AUTO_INCREMENT,
  `nom` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `prenom` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `age` int NOT NULL,
  `date_inscription` date NOT NULL,
  `statut` tinyint(1) NOT NULL,
  `type` enum('membre','non membre') COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `description` mediumtext COLLATE utf8mb4_unicode_ci,
  `salaire_annuel` int DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB;


-- salaire le plus petit


SELECT 
salaire_annuel
FROM inv_personne
ORDER BY salaire_annuel ASC limit 1;

Select MIN(salaire_annuel)
from inv_personne;


--salaire le plus grand
SELECT 
salaire_annuel
FROM inv_personne
ORDER BY salaire_annuel DESC limit 1;

Nom du salaire le plus petit
SELECT 
nom,prenom
FROM inv_personne
ORDER BY salaire_annuel ASC limit 1;

Nom du salaire le plus grand
SELECT 
nom,prenom
FROM inv_personne
ORDER BY salaire_annuel DESC limit 1;

-- moyenne salaire
SELECT AVG(salaire_annuel) FROM inv_personne;

-- afficher le nombre de client
SELECT 
COUNT(salaire_annuel)
FROM inv_personne;

--proposer une requete avec BETWEEN
SELECT 
nom,prenom
FROM inv_personne
WHERE salaire_annuel BETWEEN 0 AND 2000000;

SELECT 
nom,prenom
FROM inv_personne
WHERE salaire_annuel > 0 AND < 2000000;

--proposer une requete avec UCASE(), UPPER(),LCASE(),LOWER()
SELECT UCASE(nom) AS Nom_majuscule
FROM inv_personne; 

SELECT lower(nom) AS Nom_minuscule
FROM inv_personne; 

--proposer une requete avec LIKE '%..%'
SELECT * FROM inv_personne
WHERE nom LIKE 'c%'; 

--Trier par age les membres
SELECT 
nom,prenom
FROM inv_personne
ORDER BY age DESC ;

``` Exercice CRM
```

-- creation tables

CREATE DATABASE IF NOT EXISTS customer_relationship_management;
use customer_relationship_management;
CREATE TABLE client(
    id_client INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    nom VARCHAR(255) NOT NULL
)ENGINE=InnoDB;
CREATE TABLE projet(
    id_projet INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    id_client INT,
    nom VARCHAR(255) NOT NULL
)ENGINE=InnoDB;
CREATE TABLE devis(
    id_devis INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    version INT NOT NULL,
    reference VARCHAR(10),
    prix FLOAT NOT NULL,
    id_projet INT
)ENGINE=InnoDB;
CREATE TABLE facture(
    id_facture INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    reference VARCHAR(10) NOT NULL,
    info VARCHAR(255) NOT NULL,
    total FLOAT NOT NULL,
    date_crea date NOT NULL,
    date_paiement date,
    id_devis INT
)ENGINE=InnoDB;

-- création des liens

use customer_relationship_management;
alter TABLE projet add FOREIGN key (id_client) REFERENCES client (id_client) on delete cascade;
alter TABLE devis add FOREIGN key (id_projet) REFERENCES projet (id_projet) on delete cascade;
alter TABLE facture add FOREIGN key (id_devis) REFERENCES devis (id_devis) on delete cascade;
USE customer_relationship_management;

-- insertion des valeurs

INSERT INTO client (nom) VALUES 
	('Mairie de Rennes'),
	('Neo Soft'),
	('Sopra'),
	('Accenture'),
	('Amazon');

INSERT INTO projet (nom, id_client) VALUES
	('Création de site internet', 1),
	('Creationde site internet', 1),
	('Logiciel CRM', 2),
	('Logiciel de devis', 3),
	('Site internet e-commerce', 4),
	('Logiciel ERP', 2),
	('Logicielgestion de stock',4);

    
INSERT INTO devis (version,reference, prix, id_projet) VALUES
	('1','DEV2100A', 3000, 1),
	('2','DEV2100B', 5000, 2),
	('1','DEV2100C', 5000, 3),
	('1','DEV2100D', 3000, 4),
	('1','DEV2100E', 5000, 5),
	('1','DEV2100F', 2000, 6),
	('1','DEV2100G', 1000, 7);

INSERT INTO facture (reference,info,total,id_devis,date_crea,date_paiement)	
    VALUES
	('FA001', 'site internet partie 1', 1500, 1, '2023-09-01','2023-10-01'),
	('FA002', 'site internet partie 2', 1500, 1, '2023-09-20',null),
	('FA003', 'logiciel CRM', 5000, 2, '2024-02-01',null),
	('FA004', 'logiciel devis', 3000, 3, '2024-03-03','2024-04-03'),
	('FA005', 'site ecommerce', 5000, 4, '2024-04-01',null),
	('FA006', 'logiciel ERP', 2000, 2, '2024-04-01',null);


-- 1 - Afficher toutes les factures avec le nom des clients
SELECT facture.id_facture,client.nom
FROM facture
INNER JOIN devis ON facture.id_devis = devis.id_devis
INNER JOIN projet ON devis.id_projet = projet.id_projet
INNER JOIN client ON projet.id_client = client.id_client;

-- 2 - Afficher le nombre de factures par client afficher 0 factures si il n'y a pas de factures

SELECT client.nom, COUNT(facture.id_facture) AS nombre_de_factures
FROM client
LEFT JOIN projet ON client.id_client = projet.id_client
LEFT join devis on projet.id_projet = devis.id_projet
LEFT JOIN facture ON devis.id_devis = facture.id_devis
GROUP BY client.nom;

-- 3 - afficher le chiffre d'affaire par client

SELECT client.nom, sum(facture.total) AS chiffre_affaire
FROM client
LEFT JOIN projet ON client.id_client = projet.id_client
LEFT join devis on projet.id_projet = devis.id_projet
LEFT JOIN facture ON devis.id_devis = facture.id_devis
group by client.nom;

-- 4 - afficher le CA total

SELECT sum(facture.total) AS chiffre_affaire
FROM facture;

-- 5 - afficher la somme des factures en attente de paiement

SELECT sum(facture.total) AS chiffre_affaire
FROM facture
where date_paiement IS NULL group by;

-- 6 - afficher les factures en retard de paiment 30 jours max avec le nombre de jours de retard

SELECT facture.id_facture , facture.date_crea,facture.date_paiement, datediff(now(), facture.date_crea)
FROM facture
WHERE date_paiement IS NULL
	AND DATEDIFF(NOW(), date_crea) < 30;

```exercice chat 1 table
```

-- 1 - Créer la data base zoo
create DATABASE if not EXISTS zoo;
-- 2 - Créer la table chat
USE zoo;
create TABLE chat(
    id_chat INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    nom VARCHAR(255) NOT NULL,
    yeux VARCHAR(255) NOT NULL,
    age int
)ENGINE=InnoDB;

-- 3- ajouter les données
INSERT INTO chat (nom,yeux,age)	
    VALUES
	('Main coon', 'marron', 20),
	('Siamois', 'bleu', 15),
	('Bengal', 'marron', 18),
    ('Scottish', 'Marron', 10);

-- 3 - Afficher le chat avec l'id :2

select * from chat
	WHERE id_chat=2;

-- 4 - Trier les chats par nom et par age

select * from chat
	Order by age ASC;

-- 5 - Afficher les chats qui vive entre 11 et 19 ans

select * from chat
	where age between 11 and 19;

-- 5bis - Afficher le ou les chats dont le nom contient 'sia'

select * from chat
	WHERE nom LIKE '%sia%';


-- 6 - Afficher la moyenne d'age des chats

select avg(age) from chat;

-- 7 - Afficher le nombre de chats dans la table

select count(id_chat) from chat;


```exercice chats 2 tables
```


create DATABASE if not EXISTS spa;
-- 2 - Créer la table chat
USE spa;
create TABLE couleur(
    id_couleur INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    couleur VARCHAR(255) NOT NULL
)ENGINE=InnoDB;

create TABLE chat(
    id_chat INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    nom VARCHAR(255) NOT NULL,
    yeux int NOT NULL,
    age int
)ENGINE=InnoDB;



ALTER TABLE chat ADD FOREIGN KEY (yeux) REFERENCES couleur(id_couleur) on DELETE RESTRICT;

-- 3- ajouter les données
INSERT INTO couleur (id_couleur,couleur)
	VALUES
    (1,'marron'),
    (2,'bleu');
    
INSERT INTO chat (nom,yeux,age)	
    VALUES
	('Main coon', 1, 20),
	('Siamois', 2, 15),
	('Bengal', 1, 18),
    ('Scottish', 1, 10);
     
-- afficher le nom du chat et la couleir des yeux
use spa;
SELECT chat.nom,couleur.couleur as couleur_des_yeux
FROM chat
INNER JOIN couleur ON chat.yeux = couleur.id_couleur;

-- les bronzées font du ski

create database if not EXISTS location_ski;
use location_ski;
create table clients(
    noCli INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    nom VARCHAR(30) NOT NULL,
    prenom VARCHAR(30),
    adresse VARCHAR(120),
    cpo VARCHAR(5) NOT NULL,
    ville VARCHAR(80) NOT NULL
)ENGINE=InnoDB;

CREATE TABLE fiches(
    noFic INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    noCli INT NOT NULL,
    dateCrea datetime NOT NULL,
    datePaye datetime,
    etat enum('SO','RE','EC') NOT NULL
)ENGINE=InnoDB;

Alter table fiches add foreign key (noCli) references clients(noCli) on delete RESTRICT;

use location_ski;
CREATE TABLE lignesfic(
    noFic INT NOT NULL ,
    noLig INT NOT NULL AUTO_INCREMENT,
    refart char(8) NOT NULL,
    depart datetime not null,
    retour datetime,
    PRIMARY KEY(noLig,noFic)
)ENGINE=InnoDB;

Alter table lignesfic add foreign key (noFic) references fiches(noFic) on delete RESTRICT;

use location_ski;
CREATE TABLE articles(
    refart CHAR(8) NOT NULL PRIMARY KEY,
    designation varchar(80) NOT NULL,
    codeGam char(5),
    codeCate char(5)
)ENGINE=InnoDB;

Alter table lignesfic add foreign key (refart) references articles(refart) on delete RESTRICT;

use location_ski;
CREATE TABLE gammes(
    codeGam char(5) NOT null PRIMARY KEY,
    libelle varchar(45) NOT null
)ENGINE=InnoDB;

CREATE TABLE categories(
    codeCate char(5) NOT null PRIMARY KEY,
    libelle varchar(30) NOT null
)ENGINE=InnoDB;

CREATE TABLE grilletarifs(
    codeGam char(5) NOT null,
    codeCate char(5) NOT null,
    codeTarif char(5),
    PRIMARY KEY (codeGam,codeCate)
)ENGINE=InnoDB;

use location_ski;
Alter table grilletarifs add foreign key (codeGam) references gammes(codeGam) on delete RESTRICT;

use location_ski;
Alter table grilletarifs add foreign key (codeCate) references categories(codeCate) on delete RESTRICT;

use location_ski;
Alter table articles add foreign key (codeCate) references grilletarifs(codeCate) on delete RESTRICT;
Alter table articles add foreign key (codeGam) references grilletarifs(codeGam) on delete RESTRICT;

use location_ski;
CREATE TABLE tarifs(
    codeTarif char(5) NOT null PRIMARY KEY,
    libelle varchar(30) NOT null,
    prixJour decimal(5) NOT null
)ENGINE=InnoDB;

Alter table grilletarifs add foreign key (codeTarif) references tarifs(codeTarif) on delete RESTRICT;


USE location_ski;
INSERT INTO clients (noCli,nom,prenom,adresse,cpo,ville) 
	VALUES 
    (1,'Albert', 'Anatole', 'Rue des accacias', '61000', 'Amiens'),
	(2,'Bernard', 'Barnabé', 'Rue du bar', '1000', 'Bourg en Bresse'),
	(3,'Dupond', 'Camille', 'Rue Crébillon', '44000', 'Nantes'),
	(4,'Desmoulin', 'Daniel', 'Rue descendante', '21000', 'Dijon'),
	(5,'Ernest', 'Etienne', 'Rue de l’échaffaud', '42000', 'Saint Étienne'),
	(6,'Ferdinand', 'François', 'Rue de la convention', '44100', 'Nantes'),
	(9,'Dupond', 'Jean', 'Rue des mimosas', '75018', 'Paris'),
	(14,'Boutaud', 'Sabine', 'Rue des platanes', '75002', 'Paris');

INSERT INTO fiches (noFic,noCli,dateCrea,datePaye,etat) 
	VALUES 
    (1001, 14,  DATE_SUB(NOW(),INTERVAL  15 DAY), DATE_SUB(NOW(),INTERVAL  13 DAY),'SO'),
	(1002, 4,  DATE_SUB(NOW(),INTERVAL  13 DAY), NULL, 'EC'),
	(1003, 1,  DATE_SUB(NOW(),INTERVAL  12 DAY), DATE_SUB(NOW(),INTERVAL  10 DAY),'SO'),
	(1004, 6,  DATE_SUB(NOW(),INTERVAL  11 DAY), NULL, 'EC'),
	(1005, 3,  DATE_SUB(NOW(),INTERVAL  10 DAY), NULL, 'EC'),
	(1006, 9,  DATE_SUB(NOW(),INTERVAL  10 DAY),NULL ,'RE'),
	(1007, 1,  DATE_SUB(NOW(),INTERVAL  3 DAY), NULL, 'EC'),
	(1008, 2,  DATE_SUB(NOW(),INTERVAL  0 DAY), NULL, 'EC');


INSERT INTO categories (codeCate, libelle) 
	VALUES ('MONO', 'Monoski'),
	('SURF', 'Surf'),
	('PA', 'Patinette'),
	('FOA', 'Ski de fond alternatif'),
	('FOP', 'Ski de fond patineur'),
	('SA', 'Ski alpin');

 USE location_ski;
INSERT INTO tarifs(codeTarif, libelle, prixJour) 
	VALUES 
    ('T1', 'Base', 10),
	('T2', 'Chocolat', 15),
	('T3', 'Bronze', 20),
	('T4', 'Argent', 30),
	('T5', 'Or', 50),
	('T6', 'Platine', 90);
    

INSERT INTO gammes (codeGam, libelle) 
	VALUES 
    ('PR', 'Matériel Professionnel'),
	('HG', 'Haut de gamme'),
	('MG', 'Moyenne gamme'),
	('EG', 'Entrée de gamme');

  INSERT INTO grilletarifs (codeGam, codeCate, codeTarif)
	VALUES 
    ('EG', 'MONO', 'T1'),
	('MG', 'MONO', 'T2'),
	('EG', 'SURF', 'T1'),
	('MG', 'SURF', 'T2'),
	('HG', 'SURF', 'T3'),
	('PR', 'SURF', 'T5'),
	('EG', 'PA', 'T1'),
	('MG', 'PA', 'T2'),
	('EG', 'FOA', 'T1'),
	('MG', 'FOA', 'T2'),
	('HG', 'FOA', 'T4'),
	('PR', 'FOA', 'T6'),
	('EG', 'FOP', 'T2'),
	('MG', 'FOP', 'T3'),
	('HG', 'FOP', 'T4'),
	('PR', 'FOP', 'T6'),
	('EG', 'SA', 'T1'),
	('MG', 'SA', 'T2'),
	('HG', 'SA', 'T4'),
	('PR', 'SA', 'T6');

  USE location_ski;
INSERT INTO articles (refart, designation, codeGam, codeCate) 
	VALUES 
    ('F01', 'Fischer Cruiser', 'EG', 'FOA'),
	('F02', 'Fischer Cruiser', 'EG', 'FOA'),
	('F03', 'Fischer Cruiser', 'EG', 'FOA'),
	('F04', 'Fischer Cruiser', 'EG', 'FOA'),
	('F05', 'Fischer Cruiser', 'EG', 'FOA'),
	('F10', 'Fischer Sporty Crown', 'MG', 'FOA'),
	('F20', 'Fischer RCS Classic GOLD', 'PR', 'FOA'),
	('F21', 'Fischer RCS Classic GOLD', 'PR', 'FOA'),
	('F22', 'Fischer RCS Classic GOLD', 'PR', 'FOA'),
	('F23', 'Fischer RCS Classic GOLD', 'PR', 'FOA'),
	('F50', 'Fischer SOSSkating VASA', 'HG', 'FOP'),
	('F60', 'Fischer RCS CARBOLITE Skating', 'PR', 'FOP'),
	('F61', 'Fischer RCS CARBOLITE Skating', 'PR', 'FOP'),
	('F62', 'Fischer RCS CARBOLITE Skating', 'PR', 'FOP'),
	('F63', 'Fischer RCS CARBOLITE Skating', 'PR', 'FOP'),
	('F64', 'Fischer RCS CARBOLITE Skating', 'PR', 'FOP'),
	('P01', 'Décathlon Allegre junior 150', 'EG', 'PA'),
	('P10', 'Fischer mini ski patinette', 'MG', 'PA'),
	('P11', 'Fischer mini ski patinette', 'MG', 'PA'),
	('S01', 'Décathlon Apparition', 'EG', 'SURF'),
	('S02', 'Décathlon Apparition', 'EG', 'SURF'),
	('S03', 'Décathlon Apparition', 'EG', 'SURF'),
	('A01', 'Salomon 24X+Z12', 'EG', 'SA'),
	('A02', 'Salomon 24X+Z12', 'EG', 'SA'),
	('A03', 'Salomon 24X+Z12', 'EG', 'SA'),
	('A04', 'Salomon 24X+Z12', 'EG', 'SA'),
	('A05', 'Salomon 24X+Z12', 'EG', 'SA'),
	('A10', 'Salomon Pro Link Equipe 4S', 'PR', 'SA'),
	('A11', 'Salomon Pro Link Equipe 4S', 'PR', 'SA'),
	('A21', 'Salomon 3V RACE JR+L10', 'PR', 'SA');


INSERT INTO lignesfic (noFic, noLig, refart, depart, retour) 
	VALUES 
    (1001, 1, 'F05', DATE_SUB(NOW(),INTERVAL  15 DAY), DATE_SUB(NOW(),INTERVAL  13 DAY)),
	(1001, 2, 'F50', DATE_SUB(NOW(),INTERVAL  15 DAY), DATE_SUB(NOW(),INTERVAL  14 DAY)),
	(1001, 3, 'F60', DATE_SUB(NOW(),INTERVAL  13 DAY), DATE_SUB(NOW(),INTERVAL  13 DAY)),
	(1002, 1, 'A03', DATE_SUB(NOW(),INTERVAL  13 DAY), DATE_SUB(NOW(),INTERVAL  9 DAY)),
	(1002, 2, 'A04', DATE_SUB(NOW(),INTERVAL  12 DAY), DATE_SUB(NOW(),INTERVAL  7 DAY)),
	(1002, 3, 'S03', DATE_SUB(NOW(),INTERVAL  8 DAY), NULL),
	(1003, 1, 'F50', DATE_SUB(NOW(),INTERVAL  12 DAY), DATE_SUB(NOW(),INTERVAL  10 DAY)),
	(1003, 2, 'F05', DATE_SUB(NOW(),INTERVAL  12 DAY), DATE_SUB(NOW(),INTERVAL  10 DAY)),
	(1004, 1, 'P01', DATE_SUB(NOW(),INTERVAL  6 DAY), NULL),
	(1005, 1, 'F05', DATE_SUB(NOW(),INTERVAL  9 DAY), DATE_SUB(NOW(),INTERVAL  5 DAY)),
	(1005, 2, 'F10', DATE_SUB(NOW(),INTERVAL  4 DAY), NULL),
	(1006, 1, 'S01', DATE_SUB(NOW(),INTERVAL  10 DAY), DATE_SUB(NOW(),INTERVAL  9 DAY)),
	(1006, 2, 'S02', DATE_SUB(NOW(),INTERVAL  10 DAY), DATE_SUB(NOW(),INTERVAL  9 DAY)),
	(1006, 3, 'S03', DATE_SUB(NOW(),INTERVAL  10 DAY), DATE_SUB(NOW(),INTERVAL  9 DAY)),
	(1007, 1, 'F50', DATE_SUB(NOW(),INTERVAL  3 DAY), DATE_SUB(NOW(),INTERVAL  2 DAY)),
	(1007, 3, 'F60', DATE_SUB(NOW(),INTERVAL  1 DAY), NULL),
	(1007, 2, 'F05', DATE_SUB(NOW(),INTERVAL  3 DAY), NULL),
	(1007, 4, 'S02', DATE_SUB(NOW(),INTERVAL  0 DAY), NULL),
	(1008, 1, 'S01', DATE_SUB(NOW(),INTERVAL  0 DAY), NULL);


  -- 1 Liste des clients (nom, prénom, adresse, code postal, ville) ayant au moins une fiche de location en cours.
use location_ski;
select * 
	from clients
	INNER JOIN fiches on clients.noCli=fiches.noCli
    where fiches.etat='EC'
    ORDER by clients.noCli ASC;

-- 2 Détail de la fiche de location de M. Dupond Jean de Paris (avec la désignation des articles loués, la date de départ et de retour).
use location_ski;
select clients.nom,clients.prenom,clients.ville,articles.designation,lignesfic.depart,lignesfic.retour
	FROM articles
    left join lignesfic on articles.refart=lignesfic.refart
    left join fiches on lignesfic.noFic=fiches.noFic
    left join clients on fiches.noCli=clients.noCli
    where clients.nom="Dupond" and clients.prenom="Jean";

-- 3 Liste de tous les articles (référence, désignation et libellé de la catégorie) dont le libellé de la catégorie contient ski.
use location_ski;
SELECT articles.refart,articles.designation,categories.libelle
	from categories
    left join grilletarifs on categories.codeCate=grilletarifs.codeCate
    left join articles on grilletarifs.codeCate=articles.codeCate
    left join lignesfic on articles.refart=lignesfic.refart
    left join fiches on lignesfic.noFic=fiches.noFic
    left join clients on fiches.noCli=clients.noCli;

-- 4 Calcul du montant de chaque fiche soldée .
use location_ski;
select fiches.noFic,fiches.etat,sum(tarifs.prixJour)
	from tarifs
    left join grilletarifs on tarifs.codeTarif=grilletarifs.codeTarif
    left join articles on grilletarifs.codeCate=articles.codeCate
    left join lignesfic on articles.refart=lignesfic.refart
    left join fiches on lignesfic.noFic=fiches.noFic
    left join clients on fiches.noCli=clients.noCli
    where fiches.etat="SO"
    group by fiches.noFic;

-- 4b Calcul du montant total des fiches.
use location_ski;
select fiches.noFic,fiches.etat,sum(tarifs.prixJour)
	from tarifs
    left join grilletarifs on tarifs.codeTarif=grilletarifs.codeTarif
    left join articles on grilletarifs.codeCate=articles.codeCate
    left join lignesfic on articles.refart=lignesfic.refart
    left join fiches on lignesfic.noFic=fiches.noFic
    left join clients on fiches.noCli=clients.noCli
    where fiches.etat="SO"

-- 5 Calcul du nombre d’articles actuellement en cours de location.
use location_ski;
select count(lignesfic.noLig) as Nb_articles_en_cours_de_location
	from lignesfic
    left join fiches on lignesfic.noFic=fiches.noFic
    left join clients on fiches.noCli=clients.noCli
    where fiches.etat="EC";

-- 6 Calcul du nombre d’articles loués, par client.
use location_ski;
select clients.nom,count(lignesfic.noLig) as Nb_articles_loues
	from lignesfic
    left join fiches on lignesfic.noFic=fiches.noFic
    left join clients on fiches.noCli=clients.noCli
    group by clients.nom;

-- 7 Liste des clients qui ont effectué (ou sont en train d’effectuer) plus de 200€ de location.
USE location_ski;

SELECT clients.nom, clients.prenom, SUM(tarifs.prixJour) AS total_prix
FROM tarifs
RIGHT JOIN grilletarifs ON tarifs.codeTarif = grilletarifs.codeTarif
RIGHT JOIN articles ON grilletarifs.codeCate = articles.codeCate
RIGHT JOIN lignesfic ON articles.refart = lignesfic.refart
RIGHT JOIN fiches ON lignesfic.noFic = fiches.noFic
RIGHT JOIN clients ON fiches.noCli = clients.noCli
GROUP BY clients.nom, clients.prenom
HAVING total_prix > 200;

-- 8 Liste de tous les articles (loués au moins une fois) et le nombre de fois où ils ont été loués, triés du plus loué au moins loué.
USE location_ski;

SELECT articles.refart,articles.designation, count(fiches.noFic)
FROM articles
left JOIN lignesfic ON articles.refart = lignesfic.refart
left JOIN fiches ON lignesfic.noFic = fiches.noFic
left JOIN clients ON fiches.noCli = clients.noCli
GROUP BY articles.designation
having count(fiches.noFic)>0;

-- 9 Liste des fiches (n°, nom, prénom) de moins de 150€.
USE location_ski;

SELECT fiches.noFic,clients.nom, clients.prenom, SUM(tarifs.prixJour) AS total
FROM tarifs
RIGHT JOIN grilletarifs ON tarifs.codeTarif = grilletarifs.codeTarif
RIGHT JOIN articles ON grilletarifs.codeCate = articles.codeCate
RIGHT JOIN lignesfic ON articles.refart = lignesfic.refart
RIGHT JOIN fiches ON lignesfic.noFic = fiches.noFic
RIGHT JOIN clients ON fiches.noCli = clients.noCli
GROUP BY fiches.noFic
HAVING total <150;

-- 10 Calcul de la moyenne des recettes de location de surf (combien peut-on espérer gagner pour une location d'un surf ?).
USE location_ski;

SELECT avg(tarifs.prixJour) AS moyenne
FROM tarifs
RIGHT JOIN grilletarifs ON tarifs.codeTarif = grilletarifs.codeTarif
RIGHT JOIN articles ON grilletarifs.codeCate = articles.codeCate
RIGHT JOIN lignesfic ON articles.refart = lignesfic.refart
RIGHT JOIN fiches ON lignesfic.noFic = fiches.noFic
RIGHT JOIN clients ON fiches.noCli = clients.noCli
where grilletarifs.codeCate="surf";

-- 11 Calcul de la durée moyenne d'une location d'une paire de skis (en journées entières).
USE location_ski;
SELECT AVG(DATEDIFF(lignesfic.retour, lignesfic.depart)) AS moyenne
FROM lignesfic
LEFT JOIN fiches ON lignesfic.noFic = fiches.noFic
LEFT JOIN clients ON fiches.noCli = clients.noCli;

