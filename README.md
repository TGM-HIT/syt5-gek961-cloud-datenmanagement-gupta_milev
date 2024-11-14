by Ivan Milev, Rahul Gupta

Dieses Projekt implementiert einen einfachen Webservice zur Benutzerverwaltung mit den Endpunkten für Registrierung, Login und Verifizierung. Es handelt sich um eine einfache Lösung, bei der Benutzerrollen verwaltet und die Authentifizierung über JWT (JSON Web Token) durchgeführt wird.

### Funktionen

**Registrierung (/auth/admin/register):**

- Administratoren können neue Benutzer registrieren, indem sie einen Namen, eine E-Mail-Adresse (als Benutzer-ID), eine Liste von Rollen (ADMIN, READER, MODERATOR) und ein Passwort angeben.
- Passwörter werden verschlüsselt gespeichert (nicht im Klartext).
- Die Benutzerinformationen werden in einer relationalen Datenbank gespeichert.
- Beim Start der Anwendung können Benutzerinformationen aus einer JSON-Datei geladen werden.

**Login (/auth/signin):**

- Benutzer können sich mit ihrer E-Mail-Adresse und ihrem Passwort anmelden.
- Nach erfolgreichem Login wird ein signiertes JWT zurückgegeben, das die genehmigten Rollen des Benutzers enthält.

**Verifizierung (/auth/verify):**

- Das JWT wird überprüft, und wenn es gültig ist, wird die Benutzerrolle bestätigt.
- Bei ungültigem JWT wird eine 403 Unauthorized-Antwort zurückgegeben.

## Deployment

Das Deployment erfolgt mittels Docker insbesonder docker-compose.yml

### 1. Directory erstellen

### 2. Docker-Compose runterladen

Vom github runterladen und in den obigen ordner verschieben

### 3. .env lokal erstellen (.env.example umbennen zu .env)

Im obigen Ordner soll eine .env datei erstellt werden, syntax wie in der —> .env.example 

### 4. Terminal öffnen

Im Ordner ein terminal öffnen und den Befehl

```sql
docker-compose up --build
```

In diesem Terminal läuft nun die Datenbank samt der Java anwendung. Um die Applikation zu testen folge das Tutorial unten.

## Testing

Register User through Admin JWT Token

```bash
curl -X PUT http://localhost:8080/auth/admin/register -H "Content-Type: application/json" -H "Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJqb2huZHNvZUBleGFtcGxzZGUuY29tIiwicm9sZXMiOlsiUkVBREVSIl0sImlhdCI6MTczMDQyNjc0OCwiZXhwIjoxNzMwNDI4MTg4fQ.fPlH-zbFPYWY8Vvtd7c5_4X_hXQZccJIuec5AKm4zyQ" -d '{"username": "John Doe", "email": "johndsoe@examplsde.com", "roles": ["READER"], "password": "securePassword"}'
```

Login mittels /sign

```bash
curl -X POST http://localhost:8080/auth/signin -H "Content-Type: application/json" -d '{"email": "admin@example.com", "password": "securePassword1"}'
```

Nach dem Sich der Admin einlogged kriegt man ein JWT Token womit man dann im nächsten Schritt den JWT Token überprüfen kann

Verify using /verify 

```bash
curl -X GET http://localhost:8080/auth/verify -H "Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJhZG1pbkBleGFtcGxlLmNvbSIsInJvbGVzIjpbIkFETUlOIl0sImlhdCI6MTczMDQyNjQ1MCwiZXhwIjoxNzMwNDI3ODkwfQ.M-zxams69_tI_igCZ1HW7IU1kfLHwmtH5ry4zee5stM"
```

### Befehle für das Windows cmd

Register User through Admin JWT Token

```sql
curl -X POST http://localhost:8080/auth/signin -H "Content-Type: application/json" -d "{\"email\": \"admin@example.com\", \"password\": \"securePassword1\"}"
```

Login mittels /sign

```sql
curl -X POST http://localhost:8080/auth/signin -H "Content-Type: application/json" -d "{\"email\": \"admin@example.com\", \"password\": \"securePassword1\"}"
```

Nach dem Sich der Admin einlogged kriegt man ein JWT Token womit man dann im nächsten Schritt den JWT Token überprüfen kann

Verify using /verify 

```sql
curl -X GET http://localhost:8080/auth/verify -H "Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJhZG1pbkBleGFtcGxlLmNvbSIsInJvbGVzIjpbIkFETUlOIl0sImlhdCI6MTczMTU5OTgwMSwiZXhwIjoxNzMxNjAxMjQxfQ.maqyNh1H-jLkdRCV8IMqUbQqHWIj0Lnjgco8eIKyG8U"
```

Check if Data succesfully changed:

```bash
docker exec -it postgrescontainer /bin/bash

psql -U postgres

\c users
\dt
SELECT * FROM users;
```

## Anmerkungen

nvd api ist sehr hilfreich um (ist auch ein maven plugin) um dependencies auf ihre vulnerabilites zu überprüfen

## Literaturverzeichnis

[1] https://elearning.tgm.ac.at/mod/assign/view.php?id=136679

[2] [https://start.spring.io](https://start.spring.io/) 

[3] Java JWT, https://mvnrepository.com/artifact/com.auth0/java-jwt/4.4.0

https://mvnrepository.com/artifact/org.postgresql/postgresql/42.7.4

https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-jpa

https://mvnrepository.com/artifact/org.springframework.security/spring-security-config/6.3.4
