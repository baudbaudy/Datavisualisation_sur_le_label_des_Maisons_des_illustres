# 1. Base de données initiale
Le label « Maison des Illustres », décerné par le ministère de la culture,
valorise des logements et lieux, sur le territoire français et à l’étranger, dont les
occupants ont marqué l’histoire de France. Ce label permet donc de garder ces
endroits dans la mémoire nationale. Le jeu de données initial “maison-des-illustres”
fournit donc les noms de lieux, leurs coordonnées géographiques (géolocalisation,
ville, département, région, pays, code postal), un début de biographie des illustres
occupants, l’identité des actuels propriétaires des lieux, liens et tags vers des
ressources extérieures. Ce jeu de données est déjà bien complet et exhaustif.
Cependant, nous avons décidé de l'enrichir par des données qui fournissent des
qualificatifs et des renseignements sur les lieux de la base (ex: style d’architecture,
lieu de naissance, rôle et emploi actuel du lieu, etc…), des données dont nous
pensons que l’étude et la visualisation seront pertinentes pour en apprendre
davantage sur les caractéristiques de ces “Maisons des Illustres”.

# 2. Travail sur les données initiales
Transformation de la colonne “code_id” :

## 1. Nous avons remarqué que les valeurs de la première colonne du dataset
initial, CODE ID, étaient définies ainsi: APN00_”numéro de département”_”ID
Mérimée”. L’ ID Mérimée identifie les objets de la base Mérimée, base de
données qui regroupe des notices sur le patrimoine architectural français.
Aussi il nous semble judicieux d’arranger cette colonne afin de ne retenir que
la chaîne de caractère correspondant à l’ “IDMérimée”, identifiant qui est
connu sur Wikidata. Ainsi, avoir une colonne ne contenant que le “IDMérimée”
dans le dataset initial et le ou les dataset(s) secondaire(s) permettra la
jointure de ces tables de données.

## 2. Pour obtenir la chaîne de caractère de l’ “IDMérimée , on applique une regex
pour saisir et éliminer la première partie de l’ID : APN00_[0-9]*_
Signalisation des personnes physiques nommées

1. Nous avons décidé de produire un champ contenant le nom des personnes
illustres. Pour ce faire, nous comptons employer une série d’expressions
régulières adaptées à la colonne “Descriptif” pour en extraire les prénoms,
noms et dates de vie des personnes concernées. Cela est possible du fait de
l’indication quasi systématique des dates de vie, mais des expressions
régulières supplémentaires sont nécessaires pour traiter des cas particuliers
(tels que des noms contenant plus de deux mots ou les cas où plusieurs
personnes sont visées). Pour faire ce relevé, on ne peut pas passer par
Wikidata car malgré ses notices sur les “Maisons des Illustres”, ces dernières
ne comportent pas forcément d’informations sur les “personnes illustres” qui
les ont habité.
Création d’un champ “année de labellisation"
Reformatage du champ "tag_annee_de_labellisation" et de ses valeurs pour
en faire un champ "année de labellisation" avec en valeur date les années.
Suppression de données
Le dataset originel contient plusieurs champs “tag”
(“tag_architecture_contemporaine_remarquable”, “tag_litterature_et_idees”,
“tag_tourisme_et_handicap, etc…). Parmi ceux-ci se trouvent le champ
“tag_maison_des_illustres” que nous ne jugeons pas pertinent (puisque
s’agissant de la table “Maison des Illustres” il est inutile d’avoir un champ qui
réitère cette information). Nous préférons donc supprimer ce champ.

## 3. Enrichissements

Recherche et association d’informations depuis Wikidata
Afin d’enrichir la base initiale, nous souhaitons via une requête SPARQL
interroger Wikidata et lui demander de nous ressortir diverses informations pour
toute notice ayant le label “Maison des Illustres”. Nous avons donc réalisé la requête
suivante (requête SPARQL).
Cette requête permet de récupérer les informations de type:
ID Mérimée : Wikidata connaît l’ID Mérimée des “maisons des illustres”. Cependant,
nous obtenons des doublons car Wikidata nous sort systématiquement deux id
labellisés “ID Mérimée”, un id type “PA” et un second id type “MI”. Ceci n’est pas un
problème car il nous suffira d’utiliser sur Dataiku l’ID Mi pour faire le mash-up avec
un JOIN sur la colonne CODE ID (préalablement transformée) du jeu original. Ainsi
nous pourrons regrouper nos tables et éliminer les doublons. De plus, nous
espérons grâce à cet id récupérer d’autres informations comme des descriptions en
prose dans des graphes tels que sur DBpedia.
Type de lieux, champ “type_maisonLabel” : informations sur les statuts des lieux
concernés (maison, château, musée, etc.). Cela pourra être exploité pour construire
des data-visualisations interactives. A noter qu’un site peut avoir plusieurs propriétés
P31 (une “Maison des illustres” peut à la fois être un “château” et un “musée”) ce qui
multiplie les résultats : il faudra donc faire un tri et multiplier les colonnes sur Dataiku
afin d’éliminer les entrées en doublon tout en gardant ces informations. On peut
envisager ainsi une colonne qui indique le style architecturale du lieu (ex: “château”,
“abbaye”, “villa”, etc…), une autre qui valide si les sites sont des maisons natales ou
une autre encore qui indiquerait l’emploi actuel du lieu (ex: “musée”, “musée d’art”,
“bibliothèque”, etc…).

