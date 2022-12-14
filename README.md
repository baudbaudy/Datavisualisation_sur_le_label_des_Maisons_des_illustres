#Présentation du projet 

Créé en septembre 2011 par le ministère de la culture, le label « Maison des Illustres » valorise les lieux sur le territoire français (territoires de métropole et d’outre-mer), ainsi qu’exceptionnellement en territoires étrangers, dont les personnalités qui les ont fréquentés ont marqué l’histoire de France. Ce label permet ainsi de garder ces endroits dans la mémoire nationale et de méditer sur évènements et les personnes qui les ont marqué.
Après une étude du jeu de données "maison-des-illustres", bien que relativement complet et exhaustif, nous avons constaté quelques lacunes et informations non renseignées, notamment au niveau des renseignements géographiques des lieux (colonne des noms de département incomplète et absence des numéros de département). Nous avons importé ce jeu de données dans notre projet Dataiku DSS afin de la compléter et le nettoyer.


#Compléter et harmoniser la base de données

#Ajout de datasets:

Afin de compléter les cellules vides relevées, nous sommes allés chercher d’autres bases de donnée. Sur le site de l’INSEE (Institut national de la statistique et des études économiques), nous avons pu récupérer les codes officiels géographiques du territoire français en 2022 (https://www.insee.fr/fr/information/6051727). De cette source ont seulement été conservés les noms et numéros des départements français qui ont constitué un nouveau dataset "departement" de notre projet Dataiku. Afin de faciliter la cohérence et la vérification de la jointure des deux datasets, le jeu de données "maison-des-illustres" a été divisé en trois tableaux, un tableau "France_métropole", un tableau "France_dromcom" et un tableau "reste_du_monde". Grâce à l’outil "Join", nous avons pu joindre le dataset "departement" avec les tableaux "France_métropole" et "France_dromcom" afin que ces derniers aient leur colonne nom de département complète et disposent d’une colonne numéro de département. Ensuite, ces deux tableaux ont été empilés pour former le tableau 
"maison_des_illustres_France". Ce dernier a enfin été empilé avec le tableau "reste_du_monde" pour reformer un tableau "maison_des_illustres" complétés.

#Harmonisation et nettoyage de la base de données:

La colonne "geolocalisation" étant invalide pour Dataiku en tant que colonne de type geopoint, nous avons décidé de la retravailler afin qu’elle puisse, en cas de besoin, être exploitable. La colonne a été scindée en deux, une colonne "lattitude" et une colonne "longitude". Ensuite, grâce à l’outil "Geojoin", les données de ces deux colonnes ont été rassemblées pour produire une colonne "geolocalisation" de type geopoint valide.
