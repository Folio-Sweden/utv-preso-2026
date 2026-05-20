title: Utveckling & teknik
output: docs/index.html
controls: false
style: styles.css
--
<!-- theme: sudodoki/reveal-cleaver-theme -->
# Utveckling & teknik
## Snillen spekulerar om Låntagar-APIer och annat utvecklarrelaterat

Martin Ericsson, Kungliga biblioteket  
Lari Kovanen, Chalmers tekniska högskola
--
### Vad vi skall titta på
- Var hittar man info
- Att skapa användare
- Att radera användare 
--
### Var hittar man info
- API-dokumentation hittas på [https://dev.folio.org](https://dev.folio.org) som är uppdelad efter FOLIO-modulerna.  
- I FOLIO-wikin [https://wiki.folio.org](https://wiki.folio.org) kan man hitta mer info om apierna och mer dokumentation om apierna.  
- I FOLIO-slacken så kan man givetvis fråga om allt men även om apier.  
- WEB-GUIet för FOLIO är fin källa till att se hur saker och ting går till via utvecklarverktygen i browsern. 
--
### Att skapa användare
Vilken information är obligatorisk
- Efternamn
- Låntagargrupp (id)
- Status
- E-mejl
- Föredragen kontaktväg
--
### Att skapa användare
Bra att ha information
- användarnamn
- externt användarnamn
- streckkod
--
### Vilka steg behövs för att skapa användare.
1. Skaffa api-token med användare som har korrekta rättigheter
1. Patrongroup
1. Skapa en user
1. Skapa Permissions (Enbart i OKAPI-system)
1. Skapa RequestPreference
1. ID-block?
1. Sätt pinkod/lösenord

--
### Skaffa api-token
Posta till `/authn/login-with-expiry`  
där headern innehåller vilken tenant som avses i `X-OKAPI-TOKEN` som
```json
{
    "username":"användarnamn",
    "password":"lösenord"
}
```
--
Två cookies med token returneras.
```
set-cookie: folioAccessToken=xxx; Max-Age=600; Expires=Mon, 18 May 2026 16:07:35 GMT;
Path=/; Secure; HTTPOnly; SameSite=None
set-cookie: folioRefreshToken=yyy; Max-Age=604800; Expires=Mon, 25 May 2026 15:57:35 GMT;
Path=/authn; Secure; HTTPOnly; SameSite=None
```
--
och bodyn består av json med expire-tiderna upprepad.
```json
{
    "accessTokenExpiration":"2026-05-18T16:07:35Z",
    "refreshTokenExpiration":"2026-05-25T15:57:35Z"
}
```
--
### Hitta låntagargrupp
GET `/groups`
```json
{
  "usergroups": [
    {
      "group": "faculty",
      "desc": "Faculty Member",
      "id": "503a81cd-6c26-400f-b620-14c08943697c",
      "expirationOffsetInDays": 365,
      "metadata": {
        "createdDate": "2026-05-19T01:51:33.022+00:00",
        "updatedDate": "2026-05-19T01:51:33.022+00:00"
      }
    },
    ......
```
--
### Skapa användaren
POST `/users`
```json
{
    "username": "string",
    "externalSystemId": "string",
    "active": true,
    "personal": {
        "firstName": "string",
        "lastName": "string",
        "email": "string",
        "preferredContactTypeId": "002"
    },
    "barcode":"string",
    "patronGroup":"GUID"
}
```
--
### Skapa rättighegter för användaren
Post till `/perms/users`  
```json
{
    "userId": "GUID",
    "permissions": []
}
```
--
### Skapa beställarpreferens
Post till `/request-preference-storage/request-preference`  
```json
{
    "userId": "GUID",
    "holdShelf": true,
    "delivery": false
}
```
--
### Skapa ID-block
Post till `/manualblocks`  
```json
{
    "userId": "GUID",
    "type": "Manual",
    "desc": "Nyskapat konto. ID-koll!!",
    "staffInformation": "Info som personal behöver.",
    "patronMessage": "Eventuell info till användaren. Visa ID.",
    "borrowing": true,
    "renewals": false,
    "requests": false
}
```
--
### Skapa pin/lösenord
Post till `/patron-pin` med header `Accept: text/plain`  
```json
{
    "id": "GUID",
    "pin": "pin/lösenord"
}
```
--
### Radera användare
- Kolla om användare har några öppna transaktioner
- Radera användaren
--
### Öppna transaktioner
GET `/bl-users/by-id/{Användar id}/open-transactions`
```json
{
  "userId": "Användar id",
  "hasOpenTransactions": false,
  "loans": 0,
  "requests": 0,
  "feesFines": 0,
  "proxies": 0,
  "blocks": 0
}
```
--
### Radera användare
DELETE `/bl-users/by-id/{användar id}`  

Returnerar Status 204 om den lyckades.
--
### Slides och exempel api anrop
Repot till dessa slides finns på  
`https://github.com/Folio-Sweden/utv-preso-2026`  
och själva slides finns på  
`https://folio-sweden.github.io/utv-preso-2026/`