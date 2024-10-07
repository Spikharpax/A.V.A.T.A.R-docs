# Les règles de plugin

A.V.A.T.A.R étant une application multi-langages et afin d'avoir une correspondance de règles indépendante du langage, les règles sont obligatoirement écrites en Anglais. L'Anglais est aussi le langage de prédilection du [Natural Language Processing](https://en.wikipedia.org/wiki/Natural_language_processing).

A.V.A.T.A.R incorpore son propre traitement naturel du langage qui fournit un moyen pratique de rechercher et faire correspondre des parties d'une phrase avec une règle dictée.

## Correspondances

Ci-dessous les correspondances qui peuvent être utilisées pour écrire une règle :

|Correspondance| Description  | Règle  | <span style="width=350;">Phrase </span> | Correspondance  |
|:-----|:---|:---|:---|:---:|
|Basic matching| The rule must correspond exactly<br>to a sequence in the sentence|<code>what time is it</code> |<code>what time is it</code> | ![ok](img/ok.png){width="12"}| 
||||<code>what time is it in Paris</code>| ![ok](img/ok.png){width="12"}| 
||||<code>please what time is it exactly</code>| ![ok](img/ok.png){width="12"}| 
||||<code>what time</code>|![ko](img/ko.png){width="12"}| 
|Any one term| The `.` character means _any one term_<br>|<code>turn on . light</code>|<code>turn on the light</code>|![ok](img/ok.png){width="12"}|
|| ||<code>turn on the led light</code> |![ko](img/ko.png){width="12"} | 
|All terms until| The * means all terms until - it may be 0|<code>turn on * light</code>|<code>turn on the light</code> |![ok](img/ok.png){width="12"} | 
||||<code>turn on the led light</code> |![ok](img/ok.png){width="12"} | 
|Options-list - ()| `(word1|word2)` parentheses allow <br>listing possible matches for the word.<br>- ( 'OR' logic )|<code>going to (bed\|sleep)</code>|<code>going to bed</code> |![ok](img/ok.png){width="12"}| 
|| ||<code>going to sleep</code> |![ok](img/ok.png){width="12"} | 
|First | The `^` character means something should be in the start of a match|<code>^John eats glue</code>|<code>John eats glue</code> |![ok](img/ok.png){width="12"}| 
||||<code>Mister John eats glue</code> |![ko](img/ko.png){width="12"} |
|Last | The `$` character means something should <br>be in the end of a match|<code>am going$</code>|<code>i am going</code> |![ok](img/ok.png){width="12"}<br> | 
||||<code>i am going to the bed</code> |![ko](img/ko.png){width="12"} | 
|Root matches | `{}` characters mean the 'root' 'stemmed' <br>version of the word<br>The root form of a #Verb is an #Infinitive form.|<code>{go} to bed</code><br>|<code>i am going to bed</code>|![ok](img/ok.png){width="12"}| 
||||<code>i went to bed</code>|![ok](img/ok.png){width="12"}| 
||||<code>i will go to bed</code> |![ok](img/ok.png){width="12"}| 
|Regex<br>suffix lookup| Looks for sub-word matches with the <br> regex `/ /` characters|<code>/nny$/</code><br>|<code>tell me a funny story</code>|![ok](img/ok.png){width="12"}| 
||||<code>tell me a story</code> |![ko](img/ko.png){width="12"} |
|Regex<br>infix lookup| Looks for sub-word matches with the <br> regex `/ /` characters|<code>/stor/</code>|<code>tell me a story</code>|![ok](img/ok.png){width="12"}| 
||||<code>tell me a joke</code> |![ko](img/ko.png){width="12"} | 

Les correspondances peuvent être associées, par exemple :

```
{go} * bed              ==> the 'root' version of "go" and all terms until bed
{go} * bed$             ==> the 'root' version of "go" and all terms until bed and bed is the last term
{go} * (bed|sleep)      ==> the 'root' version of "go" and all terms until bed OR sleep
^{go} . (bed|sleep)     ==> the 'root' version of "go" must be in the start and only one term until bed OR sleep    
```



## Gérer les règles

Chaque plugin a un fichier de propriétés au format `.json` dans lequel les règles sont définies:  
```
app/
    core/
        plugins/
            <plugin>/
                <plugin>.prop
```

/// Tip
L'application vous offre la possibilité de gérer les propriétés des plugins par l'interface [_Plugin Studio_](plugin-studio.md).
///

<br><br>
[:material-skip-previous: Pour commencer](getting-started.md){ .md-button style="float:left;"} 
<br><br>