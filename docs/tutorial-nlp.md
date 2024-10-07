# Natural Language Processing

A.V.A.T.A.R incorporates a modest but functional [Natural Language Processing (NLP)](https://en.wikipedia.org/wiki/Natural_language_processing).  

This step is automatic and internal to A.V.A.TA.R. It is called **before** checking [the intent](tutorial-intention.md) file of the plugins.

## Example
If the sentence is :

```
Make a 1 hour appointment with the dentist tomorrow at 2:00 p.m. in Paris
```

A.V.A.T.A.R will analyze the sentence and create a relationship scenario, then suggest :

* Action: `make`
* Object: `appointment`
* Duration: `1 hour`
* Item: `dentist`
* When: `June 25th 2024 2:00 pm`
* Location: `paris`

If the intention is verified, the [`data` object](tutorial-script.md#actiondata-callback) accessible in the plugin script will receive the relationship scenario that NLP has built, which you can then use to, for example, create an appointment in your agenda.

```
Set an appointment in phone's calendar like: ${Object} ${Item} in ${Location} on ${When} of ${Duration}
```

## Objects
Below are the objects created by NLP that you can use in the [`data` object](tutorial-script.md#actiondata-callback) parameter of the `action` method in the script file. 

### data.tokens - Array
Array of words of the sentence.

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
Array of word types of the sentence.

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

You can consult the complete list of possible tags [here](https://observablehq.com/@spencermountain/compromise-tags)  

???+ warning
    As tags are the types of each word, the `tokens` and `tags` arrays must have identical lengths. 

    If this is not the case, the intention is not resolved.  
    This is usually due to a compound word, which must then be correctly written into the [collection of terms](tutorial-intention.md#by-the-syntax) to correct the problem.


### data.relations - Object
The relationship scenario

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
[:material-skip-previous: Folders and Files](tutorial-plugin-folders-files.md){ .md-button style="float:left;"}[The intent :material-skip-next:](tutorial-intention.md){ .md-button style="float:right;"}   
<br><br>