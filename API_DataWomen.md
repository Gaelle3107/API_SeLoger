API Se Loger
================

``` r
library(XML)
library(httr)
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
library(readr)
```

Présentation rapide de l'API
----------------------------

L'API Se Loger est finalement "juste" une URL permettant de récupérer les résultats d'une requête au format xml. L'URL a le même type de construction que celle d'une recherche HTML classique. La principale difficulté est que Se Loger n'utilise pas de code postal mais un genre de Code Insee modifié pour identifier les villes. Le plus simple pour connaître le code est de faire une requete simple dans se loger (acheter un appartement ou maison à Paris 12è, et de récupérer le code après "co=" dans l'URL : 750112 dans notre cas). Voici le résumé de la construction de l'URL de l'API : <https://github.com/pasnox/housing/blob/master/SeLoger.com.api.txt>

Exemple
-------

Ventes d'appartements ou de maisons de 2 pièces à Châtenay-Malabry, ayant une surface supérieure à 35 m², pour un prix compris entre 150 000 et 200 000 €, triées par date.

Télechargement du fichier XML (on peut aussi directement copier / coller cette url dans un navigateur, ça permet de bien voir ce qu'on récupère)

``` r
url_achat <- "http://ws.seloger.com/search.xml?idtt=2&idtypebien=1,2&nb_pieces=2&pxmin=150000&pxmax=200000&surfacemin=35&surfacemax&ci=920019&tri=d_dt_crea"

download.file(url_achat, destfile = "achat.xml")
doc_achat <- xmlTreeParse("achat.xml", useInternalNodes = TRUE)
rootNode_achat <- xmlRoot(doc_achat)
```

Pour mon application, je n'avais besoin que de récupérer la liste des prix et des surfaces :

``` r
#ACHAT

liste_prix_achat <- as.numeric(xpathSApply(rootNode_achat, "//prix", xmlValue))
liste_surface <- as.numeric(xpathSApply(rootNode_achat, "//surface", xmlValue))

prix_achat <- data.frame(liste_prix_achat, liste_surface)
```

Je pense que vous avez tout ce qu'il faut pour aller plus loin si vous voulez scrapper automatiquement les annonces par exemple. Un tuto pour XML avec R par exemple : <https://www.tutorialspoint.com/r/r_xml_files.htm>
