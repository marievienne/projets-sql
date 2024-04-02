# TP 1
```mysql
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

```