### Images : 
récupération d’images des sites et des lieux si il y en a.
Personnages historiques concernées : La requête permet de récupérer quelques
personnages historiques concernés, et liés aux sites par des propriétés diverses
(propriétaire, occupant, maison de naissance). Cependant, les résultats sont très
peu satisfaisants car ces renseignements sont extrêmement lacunaires.
nom labellisé des lieux: Nous choisissons de récupérer les noms des “maisons
illustres” sur Wikidata pour faire figure d’autorité. Nous devrons donc, via Dataiku,
supprimer la colonne “nom_de_la_maison” du dataset d’origine et conserver les
valeurs du champ “itemLabel” du dataset secondaire en guise de référence pour les
noms des lieux.

### Recherche sur DBpedia

Grâce à l’ID Mérimé, il est question d’aller chercher des descriptions en prose
plus élaborées sur DBpedia. Il est pertinent aussi d’explorer la possibilité de
récupérer plus de personnages historiques liés aux sites. Cette requête n’a pas
encore été construite.

## 4. Emploi des champs pour la visualisation des données

### Visualisations géographiques
Le jeu de données originales a été exploité pour créer cette carte :
https://www.culture.gouv.fr/Aides-demarches/Protections-labels-et-appellations/Com
posants-Labels/MdI/Carte-des-Maisons-des-illustres#/search@46.5601528,0.84710
88,6.47

Nous proposons de créer un instrument cartographique plus élaboré et
l’accompagner de datavisualisations pertinentes. La carte proposée serait une
version améliorée de la première qui a été construite dans le cadre du devoir. Elle
permettrait de visualiser et isoler des départements et des régions pour connaître les
sites qu’ils contiennent. Pour les régions ou départements avec de nombreuses
maisons (Île de France par exemple), des cartes individuelles pourront être
construites.

La carte garderait certains éléments déjà mis en oeuvre, notamment l’affichage de la
description de chaque maison lors du survol avec la souris, et l’indication des
personnages concernés extraits de ces descriptions avec une regex adaptée.
De plus, nous savons que OpenStreetMap France propose des fichiers svg traçant
les contours des départements et régions de France. Il serait intéressant de les
utiliser pour créer une cartographie sur plusieurs niveaux :
https://www.openstreetmap.fr/donnees/
“Contours des départements issus d’OpenStreetMap”
“Contours des régions françaises sur OpenStreetMap”
- ces fichiers indiquent pour chaque département ou région son code INSEE
- ils ne peuvent être téléchargés que sur le lien ci-dessus et sont indiqués
comme non disponibles sur data.culture.gouv
Quant aux informations géographiques, le jeu de données original est en principe
très complet : il indique les codes de département, les noms des villes, les
coordonnées. Cela a suffit à construire une carte exploitable pour le premier rendu. Il
serait tout de même possible de nettoyer, corriger ou préciser ces informations avec
des jeux de données tels que le Code officiel géographique (COG) de l’INSEE
(https://www.insee.fr/fr/information/2560452#:~:text=Il%20est%20possible%20de%2
0les,API%20d'insee.fr) ou à partir de GeoNames. En normalisant les codes ou
appellations, on aurait ainsi un jeu plus adapté à certains traitements secondaires,
tels que la signalisation de métros et gares de trains proches des sites
(https://ressources.data.sncf.com/explore/dataset/liste-des-gares/table/ ).
GeoNames réunit d’ailleurs des liens vers ce type de ressources :
https://www.geonames.org/countries/FR/france.html
Visualisations des qualificatifs et historiques des sites

### Graphe en courbe

Grâce au champ “année de labellisation” préalablement organisé, nous pourrions
utiliser les valeurs de ce champ pour produire un graphe en courbe présentant le
nombre de labels “Maison des Illustres” décernés en France au cours de la dernière
décennie. Nous pourrions imaginer d’aller plus loin en faisant plusieurs courbes,
chacune représentant une caractéristique choisie parmi certaines valeurs du champ
“type maisonLabel” (ex: château, maison, abbaye, etc…).
Les types des sites des “Maisons des Illustres”
Pour poursuivre sur le thème du type des maisons, il serait bien sûr intéressant de
continuer d’exploiter les valeurs du champ “type maisonLabel”. Il pourrait être
pertinant de représenter la variété des formes architecturales (château, abbaye, villa,
etc…) avec un diagramme en secteur ou en colonne. Par ailleurs, il serait aussi
intéressant d’étudier cette variété des styles et leur répartition géographique en les
localisant et en les distinguant sur une carte. Ces représentations de la forme
architecturale des “Maisons des Illustres” peuvent aussi s’appliquer à l’étude de
“l’emploi actuel” de ces lieux (“musée”, “bibliothèque”, etc…).
Réseaux sociaux
Le jeu de données original propose des liens vers les réseaux sociaux utilisés par
les sites concernés. Ces informations pourraient être utilisées pour construire un
feed fédérant les nouvelles des “Maisons des illustres”.

### TAGs

Les tags avaient été utilisés pour un graphique dans le premier rendu. Celui-ci
pourrait être amélioré si cela permet une recherche par facettes sur la carte, ou bien
d’obtenir une liste des sites avec des tags spécifiques.
