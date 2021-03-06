# RESTful API: Einführung

> ## Inhalt
> - [Erste Schritte](#erste-schritte)
> - [Konfiguration/Endpoints](#config)
>   - [Model](#config-model)
>   - [Route](#config-route)
> - [Nutzung eines Endpoints](#use)
>   - [GET / Datensätze laden](#use-get)
>   - [POST](#use-post)
>   - [DELETE](#use-delete)
> - [Authentifizierung](#auth)

<a name="erste schritte"></a>
## Erste Schritte

Mit dem REST-Plugin lässt sich eine Schnittstelle aktivieren, mit der man YForm Tabellen von außen abrufen, verändern und löschen kann. Dabei wird auf die REST-API gesetzt.
Die Klasse muss zunächst registriert werden siehe [YOrm](yorm.md), damit auf diese mit REST zugegriffen werden kann. Alle Übergaben und Rückgabewerte werden als JSON übertragen.

> Hinweis: Die Tabellem müssen mit YForm verwaltbar sein, da diese Felder automatisch genutzt werden.


<a name="config"></a>
## Konfiguration / Endpoints

Die Zugriff über REST muss für jeden Endpoint einzeln definiert werden. D.h. ich muss für jede Tabelle und für unterschiedliche Nutzungszenarien diese fest definieren.

Die Standardroute der REST Api ist auf "/rest" gesetzt, d.h. hierunter können eigene Routen definiert werden. 

Die Konfiguration wird über PHP festgelegt und sollte im project-AddOn in der boot.php abgelegt werden. Kann aber auch wonanders abgelegt werden, solange diese während der initialisierung aufgerufen wird.


Hier ein Beispiel, um YCom-User über die REST API zu verwalten:


```

// diese Zeile ist normalerweise nötig. Dieses Bespiel nutzt aber eine YCom-Tabelle, die bereits über das AddOn registriert ist.
#rex_yform_manager_dataset::setModelClass('rex_ycom_user', rex_ycom_user::class);


// Konfiguration des REST Endpoints.
$model = new \rex_yform_rest_model(
    [
        'auth' => \rex_yform_rest_auth_token::checkToken(),
        'table' => \rex_ycom_user::table(),
        'query' => \rex_ycom_user::query(),
        'get' => [],
        'post' => [],
        'delete' => [],
    ]
);

// Einbinden der Konfiguration
\rex_yform_rest_route::factory()
    ->setPath('/v1/user/')
    ->addModel($model);
```

Dieses Beispiel führt dazu, dass



<a name="config-model"></a>
### Model-Konfiguration

`auth`
ist optional und kann komplett weggelassen werden, wenn man keine Authentifizerung für einen Endpoint haben möchte. Erlaubte werden sind callbacks und Funktionsnamen.

Beispiele

* **\rex_yform_rest_auth_token::checkToken()** für die interne Authentifizierung mit Token
* **function() { return (date("d") == 1) ? true : false }**
* **"MeineFunktion"**

Wenn man keine Authentifizierung einträgt kann jeder diese Daten entsprechend der weiteren Konfiguration nutzen. Sollte man nur bei Tabell wie PLZ oder ähnlich offensichtlich freien Daten machen.


`table`
hier wird die entsprechend Tabelle übergeben, die in YOrm definiert ist.

Beispiel

* **\rex_ycom_user::table()**

`get`

`post`

`delete`

<a name="config-route"></a>
### Route

Die Modelkonfiguration wird nun eingebunden über 

```
// Einbinden der Konfiguration
\rex_yform_rest_route::factory()
    ->setPath('/v1/user/') // Mein Pfad. Im Normalfall wie die Tabellenbezeichnung
    ->addModel($model); // Die Modelkonfiguration
```

Da die Defaultpfadeinstellung mit `/rest` ergänzt wird, ergibt sich in diesem Beispiel dieser Endpoint:

`/rest/v1/user`

<a name="use"></a>
## Nutzung eines Endpoints

URL (z.B. https://domain/rest/v1/user)
In den Beispielen wird davon ausgegangen, dass es keine Authentifizierung gibt

* Fehler und Statusmeldeungen

<a name="use-get"></a>
### GET

* Filter
* Felder

<a name="use-post"></a>
### POST

* Anlegen von Datensätzen
* Update von Datensätzen
* Validierung

<a name="use-delete"></a>
### DELETE

* Filter
* Nach ID

<a name="auth"></a>
## Authentifizierung

### Standardauthentifizierung

Wenn im Model folgende Authentifizerung angegeben wurde: `\rex_yform_rest_auth_token::checkToken()` ist das die Standardauthentifizierung mit Tokens aus der YForm:Rest:Tokenverwaltung.

Die hier erstellen Token werden entsprechend überprüft und mussen im Header übergeben werden. `token=###meintoken###` Nur aktiver Tokens funktionieren.