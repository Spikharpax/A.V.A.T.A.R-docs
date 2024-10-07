# Traitement Naturel du Langage (NLP)

/// tip | Ceci est la **partie 4** du tutoriel
///

A.V.A.T.A.R incorpore un modeste mais fonctionnel Traitement Naturel du Langage.  
:flashlight:
Cette étape est automatique et interne à A.V.A.TA.R. Elle est appelée **avant** de vérifier [l'intention](tutorial-intention.md "L'intention") des plugins.

## Exemple
Si la phrase est :

```
Make a 1 hour appointment with the dentist tomorrow at 2:00 p.m. in Paris
```

A.V.A.T.A.R va analyser votre phrase et créer un scénario de relation et vous proposera :

* Action `make`
* Object `appointment`
* Duration `1 hour`
* Item `dentist`
* When `June 25th 2024 2:00 pm`
* Location `paris`

Si l'intention est vérifiée, l'objet [`data`](tutorial-script.md#actiondata-callback) accessible dans le script du plugin recevra le scénario de relation que vous pourrez ensuite utiliser pour, comme par exemple, créer un rendez-vous.
```
Set an appointment in phone's calendar like: ${Object} ${Item} in ${Location} on ${When} of ${Duration}
```

## Les objects
Retrouvez ci-dessous les objects créés par le Traitement Naturel du Langage que vous pouvez exploiter dans le paramètre _data_ de la méthode _action_ du fichier script du plugin. 


### data.tokens - Array
Le tableau des mots de la phrase
``` js
data.tokens: [
    'make',        'a',
    '1',           'hour',
    'appointment', 'with',
    'the',         'dentist',
    'tomorrow',    'at',
    '2:00',        'pm',
    'in',          'paris'
  ]
```

### data.tags - Array
Le tableau des types de mots de la phrase
``` js
tags: [
    'Imperative',  'Determiner',
    'Date',        'Singular',
    'Noun',        'Preposition',
    'Determiner',  'Singular',
    'Date',        'Time',
    'Time',        'Time',
    'Preposition', 'City'
  ]
```

Vous pouvez consulter la liste complète des tags possibles [içi](https://observablehq.com/@spencermountain/compromise-tags)  

??? warning
    Les tableaux `tokens` et `tags` doivent avoir des longueurs identiques. 

    Si ce n'est pas le cas, l'intention n'est pas résolue.  
    Cela provient généralement d'un mot composé qui doit alors être correctement écrit dans la [collection de termes](tutorial-intention.md#par-la-syntaxe-de-la-phrase) pour corriger le problème.


### data.relations - Object
Le scénario de relation

``` js
relations: {
    action: { 
        verb: { 
            tense: 'verb', 
            negative: false 
        }, 
        tag: 'verb', 
        text: 'make' 
    },
    duration: { 
        tag: 'duration', 
        text: '1 hour' 
    },
    object: { 
        tag: 'noun', 
        text: 'appointment' 
    },
    item: { 
        tag: 'noun', 
        text: 'dentist' 
    },
    when: { 
        info:  {
            start: '2024-06-25T14:00:00.000+02:00',
            timezone: 'Europe/Paris'
        }, 
        tag: 'date', 
        text: 'June 25th 2024 2:00 pm' 
    },
    location: { 
        tag: 'city', 
        text: 'paris' 
    }
}
```


<br>
[:material-skip-previous: Le répertoire et les fichiers du plugin](tutorial-folder-plugin.md){ .md-button style="float:left;"}[L'intention :material-skip-next:](tutorial-intention.md){ .md-button style="float:right;"}   
<br><br>