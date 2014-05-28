# Modification d'un document 

## Url

    PUT /api/families/<famName>/<id>

Modification d'un document de la famille `<famName>`

ou

    PUT /api/documents/<id>

Modification du document <id>

L'extension ".json" peut être ajoutée pour expliciter le format de sortie.

Exemple :

    PUT /api/documents/1234.json


Note : la différence entre la ressources `families` et `docoments` est que
l'identifiant doit être dans la famille indiqué pour être modifié sinon une
erreur 404 (ressource non trouvée) sera retournée.

L'identifiant du document peut être son nom logique, son identifiant numérique.
L'identifiant numérique peut référencer n'importe quelle révision du document. 
Dans tous les cas, la modification porte sur la dernière révision du document.

## Content

### Format JSON

Le contenu de la requête doit contenir une donnée JSON avec la liste des attributs modifiés.

    [php]
    {
      "attributes" : {
          <attrName> : {
            value : <newValue>
          }
      }
    }

Le type de la requête est `application/json`.

Exemple :

    [php]
    {
          "attributes" : {
              "ba_title" : {"value" : "Hello world"},
              "ba_desc" : {"value" : "Nice Day"}
          }
    }


Note : Toute donnée additionnelle sera ignorée.

### Format urlEncoded

Le contenu de la requête contient la liste des valeurs d'attributs à enregistrer.
Chaque variable (PUT) est le nom de l'attribut (casse insensible).

Le type de la requête est `application/x-www-form-urlencoded`.

Note : Ce format peut être utilisé directement depuis un formulaire HTML.

Cette forme permet aussi d'enregistrer des fichiers dans le document.

## Structure de retour

Le retour est une donnée JSON.

### En cas de réussite :

La partie `data` contient 3 champs :


1.  `document.uri` : uri d'accès à la ressource modifiée
1.  `document.properties` : liste des valeurs des propriétés
1.  `document.attributes` : liste des valeurs des attributs
1.  `changes` : liste des valeurs modifiées

Exemple :

    [php]
    {
      "success" :true,
      "messages" : [],
      "data" : {
          "document" : {
                "uri" : "http;//www.example.org/api/documents/1256",
                "properties" : { 
                   "id" : 1256,
                   "title" : "Hello world",
                   "locked" : 0,
                   ....
                 },
                "attributes" : { 
                    "ba_title" : {
                        "value" : "Hello world"
                        "displayValue" : "Hello world"
                    },
                    "ba_cost" : {
                        "value" : 234
                        "displayValue" : "234.00 €"
                    }
                }
          },
          "changes" : {
              "my_name" : {"before" : "Dupont", "after" : "Dupond"}, 
              "my_date" : {"before" : "", "after" : "2012-02-22"}},
        }
    }

### En cas d'échec

Les raisons d'échecs spécifiques à cette requête sont 

|                          Raison                         | Status HTTP | Error Code |
| ------------------------------------------------------- | ----------- | ---------- |
| Privilège insuffisant pour modifier le document         |         403 | API0106    |
| Tentative de modification d'un attribut en visibilité I |         403 | API0101    |
| Document supprimé                                       |         403 | API0108    |
| Document verrouillé                                     |         403 | API0109    |
| preUpdate erreur                                        |         400 | API0107    |
| Erreur de contrainte                                    |         400 | API0104    |
| Attribut obligatoire vide                               |         400 | API0105    |
|                                                         |             |            |

Exemple : 

Cas d'erreur de privilège

    [php]
    {
      "success" :false,
      "messages" : [{
            "type" : "error", 
            "contentText" : "Need acl \"edit\" to modify the document",
            "contentHtml" : "",
            "code" : "API0106", 
            "uri" : "http://api.dynacase.org/code/API0106.html",
            "data" : null
            }],
    }



