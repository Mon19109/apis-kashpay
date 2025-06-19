---
title: API Kashpay

language_tabs: # must be one of https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers
  - java
  - php
  - ruby
  - python
  - shell

toc_footers:
  - <a href='#'>Kashpay</a>
  - <a href='#'>Onsigna</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: false

meta:
  - name: description
    content: Documentacion de Kashpay APIS
---

# Introduccion

Bienvenidos a la documentacion de las APIS de Kashpay, donde encontraras los servicios web para uso de las mismas que conforman nuestros desarrollos. 

Las APIS que conforman el entorno Kashpay son:

<ul>
  <li>API AUTENTICACION</li>
  <li>API ADQUIRENCIA</li>
    <ul>
      <li>API OKPAY</li>
    </ul>
  <li>API EMISION</li>
  <li>API ENTITIES</li>
  <li>API CARD</li>
</ul>

Nuestros servicios estan diseñados con la estructura RESTful. Los request y response se presentan en  formato JSON.

<aside class="notice">
Si tiene alguna duda específica con el desarrollo o la integración, puede enviar un correo electrónico a soporte@onsigna.com y a través del canal de soporte responderemos a sus solicitudes. 
</aside>



# Autenticacion <span style="float: right; color:#0f70ec;font-size: 9px;">POST</span>

Valida credenciales y genera tokenSesion para el consumo de los diferentes servicios.

```java
// JAVA - Unirest
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.post("http://sdbx-polaris.kashplataforma.com/AuthenticationService/authenticate")
  .header("Content-Type", "application/json")
  .body("{\n    \"entity\": \"com.onsigna\",\n    \"password\": \"onsig1234qw\",\n    \"user\": \"onsigna@gmail.com\"\n\n  }")
  .asString();

```

```php
//PHP - cURL
$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'http://sdbx-polaris.kashplataforma.com/AuthenticationService/authenticate',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'POST',
  CURLOPT_POSTFIELDS =>'{
    "entity": "com.onsigna",
    "password": "onsig1234qw",
    "user": "onsigna@gmail.com"

  }',
  CURLOPT_HTTPHEADER => array(
    'Content-Type: application/json'
  ),
));

$response = curl_exec($curl);

curl_close($curl);
echo $response;

```

```ruby
# RUBY - Net::HTTP
require "uri"
require "json"
require "net/http"

url = URI("http://sdbx-polaris.kashplataforma.com/AuthenticationService/authenticate")

http = Net::HTTP.new(url.host, url.port);
request = Net::HTTP::Post.new(url)
request["Content-Type"] = "application/json"
request.body = JSON.dump({
  "entity": "com.onsigna",
  "password": "onsig1234qw",
  "user": "onsigna@gmail.com"
})

response = http.request(request)
puts response.read_body

```

```python
# PYTHON - http.client
import http.client
import json

conn = http.client.HTTPConnection("sdbx-polaris.kashplataforma.com")
payload = json.dumps({
  "entity": "com.onsigna",
  "password": "onsig1234qw",
  "user": "onsigna@gmail.com"
})
headers = {
  'Content-Type': 'application/json'
}
conn.request("POST", "/AuthenticationService/authenticate", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))

```

```shell
# SHELL - httpie
printf '{
    "entity": "com.onsigna",
    "password": "onsig1234qw",
    "user": "onsigna@gmail.com"

  }'| http  --follow --timeout 3600 POST 'http://sdbx-polaris.kashplataforma.com/AuthenticationService/authenticate' \
 Content-Type:'application/json'

```


> El codigo anterior devuelve un JSON estructurado así:

```json
{
  "success": true,
  "authenticationResponse": {
    "token": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJqaW1AY2FzY2FkZWZpbnRlY2guY29tIiwiZXhwIjoxNjY5Nzk5MjA4LCJpYXQiOjE2Njk3OTMyMzJ9.Xhk_B_iOaXPQzkiEdMnvpuUVI8nz6_6uZB9HF4rEJI2zpi0G3K7UqfeBTsbWHP-hFtkHgLDnNHXKnMWc8ixFzA",
    "expires": "5975999"
  }
}s
```

Para utilizar los Servicios de la Plataforma Kashpay (POS, Comercio Electrónico, Pago de Facturas, Libro a Libro, etc.) es obligatorio que se pueda generar un Token de Sesión.

### HTTP Request

`POST http://sdbx-polaris.kashplataforma.com/AuthenticationService/authenticate`

### Query Parameters

Parameter | Description
--------- | -----------
entity    | Entidad previamente registrada.
password  | Clave privada asignada por el usuario.
user      | Correo electronico registrado.

<aside class="success">
Recuerda — Las credenciales para este propósito se proporcionan a nivel de Entidad.
</aside>



# API Adquirencia





# API Emision

## Encriptación del Body

Para poder hacer peticiones a nuestra API Emision es necesario que Kashpay proporcione una llave publica, la cual dara acceso al uso de los WebServices de la siguiente manera:

<aside style="background: #c7924b;color: #fff;font-size:11px;">
{ <br>
  "key": "23su4WF4WFYcizxFAKKQ7yZ2MeLS0AKK4654rd54312WFAKK4WFAKK3d",<br>
  "data": "GFAKKQ7yZ2M34WFAKKQ7yZ2MV4M9hsl5wjEFAKKMq7xKKQ7yZ2MeLS0"<br>
}
</aside>

Para poder llegar a la estructura anterior lo haremos con los siguientes pasos:
 
1.- Para poder encriptar en AES necesitamos generar una key, para nuestro caso vamos usar llaves aleatoria de tamaño fijo de 32.

