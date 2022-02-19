<h1 align="center">MongoSK-API - Documentation</h1>
<p align="center">MongoSK-API est un skript permettant d'utiliser facilement l'add-on MongoSK !</p><br />

## 🏹 Plus d'informations ?
Vous souhaitez avoir des informations sur comment télécharger et installer ce code ?
Rendez vous sur cette page : [README.md](https://github.com/Maxime-LEGRAND/MongoSK-API/blob/principal/README.md)

## 👩🏻‍💻 MONGO_RequestValue
- **_Cette fonction si dessous, va vous permettre de récupérer une valeur._**
```
function MONGO_RequestValue(collection: text, file: text, value: text) :: string:
    {_collection}, {_file} and {_value} isn't "<none>", "" or " ":
        set {_database} to mongo database named "{@database}" of {mongosk-api.bridge}
        "%{_database}'s mongo collections%" contains "%mongo collection named {_collection} of {_database}%":
            set {_collection} to mongo collection named "%{_collection}%" of {_database}
            set {_file} to first mongo document with filter mongo filter field "_id" is "%{_file}%" of collection {_collection}
            {_file} is set:
                mongo value "%{_value}%" of {_file} is set:
                    return "%mongo value {_value} of {_file}%"
```
- **_La variable {var} si dessous va chercher à contenir le "Grade", contenu dans le document "player's uuid" qui est lui même dans la collection "Joueurs" :_**
```
set {var} to MONGO_RequestValue("Joueurs", player's uuid, "Grade")
```
- **_Le code si dessous va vous permettre d'utiliser la fonction sans passer par une variable :_** 
```
broadcast MONGO_RequestValue("Joueurs", player's uuid, "Emoji")
```
## 👩🏻‍💻 MONGO_ResquestValues
- **_Cette fonction si dessous, va vous permettre de récupérer une valeur précise dans tous les documents de la collection choisie :_**
```
function MONGO_RequestValues(collection: text, value: text) :: objects:
    {_collection} and {_value} isn't "<none>", "" or " ":
        set {_database} to mongo database named "{@database}" of {mongosk-api.bridge}
        "%{_database}'s mongo collections%" contains "%mongo collection named {_collection} of {_database}%":
            set {_collection} to mongo collection named "%{_collection}%" of {_database}
            set {_files::*} to all mongo documents of collection {_collection}
            set {_size} to size of {_files::*}
            loop {_size} times:
                set {_id} to mongo value "_id" of {_files::%loop-value%}
                set {_values} to mongo value "%{_value}%" of {_files::%loop-value%}
                add "%{_values}%::%{_id}%" to {_return::*}
            return {_return::*}
```

- **_Le code si dessous va vous permettre de réaliser un classement :_** ⚠️**NE MARCHE QU'AVEC LES UUID**
```
set {_kills::*} to MONGO_RequestValues("Vacuum", "kills")
loop {_kills::*}:

    # Permet de séparer 
    set {_value} to loop-value
    set {_p} to the last 36 characters of {_value}
    replace all "%{_p}%" and "::" with "" in {_value}

    {_value} parsed as integer > {_kills.1}:
```
## 👩🏻‍💻 MONGO_RequestList
- **_Cette fonction si dessous, va vous permettre de récupérer une liste :_**
```
function MONGO_RequestList(collection: text, file: text, value: text) :: objects:
    {_collection}, {_file} and {_value} isn't "<none>", "" or " ":
        set {_database} to mongo database named "{@database}" of {mongosk-api.bridge}
        "%{_database}'s mongo collections%" contains "%mongo collection named {_collection} of {_database}%":
            set {_collection} to mongo collection named "%{_collection}%" of {_database}
            set {_file} to first mongo document with filter mongo filter field "_id" is "%{_file}%" of collection {_collection}
            {_file} is set:
                mongo value "%{_value}%" of {_file} is set:
                    return mongo list {_value} of {_file}
```
- **_La variable {var} si dessous va chercher à contenir les amis du joueur "Friends::*", contenu dans le document "player's uuid" qui est lui même dans la collection "Joueurs" :_**
```
set {var::*} to MONGO_RequestList("Joueurs", player's uuid, "Friends::*"
```
## 👩🏻‍💻 MONGO_SetValue
- **_Cette fonction si dessous, va vous permettre de mettre en place une valeur :_**
```
function MONGO_SetValue(collection: text, file: text, value: text, thing: text):
    {_collection}, {_file}, {_value} and {_thing} isn't "<none>", "" or " ":
        set {_database} to mongo database named "{@database}" of {mongosk-api.bridge}
        "%{_database}'s mongo collections%" contains "%mongo collection named {_collection} of {_database}%":
            create mongo collection named "%{_collection}%" in {_database}
        set {_id} to {_file}
        set {_collection} to mongo collection named "%{_collection}%" of {_database}
        set {_file} to first mongo document with filter mongo filter field "_id" is "%{_file}%" of collection {_collection}
        {_file} isn't set:
            set {_newfile} to new mongo document with:
                mongo "_id": {_id}
            insert mongo document {_newfile} into collection {_collection}
        set {_file} to first mongo document with filter mongo filter field "_id" is "%{_file}%" of collection {_collection}
        
        set {_look} to the last 3 characters of {_value}
        {_look} = "::*":
            set mongo list "%{_value}%" of {_file} to {_thing}
        else:
            set mongo value "%{_value}%" of {_file} to {_thing}
        update mongo document {_file} of {_collection}
```
- **_Le code si dessous va vous permettre de mettre en place une valeur dans la collection "Joueurs" dans le document "player's uuid" comme valeur "Pseudo" et son contenu sera "%player%" :_**
```
MONGO_SetValue("Joueurs", player's uuid, "Pseudo", player)
```
## 👩🏻‍💻 MONGO_DelValue
- **_Cette fonction si dessous, va vous permettre de supprimer complètement une valeur d'un document :_**
```
function MONGO_DelValue(collection: text, file: text, value: text):
    {_collection}, {_file} and {_value} isn't "<none>", "" or " ":
        set {_database} to mongo database named "{@database}" of {mongosk-api.bridge}
        "%{_database}'s mongo collections%" contains "%mongo collection named {_collection} of {_database}%":
            set {_collection} to mongo collection named "%{_collection}%" of {_database}
            {_value} = "":
                delete first mongo document with filter mongo filter field "_id" is "%{_file}%" of collection {_collection}
            else:
                set {_file} to first mongo document with filter mongo filter field "_id" is "%{_file}%" of collection {_collection}
                {_file} is set:
                    mongo value "%{_value}%" of {_file} is set:
                        delete mongo value "%{_value}%" of {_file}
                        update mongo document {_file} of {_collection}
```
- **_Le code si dessous va vous permettre de supprimer complètement une valeur dans la collection "Joueurs" dans le document "player's uuid" comme valeur "Coins" :_**
```
MONGO_DelValue("Joueurs", player's uuid, "Coins")
```
## 👩🏻‍💻 MONGO_AddThing
- **_Cette fonction si dessous, va vous permettre d'ajouter du contenu à une liste ou de réaliser une addition (si le contenu est un nombre) avec l'ancienne valeur :_**
```
function MONGO_AddThing(collection: text, file: text, value: text, thing: text):
    {_collection}, {_file}, {_value} and {_thing} isn't "<none>", "" or " ":
        set {_database} to mongo database named "{@database}" of {mongosk-api.bridge}
        "%{_database}'s mongo collections%" contains "%mongo collection named {_collection} of {_database}%":
            create mongo collection named "%{_collection}%" in {_database}
        set {_id} to {_file}
        set {_collection} to mongo collection named "%{_collection}%" of {_database}
        set {_file} to first mongo document with filter mongo filter field "_id" is "%{_file}%" of collection {_collection}
        {_file} isn't set:
            set {_newfile} to new mongo document with:
                mongo "_id": {_id}
            insert mongo document {_newfile} into collection {_collection}
        set {_file} to first mongo document with filter mongo filter field "_id" is "%{_file}%" of collection {_collection}

        set {_look} to the last 3 characters of {_value}
        {_look} = "::*":
            set {_values::*} to mongo list "%{_value}%" of {_file}
            add "%{_thing}%" to {_values::*}
            set mongo list "%{_value}%" of {_file} to {_values::*}
        else if {_thing} parsed as number = number:
            set {_values} to mongo value "%{_value}%" of {_file}
            set {_values} to ({_values} parsed as number + {_thing} parsed as number)
            set mongo value "%{_value}%" of {_file} to "%{_values}%"
        update mongo document {_file} of {_collection}
```
- **_Le code si dessous va vous permettre d'ajouter du contenu à une liste :_**
```
MONGO_AddThing("Joueurs", player's uuid, "Party", "Derlock_")
```
- **_Le code si dessous va vous permettre de réaliser une addition avec l'ancienne valeur :_**
```
MONGO_AddThing("Joueurs", player's uuid, "Coins", "1000")
```
## 👩🏻‍💻 MONGO_RemThing
- **_Cette fonction si dessous, va vous permettre de retirer du contenu à une liste ou de réaliser une soustraction (si le contenu est un nombre) avec l'ancienne valeur :_**
```
function MONGO_RemThing(collection: text, file: text, value: text, thing: text):
    {_collection}, {_file}, {_value} and {_thing} isn't "<none>", "" or " ":
        set {_database} to mongo database named "{@database}" of {mongosk-api.bridge}
        "%{_database}'s mongo collections%" contains "%mongo collection named {_collection} of {_database}%":
            set {_collection} to mongo collection named "%{_collection}%" of {_database}
            set {_file} to first mongo document with filter mongo filter field "_id" is "%{_file}%" of collection {_collection}
            {_file} is set:            
                set {_look} to the last 3 characters of {_value}
                {_look} = "::*":
                    set {_values::*} to mongo list "%{_values}%" of {_file}
                    remove "%{_thing}%" from {_values::*}
                    size of {_values::*} = 0:
                        delete mongo value "%{_value}%" of {_file}
                    else:
                        set mongo list "%{_value}%" of {_file} to {_values::*}
                    update mongo document {_file} of {_collection}
                else if {_thing} parsed as number = number:
                    set {_values} to mongo value "%{_value}%" of {_file}
                    set {_values} to ({_values} parsed as number - {_thing} parsed as number)
                    set mongo value "%{_value}%" of {_file} to {_values}
                    update mongo document {_file} of {_collection}
```
- **_Le code si dessous va vous permettre de retirer du contenu à une liste :_**
```
MONGO_RemThing("Joueurs", player's uuid, "Party", "Derlock_")
```
- **_Le code si dessous va vous permettre de réaliser une soustraction avec l'ancienne valeur :_**
```
MONGO_RemThing("Joueurs", player's uuid, "Coins", "1000")
```