<aside style="background: #c7924b;color: #fff;font-size:11px;">
Llave generada aleatoria: ce8effce2465785993e1f0f866eac737<br>
JSON a encriptar en AES 256: {"application_bundle":"Fortia”}
</aside>

2.- Realizar encriptación AES y el resultado encriptado será nuestro campo "data"
 
<aside style="background: #c7924b;color: #fff;font-size:11px;">
"data": "GFAKKQ7yZ2M34WFAKKQ7yZ2MV4M9hsl5wjEFAKKMq7xKKQ7yZ2MeLS0"
</aside>

3.- Para que el WS se pueda desencriptar este campo necesitará la llave que se generó aleatoria.
Esta llave(Llave generada aleatoria) la vamos a encriptar  mediante el mecanismo de cipher RSA, para esto vamos a necesitar la llave pública que les otorgamos en el formulario.

<aside style="background: #c7924b;color: #fff;font-size:11px;">
Llave generada aleatoria (Texto a encriptar con RSA): ce8effce2465785993e1f0f866eac737<br>
Llave publica: D1FI+hV1DRtowfFwAleTJgLwASP6VMbKwhaVL1jaAUZx7OGCTzfNgBuI6JIxgxg
</aside>

4.- Realizar encriptación y el resultado encriptado será nuestro campo "key"

<aside style="background: #c7924b;color: #fff;font-size:11px;">
"key": "23su4WF4WFYcizxFAKKQ7yZ2MeLS0AKK4654rd54312WFAKK4WFAKK3d",
</aside> 

<aside class="success">
Recuerda — El JSON request con la información solicitada en cada servicio debe ser enciptado antes de enviarlo en el servicio. 
Se soportan dos padding (“AES” default  y “AES/ECB/PKCS5Padding”) , se anexan una pagina web donde se pueden consultar algunos <a href="http://8gwifi.org/CipherFunctions.jsp">ejemplos</a>
</aside>
 

## login  <span style="float: right; color:#0f70ec;font-size: 9px;">POST</span>

Valida las credenciales para acceder en la app wallet, permite o deniega el acceso. Una vez que los accesos sean correctos, dicho servicio devuelve los saldos  asignados, así como el estatus, información y la tarjeta asociada a la cuenta. 

> JSON request a encriptar:

``` json
{ 
  "loginRequest": { 
      "authentication": { 
       "user": "test@gmail.com", 
       "mail": "test@gmail.com", 
       "password": "**********" 
      }, 
     "latitude": "19.6735912", 
     "longitude": "-99.0711819", 
     "dateTransaction": "20230113", 
     "hourTransaction": "134209"
   }, 
   "device": { 
     "os": "Android", 
     "systemOperativeName": "Version Desconocida", 
     "systemOperativeVersion": "15", 
     "manufacturer": "Google", 
     "model": "Pixel 7 Pro", 
     "latitude": "19.6735912", 
     "longitude": "-99.0711819" 
   }, 
   "appInfo": { 
     "nameApp": "eVolv", 
     "versionApp": "21.0.0", 
     "enviroment": "SANDBOX", 
     "versionConnector": "2.0.0" 
   } 
}
```


```java
// JAVA - Unirest
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.post("http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/login")
  .header("Content-Type", "application/json")
  .body("{\n    \"key\": \"lvGrF8xZz/q1XSetxqRkkB665GRwUZs2QPriwCf1gYAAtR96r/qMWf1Wa9TYrrfB\\u003d\\u003d\",\n    \"data\": \"OiBBsLVP2t+0yaeKsvp1F9yPRAA8fnp765QVoA/LmvMzC2z0fZP12GcIjr9rtg/...\" \n}")
  .asString();

```

```php
// PHP - cURL
$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/login',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'POST',
  CURLOPT_POSTFIELDS =>'{
    "key": "lvGrF8xZz/q1XSetxqRkkB665GRwUZs2QPriwCf1gYAAtR96r/qMWf1Wa9TYrrfB\\u003d\\u003d",
    "data": "OiBBsLVP2t+0yaeKsvp1F9yPRAA8fnp765QVoA/LmvMzC2z0fZP12GcIjr9rtg/..." 
  }',
  CURLOPT_HTTPHEADER => array(
    'Content-Type: application/json'
  ),
));

$response = curl_exec($curl);

curl_close($curl);
echo $response;

```

```ruby
# RUBY - Net::HTTP
require "uri"
require "json"
require "net/http"

url = URI("http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/login")

http = Net::HTTP.new(url.host, url.port);
request = Net::HTTP::Post.new(url)
request["Content-Type"] = "application/json"
request.body = JSON.dump({
  "key": "lvGrF8xZz/q1XSetxqRkkB665GRwUZs2QPriwCf1gYAAtR96r/qMWf1Wa9TYrrfB==",
  "data": "OiBBsLVP2t+0yaeKsvp1F9yPRAA8fnp765QVoA/LmvMzC2z0fZP12GcIjr9rtg/..."
})

response = http.request(request)
puts response.read_body


```

```python
# PYTHON - http.client
import http.client
import json

conn = http.client.HTTPConnection("http://sdbx-aldebaran.kashplataforma.com")
payload = json.dumps({
  "key": "lvGrF8xZz/q1XSetxqRkkB665GRwUZs2QPriwCf1gYAAtR96r/qMWf1Wa9TYrrfB==",
  "data": "OiBBsLVP2t+0yaeKsvp1F9yPRAA8fnp765QVoA/LmvMzC2z0fZP12GcIjr9rtg/..."
})
headers = {
  'Content-Type': 'application/json'
}
conn.request("POST", "/aldbrn/internal/aw/login", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))

```

```shell
# SHELL - httpie
printf '{
    "key": "lvGrF8xZz/q1XSetxqRkkB665GRwUZs2QPriwCf1gYAAtR96r/qMWf1Wa9TYrrfB\\u003d\\u003d",
    "data": "OiBBsLVP2t+0yaeKsvp1F9yPRAA8fnp765QVoA/LmvMzC2z0fZP12GcIjr9rtg/..." 
}'| http  --follow --timeout 3600 POST 'https://sdbx-api-aldebaran.kashplataforma.com/aldbrn/internal/aw/login' \
 authorizationtoken:'Bearer jsdlworu5jb5dge5sf' \
 Content-Type:'application/json'

```

> El codigo anterior devuelve un JSON estructurado así:

```json
{
  "success": true,
  "loginResponse": {
    "cardNumber": "23473882130001",
    "idUser": 128,
    "idStatus": 1,
    "alias": "23473882130001",
    "dni": "9739347348",
    "keyOnboard": "keyOnboard",
    "pinDriver": "",
    "clabe": "646180585000000001",
    "telephone": "2347388213",
    "fullName": "ALFREDO MEDINA REYES",
    "catalogVersion": 5,
    "email": "amedina@test.com",
    "virtualAccount": "299873",
    "idSucursal": "6506",
    "state": "Durango",
    "registrationDate": "2022-04-19",
    "userApp": "",
    "address": {
      "street": "Lilas ",
      "exteriorNumber": "34",
      "postalCode": "73483",
      "location": "Las Palmas",
      "city": "Durango",
      "state": "Durango"
    },
    "mobilePin": "86267",
    "secundaryClabeAccount": "ND"
  },
  "cardsResponse": {
    "card": [
      {
        "acount": "299873",
        "saldos": {
          "availableConsumptions": "55.80",
          "cardAvailableBalance": 1.5
        },
        "cardObFuscated": "491049******XXXX",
        "dateVen": "2030-08",
        "typeCard": "fisica",
        "stateCard": "01",
        "name": "ALFREDO MEDINA REYES",
        "typeDocument": "INE",
        "documentNumber": "9739347348",
        "alias": "23473882130001",
        "clabe": "646180585000000001"
      }
    ]
  }
}
```

### HTTP Request

`POST http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/login`

### Query Parameters

Parameter | Description
--------- | -----------
user | Usuario
mail | Correo electronico
password | Clave asignada 
latitude | Latitud de la ubicacion actual
longitude | Longitud de la ubicacion actual 
dateTransaction | Fecha 
hourTransaction | Hora 
os | Sistema operativo del dispositivo
systemOperativeName | Nombre de la version del sistema operativo
systemOperativeVersion | Numero de la version del sistema operativo
manufacturer | Creado por o marca 
model | Modelo del dispositivo
latitude | Latitud de la ubicacion actual
longitude | Longitud de la ubicacion actual 
nameApp | Nombre de la aplicacion 
versionApp | Numero de version de la app
enviroment | Entorno - desarrollo o produccion 
versionConnector | Numero de version del conector
 

## getCardsCustom  <span style="float: right; color:#0f70ec;font-size: 9px;">POST</span>

Devuelve información de la cuenta asignada, número de cuenta, CLABE, tarjeta,  estado de tarjeta, saldo de la cuenta y datos generales. 

> JSON a encriptar:

``` json
{
    "cardsRequest": {
      "authentication": {
        "user": "test@gmail.com",
        "mail": "test@gmail.com"
      },
      "latitude": "-123r431",
      "longitude": "-543234223",
      "dateTransaction": "20250114",
      "hourTransaction": "154507",
      "cardNumber": "23473882130001"
    },
    "device": {
      "os": "Android",
      "systemOperativeName": "Version Desconocida",
      "systemOperativeVersion": "15",
      "manufacturer": "Google",
      "model": "Pixel 7 Pro",
      "latitude": "19.6735912",
      "longitude": "-99.0711819"
    },
    "appInfo": {
      "nameApp": "eVolv",
      "versionApp": "21.0.0",
      "enviroment": "SANDBOX",
      "versionConnector": "2.0.0"
    }
}
```

```java
// JAVA - Unirest
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.post("http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/getCardsCustom")
  .header("Content-Type", "application/json")
  .body("{\n  \"cardsRequest\": {\n    \"authentication\": {\n      \"user\": \"test@gmail.com\",\n      \"mail\": \"test@gmail.com\"\n    },\n    \"latitude\": \"-123r431\",\n    \"longitude\": \"-543234223\",\n    \"dateTransaction\": \"20250114\",\n    \"hourTransaction\": \"154507\",\n    \"cardNumber\": \"23473882130001\"\n  },\n  \"device\": {\n    \"os\": \"Android\",\n    \"systemOperativeName\": \"Version Desconocida\",\n    \"systemOperativeVersion\": \"15\",\n    \"manufacturer\": \"Google\",\n    \"model\": \"Pixel 7 Pro\",\n    \"latitude\": \"19.6735912\",\n    \"longitude\": \"-99.0711819\"\n  },\n  \"appInfo\": {\n    \"nameApp\": \"eVolv\",\n    \"versionApp\": \"21.0.0\",\n    \"enviroment\": \"SANDBOX\",\n    \"versionConnector\": \"2.0.0\"\n  }\n}")
  .asString();

```

```php
// PHP - cURL
$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/getCardsCustom',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'POST',
  CURLOPT_POSTFIELDS =>'{
    "key": "lvGrF8xZz/q1XSetxqRkkB665GRwUZs2QPriwCf1gYAAtR96r/qMWf1Wa9TYrrfB\\u003d\\u003d",
    "data": "OiBBsLVP2t+0yaeKsvp1F9yPRAA8fnp765QVoA/LmvMzC2z0fZP12GcIjr9rtg/..66." 
  }',
  CURLOPT_HTTPHEADER => array(
    'Content-Type: application/json'
  ),
));

$response = curl_exec($curl);

curl_close($curl);
echo $response;

```

```ruby
# RUBY - Net::HTTP
require "uri"
require "json"
require "net/http"

url = URI("http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/getCardsCustom")

http = Net::HTTP.new(url.host, url.port);
request = Net::HTTP::Post.new(url)
request["Content-Type"] = "application/json"
request.body = JSON.dump({
  "cardsRequest": {
    "authentication": {
      "user": "test@gmail.com",
      "mail": "test@gmail.com"
    },
    "latitude": "-123r431",
    "longitude": "-543234223",
    "dateTransaction": "20250114",
    "hourTransaction": "154507",
    "cardNumber": "23473882130001"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
})

response = http.request(request)
puts response.read_body

```

```python
# PYTHON - http.client
import http.client
import json

conn = http.client.HTTPConnection("sdbx-aldebaran.kashplataforma.com/")
payload = json.dumps({
  "cardsRequest": {
    "authentication": {
      "user": "test@gmail.com",
      "mail": "test@gmail.com"
    },
    "latitude": "-123r431",
    "longitude": "-543234223",
    "dateTransaction": "20250114",
    "hourTransaction": "154507",
    "cardNumber": "23473882130001"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
})
headers = {
  'Content-Type': 'application/json'
}
conn.request("POST", "/aldbrn/internal/aw/getCardsCustom", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))

```

```shell
# SHELL - httpie
printf '{
  "cardsRequest": {
    "authentication": {
      "user": "test@gmail.com",
      "mail": "test@gmail.com"
    },
    "latitude": "-123r431",
    "longitude": "-543234223",
    "dateTransaction": "20250114",
    "hourTransaction": "154507",
    "cardNumber": "23473882130001"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
}'| http  --follow --timeout 3600 POST 'http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/getCardsCustom' \
 Content-Type:'application/json'

```


> El codigo anterior devuelve un JSON estructurado así:

```json
{
  "success": true,
  "cardsResponse": {
    "card": [
      {
        "number": "230958******2223",
        "acount": "30470",
        "saldos": {
          "availableConsumptions": "0.00",
          "cardAvailableBalance": 0
        },
        "cardObFuscated": "230958******2223",
        "dateVen": "",
        "typeCard": "virtual",
        "stateCard": "28",
        "name": "SAMUEL ANGEL LAYNES",
        "typeDocument": "INE",
        "documentNumber": "0009104660177",
        "alias": "98122388120001",
        "clabe": "002028650669177678"
      }
    ]
  }
}

```

### HTTP Request

`POST /aldbrn/internal/aw/getCardsCustom `

### Query Parameters

Parameter cardsRequest -> authentication | Description
--------- | -----------
user | test@gmail.com
mail | test@gmail.com

Parameter cardsRequest | Description
--------- | -----------
latitude | Latitud de la ubicacion
longitud | Longitud de la ubicacion
dateTransactio | Fecha de la transaccion
hourTransactio | Hora de la transaccion
cardNumbe | Número de Tarjeta

Parameter device | Description
--------- | -----------
os | Sistema operativo del dispositivo en uso
systemOperativeName | Nombre de la version del sistema operativo
systemOperativeVersion | Número de la version del sistema operativo
manufacturer | Google
model | Modelo del dispositivo en uso
latitude | Latitud de la ubicacion
longitude | Longitud de la ubicacion

Parameter appInfo | Description
--------- | -----------
nameApp | Nombre de la aplicacion
versionApp | Version de la aplicacion
enviroment | Entorno de ejecucion de la aplicacion
versionConnector | Número de la version del conector de la aplicacion


 

## statusByCard <span style="float: right; color:#0f70ec;font-size: 9px;">POST</span>

Obtiene el estatus de la tarjeta junto con los datos asociados a la cuenta.

```java
// JAVA - Unirest
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.post("http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/statusByCard")
  .header("Content-Type", "application/json")
  .body("{\n  \"statusByCardRequest\": {\n    \"authentication\": {\n      \"user\": \"test@gmail.com\",\n      \"mail\": \"test@gmail.com\"\n    },\n    \"latitude\": \"-123r431\",\n    \"longitude\": \"-543234223\",\n    \"dateTransaction\": \"20230114\",\n    \"hourTransaction\": \"160442\",\n    \"cardNumber\": \"23473882130001\"\n  },\n  \"device\": {\n    \"os\": \"Android\",\n    \"systemOperativeName\": \"Version Desconocida\",\n    \"systemOperativeVersion\": \"15\",\n    \"manufacturer\": \"Google\",\n    \"model\": \"Pixel 7 Pro\",\n    \"latitude\": \"19.6735912\",\n    \"longitude\": \"-99.0711819\"\n  },\n  \"appInfo\": {\n    \"nameApp\": \"eVolv\",\n    \"versionApp\": \"21.0.0\",\n    \"enviroment\": \"SANDBOX\",\n    \"versionConnector\": \"2.0.0\"\n  }\n}")
  .asString();

```

```php
// PHP - cURL
$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/statusByCard',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'POST',
  CURLOPT_POSTFIELDS =>'{
    "statusByCardRequest": {
      "authentication": {
        "user": "test@gmail.com",
        "mail": "test@gmail.com"
      },
      "latitude": "-123r431",
      "longitude": "-543234223",
      "dateTransaction": "20230114",
      "hourTransaction": "160442",
      "cardNumber": "23473882130001"
    },
    "device": {
      "os": "Android",
      "systemOperativeName": "Version Desconocida",
      "systemOperativeVersion": "15",
      "manufacturer": "Google",
      "model": "Pixel 7 Pro",
      "latitude": "19.6735912",
      "longitude": "-99.0711819"
    },
    "appInfo": {
      "nameApp": "eVolv",
      "versionApp": "21.0.0",
      "enviroment": "SANDBOX",
      "versionConnector": "2.0.0"
    }
  }',
  CURLOPT_HTTPHEADER => array(
    'Content-Type: application/json'
  ),
));

$response = curl_exec($curl);

curl_close($curl);
echo $response;

```

```ruby
# RUBY - Net::HTTP
require "uri"
require "json"
require "net/http"

url = URI("http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/statusByCard")

http = Net::HTTP.new(url.host, url.port);
request = Net::HTTP::Post.new(url)
request["Content-Type"] = "application/json"
request.body = JSON.dump({
  "statusByCardRequest": {
    "authentication": {
      "user": "test@gmail.com",
      "mail": "test@gmail.com"
    },
    "latitude": "-123r431",
    "longitude": "-543234223",
    "dateTransaction": "20230114",
    "hourTransaction": "160442",
    "cardNumber": "23473882130001"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
})

response = http.request(request)
puts response.read_body

```

```python
# PYTHON - http.client
import http.client
import json

conn = http.client.HTTPConnection("sdbx-aldebaran.kashplataforma.com")
payload = json.dumps({
  "statusByCardRequest": {
    "authentication": {
      "user": "test@gmail.com",
      "mail": "test@gmail.com"
    },
    "latitude": "-123r431",
    "longitude": "-543234223",
    "dateTransaction": "20230114",
    "hourTransaction": "160442",
    "cardNumber": "23473882130001"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
})
headers = {
  'Content-Type': 'application/json'
}
conn.request("POST", "/aldbrn/internal/aw/statusByCard", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))

```

```shell
# SHELL - httpie
printf '{
  "statusByCardRequest": {
    "authentication": {
      "user": "test@gmail.com",
      "mail": "test@gmail.com"
    },
    "latitude": "-123r431",
    "longitude": "-543234223",
    "dateTransaction": "20230114",
    "hourTransaction": "160442",
    "cardNumber": "23473882130001"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
}'| http  --follow --timeout 3600 POST 'http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/statusByCard' \
 Content-Type:'application/json'

```


> El codigo anterior devuelve un JSON estructurado así:

```json
{
  "success": true,
  "statusByCardResponse": {
    "card": {
      "number": "491049******XXXX",
      "acount": "25519",
      "productCard": {
        "code": "Q274",
        "description": "KASH PREPAGO"
      },
      "state": {
        "code": "01",
        "description": "Operativa"
      },
      "validity": {
        "current": "08/2030",
        "previous": "08/2030"
      },
      "person": {
        "denomination": "ALFREDO MEDINA REYES",
        "document": {
          "type": "INE",
          "iden": "9739347348"
        },
        "telephone": "2347388213",
        "address": "Lilas 34",
        "postalCode": "73483",
        "location": "Las Palmas",
        "country": {
          "code": "484",
          "description": "MEXICO"
        }
      },
      "typeCard": "fisica"
    }
  }
}
```

### HTTP Request

`POST http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/statusByCard`

### Query Parameters

Parameter statusByCardRequest -> authentication | Description
--------- | -----------
user | 
mail | Correo electronico válido

Parameter statusByCardRequest | Description
--------- | -----------
latitude | Latitud de la ubicacion
longitud | Longitud de la ubicacion
dateTransactio | Fecha de la transaccion
hourTransactio | Hora de la transaccion
cardNumbe | Número de Tarjeta

Parameter device | Description
--------- | -----------
os | Sistema operativo del dispositivo en uso
systemOperativeName | Nombre de la version del sistema operativo
systemOperativeVersion | Número de la version del sistema operativo
manufacturer | Google
model | Modelo del dispositivo en uso
latitude | Latitud de la ubicacion
longitude | Longitud de la ubicacion

Parameter appInfo | Description
--------- | -----------
nameApp | Nombre de la aplicacion
versionApp | Version de la aplicacion
enviroment | Entorno de ejecucion de la aplicacion
versionConnector | Número de la version del conector de la aplicacion


## getBalanceTrn  <span style="float: right; color:#0f70ec;font-size: 9px;">POST</span>

Consulta saldo con movimientos de las transacciones con tarjeta. 


```java
// JAVA - Unirest
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.post("http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/getBalanceTrn ")
  .header("Content-Type", "application/json")
  .body("{\n  \"balanceTransactionsRequest\": {\n    \"authentication\": {\n      \"user\": \"test@gmail.com\",\n      \"mail\": \"test@gmail.com\"\n    },\n    \"latitude\": \"-123r431\",\n    \"longitude\": \"-543234223\",\n    \"dateTransaction\": \"20250114\",\n    \"hourTransaction\": \"203058\",\n    \"transactionSecuence\": \"230\",\n    \"cardNumber\": \"23473882130001\"\n  },\n  \"device\": {\n    \"os\": \"Android\",\n    \"systemOperativeName\": \"Version Desconocida\",\n    \"systemOperativeVersion\": \"15\",\n    \"manufacturer\": \"Google\",\n    \"model\": \"Pixel 7 Pro\",\n    \"latitude\": \"19.6735912\",\n    \"longitude\": \"-99.0711819\"\n  },\n  \"appInfo\": {\n    \"nameApp\": \"eVolv\",\n    \"versionApp\": \"21.0.0\",\n    \"enviroment\": \"SANDBOX\",\n    \"versionConnector\": \"2.0.0\"\n  }\n}")
  .asString();

```

```php
// PHP - cURL
$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/getBalanceTrn ',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'POST',
  CURLOPT_POSTFIELDS =>'{
    "balanceTransactionsRequest": {
      "authentication": {
        "user": "test@gmail.com",
        "mail": "test@gmail.com"
      },
      "latitude": "-123r431",
      "longitude": "-543234223",
      "dateTransaction": "20250114",
      "hourTransaction": "203058",
      "transactionSecuence": "230",
      "cardNumber": "23473882130001"
    },
    "device": {
      "os": "Android",
      "systemOperativeName": "Version Desconocida",
      "systemOperativeVersion": "15",
      "manufacturer": "Google",
      "model": "Pixel 7 Pro",
      "latitude": "19.6735912",
      "longitude": "-99.0711819"
    },
    "appInfo": {
      "nameApp": "eVolv",
      "versionApp": "21.0.0",
      "enviroment": "SANDBOX",
      "versionConnector": "2.0.0"
    }
  }',
  CURLOPT_HTTPHEADER => array(
    'Content-Type: application/json'
  ),
));

$response = curl_exec($curl);

curl_close($curl);
echo $response;

```

```ruby
# RUBY - Net::HTTP
require "uri"
require "json"
require "net/http"

url = URI("http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/getBalanceTrn ")

http = Net::HTTP.new(url.host, url.port);
request = Net::HTTP::Post.new(url)
request["Content-Type"] = "application/json"
request.body = JSON.dump({
  "balanceTransactionsRequest": {
    "authentication": {
      "user": "test@gmail.com",
      "mail": "test@gmail.com"
    },
    "latitude": "-123r431",
    "longitude": "-543234223",
    "dateTransaction": "20250114",
    "hourTransaction": "203058",
    "transactionSecuence": "230",
    "cardNumber": "23473882130001"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
})

response = http.request(request)
puts response.read_body

```

```python
# PYTHON - http.client
import http.client
import json

conn = http.client.HTTPConnection("sdbx-aldebaran.kashplataforma.com")
payload = json.dumps({
  "balanceTransactionsRequest": {
    "authentication": {
      "user": "test@gmail.com",
      "mail": "test@gmail.com"
    },
    "latitude": "-123r431",
    "longitude": "-543234223",
    "dateTransaction": "20250114",
    "hourTransaction": "203058",
    "transactionSecuence": "230",
    "cardNumber": "23473882130001"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
})
headers = {
  'Content-Type': 'application/json'
}
conn.request("POST", "/aldbrn/internal/aw/getBalanceTrn ", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))

```

```shell
# SHELL - httpie
printf '{
  "balanceTransactionsRequest": {
    "authentication": {
      "user": "test@gmail.com",
      "mail": "test@gmail.com"
    },
    "latitude": "-123r431",
    "longitude": "-543234223",
    "dateTransaction": "20250114",
    "hourTransaction": "203058",
    "transactionSecuence": "230",
    "cardNumber": "23473882130001"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
}'| http  --follow --timeout 3600 POST 'http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/getBalanceTrn ' \
 Content-Type:'application/json'

```


> El codigo anterior devuelve un JSON estructurado así:

```json
{
  "success": true,
  "balanceTransactionsResponse": {
    "availableConsumptions": 505.8,
    "transactions": [
      {
        "id": 7768484,
        "date": "20210107",
        "hour": "101546",
        "comerce": "T|TRANSF|pago com|646180585000000001",
        "amount": "13.62",
        "descriptionAmount": "",
        "numberAutorization": "6532054",
        "idOperationType": 29,
        "operationDescription": "DEPÓSITO ENTRE CUENTAS",
        "idStatus": 4,
        "statusDescription": "APROBADA"
      },
      {
        "id": 8484993,
        "date": "20210106",
        "hour": "160648",
        "comerce": "BANCOPPEL ****1326",
        "amount": "-9000.00",
        "descriptionAmount": "",
        "numberAutorization": "6527558",
        "idOperationType": 1,
        "operationDescription": "Transferencia SPEI - Otros Bancos",
        "idStatus": 15,
        "statusDescription": "CONFIRMADA"
      }
    ]
  }
}
```

### HTTP Request

`POST http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/getBalanceTrn`

### Query Parameters

Parameter -> balanceTransactionsRequest -> authentication | Description
--------- | -----------
user | test@gmail.com
mail | test@gmail.com

Parameter -> balanceTransactionsRequest -> authentication | Description
--------- | -----------
latitude | Latitud de la ubicacion
longitud | Longitud de la ubicacion
dateTransactio | Fecha de la transaccion
hourTransactio | Hora de la transaccion
transactionSecuence | Número de secuencia de la transaccion
cardNumber | Número de la tarjeta

Parameter device | Description
--------- | -----------
os | Sistema operativo del dispositivo en uso
systemOperativeName | Nombre de la version del sistema operativo
systemOperativeVersion | Número de la version del sistema operativo
manufacturer | Google
model | Modelo del dispositivo en uso
latitude | Latitud de la ubicacion
longitude | Longitud de la ubicacion

Parameter appInfo | Description
--------- | -----------
nameApp | Nombre de la aplicacion
versionApp | Version de la aplicacion
enviroment | Entorno de ejecucion de la aplicacion
versionConnector | Número de la version del conector de la aplicacion


## generateCvv  <span style="float: right; color:#0f70ec;font-size: 9px;">POST</span>

Genera un CVV dinámico virtual con un tiempo de expiración de 5 minutos, el  control del timer debe ser gestionado por el cliente UIX que lo implementa. 


```java
// JAVA - Unirest
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.post("http://35.174.63.34/aldbrn/internal/aw/generateCvv")
  .header("Content-Type", "application/json")
  .body("{\n  \"cvvDinamicRequest\": {\n    \"cardNumber\": \"23473882130001\",\n    \"tipoCvv\": \"D\"\n  },\n  \"device\": {\n    \"os\": \"Android\",\n    \"systemOperativeName\": \"Version Desconocida\",\n    \"systemOperativeVersion\": \"15\",\n    \"manufacturer\": \"Google\",\n    \"model\": \"Pixel 7 Pro\",\n    \"latitude\": \"19.6735912\",\n    \"longitude\": \"-99.0711819\"\n  },\n  \"appInfo\": {\n    \"nameApp\": \"eVolv\",\n    \"versionApp\": \"21.0.0\",\n    \"enviroment\": \"SANDBOX\",\n    \"versionConnector\": \"2.0.0\"\n  }\n}")
  .asString();

```

```php
// PHP - cURL
$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/generateCvv',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'POST',
  CURLOPT_POSTFIELDS =>'{
    "cvvDinamicRequest": {
      "cardNumber": "23473882130001",
      "tipoCvv": "D"
    },
    "device": {
      "os": "Android",
      "systemOperativeName": "Version Desconocida",
      "systemOperativeVersion": "15",
      "manufacturer": "Google",
      "model": "Pixel 7 Pro",
      "latitude": "19.6735912",
      "longitude": "-99.0711819"
    },
    "appInfo": {
      "nameApp": "eVolv",
      "versionApp": "21.0.0",
      "enviroment": "SANDBOX",
      "versionConnector": "2.0.0"
    }
  }',
  CURLOPT_HTTPHEADER => array(
    'Content-Type: application/json'
  ),
));

$response = curl_exec($curl);

curl_close($curl);
echo $response;

```

```ruby
# RUBY - Net::HTTP
require "uri"
require "json"
require "net/http"

url = URI("http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/generateCvv")

http = Net::HTTP.new(url.host, url.port);
request = Net::HTTP::Post.new(url)
request["Content-Type"] = "application/json"
request.body = JSON.dump({
  "cvvDinamicRequest": {
    "cardNumber": "23473882130001",
    "tipoCvv": "D"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
})

response = http.request(request)
puts response.read_body

```

```python
# PYTHON - http.client
import http.client
import json

conn = http.client.HTTPConnection("sdbx-aldebaran.kashplataforma.com")
payload = json.dumps({
  "cvvDinamicRequest": {
    "cardNumber": "23473882130001",
    "tipoCvv": "D"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
})
headers = {
  'Content-Type': 'application/json'
}
conn.request("POST", "/aldbrn/internal/aw/generateCvv", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))

```

```shell
# SHELL - httpie
printf '{
  "cvvDinamicRequest": {
    "cardNumber": "23473882130001",
    "tipoCvv": "D"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
}'| http  --follow --timeout 3600 POST 'http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/generateCvv' \
 Content-Type:'application/json'

```


> El codigo anterior devuelve un JSON estructurado así:

```json
{
  "success": true,
  "cvvDinamicResponse": {
    "valueCVC": "429",
    "tiempoRestante": 5
  }
}

```

### HTTP Request

`POST http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/generateCvv`

### Query Parameters

Parameter cvvDinamicRequest | Description
--------- | -----------
cardNumber | Número de la tarjeta
tipoCvv | Tipo de CVV

Parameter device | Description
--------- | -----------
os | Sistema operativo del dispositivo en uso
systemOperativeName | Nombre de la version del sistema operativo
systemOperativeVersion | Número de la version del sistema operativo
manufacturer | Google
model | Modelo del dispositivo en uso
latitude | Latitud de la ubicacion
longitude | Longitud de la ubicacion 

Parameter appInfo | Description
--------- | -----------
nameApp | Nombre de la aplicacion
versionApp | Version de la aplicacion
enviroment | Entorno de ejecucion de la aplicacion
versionConnector | Número de la version del conector de la aplicacion 


## getPinCard <span style="float: right; color:#0f70ec;font-size: 9px;">POST</span>

Obtiene el NIP de la tarjeta en el CardManager. 


```java
// JAVA - Unirest
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.post("http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/getPinCard ")
  .header("Content-Type", "application/json")
  .body("{\n  \"getPinCard\": {\n    \"cardNumber\": \"23473882130001\"\n  },\n  \"device\": {\n    \"os\": \"Android\",\n    \"systemOperativeName\": \"Version Desconocida\",\n    \"systemOperativeVersion\": \"15\",\n    \"manufacturer\": \"Google\",\n    \"model\": \"Pixel 7 Pro\",\n    \"latitude\": \"19.6735912\",\n    \"longitude\": \"-99.0711819\"\n  },\n  \"appInfo\": {\n    \"nameApp\": \"eVolv\",\n    \"versionApp\": \"21.0.0\",\n    \"enviroment\": \"SANDBOX\",\n    \"versionConnector\": \"2.0.0\"\n  }\n}")
  .asString();

```

```php
// PHP - cURL
$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/getPinCard ',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'POST',
  CURLOPT_POSTFIELDS =>'{
    "getPinCard": {
      "cardNumber": "23473882130001"
    },
    "device": {
      "os": "Android",
      "systemOperativeName": "Version Desconocida",
      "systemOperativeVersion": "15",
      "manufacturer": "Google",
      "model": "Pixel 7 Pro",
      "latitude": "19.6735912",
      "longitude": "-99.0711819"
    },
    "appInfo": {
      "nameApp": "eVolv",
      "versionApp": "21.0.0",
      "enviroment": "SANDBOX",
      "versionConnector": "2.0.0"
    }
  }',
  CURLOPT_HTTPHEADER => array(
    'Content-Type: application/json'
  ),
));

$response = curl_exec($curl);

curl_close($curl);
echo $response;


```

```ruby
# RUBY - Net::HTTP
require "uri"
require "json"
require "net/http"

url = URI("http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/getPinCard ")

http = Net::HTTP.new(url.host, url.port);
request = Net::HTTP::Post.new(url)
request["Content-Type"] = "application/json"
request.body = JSON.dump({
  "getPinCard": {
    "cardNumber": "23473882130001"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
})

response = http.request(request)
puts response.read_body

```

```python
# PYTHON - http.client
import http.client
import json

conn = http.client.HTTPConnection("sdbx-aldebaran.kashplataforma.com")
payload = json.dumps({
  "getPinCard": {
    "cardNumber": "23473882130001"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
})
headers = {
  'Content-Type': 'application/json'
}
conn.request("POST", "/aldbrn/internal/aw/getPinCard ", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))

```

```shell
# SHELL - httpie
printf '{
  "getPinCard": {
    "cardNumber": "23473882130001"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
}'| http  --follow --timeout 3600 POST 'http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/getPinCard' \
 Content-Type:'application/json'

```


> El codigo anterior devuelve un JSON estructurado así:

```json
{
  "success": true,
  "obtenerPinResponse": {
    "pin": "9507"
  }
}

```

### HTTP Request

`POST http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/getPinCard`

### Query Parameters

Parameter getPinCard | Description
--------- | -----------
cardNumber | Número de tarjeta

Parameter device | Description
--------- | -----------
os | Sistema operativo del dispositivo en uso
systemOperativeName | Nombre de la version del sistema operativo
systemOperativeVersion | Número de la version del sistema operativo
manufacturer | Google
model | Modelo del dispositivo en uso
latitude | Latitud de la ubicacion
longitude | Longitud de la ubicacion 

Parameter appInfo | Description
--------- | -----------
nameApp | Nombre de la aplicacion
versionApp | Version de la aplicacion
enviroment | Entorno de ejecucion de la aplicacion
versionConnector | Número de la version del conector de la aplicacion 



## changeStatusByCard <span style="float: right; color:#0f70ec;font-size: 9px;">POST</span>

Realizar el cambio de estado/status y se actualiza el estatus de la tarjeta, los  estatus se mencionan a continuación: 


```java
// JAVA - Unirest
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.post("http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/changeStatusByCard")
  .header("Content-Type", "application/json")
  .body("{\n  \"changeStatusCardRequest\": {\n    \"authentication\": {\n      \"user\": \"test@gmail.com\",\n      \"mail\": \"test@gmail.com\"\n    },\n    \"latitude\": \"-123r431\",\n    \"longitude\": \"-543234223\",\n    \"dateTransaction\": \"20250114\",\n    \"hourTransaction\": \"211515\",\n    \"cardNumber\": \"23473882130001\",\n    \"newState\": \"01\"\n  },\n  \"device\": {\n    \"os\": \"Android\",\n    \"systemOperativeName\": \"Version Desconocida\",\n    \"systemOperativeVersion\": \"15\",\n    \"manufacturer\": \"Google\",\n    \"model\": \"Pixel 7 Pro\",\n    \"latitude\": \"19.6735912\",\n    \"longitude\": \"-99.0711819\"\n  },\n  \"appInfo\": {\n    \"nameApp\": \"eVolv\",\n    \"versionApp\": \"21.0.0\",\n    \"enviroment\": \"SANDBOX\",\n    \"versionConnector\": \"2.0.0\"\n  }\n}")
  .asString();

```

```php
// PHP - cURL
$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/changeStatusByCard',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'POST',
  CURLOPT_POSTFIELDS =>'{
    "changeStatusCardRequest": {
      "authentication": {
        "user": "test@gmail.com",
        "mail": "test@gmail.com"
      },
      "latitude": "-123r431",
      "longitude": "-543234223",
      "dateTransaction": "20250114",
      "hourTransaction": "211515",
      "cardNumber": "23473882130001",
      "newState": "01"
    },
    "device": {
      "os": "Android",
      "systemOperativeName": "Version Desconocida",
      "systemOperativeVersion": "15",
      "manufacturer": "Google",
      "model": "Pixel 7 Pro",
      "latitude": "19.6735912",
      "longitude": "-99.0711819"
    },
    "appInfo": {
      "nameApp": "eVolv",
      "versionApp": "21.0.0",
      "enviroment": "SANDBOX",
      "versionConnector": "2.0.0"
    }
  }',
  CURLOPT_HTTPHEADER => array(
    'Content-Type: application/json'
  ),
));

$response = curl_exec($curl);

curl_close($curl);
echo $response;

```

```ruby
# RUBY - Net::HTTP
require "uri"
require "json"
require "net/http"

url = URI("http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/changeStatusByCard")

http = Net::HTTP.new(url.host, url.port);
request = Net::HTTP::Post.new(url)
request["Content-Type"] = "application/json"
request.body = JSON.dump({
  "changeStatusCardRequest": {
    "authentication": {
      "user": "test@gmail.com",
      "mail": "test@gmail.com"
    },
    "latitude": "-123r431",
    "longitude": "-543234223",
    "dateTransaction": "20250114",
    "hourTransaction": "211515",
    "cardNumber": "23473882130001",
    "newState": "01"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
})

response = http.request(request)
puts response.read_body

```

```python
# PYTHON - http.client
import http.client
import json

conn = http.client.HTTPConnection("sdbx-aldebaran.kashplataforma.com")
payload = json.dumps({
  "changeStatusCardRequest": {
    "authentication": {
      "user": "test@gmail.com",
      "mail": "test@gmail.com"
    },
    "latitude": "-123r431",
    "longitude": "-543234223",
    "dateTransaction": "20250114",
    "hourTransaction": "211515",
    "cardNumber": "23473882130001",
    "newState": "01"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
})
headers = {
  'Content-Type': 'application/json'
}
conn.request("POST", "/aldbrn/internal/aw/changeStatusByCard", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))

```

```shell
# SHELL - httpie
printf '{
  "changeStatusCardRequest": {
    "authentication": {
      "user": "test@gmail.com",
      "mail": "test@gmail.com"
    },
    "latitude": "-123r431",
    "longitude": "-543234223",
    "dateTransaction": "20250114",
    "hourTransaction": "211515",
    "cardNumber": "23473882130001",
    "newState": "01"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
}'| http  --follow --timeout 3600 POST 'http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/changeStatusByCard' \
 Content-Type:'application/json'

```


> El codigo anterior devuelve un JSON estructurado así:

```json
{
  "success": true
}

```

### HTTP Request

`POST http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/changeStatusByCard`

### Query Parameters

Parameter changeStatusCardRequest -> authentication | Description
--------- | -----------
      "user": "test@gmail.com",
      "mail": "test@gmail.com"

Parameter changeStatusCardRequest | Description
--------- | -----------  
latitude | Latitud de la ubicacion
longitud | Longitud de la ubicacion
dateTransactio | Fecha de la transaccion
hourTransactio | Hora de la transaccion
cardNumber | Número de la tarjeta
newState | Valor del estatus a modificar

Parameter device | Description
--------- | -----------
os | Sistema operativo del dispositivo en uso
systemOperativeName | Nombre de la version del sistema operativo
systemOperativeVersion | Número de la version del sistema operativo
manufacturer | Google
model | Modelo del dispositivo en uso
latitude | Latitud de la ubicacion
longitude | Longitud de la ubicacion 

Parameter appInfo | Description
--------- | -----------
nameApp | Nombre de la aplicacion
versionApp | Version de la aplicacion
enviroment | Entorno de ejecucion de la aplicacion
versionConnector | Número de la version del conector de la aplicacion 


## applyforCard <span style="float: right; color:#0f70ec;font-size: 9px;">POST</span>

Asigna una nueva tarjeta a un usuario ya registrado. 


```java
// JAVA - Unirest
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.post("http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/applyforCard")
  .header("Content-Type", "application/json")
  .body("{\n  \"registerOnboardRequest\": {\n    \"idUser\": \"1795\",\n    \"cardNumber\": \"1234\"\n  },\n  \"device\": {\n    \"os\": \"Android\",\n    \"systemOperativeName\": \"Version Desconocida\",\n    \"systemOperativeVersion\": \"15\",\n    \"manufacturer\": \"Google\",\n    \"model\": \"Pixel 7 Pro\",\n    \"latitude\": \"19.6735912\",\n    \"longitude\": \"-99.0711819\"\n  },\n  \"appInfo\": {\n    \"nameApp\": \"eVolv\",\n    \"versionApp\": \"21.0.0\",\n    \"enviroment\": \"SANDBOX\",\n    \"versionConnector\": \"2.0.0\"\n  }\n}")
  .asString();

```

```php
// PHP - cURL
$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/applyforCard',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'POST',
  CURLOPT_POSTFIELDS =>'{
  "registerOnboardRequest": {
    "idUser": "1795",
    "cardNumber": "1234"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
}',
  CURLOPT_HTTPHEADER => array(
    'Content-Type: application/json'
  ),
));

$response = curl_exec($curl);

curl_close($curl);
echo $response;

```

```ruby
# RUBY - Net::HTTP
require "uri"
require "json"
require "net/http"

url = URI("http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/applyforCard")

http = Net::HTTP.new(url.host, url.port);
request = Net::HTTP::Post.new(url)
request["Content-Type"] = "application/json"
request.body = JSON.dump({
  "registerOnboardRequest": {
    "idUser": "1795",
    "cardNumber": "1234"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
})

response = http.request(request)
puts response.read_body

```

```python
# PYTHON - http.client
import http.client
import json

conn = http.client.HTTPConnection("sdbx-aldebaran.kashplataforma.com")
payload = json.dumps({
  "registerOnboardRequest": {
    "idUser": "1795",
    "cardNumber": "1234"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
})
headers = {
  'Content-Type': 'application/json'
}
conn.request("POST", "/aldbrn/internal/aw/applyforCard", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))

```

```shell
# SHELL - httpie
printf '{
  "registerOnboardRequest": {
    "idUser": "1795",
    "cardNumber": "1234"
  },
  "device": {
    "os": "Android",
    "systemOperativeName": "Version Desconocida",
    "systemOperativeVersion": "15",
    "manufacturer": "Google",
    "model": "Pixel 7 Pro",
    "latitude": "19.6735912",
    "longitude": "-99.0711819"
  },
  "appInfo": {
    "nameApp": "eVolv",
    "versionApp": "21.0.0",
    "enviroment": "SANDBOX",
    "versionConnector": "2.0.0"
  }
}'| http  --follow --timeout 3600 POST 'http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/applyforCard' \
 Content-Type:'application/json'

```


> El codigo anterior devuelve un JSON estructurado así:

```json
{
  "success": true
}

```

### HTTP Request

`POST http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/applyforCard`

### Query Parameters

Parameter registerOnboardRequest | Description
--------- | -----------
idUser | 1795
cardNumber | 1234

Parameter device | Description
--------- | -----------
os | Android
systemOperativeName | Version Desconocida
systemOperativeVersion | 15
manufacturer | Google
model | Pixel 7 Pro
latitude | 19.6735912
longitude | -99.0711819

Parameter appInfo | Description
--------- | -----------
nameApp | eVolv
versionApp | 21.0.0
enviroment | SANDBOX
versionConnector | 2.0.0


## cardReplacement 

Realiza la sustitución de tarjeta de un usuario ya existente. 


```java
// JAVA - Unirest


```

```php
// PHP - cURL


```

```ruby
# RUBY - Net::HTTP


```

```python
# PYTHON - http.client


```

```shell
# SHELL - httpie


```


> El codigo anterior devuelve un JSON estructurado así:

```json


```

### HTTP Request

`POST http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/login`

### Query Parameters

Parameter | Description
--------- | -----------
user | Usuario


## cardRecharge 

Realiza un abono de saldo a la tarjeta del usuario. 


```java
// JAVA - Unirest


```

```php
// PHP - cURL


```

```ruby
# RUBY - Net::HTTP


```

```python
# PYTHON - http.client


```

```shell
# SHELL - httpie


```


> El codigo anterior devuelve un JSON estructurado así:

```json


```

### HTTP Request

`POST http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/login`

### Query Parameters

Parameter | Description
--------- | -----------
user | Usuario


## registerOnboard <span style="float: right; color:#0f70ec;font-size: 9px;">POST</span>

Registra en la plataforma a nuevos usuarios, es forzoso mandar los datos que  se indican. 


```java
// JAVA - Unirest


```

```php
// PHP - cURL


```

```ruby
# RUBY - Net::HTTP


```

```python
# PYTHON - http.client


```

```shell
# SHELL - httpie


```


> El codigo anterior devuelve un JSON estructurado así:

```json


```

### HTTP Request

`POST http://sdbx-aldebaran.kashplataforma.com/aldbrn/internal/aw/registerOnboard `

### Query Parameters

Parameter | Description
--------- | -----------
user | Usuario










# API OKPay
## Crear orden <span style="float: right; color:#0f70ec;font-size: 9px;">POST</span>

Este servicio ejecuta varios tipos de operaciones de Remesas. Transferir fondos (también conocido como SPEI), ingreso y retiro de efectivo, pago Swift, Mastercard y Visa.
El envío de dinero nacional se realiza a través del operativo "1 - Transferencia Interbancaria SPEI", se puede visualizar en la pestaña Modelo.

```java
// JAVA - Unirest
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.post("http://sdbx-centauri.kashplataforma.com/OKPay/createOrder")
  .header("Authorization", "Basic YWRtaW46c2VjcmV0")
  .header("Content-Type", "application/json")
  .body("{\n  \"alphanumericReference\": \"PROVV0011712091\",\n  \"amount\": 250,\n  \"beneficiaryAcount\": 87459854,\n  \"beneficiaryAcountSPEI\": 87459854,\n  \"beneficiaryAddress1\": \"Vicente G 132 Mz5 Lt14\",\n  \"beneficiaryAddress2\": \"Vicente G 132 Mz5 Lt14\",\n  \"beneficiaryCity\": \"CDMX\",\n  \"beneficiaryName\": \"Florentino Eter\",\n  \"beneficiaryTypeAcount\": \"string\",\n  \"codeInstituteBank\": \"002\",\n  \"concept\": \"Deposito Maria\",\n  \"currency\": 484,\n  \"dataAccreditationBeneficiary\": 99,\n  \"dateTransaction\": \"030421\",\n  \"description\": \"prueba\",\n  \"email\": \"testrest@gmail.com\",\n  \"idBank\": 4177,\n  \"idEntity\": 4177,\n  \"numericReference\": 43223123,\n  \"observation\": \"test@gmail.com\",\n  \"orderingAccount\": 111,\n  \"orderingTypeAcount\": \"string\",\n  \"postalCode\": 57800,\n  \"referenceCreatedPayment\": 811111112,\n  \"rfc\": \"ROPL951201091\",\n  \"typeTransaction\": 1\n}\n")
  .asString();

```

```php
// PHP - cURL
$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'http://sdbx-centauri.kashplataforma.com/OKPay/createOrder',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'POST',
  CURLOPT_POSTFIELDS =>'{
    "alphanumericReference": "PROVV0011712091",
    "amount": 250,
    "beneficiaryAcount": 87459854,
    "beneficiaryAcountSPEI": 87459854,
    "beneficiaryAddress1": "Vicente G 132 Mz5 Lt14",
    "beneficiaryAddress2": "Vicente G 132 Mz5 Lt14",
    "beneficiaryCity": "CDMX",
    "beneficiaryName": "Florentino Eter",
    "beneficiaryTypeAcount": "string",
    "codeInstituteBank": "002",
    "concept": "Deposito Maria",
    "currency": 484,
    "dataAccreditationBeneficiary": 99,
    "dateTransaction": "030421",
    "description": "prueba",
    "email": "testrest@gmail.com",
    "idBank": 4177,
    "idEntity": 4177,
    "numericReference": 43223123,
    "observation": "test@gmail.com",
    "orderingAccount": 111,
    "orderingTypeAcount": "string",
    "postalCode": 57800,
    "referenceCreatedPayment": 811111112,
    "rfc": "ROPL951201091",
    "typeTransaction": 1
  }',
  CURLOPT_HTTPHEADER => array(
    'Authorization: Bearer YWRtaW46c2VjcmV0',
    'Content-Type: application/json'
  ),
));

$response = curl_exec($curl);

curl_close($curl);
echo $response;

```

```ruby
# RUBY - Net::HTTP


```

```python
# PYTHON - http.client


```

```shell
# SHELL - httpie


```


> El codigo anterior devuelve un JSON estructurado así, si la respuesta success es true:

```json
{
  "success": true,
  "orderEntityResponse": {
    "createdAt": "2022-12-20T17:11:46.794+0000",
    "uptadedAt": "2022-12-20T17:11:46.794+0000",
    "id": "13699",
    "numericReference": "43223123",
    "alphanumericReference": "PROVV0011712091",
    "referenceCreatedPayment": "on000000011598",
    "status": 25,
    "initialBalance": 10000,
    "finalBalance": 10250
  },
  "totalItems": 0,
  "totalPages": 0,
  "currentPage": 0
}
```
> Si la respuesta success es false

```json
{
  "success": false,
  "error": {
    "name": "EntityException",
    "message": "No se pudo procesar el pago",
    "code": 1022
  },
  "totalItems": 0,
  "totalPages": 0,
  "currentPage": 0
}
```

### HTTP Request

`POST http://sdbx-centauri.kashplataforma.com/OKPay/createOrder`

### Query Parameters

Parameter | Description
--------- | -----------
alphanumericReference | Referencia alfanumerica aleatoria
amount | Monto de la transaccion
beneficiaryAcount | Cuenta beneficiaria
beneficiaryAddress1 | Direccion del beneficiario
beneficiaryCity |Ciudad del beneficiario
beneficiaryName | Nombre del beneficiario
beneficiaryTypeAcount | Tipo de la cuenta beneficiaria
codeInstituteBank | Codigo de la institucion
concept | Concepto de la transaccion
currency | 484 por default
dataAccreditationBeneficiary | 99
dateTransaction | 030421
description | descripcion de la transaccion
email | Correo electronico
idBank | ID de la institucion bancaria
idEntity | ID de la entidad 
numericReference | Referencia numerica
postalCode | Codigo postal
referenceCreatedPayment | 811111112
typeTransaction | Tipo de la transaccion






# API EntitiesServices

## addEntity <span style="float: right; color:#0f70ec;font-size: 9px;">POST</span>

```java
// JAVA - Unirest
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.post("http://sdbx-sirio.kashplataforma.com/EntitiesServices/addEntity")
  .header("Authorization", "Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hQGdtYWlsLmNvbSIsImV4cCI6MTc0MTczOTM1MSwiaWF0IjoxNzQxNzMzMzc1fQ.GbzFRlsbwtLH_JyZLzZN8kJNcYKCgYScUU_ClnywK3ZHIhftKlKI2W75TAfS0ONuQmvrnMDDpX9OS_nXc-JWDA")
  .header("Entity-i", "com.onsigna")
  .header("Content-Type", "application/json")
  .body("{\n  \"bussinesName\": \"Test kash2\",\n  \"bussinesNameShort\": \"SUC kash 2\",\n  \"contextFatherID\": \"com.onsigna\",\n  \"userRequest\": {   \n    \"email\": \"userkash2@gmail.com\",   \n    \"telephoneNumber\": \"565311100\",\n    \"user\": \"72663-7266-8j71-76129\" \n  }\n}")
  .asString();

```

```php
// PHP - cURL
$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'http://sdbx-sirio.kashplataforma.com/EntitiesServices/addEntity',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'POST',
  CURLOPT_POSTFIELDS =>'{
  "bussinesName": "Test kash2",
  "bussinesNameShort": "SUC kash 2",
  "contextFatherID": "com.onsigna",
  "userRequest": {   
    "email": "userkash2@gmail.com",   
    "telephoneNumber": "565311100",
    "user": "72663-7266-8j71-76129" 
  }
  }',
  CURLOPT_HTTPHEADER => array(
    'Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hQGdtYWlsLmNvbSIsImV4cCI6MTc0MTczOTM1MSwiaWF0IjoxNzQxNzMzMzc1fQ.GbzFRlsbwtLH_JyZLzZN8kJNcYKCgYScUU_ClnywK3ZHIhftKlKI2W75TAfS0ONuQmvrnMDDpX9OS_nXc-JWDA',
    'Entity-i: com.onsigna',
    'Content-Type: application/json'
  ),
));

$response = curl_exec($curl);

curl_close($curl);
echo $response;

```

```ruby
# RUBY - Net::HTTP
require "uri"
require "json"
require "net/http"

url = URI("http://sdbx-sirio.kashplataforma.com/EntitiesServices/addEntity")

http = Net::HTTP.new(url.host, url.port);
request = Net::HTTP::Post.new(url)
request["Authorization"] = "Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hQGdtYWlsLmNvbSIsImV4cCI6MTc0MTczOTM1MSwiaWF0IjoxNzQxNzMzMzc1fQ.GbzFRlsbwtLH_JyZLzZN8kJNcYKCgYScUU_ClnywK3ZHIhftKlKI2W75TAfS0ONuQmvrnMDDpX9OS_nXc-JWDA"
request["Entity-i"] = "com.onsigna"
request["Content-Type"] = "application/json"
request.body = JSON.dump({
  "bussinesName": "Test kash2",
  "bussinesNameShort": "SUC kash 2",
  "contextFatherID": "com.onsigna",
  "userRequest": {
    "email": "userkash2@gmail.com",
    "telephoneNumber": "565311100",
    "user": "72663-7266-8j71-76129"
  }
})

response = http.request(request)
puts response.read_body

```

```python
# PYTHON - http.client
import http.client
import json

conn = http.client.HTTPConnection("sdbx-sirio.kashplataforma.com")
payload = json.dumps({
  "bussinesName": "Test kash2",
  "bussinesNameShort": "SUC kash 2",
  "contextFatherID": "com.onsigna",
  "userRequest": {
    "email": "userkash2@gmail.com",
    "telephoneNumber": "565311100",
    "user": "72663-7266-8j71-76129"
  }
})
headers = {
  'Authorization': 'Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hQGdtYWlsLmNvbSIsImV4cCI6MTc0MTczOTM1MSwiaWF0IjoxNzQxNzMzMzc1fQ.GbzFRlsbwtLH_JyZLzZN8kJNcYKCgYScUU_ClnywK3ZHIhftKlKI2W75TAfS0ONuQmvrnMDDpX9OS_nXc-JWDA',
  'Entity-i': 'com.onsigna',
  'Content-Type': 'application/json'
}
conn.request("POST", "/EntitiesServices/addEntity", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))

```

```shell
# SHELL - httpie
printf '{
  "bussinesName": "Test kash2",
  "bussinesNameShort": "SUC kash 2",
  "contextFatherID": "com.onsigna",
  "userRequest": {   
    "email": "userkash2@gmail.com",   
    "telephoneNumber": "565311100",
    "user": "72663-7266-8j71-76129" 
  }
}'| http  --follow --timeout 3600 POST 'http://sdbx-sirio.kashplataforma.com/EntitiesServices/addEntity' \
 Authorization:'Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hQGdtYWlsLmNvbSIsImV4cCI6MTc0MTczOTM1MSwiaWF0IjoxNzQxNzMzMzc1fQ.GbzFRlsbwtLH_JyZLzZN8kJNcYKCgYScUU_ClnywK3ZHIhftKlKI2W75TAfS0ONuQmvrnMDDpX9OS_nXc-JWDA' \
 Entity-i:'com.onsigna' \
 Content-Type:'application/json'

```


> El codigo anterior devuelve un JSON estructurado así:

```json
{
  "success": true,
  "onsignaEntity": {
    "id": "72663-7266-8j71-76129",
    "name": "Test kash2",
    "prefix": "SUC kash 2",
    "fiscalID": null,
    "clabeAccount": "002028650641359744",  //CLABE CUENTA CITI
    "virtualAccount": "6506000004135974",  //CUENTA CHEQUES
    "branchAcount": null,
    "webhookURL": null,
    "email": "userkash2@gmail.com",
    "phoneNumber": "565311100",
    "fatherID": "com.onsigna",
    "balance": 0,
    "warrantyBalance": 0,
    "customerNetworkBalance": 0,
    "warrantyClabeAccount": null,
    "warrantyVirtualAccount": null,
    "cryptedCard": null,
    "newCard": null,
    "assignClabeAccount": true,
    "feeID": 0,
    "internalReference": null,
    "liquidationLevel": null,
    "liquidationType": null,
    "entityType": null,
    "affiliationId": null
  }
}

```

### HTTP Request

`POST http://sdbx-sirio.kashplataforma.com/EntitiesServices/addEntity`

### Query Parameters

Parameter | Description
--------- | -----------
bussinesName | Nombre de la entidad
bussinesNameShort | Nombre corto de la entidad
contextFatherID | ID de la entidad
email | Correo electronico 
telephoneNumber | Telefono
user | Usuario


## getBalance <span style="float: right; color:#029702;font-size: 9px;">GET</span>

Servicio para obtener el saldo de la Entidad/Subentidad

```java
// JAVA - Unirest
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.get("http://sdbx-sirio.kashplataforma.com/EntitiesServices/getBalance")
  .header("Authorization", "Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hQGdtYWlsLmNvbSIsImV4cCI6MTc0MDY5NTQwMywiaWF0IjoxNzQwNjg5NDI3fQ.gnLouIsKyB2qs22IqU6juVsTLwZfRHX-CnSH_pUWPUrmLOZHFr83MLEbFJIB5-mu0oyBhcA1DYP7Yz0_-rwN_w")
  .header("Entity-i", "com.onsigna")
  .asString();

```

```php
// PHP - cURL
$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'http://sdbx-sirio.kashplataforma.com/EntitiesServices/getBalance',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'GET',
  CURLOPT_HTTPHEADER => array(
    'Authorization: Bearer eeyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hQGdtYWlsLmNvbSIsImV4cCI6MTc0MDY5NTQwMywiaWF0IjoxNzQwNjg5NDI3fQ.gnLouIsKyB2qs22IqU6juVsTLwZfRHX-CnSH_pUWPUrmLOZHFr83MLEbFJIB5-mu0oyBhcA1DYP7Yz0_-rwN_w',
    "Entity-i: com.onsigna"
  ),
));

$response = curl_exec($curl);

curl_close($curl);
echo $response;

```

```ruby
# RUBY - Net::HTTP
require "uri"
require "net/http"

url = URI("http://sdbx-sirio.kashplataforma.com/EntitiesServices/getBalance")

http = Net::HTTP.new(url.host, url.port);
request = Net::HTTP::Get.new(url)
request["Authorization"] = "Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hQGdtYWlsLmNvbSIsImV4cCI6MTc0MTcxNjY4NywiaWF0IjoxNzQxNzEwNzExfQ.44L_ar-989GzU6KTKDgS6dqzOOI4OWkJzheuMSZNd9AnJKC09R0squVMlz7b_1huq56aX1G4n8kaJYVosixPAA"
request["Entity-i"] = "com.onsigna"

response = http.request(request)
puts response.read_body

```

```python
# PYTHON - http.client
import http.client

conn = http.client.HTTPConnection("sdbx-sirio.kashplataforma.com")
payload = ''
headers = {
  'Authorization': 'Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hQGdtYWlsLmNvbSIsImV4cCI6MTc0MTcxNjY4NywiaWF0IjoxNzQxNzEwNzExfQ.44L_ar-989GzU6KTKDgS6dqzOOI4OWkJzheuMSZNd9AnJKC09R0squVMlz7b_1huq56aX1G4n8kaJYVosixPAA',
  'Entity-i': 'com.onsigna'
}
conn.request("GET", "/EntitiesServices/getBalance", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))

```

```shell
# SHELL - httpie
http --follow --timeout 3600 GET 'http://sdbx-sirio.kashplataforma.com/EntitiesServices/getBalance' \
 Authorization:'Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hQGdtYWlsLmNvbSIsImV4cCI6MTc0MTcxNjY4NywiaWF0IjoxNzQxNzEwNzExfQ.44L_ar-989GzU6KTKDgS6dqzOOI4OWkJzheuMSZNd9AnJKC09R0squVMlz7b_1huq56aX1G4n8kaJYVosixPAA' \
 Entity-i:'com.onsigna'

```


> El codigo anterior devuelve un JSON estructurado así:

```json
{
    "success": true,
    "onsignaEntity": {
        "id": "com.onsigna",
        "name": "Onsigna",
        "prefix": "on",
        "fiscalID": null,
        "clabeAccount": "002028650641309053",
        "virtualAccount": "6506000004130920",
        "branchAcount": null,
        "webhookURL": null,
        "email": "suport@onsigna.com",
        "phoneNumber": "4444433333",
        "fatherID": null,
        "balance": 8437673.61,
        "warrantyBalance": 0.00,
        "customerNetworkBalance": 32769.34,
        "warrantyClabeAccount": "0",
        "warrantyVirtualAccount": null,
        "cryptedCard": null,
        "newCard": null,
        "assignClabeAccount": true,
        "feeID": 0,
        "internalReference": null,
        "liquidationLevel": "0",
        "liquidationType": null,
        "entityType": null,
        "affiliationId": null
    },
    "totalItems": 0,
    "totalPages": 0,
    "currentPage": 0
}

```


### HTTP Request

`GET http://sdbx-sirio.kashplataforma.com/EntitiesServices/getBalance`

### Query Parameters

Parameter | Description
--------- | -----------
Authorization | Default value : Bearer token
Entity ( entidad Maestra) | Default value : com.example



## getOperations <span style="float: right; color:#029702;font-size: 9px;">GET</span>

Servicio para obtener operaciones a nivel cuenta de la Entidad/Subentidad asociada a la entidad maestra 


```java
// JAVA - Unirest
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.get("http://sdbx-sirio.kashplataforma.com/EntitiesServices/getOperations")
  .header("Authorization", "Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hQGdtYWlsLmNvbSIsImV4cCI6MTc0MTcyNDc2NCwiaWF0IjoxNzQxNzE4Nzg4fQ.navutjFHrqxQNuI2OIaeAQfsJl8LPUJG6F1U729nOkDHIUmSnimnotl9mh9q6HY0N_g1gGU_ref1gbHDIH27RA")
  .header("Entity-i", "com.onsigna")
  .header("page", "0")
  .header("size", "10")
  .header("status", "25")
  .header("type", "1")
  .body("")
  .asString();

```

```php
# PHP - cURL
$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'http://sdbx-sirio.kashplataforma.com/EntitiesServices/getOperations',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'GET',
  CURLOPT_HTTPHEADER => array(
    'Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hQGdtYWlsLmNvbSIsImV4cCI6MTc0MTcyNDc2NCwiaWF0IjoxNzQxNzE4Nzg4fQ.navutjFHrqxQNuI2OIaeAQfsJl8LPUJG6F1U729nOkDHIUmSnimnotl9mh9q6HY0N_g1gGU_ref1gbHDIH27RA',
    'Entity-i: com.onsigna',
    'page: 0',
    'size: 10',
    'status: 25',
    'type: 1'
  ),
));

$response = curl_exec($curl);

curl_close($curl);
echo $response;

```

```ruby
# RUBY - Net::HTTP
require "uri"
require "net/http"

url = URI("http://sdbx-sirio.kashplataforma.com/EntitiesServices/getOperations")

http = Net::HTTP.new(url.host, url.port);
request = Net::HTTP::Get.new(url)
request["Authorization"] = "Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hQGdtYWlsLmNvbSIsImV4cCI6MTc0MTcyNDc2NCwiaWF0IjoxNzQxNzE4Nzg4fQ.navutjFHrqxQNuI2OIaeAQfsJl8LPUJG6F1U729nOkDHIUmSnimnotl9mh9q6HY0N_g1gGU_ref1gbHDIH27RA"
request["Entity-i"] = "com.onsigna"
request["page"] = "0"
request["size"] = "10"
request["status"] = "25"
request["type"] = "1"

response = http.request(request)
puts response.read_body

```

```python
# PYTHON - http.client
import http.client

conn = http.client.HTTPConnection("sdbx-sirio.kashplataforma.com")
payload = ''
headers = {
  'Authorization': 'Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hQGdtYWlsLmNvbSIsImV4cCI6MTc0MTcyNDc2NCwiaWF0IjoxNzQxNzE4Nzg4fQ.navutjFHrqxQNuI2OIaeAQfsJl8LPUJG6F1U729nOkDHIUmSnimnotl9mh9q6HY0N_g1gGU_ref1gbHDIH27RA',
  'Entity-i': 'com.onsigna',
  'page': '0',
  'size': '10',
  'status': '25',
  'type': '1'
}
conn.request("GET", "/EntitiesServices/getOperations", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))

```

```shell
# SHELL - httpie
http  --follow --timeout 3600 GET 'http://sdbx-sirio.kashplataforma.com/EntitiesServices/getOperations' \
 Authorization:'Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hQGdtYWlsLmNvbSIsImV4cCI6MTc0MTcyNDc2NCwiaWF0IjoxNzQxNzE4Nzg4fQ.navutjFHrqxQNuI2OIaeAQfsJl8LPUJG6F1U729nOkDHIUmSnimnotl9mh9q6HY0N_g1gGU_ref1gbHDIH27RA' \
 Entity-i:'com.onsigna' \
 page:'0' \
 size:'10' \
 status:'25' \
 type:'1'

```


> El codigo anterior devuelve un JSON estructurado así:

```json
{
  "acquiringId": "string",
  "acquiringOperation": {
    "alphanumericReference": "string",
    "amount": 0,
    "cititransaction": "string",
    "country": "string",
    "createdAt": "2025-03-11T18:47:33.673Z",
    "creditProductType": "string",
    "currency": {
      "alphabeticCode": "string",
      "createdAt": "string",
      "description": "string",
      "id": 0,
      "uptadedAt": "string"
    },
    "description": "string",
    "descriptionType": "string",
    "entity_id": "string",
    "externalReference": {},
    "finalBalance": 0,
    "id": 0,
    "initialBalance": 0,
    "internalReference": "string",
    "liquidationID": "string",
    "numericReference": "string",
    "observation": "string",
    "posDate": "string",
    "productCode": "string",
    "responseCode": "string",
    "settleAmount": 0,
    "status": 0,
    "systemSource": "string",
    "systemTraceAuditNumber": "string",
    "targetEmail": "string",
    "targetID": "string",
    "targetIDCode": "string",
    "targetName": "string",
    "timestamp": "string",
    "transactionBundler": 0,
    "transactionID": "string",
    "transactionSubType": "string",
    "transactionType": "string",
    "type": 0,
    "uptadedAt": "2025-03-11T18:47:33.673Z"
  },
  "authenticationResponse": {
    "expires": 38000,
    "timeStamp": "000",
    "token": "098NB987CV78B90JB9V8C7V8B9N0B9V8B9"
  },
  "currentPage": 0,
  "entities": [
    {
      "affiliationId": "string",
      "assignClabeAccount": true,
      "balance": 0,
      "branchAcount": "string",
      "clabeAccount": "string",
      "cryptedCard": "string",
      "customerNetworkBalance": 0,
      "email": "string",
      "entityType": "string",
      "fatherID": "string",
      "feeID": 0,
      "fiscalID": "string",
      "id": "string",
      "internalReference": "string",
      "liquidationLevel": "string",
      "liquidationType": "string",
      "name": "string",
      "newCard": "string",
      "phoneNumber": "string",
      "prefix": "string",
      "virtualAccount": "string",
      "warrantyBalance": 0,
      "warrantyClabeAccount": "string",
      "warrantyVirtualAccount": "string",
      "webhookURL": "string"
    }
  ],
  "entityID": "string",
  "entityValidationResponse": {
    "bussinesName": "string",
    "bussinesNameShort": "string",
    "context_id": 0,
    "entity": 0,
    "field1": "string",
    "rules": [
      "string"
    ],
    "user_id": 0
  },
  "error": {
    "code": 0,
    "message": "string",
    "name": "string"
  },
  "feeOperation": {
    "alphanumericReference": "string",
    "amount": 0,
    "cititransaction": "string",
    "country": "string",
    "createdAt": "2025-03-11T18:47:33.673Z",
    "creditProductType": "string",
    "currency": {
      "alphabeticCode": "string",
      "createdAt": "string",
      "description": "string",
      "id": 0,
      "uptadedAt": "string"
    },
    "description": "string",
    "descriptionType": "string",
    "entity_id": "string",
    "externalReference": {},
    "finalBalance": 0,
    "id": 0,
    "initialBalance": 0,
    "internalReference": "string",
    "liquidationID": "string",
    "numericReference": "string",
    "observation": "string",
    "posDate": "string",
    "productCode": "string",
    "responseCode": "string",
    "settleAmount": 0,
    "status": 0,
    "systemSource": "string",
    "systemTraceAuditNumber": "string",
    "targetEmail": "string",
    "targetID": "string",
    "targetIDCode": "string",
    "targetName": "string",
    "timestamp": "string",
    "transactionBundler": 0,
    "transactionID": "string",
    "transactionSubType": "string",
    "transactionType": "string",
    "type": 0,
    "uptadedAt": "2025-03-11T18:47:33.673Z"
  },
  "feeTaxOperation": {
    "alphanumericReference": "string",
    "amount": 0,
    "cititransaction": "string",
    "country": "string",
    "createdAt": "2025-03-11T18:47:33.673Z",
    "creditProductType": "string",
    "currency": {
      "alphabeticCode": "string",
      "createdAt": "string",
      "description": "string",
      "id": 0,
      "uptadedAt": "string"
    },
    "description": "string",
    "descriptionType": "string",
    "entity_id": "string",
    "externalReference": {},
    "finalBalance": 0,
    "id": 0,
    "initialBalance": 0,
    "internalReference": "string",
    "liquidationID": "string",
    "numericReference": "string",
    "observation": "string",
    "posDate": "string",
    "productCode": "string",
    "responseCode": "string",
    "settleAmount": 0,
    "status": 0,
    "systemSource": "string",
    "systemTraceAuditNumber": "string",
    "targetEmail": "string",
    "targetID": "string",
    "targetIDCode": "string",
    "targetName": "string",
    "timestamp": "string",
    "transactionBundler": 0,
    "transactionID": "string",
    "transactionSubType": "string",
    "transactionType": "string",
    "type": 0,
    "uptadedAt": "2025-03-11T18:47:33.673Z"
  },
  "liquidationId": "string",
  "onsignaEntities": [
    {
      "affiliationId": "string",
      "assignClabeAccount": true,
      "balance": 0,
      "branchAcount": "string",
      "clabeAccount": "string",
      "cryptedCard": "string",
      "customerNetworkBalance": 0,
      "email": "string",
      "entityType": "string",
      "fatherID": "string",
      "feeID": 0,
      "fiscalID": "string",
      "id": "string",
      "internalReference": "string",
      "liquidationLevel": "string",
      "liquidationType": "string",
      "name": "string",
      "newCard": "string",
      "phoneNumber": "string",
      "prefix": "string",
      "virtualAccount": "string",
      "warrantyBalance": 0,
      "warrantyClabeAccount": "string",
      "warrantyVirtualAccount": "string",
      "webhookURL": "string"
    }
  ],
  "onsignaEntity": {
    "affiliationId": "string",
    "assignClabeAccount": true,
    "balance": 0,
    "branchAcount": "string",
    "clabeAccount": "string",
    "cryptedCard": "string",
    "customerNetworkBalance": 0,
    "email": "string",
    "entityType": "string",
    "fatherID": "string",
    "feeID": 0,
    "fiscalID": "string",
    "id": "string",
    "internalReference": "string",
    "liquidationLevel": "string",
    "liquidationType": "string",
    "name": "string",
    "newCard": "string",
    "phoneNumber": "string",
    "prefix": "string",
    "virtualAccount": "string",
    "warrantyBalance": 0,
    "warrantyClabeAccount": "string",
    "warrantyVirtualAccount": "string",
    "webhookURL": "string"
  },
  "operationEntityResponse": {
    "alphanumericReference": "string",
    "amount": 0,
    "cititransaction": "string",
    "country": "string",
    "createdAt": "2025-03-11T18:47:33.673Z",
    "creditProductType": "string",
    "currency": {
      "alphabeticCode": "string",
      "createdAt": "string",
      "description": "string",
      "id": 0,
      "uptadedAt": "string"
    },
    "description": "string",
    "descriptionType": "string",
    "entity_id": "string",
    "externalReference": {},
    "finalBalance": 0,
    "id": 0,
    "initialBalance": 0,
    "internalReference": "string",
    "liquidationID": "string",
    "numericReference": "string",
    "observation": "string",
    "posDate": "string",
    "productCode": "string",
    "responseCode": "string",
    "settleAmount": 0,
    "status": 0,
    "systemSource": "string",
    "systemTraceAuditNumber": "string",
    "targetEmail": "string",
    "targetID": "string",
    "targetIDCode": "string",
    "targetName": "string",
    "timestamp": "string",
    "transactionBundler": 0,
    "transactionID": "string",
    "transactionSubType": "string",
    "transactionType": "string",
    "type": 0,
    "uptadedAt": "2025-03-11T18:47:33.673Z"
  },
  "operations": [
    {
      "alphanumericReference": "string",
      "amount": 0,
      "cititransaction": "string",
      "country": "string",
      "createdAt": "2025-03-11T18:47:33.673Z",
      "creditProductType": "string",
      "currency": {
        "alphabeticCode": "string",
        "createdAt": "string",
        "description": "string",
        "id": 0,
        "uptadedAt": "string"
      },
      "description": "string",
      "descriptionType": "string",
      "entity_id": "string",
      "externalReference": {},
      "finalBalance": 0,
      "id": 0,
      "initialBalance": 0,
      "internalReference": "string",
      "liquidationID": "string",
      "numericReference": "string",
      "observation": "string",
      "posDate": "string",
      "productCode": "string",
      "responseCode": "string",
      "settleAmount": 0,
      "status": 0,
      "systemSource": "string",
      "systemTraceAuditNumber": "string",
      "targetEmail": "string",
      "targetID": "string",
      "targetIDCode": "string",
      "targetName": "string",
      "timestamp": "string",
      "transactionBundler": 0,
      "transactionID": "string",
      "transactionSubType": "string",
      "transactionType": "string",
      "type": 0,
      "uptadedAt": "2025-03-11T18:47:33.673Z"
    }
  ],
  "orderEntityResponse": {
    "alphanumericReference": "string",
    "amount": 0,
    "beneficiaryAccountNumber": "string",
    "beneficiaryAddress1": "string",
    "beneficiaryAddress2": "string",
    "beneficiaryCode": "string",
    "beneficiaryEmail": "string",
    "beneficiaryName": "string",
    "businessTransactionCode": "string",
    "concept": "string",
    "country": "string",
    "createdAt": "2025-03-11T18:47:33.673Z",
    "creditStatus": "string",
    "creditType": "string",
    "debitType": "string",
    "descriptionType": "string",
    "entity_id": "string",
    "finalBalance": 0,
    "id": 1,
    "initialBalance": 0,
    "numericReference": 123322,
    "paymentType": "string",
    "posDate": "string",
    "productCode": "string",
    "referenceClient": "PROVV0011712091",
    "referenceCreatedPayment": 811111112,
    "responseCode": "string",
    "settleAmount": 0,
    "sourceEntity": "string",
    "status": 0,
    "systemSource": "string",
    "systemTraceAuditNumber": "string",
    "targetEntity": "string",
    "timestamp": "string",
    "transactionID": "string",
    "transactionSubType": "string",
    "transactionType": "string",
    "uptadedAt": "2025-03-11T18:47:33.673Z",
    "virtualAccount": "string"
  },
  "success": false,
  "totalItems": 0,
  "totalPages": 0,
  "transactionBundler": "string",
  "type": "string"
}

```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://sdbx-sirio.kashplataforma.com/EntitiesServices/getOperations`

### Query Parameters

Parameter | Description
--------- | -----------
Authorization | Default value : Bearer token
IdEntity ( entidad previamente dada de alta) | Default value : com.example
page (Numero de pagina a consultar) | Default value : 0
size (Tamaño de registro que va tener cada pagina) | Default value : 25
status (Estatus de operación, ver catalogo de estatus) | Default value : 25
type (Tipo de operación, ver catalogo de operaciones) | Default value : 1


### Catalogo de operaciones

          |             |           | 
--------- | ----------- | --------- | -----------
1 | Envia SPEI | RETIRO | SPEI DE SALIDA
1 | Envia SPEI | RETIRO | SPEI DE SALIDA
2 | Operación devuelta o rechazada | DEPOSITO | DEVOLUCIÓN SPEI
3 | SPEI recibido desde otra entidad | DEPOSITO | SPEI DE ENTRADA
4 | En una cuenta a cuenta, es el retiro a la cuenta origen | RETIRO | RETIRO ENTRE CUENTAS
10 | Compra de recargas telefonica | RETIRO | RECARGAS
11 | Compra Pago Servicios | RETIRO | PAGO DE SERVICIOS
12 | Retiro en ATM de la Red MasterCard | RETIRO | RETIRO EN ATM
13 | Compra  tarjeta presente/No presente  de la Red Mastercard | RETIRO | CONSUMO CON TARJETA
14 | Transacción por web service (Uso interno) | RETIRO/DEPOSITO | WebService (uso interno)
15 | Transacción por archivo batch (Uso interno) | RETIRO/DEPOSITO | Batch (uso interno)
16 | Retiro Corresponsal CITI (7Eleven, f.Gua, Citi, etc) | RETIRO | RETIRO EN CORRESPONSAL
17 | Retiro Corresponsal de la RED  (Came, TeCreemos, Comercios Onsigna) | RETIRO | RETIRO EN MI RED KASH
18 | Deposito Corresponsal de la  RED  (Came, TeCreemos, Comercios Onsigna) | DEPOSITO | DEPOSITO EN MI RED KASH
19 | Crear referencia- retiro sucursal | RETIRO | ORDEN DE PAGO
20 | Retiro de dinero con referencia en corresponsal  RED  (Came, TeCreemos, Comercios Onsigna) | RETIRO | RETIRO EN MI RED KASH
21 | Retiro de dinero con referencia en corresponsal CITI (7Eleven, f.Gua, Citi, etc) | RETIRO | RETIRO EN CORRESPONSAL
22 | Bolsas de Adquirencia | DEPOSITO | VENTA VISA
23 | Bolsas de Adquirencia | DEPOSITO | Venta Amex
24 | Bolsas de Adquirencia | DEPOSITO | Venta SPEI Referenciado
25 | Bolsas de Adquirencia | DEPOSITO | Devolucion Adqui
26 | Bolsas de Adquirencia | DEPOSITO | Cancelación Adqui
27 | Bolsas de Adquirencia | DEPOSITO | Venta MC
28 | Depósito a cuenta, dentro de la red de corresponsales CITI (7Eleven, f.Gua, Citi, etc) | DEPOSITO | DEPÓSITO EN CORRESPONSAL
29 | Depósito a  cuenta dentro de la red de corresponsales de la  RED  (Came, TeCreemos, Comercios Onsigna) | DEPOSITO | DEPÓSITO ENTRE CUENTAS
57 | Bolsas de Adquirencia | DEPOSITO | Venta Internacional
104 | Deposito en Efectivo | DEPOSITO | DEPÓSITO EN EFECTIVO
105 | Pago Referenciado | DEPOSITO | PAGO REFERENCIADO
106 | Traspaso Referenciado | DEPOSITO | TRASPASO REFERENCIADO
107 | Pago Recibido de Convenio | DEPOSITO | PAGO RECIBIDO
108 | Transferencia entre cuentas Citi | DEPOSITO | TRANSFERENCIA ENTRE CUENTAS CITI
109 | Deposito Mixto | DEPOSITO | DEPÓSITO MIXTO
110 | Deposito cheques | DEPOSITO | DEPOSITO CHEQUE
10007 | Liquidación Compra Individual |  | Liquidación Compra Individual
10008 | Liquidación Compras |  |  LIQUIDACIÓN TOTAL



# API CardServices

## addToken <span style="float: right; color:#0f70ec;font-size: 9px;">POST</span>

Este servicio tiene como propósito generar un token único asociado a una tarjeta, utilizando para ello tanto los datos explícitos de la tarjeta como la información del titular de la misma.

El token generado es un valor alfanumérico irrepetible que puede emplearse como identificador en el punto final dedicado a la tokenización de cargos. Es importante destacar que el token actúa como un sustituto seguro de los datos
confidenciales de la tarjeta, proporcionando una capa adicional de protección en las transacciones. 

<aside class="success">
Recuerda — Que el token generado sea almacenado de manera segura, siguiendo las mejores prácticas de seguridad y cumplimiento normativo para evitar cualquier acceso no autorizado o uso indebido.
</aside>

```java
// JAVA - Unirest
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.post("http://sdbx-antares.kashplataforma.com:7071/CardServices/api/v1/tokenization/add")
  .header("Authorization", "Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hdGVzdDFAZ21haWwuY29tIiwiZXhwIjoxNzM3NjYzNDc2LCJpYXQiOjE3Mzc2NTc1MDB9.wQwk5pPBkPvMX015ydgttQ5f64fg5BrfVUI7uiNQVt1neiFVvUGIu067a59cJrBtVz4eGGSTxxWW6wtaIlsvpg")
  .header("Entity-i", "com.onsigna")
  .header("Content-Type", "application/json")
  .body("{\n    \"enrollmentRequest\": {\n        \"orderInformation\": {\n            \"billTo\": {\n                \"address1\": \"1295 Charleston Road\",\n                \"administrativeArea\": \"CA\",\n                \"country\": \"US\",\n                \"firstName\": \"JUAN\",\n                \"lastName\": \"DAVID\",\n                \"email\": \"david@test.com\",\n                \"postalCode\": \"94043\",\n                \"locality\": \"Mountain View\",\n                \"phoneNumber\": \"+524441357941\"\n            }\n        },\n        \"paymentInformation\": {\n            \"card\": {\n                \"type\": \"002\",\n                \"number\": \"5223750022762439\",\n                \"expirationMonth\": \"06\",\n                \"expirationYear\": \"2026\"\n            }\n        }\n    },\n    \"customerRequest\": {\n        \"buyerInformation\": {\n            \"merchantCustomerID\": \"SUB165166235394090\",\n            \"email\": \"testNew@gmail.com\"\n        },\n        \"merchantDefinedInformation\": [\n            {\n                \"name\": \"data1\",\n                \"value\": \"Colaborador\"\n            }\n        ]\n    }\n}")
  .asString();

```

```php
// PHP - cURL
$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'http://sdbx-antares.kashplataforma.com:7071/CardServices/api/v1/tokenization/add',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'POST',
  CURLOPT_POSTFIELDS =>'{
    "enrollmentRequest": {
        "orderInformation": {
            "billTo": {
                "address1": "1295 Charleston Road",
                "administrativeArea": "CA",
                "country": "US",
                "firstName": "JUAN",
                "lastName": "DAVID",
                "email": "david@test.com",
                "postalCode": "94043",
                "locality": "Mountain View",
                "phoneNumber": "+524441357941"
            }
        },
        "paymentInformation": {
            "card": {
                "type": "002",
                "number": "5223750022762439",
                "expirationMonth": "06",
                "expirationYear": "2026"
            }
        }
    },
    "customerRequest": {
        "buyerInformation": {
            "merchantCustomerID": "SUB165166235394090",
            "email": "testNew@gmail.com"
        },
        "merchantDefinedInformation": [
            {
                "name": "data1",
                "value": "Colaborador"
            }
        ]
    }
  }',
  CURLOPT_HTTPHEADER => array(
    'Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hdGVzdDFAZ21haWwuY29tIiwiZXhwIjoxNzM3NjYzNDc2LCJpYXQiOjE3Mzc2NTc1MDB9.wQwk5pPBkPvMX015ydgttQ5f64fg5BrfVUI7uiNQVt1neiFVvUGIu067a59cJrBtVz4eGGSTxxWW6wtaIlsvpg',
    'Entity-i: com.onsigna',
    'Content-Type: application/json'
  ),
));

$response = curl_exec($curl);

curl_close($curl);
echo $response;

```

```ruby
# RUBY - Net::HTTP
require "uri"
require "json"
require "net/http"

url = URI("http://sdbx-antares.kashplataforma.com:7071/CardServices/api/v1/tokenization/add")

http = Net::HTTP.new(url.host, url.port);
request = Net::HTTP::Post.new(url)
request["Authorization"] = "Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hdGVzdDFAZ21haWwuY29tIiwiZXhwIjoxNzM3NjYzNDc2LCJpYXQiOjE3Mzc2NTc1MDB9.wQwk5pPBkPvMX015ydgttQ5f64fg5BrfVUI7uiNQVt1neiFVvUGIu067a59cJrBtVz4eGGSTxxWW6wtaIlsvpg"
request["Entity-i"] = "com.onsigna"
request["Content-Type"] = "application/json"
request.body = JSON.dump({
  "enrollmentRequest": {
    "orderInformation": {
      "billTo": {
        "address1": "1295 Charleston Road",
        "administrativeArea": "CA",
        "country": "US",
        "firstName": "JUAN",
        "lastName": "DAVID",
        "email": "david@test.com",
        "postalCode": "94043",
        "locality": "Mountain View",
        "phoneNumber": "+524441357941"
      }
    },
    "paymentInformation": {
      "card": {
        "type": "002",
        "number": "5223750022762439",
        "expirationMonth": "06",
        "expirationYear": "2026"
      }
    }
  },
  "customerRequest": {
    "buyerInformation": {
      "merchantCustomerID": "SUB165166235394090",
      "email": "testNew@gmail.com"
    },
    "merchantDefinedInformation": [
      {
        "name": "data1",
        "value": "Colaborador"
      }
    ]
  }
})

response = http.request(request)
puts response.read_body

```

```python
# PYTHON - http.client
import http.client
import json

conn = http.client.HTTPConnection("sdbx-antares.kashplataforma.com", 7071)
payload = json.dumps({
  "enrollmentRequest": {
    "orderInformation": {
      "billTo": {
        "address1": "1295 Charleston Road",
        "administrativeArea": "CA",
        "country": "US",
        "firstName": "JUAN",
        "lastName": "DAVID",
        "email": "david@test.com",
        "postalCode": "94043",
        "locality": "Mountain View",
        "phoneNumber": "+524441357941"
      }
    },
    "paymentInformation": {
      "card": {
        "type": "002",
        "number": "5223750022762439",
        "expirationMonth": "06",
        "expirationYear": "2026"
      }
    }
  },
  "customerRequest": {
    "buyerInformation": {
      "merchantCustomerID": "SUB165166235394090",
      "email": "testNew@gmail.com"
    },
    "merchantDefinedInformation": [
      {
        "name": "data1",
        "value": "Colaborador"
      }
    ]
  }
})
headers = {
  'Authorization': 'Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hdGVzdDFAZ21haWwuY29tIiwiZXhwIjoxNzM3NjYzNDc2LCJpYXQiOjE3Mzc2NTc1MDB9.wQwk5pPBkPvMX015ydgttQ5f64fg5BrfVUI7uiNQVt1neiFVvUGIu067a59cJrBtVz4eGGSTxxWW6wtaIlsvpg',
  'Entity-i': 'com.onsigna',
  'Content-Type': 'application/json'
}
conn.request("POST", "/CardServices/api/v1/tokenization/add", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))

```

```shell
# SHELL - httpie
printf '{
    "enrollmentRequest": {
        "orderInformation": {
            "billTo": {
                "address1": "1295 Charleston Road",
                "administrativeArea": "CA",
                "country": "US",
                "firstName": "JUAN",
                "lastName": "DAVID",
                "email": "david@test.com",
                "postalCode": "94043",
                "locality": "Mountain View",
                "phoneNumber": "+524441357941"
            }
        },
        "paymentInformation": {
            "card": {
                "type": "002",
                "number": "5223750022762439",
                "expirationMonth": "06",
                "expirationYear": "2026"
            }
        }
    },
    "customerRequest": {
        "buyerInformation": {
            "merchantCustomerID": "SUB165166235394090",
            "email": "testNew@gmail.com"
        },
        "merchantDefinedInformation": [
            {
                "name": "data1",
                "value": "Colaborador"
            }
        ]
    }
}'| http  --follow --timeout 3600 POST 'http://sdbx-antares.kashplataforma.com:7071/CardServices/api/v1/tokenization/add' \
 Authorization:'Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hdGVzdDFAZ21haWwuY29tIiwiZXhwIjoxNzM3NjYzNDc2LCJpYXQiOjE3Mzc2NTc1MDB9.wQwk5pPBkPvMX015ydgttQ5f64fg5BrfVUI7uiNQVt1neiFVvUGIu067a59cJrBtVz4eGGSTxxWW6wtaIlsvpg' \
 Entity-i:'com.onsigna' \
 Content-Type:'application/json'

```


> El codigo anterior devuelve un JSON estructurado así:

```json
{
    "success": true,
    "createCustomerResponse": {
        "links": null,
        "id": "3360FB142697BA0AE063AF598E0A8E76",
        "objectInformation": null,
        "buyerInformation": {
            "merchantCustomerID": "SUB165166235394090",
            "email": "testNew@gmail.com"
        },
        "clientReferenceInformation": null,
        "merchantDefinedInformation": [
            {
                "name": "data1",
                "value": "Colaborador"
            }
        ],
        "defaultPaymentInstrument": null,
        "defaultShippingAddress": null,
        "metadata": null,
        "embedded": null
    },
    "createInstrumentIdentifierRespone": {
        "links": {
            "self": {
                "href": "https://apitest.cybersource.com/tms/v1/instrumentidentifiers/7039989999951132439"
            },
            "paymentInstruments": {
                "href": "https://apitest.cybersource.com/tms/v1/instrumentidentifiers/7039989999951132439/paymentinstruments"
            }
        },
        "id": "7039989999951132439",
        "object": "instrumentIdentifier",
        "state": "ACTIVE",
        "type": null,
        "tokenProvisioningInformation": null,
        "card": {
            "number": null,
            "expirationMonth": null,
            "expirationYear": null,
            "securityCode": null
        },
        "bankAccount": null,
        "tokenizedCard": null,
        "issuer": null,
        "processingInformation": null,
        "billTo": null,
        "metadata": {
            "creator": "miredkash001"
        },
        "embedded": null
    },
    "createCustomerPaymentInstrumentResponse": {
        "links": {
            "self": {
                "href": "/tms/v2/customers/3360FB142697BA0AE063AF598E0A8E76/payment-instruments/346845F2F29A58FAE063AF598E0AB486"
            },
            "customer": {
                "href": "/tms/v2/customers/3360FB142697BA0AE063AF598E0A8E76"
            }
        },
        "id": "346845F2F29A58FAE063AF598E0AB486",
        "object": null,
        "state": "ACTIVE",
        "type": null,
        "bankAccount": null,
        "card": {
            "expirationMonth": null,
            "expirationYear": null,
            "type": "002",
            "issueNumber": null,
            "startMonth": null,
            "startYear": null,
            "useAs": null,
            "hash": null,
            "tokenizedInformation": null
        },
        "buyerInformation": null,
        "billTo": {
            "firstName": "JUAN",
            "lastName": "DAVID",
            "company": null,
            "address1": "1295 Charleston Road",
            "address2": null,
            "locality": "Mountain View",
            "administrativeArea": null,
            "postalCode": "94043",
            "country": "US",
            "email": "david@test.com",
            "phoneNumber": "+524441357941"
        },
        "processingInformation": null,
        "merchantInformation": null,
        "instrumentIdentifier": {
            "id": "7039989999951132439"
        },
        "metadata": {
            "creator": "miredkash001"
        },
        "embedded": {
            "instrumentIdentifier": {
                "links": {
                    "self": {
                        "href": "/tms/v1/instrumentidentifiers/7039989999951132439"
                    },
                    "paymentInstruments": {
                        "href": "/tms/v1/instrumentidentifiers/7039989999951132439/paymentinstruments"
                    }
                },
                "id": "7039989999951132439",
                "object": "instrumentIdentifier",
                "state": "ACTIVE",
                "type": null,
                "tokenProvisioningInformation": null,
                "card": {
                    "number": null,
                    "expirationMonth": null,
                    "expirationYear": null,
                    "securityCode": null
                },
                "bankAccount": null,
                "tokenizedCard": null,
                "issuer": null,
                "processingInformation": null,
                "billTo": null,
                "metadata": {
                    "creator": "miredkash001"
                },
                "embedded": null
            }
        }
    }
}
```

This endpoint retrieves all kittens.

### HTTP Request

`POST http://sdbx-antares.kashplataforma.com:7071/CardServices/api/v1/cardToken/add`

### Query Parameters 

Parameter enrollmentRequest -> orderInformation -> billTo | Description
--------- | -----------
address1 | Domicilio
administrativeArea | CA
country | Moneda
firstName | Nombre
lastName | Apellido
email | Correo electronico válido
postalCode | Código postal
locality | Ciudad o Municipio
phoneNumber | Número de telefono 

Parameter enrollmentRequest -> paymentInformation -> card | Description
--------- | -----------
type | 002
number | Número de la tarjeta
expirationMonth | Mes de expiracion
expirationYear | Año de expiracion

Parameter customerRequest -> buyerInformation | Description
--------- | -----------
merchantCustomerID | Identificador del usuario
email | Correo electronico válido

Parameter customerRequest -> merchantDefinedInformation | Description
--------- | -----------
name | data1
value | Colaborador

## getToken <span style="float: right; color:#029702;font-size: 9px;">GET</span>

Este servicio tiene como propósito generar un token único asociado a una tarjeta, utilizando para ello tanto los datos explícitos de la tarjeta como la información del titular de la misma.

El token generado es un valor alfanumérico irrepetible que puede emplearse como identificador en el punto final dedicado a la tokenización de cargos. Es importante destacar que el token actúa como un sustituto seguro de los datos
confidenciales de la tarjeta, proporcionando una capa adicional de protección en las transacciones. 

<aside class="success">
Recuerda — Que el token generado sea almacenado de manera segura, siguiendo las mejores prácticas de seguridad y cumplimiento normativo para evitar cualquier acceso no autorizado o uso indebido.
</aside>

```java
// JAVA - Unirest
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.get("http://sdbx-antares.kashplataforma.com:7071/CardServices/api/v1/tokenization/getTokens?merchanID=SUB165166235394090")
  .header("Authorization", "Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hdGVzdDFAZ21haWwuY29tIiwiZXhwIjoxNzM3NjYzNDc2LCJpYXQiOjE3Mzc2NTc1MDB9.wQwk5pPBkPvMX015ydgttQ5f64fg5BrfVUI7uiNQVt1neiFVvUGIu067a59cJrBtVz4eGGSTxxWW6wtaIlsvpg")
  .header("Entity-i", "com.onsigna")
  .asString();

```

```php
// PHP - cURL
$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'http://sdbx-antares.kashplataforma.com:7071/CardServices/api/v1/tokenization/getTokens?merchanID=SUB165166235394090',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'GET',
  CURLOPT_HTTPHEADER => array(
    'Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hdGVzdDFAZ21haWwuY29tIiwiZXhwIjoxNzM3NjYzNDc2LCJpYXQiOjE3Mzc2NTc1MDB9.wQwk5pPBkPvMX015ydgttQ5f64fg5BrfVUI7uiNQVt1neiFVvUGIu067a59cJrBtVz4eGGSTxxWW6wtaIlsvpg',
    'Entity-i: com.onsigna'
  ),
));

$response = curl_exec($curl);

curl_close($curl);
echo $response;


```

```ruby
# RUBY - Net::HTTP
import http.client

conn = http.client.HTTPConnection("sdbx-antares.kashplataforma.com", 7071)
payload = ''
headers = {
  'Authorization': 'Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hdGVzdDFAZ21haWwuY29tIiwiZXhwIjoxNzM3NjYzNDc2LCJpYXQiOjE3Mzc2NTc1MDB9.wQwk5pPBkPvMX015ydgttQ5f64fg5BrfVUI7uiNQVt1neiFVvUGIu067a59cJrBtVz4eGGSTxxWW6wtaIlsvpg',
  'Entity-i': 'com.onsigna'
}
conn.request("GET", "/CardServices/api/v1/tokenization/getTokens?merchanID=SUB165166235394090", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))

```

```python
# PYTHON - http.client
import http.client

conn = http.client.HTTPConnection("sdbx-antares.kashplataforma.com", 7071)
payload = ''
headers = {
  'Authorization': 'Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hdGVzdDFAZ21haWwuY29tIiwiZXhwIjoxNzM3NjYzNDc2LCJpYXQiOjE3Mzc2NTc1MDB9.wQwk5pPBkPvMX015ydgttQ5f64fg5BrfVUI7uiNQVt1neiFVvUGIu067a59cJrBtVz4eGGSTxxWW6wtaIlsvpg',
  'Entity-i': 'com.onsigna'
}
conn.request("GET", "/CardServices/api/v1/tokenization/getTokens?merchanID=SUB165166235394090", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))

```

```shell
# SHELL - httpie
http --follow --timeout 3600 GET 'http://sdbx-antares.kashplataforma.com:7071/CardServices/api/v1/tokenization/getTokens?merchanID=SUB165166235394090' \
 Authorization:'Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hdGVzdDFAZ21haWwuY29tIiwiZXhwIjoxNzM3NjYzNDc2LCJpYXQiOjE3Mzc2NTc1MDB9.wQwk5pPBkPvMX015ydgttQ5f64fg5BrfVUI7uiNQVt1neiFVvUGIu067a59cJrBtVz4eGGSTxxWW6wtaIlsvpg' \
 Entity-i:'com.onsigna'

```


> El codigo anterior devuelve un JSON estructurado así:

```json
[
    {
        "id": 1,
        "name": "JUAN DAVID",
        "card": "**** **** **** 4288",
        "expirationDate": "06-2026",
        "address": "1295 Charleston Road",
        "city": "Mountain View",
        "locality": "Mountain View",
        "postalCode": "94043",
        "country": "US",
        "email": "david@test.com",
        "phoneNumber": "+524441357941",
        "merchanID": "SUB165166235394090",
        "institution": "BBVA BANCOMER",
        "brand": "VISA",
        "active": true,
        "lastUserModify": "0",
        "lastDateModify": "2025-04-22T10:53:17",
        "paymentInstrumentID": "3361C2A90D11B7FEE063AF598E0A76F5",
        "instrumentIdentifierID": "7039989999951944288"
    },
    {
        "id": 2,
        "name": "JUAN DAVID",
        "card": "**** **** **** 2439",
        "expirationDate": "06-2026",
        "address": "1295 Charleston Road",
        "city": "Mountain View",
        "locality": "Mountain View",
        "postalCode": "94043",
        "country": "US",
        "email": "david@test.com",
        "phoneNumber": "+524441357941",
        "merchanID": "SUB165166235394090",
        "institution": "Unknown",
        "brand": "MASTER CARD",
        "active": true,
        "lastUserModify": "0",
        "lastDateModify": "2025-04-22T10:54:15",
        "paymentInstrumentID": "3361D366223BD11DE063AF598E0ACFC2",
        "instrumentIdentifierID": "7039989999951132439"
    },
    {
        "id": 3,
        "name": "JUAN DAVID",
        "card": "**** **** **** 2439",
        "expirationDate": "06-2026",
        "address": "1295 Charleston Road",
        "city": "Mountain View",
        "locality": "Mountain View",
        "postalCode": "94043",
        "country": "US",
        "email": "david@test.com",
        "phoneNumber": "+524441357941",
        "merchanID": "SUB165166235394090",
        "institution": "Unknown",
        "brand": "MASTER CARD",
        "active": true,
        "lastUserModify": "0",
        "lastDateModify": "2025-05-05T12:07:39",
        "paymentInstrumentID": "346845F2F29A58FAE063AF598E0AB486",
        "instrumentIdentifierID": "7039989999951132439"
    }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://sdbx-antares.kashplataforma.com:7071/CardServices/api/v1/tokenization/getTokens?merchanID=SUB165166235394090`

### Query Parameters

Parameter | Description
--------- | -----------
Authorization | Default value : Bearer token
Entity ( entidad Maestra) | Default value : com.example
merchanID | identificador de la entidad a cunsultar

## getTokenDetail <span style="float: right; color:#029702;font-size: 9px;">GET</span>

Obtiene el detalle del token asociado al titular de la tarjeta.

```java
// JAVA - Unirest
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.get("http://sdbx-antares.kashplataforma.com:7071/CardServices/api/v1/tokenization/getTokenDetail?merchanID=SUB165166235394090&cardToken=7039989999951944288")
  .header("Authorization", "Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hdGVzdDFAZ21haWwuY29tIiwiZXhwIjoxNzM3NjYzNDc2LCJpYXQiOjE3Mzc2NTc1MDB9.wQwk5pPBkPvMX015ydgttQ5f64fg5BrfVUI7uiNQVt1neiFVvUGIu067a59cJrBtVz4eGGSTxxWW6wtaIlsvpg")
  .header("Entity-i", "com.onsigna")
  .asString();

```

```php
// PHP - cURL
$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'http://sdbx-antares.kashplataforma.com:7071/CardServices/api/v1/tokenization/getTokenDetail?merchanID=SUB165166235394090&cardToken=7039989999951944288',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'GET',
  CURLOPT_HTTPHEADER => array(
    'Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hdGVzdDFAZ21haWwuY29tIiwiZXhwIjoxNzM3NjYzNDc2LCJpYXQiOjE3Mzc2NTc1MDB9.wQwk5pPBkPvMX015ydgttQ5f64fg5BrfVUI7uiNQVt1neiFVvUGIu067a59cJrBtVz4eGGSTxxWW6wtaIlsvpg',
    'Entity-i: com.onsigna'
  ),
));

$response = curl_exec($curl);

curl_close($curl);
echo $response;

```

```ruby
# RUBY - Net::HTTP
require "uri"
require "net/http"

url = URI("http://sdbx-antares.kashplataforma.com:7071/CardServices/api/v1/tokenization/getTokenDetail?merchanID=SUB165166235394090&cardToken=7039989999951944288")

http = Net::HTTP.new(url.host, url.port);
request = Net::HTTP::Get.new(url)
request["Authorization"] = "Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hdGVzdDFAZ21haWwuY29tIiwiZXhwIjoxNzM3NjYzNDc2LCJpYXQiOjE3Mzc2NTc1MDB9.wQwk5pPBkPvMX015ydgttQ5f64fg5BrfVUI7uiNQVt1neiFVvUGIu067a59cJrBtVz4eGGSTxxWW6wtaIlsvpg"
request["Entity-i"] = "com.onsigna"

response = http.request(request)
puts response.read_body

```

```python
# PYTHON - http.client
import http.client

conn = http.client.HTTPConnection("sdbx-antares.kashplataforma.com", 7071)
payload = ''
headers = {
  'Authorization': 'Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hdGVzdDFAZ21haWwuY29tIiwiZXhwIjoxNzM3NjYzNDc2LCJpYXQiOjE3Mzc2NTc1MDB9.wQwk5pPBkPvMX015ydgttQ5f64fg5BrfVUI7uiNQVt1neiFVvUGIu067a59cJrBtVz4eGGSTxxWW6wtaIlsvpg',
  'Entity-i': 'com.onsigna'
}
conn.request("GET", "/CardServices/api/v1/tokenization/getTokenDetail?merchanID=SUB165166235394090&cardToken=7039989999951944288", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))

```

```shell
# SHELL - httpie
http --follow --timeout 3600 GET 'http://sdbx-antares.kashplataforma.com:7071/CardServices/api/v1/tokenization/getTokenDetail?merchanID=SUB165166235394090&cardToken=7039989999951944288' \
 Authorization:'Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJvbnNpZ25hdGVzdDFAZ21haWwuY29tIiwiZXhwIjoxNzM3NjYzNDc2LCJpYXQiOjE3Mzc2NTc1MDB9.wQwk5pPBkPvMX015ydgttQ5f64fg5BrfVUI7uiNQVt1neiFVvUGIu067a59cJrBtVz4eGGSTxxWW6wtaIlsvpg' \
 Entity-i:'com.onsigna'

```


> El codigo anterior devuelve un JSON estructurado así:

```json
{
    "success": true,
    "paymentInstrument": {
        "id": 1,
        "name": "JUAN DAVID",
        "card": "**** **** **** 34288",
        "expirationDate": "06-2026",
        "address": "1295 Charleston Road",
        "city": "Mountain View",
        "locality": "Mountain View",
        "postalCode": "94043",
        "country": "US",
        "email": "david@test.com",
        "phoneNumber": "+524441357941",
        "merchanID": "SUB165166235394090",
        "institution": "BBVA BANCOMER",
        "brand": "VISA",
        "active": true,
        "lastUserModify": "0",
        "lastDateModify": "2025-04-22T10:53:17",
        "paymentInstrumentID": "3361C2A90D11B7FEE063AF598E0A76F5",
        "instrumentIdentifierID": "7039989999951944288"
    }
}
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://sdbx-antares.kashplataforma.com:7071/CardServices/api/v1/tokenization/getTokenDetail?merchanID=SUB165166235394090&cardToken=7039989999951944288`

### Query Parameters

Parameter | Description
--------- | -----------
Authorization | Default value : Bearer token
Entity ( entidad Maestra) | Default value : com.example
merchanID | identificador de la entidad a cunsultar
cardToken | token de la tarjeta
