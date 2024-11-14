## Fragestellungen:

### Welche grundlegenden Elemente müssen bei einer REST Schnittstelle zur Verfügung gestellt werden? [1]

Eine REST-API sollte folgende grundlegende Elemente enthalten:

- **Ressourcenorientiertes Design**: Jede Ressource (z. B. Benutzer, Artikel) wird durch eine URL dargestellt und mit HTTP-Methoden manipuliert.
- **HTTP-Methoden**: Die Nutzung der HTTP-Methoden (GET, POST, PUT, DELETE) steht für verschiedene Operationen auf Ressourcen.
- **Zustandslosigkeit**: Jede Anfrage enthält alle notwendigen Informationen, um sie zu verstehen und zu verarbeiten, ohne auf vorherige Anfragen angewiesen zu sein.
- Einheitliche Schnittstelle: Durch die Verwendung standardisierter HTTP-Methoden und Statuscodes wird eine konsistente und vorhersehbare Interaktion zwischen Client und Server gewährleistet.

## Wie stehen diese mit den HTTP-Befehlen in Verbindung? [2]

- **GET**: Diese Methode wird verwendet, um eine Repräsentation einer Ressource vom Server anzufordern. Sie ist sicher und idempotent, was bedeutet, dass mehrere identische GET-Anfragen keine unterschiedlichen Ergebnisse liefern und keine Zustandsänderungen auf dem Server verursachen sollten. Beispielsweise ruft eine GET-Anfrage an `/index.html` die entsprechende HTML-Seite vom Server ab.
- **POST**: Mit der POST-Methode sendet der Client Daten an den Server, um eine neue Ressource zu erstellen oder eine bestehende zu modifizieren. POST ist weder sicher noch idempotent; wiederholte Anfragen können zu unterschiedlichen Ergebnissen führen, wie etwa mehrfaches Erstellen derselben Ressource. Ein typisches Beispiel ist das Absenden eines Formulars, bei dem die eingegebenen Daten an den Server gesendet werden.
- **PUT**: Die PUT-Methode dient dazu, eine Ressource unter einer angegebenen URI zu erstellen oder vollständig zu ersetzen. Sie ist idempotent; mehrere identische PUT-Anfragen führen zum selben Ergebnis, da die Ressource durch jede Anfrage in denselben Zustand versetzt wird. Ein Beispiel wäre das Hochladen einer Datei an eine spezifische URI, wobei die Datei bei jeder PUT-Anfrage überschrieben wird.
- **DELETE**: Mit DELETE fordert der Client das Entfernen einer Ressource vom Server an. Diese Methode ist ebenfalls idempotent; das mehrfache Senden einer DELETE-Anfrage hat denselben Effekt wie eine einzelne, da die Ressource nach der ersten Anfrage bereits gelöscht ist. Ein Beispiel ist das Löschen eines bestimmten Datensatzes in einer Datenbank über eine DELETE-Anfrage an die entsprechende URI.

### Welche Datenbasis bietet sich für einen solchen Use-Case an? [2]

Für eine REST-Schnittstelle im beschriebenen Use-Case eignet sich eine **NoSQL-Datenbank** wie **MongoDB** oder eine **relationale Datenbank** wie **PostgreSQL**:

1. **Relational (PostgreSQL)**:
    - **Datenstruktur**: Tabellenbasiert, eignet sich gut für strukturierte Daten mit Beziehungen.
    - **Sicherheit**: Hohe Sicherheitsstandards, Unterstützung für Datenverschlüsselung und Zugriffssteuerung.
    - **Simplicität**: Starke Unterstützung für SQL-Queries und Transaktionen; ideal, wenn Datenintegrität und einfache Wartung wichtig sind.
2. **NoSQL (MongoDB)**:
    - **Datenstruktur**: Dokumentenorientiert, flexibel, passt gut für JSON-ähnliche REST-APIs.
    - **Sicherheit**: Bietet integrierte Authentifizierung und Verschlüsselung; gut geeignet für verteilte Umgebungen.
    - **Simplicität**: Einfaches Schema, flexibel bei Strukturänderungen; keine komplizierten Tabellenrelationen notwendig.

### Welche Erfordernisse bezüglich der Datenbasis sollten hier bedacht werden?

Bei der Auswahl einer Datenbank für eine REST-Schnittstelle sollten folgende Aspekte berücksichtigt werden:

1. **Datenstruktur**: Die Datenbank sollte die Struktur der zu speichernden Daten unterstützen. Für flexible, dokumentenbasierte Daten eignet sich eine NoSQL-Datenbank wie MongoDB, während für strukturierte Daten mit klaren Beziehungen eine relationale Datenbank wie PostgreSQL vorteilhaft ist.
2. **Skalierbarkeit**: Die Datenbank muss in der Lage sein, mit dem erwarteten Datenvolumen und der Anzahl der Anfragen zu wachsen. NoSQL-Datenbanken bieten oft horizontale Skalierbarkeit, während relationale Datenbanken vertikal skalieren.
3. **Sicherheit**: Es sollten Mechanismen für Authentifizierung, Autorisierung und Datenverschlüsselung vorhanden sein, um sensible Informationen zu schützen.
4. **Transaktionsunterstützung**: Für Anwendungen, die ACID-Eigenschaften (Atomicity, Consistency, Isolation, Durability) erfordern, sind relationale Datenbanken oft besser geeignet.
5. **Integration**: Die Datenbank sollte sich nahtlos in die bestehende Systemarchitektur integrieren lassen und mit den verwendeten Technologien kompatibel sein.

Für unser Projekt werden wir PostgresSQL verwenden da es alle Ansprüche oben erfüllt und uns das deployen und die Sicherheit wichtig sind.

### Verschiedene Frameworks bieten schnelle Umsetzungsmöglichkeiten, Welche Eckpunkte müssen bei einer öffentlichen Bereitstellung (Production) von solchen Services beachtet werden?

Bei der öffentlichen Bereitstellung eines Services sollten folgende Eckpunkte beachtet werden:

1. **Sicherheit**: HTTPS, Authentifizierung, Autorisierung, Firewalls und Eingabeverifizierung.
2. **Skalierbarkeit**: Lastverteilung, horizontale Skalierung und Caching (z. B. Redis).
3. **Performance**: Optimierung von Datenbankabfragen, Minimierung der Antwortzeit und Monitoring.
4. **Fehlertoleranz**: Redundanz, automatisierte Backups und Monitoring für Ausfallzeiten.
5. **Wartbarkeit**: Logging, automatisierte Deployments und dokumentierte API-Versionierung.
6. **Compliance**: Datenschutzrichtlinien (z. B. DSGVO), regelmäßige Sicherheitsüberprüfungen.

## Umsetzung

### Projektkonfiguration

Mithilfe des Spring Initializr [2] wird zunächst einmal ein Springboot Projekt mit folgenden Dependencies intialisieren: “Spring Boot DevTools”, “Spring Web”, “H2 Database”, “Lombok” “Spring Security”. Als Build-Management-Tool wähle ich Maven. Zusätzlich füge ich in der “pom.xml” von dem generierten Projekt folgende Zeilen an Code hinzu: 

```xml
		<!-- https://mvnrepository.com/artifact/com.auth0/java-jwt -->
		<dependency>
			<groupId>com.auth0</groupId>
			<artifactId>java-jwt</artifactId>
			<version>4.4.0</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
```

Diese fügen die Dependencies “java-jwt” [3] und “spring-boot-starter-data-jpa” [4] hinzu. Dabei ist ersteres sinnvoll für die Authentifikation, die ja laut Aufgabenstellung [1] mittels JWT-Token stattfinden soll. Die zweite Abhängigkeit hingegen vereinfacht die Anbindung einer relationalen Datenbank. 

### Datenbankentwurf

Eine postgres Datenbank namens `users` wird erstellt. In der Gibt es 2 tables, welche folgenden Attribute haben:

**Table users**

| **users** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| user_name | VARCHAR | Name des Benutzers |
| user_email | VARCHAR | Eindeutige E-Mail-Adresse des Benutzers (unique, not null), Primärschlüssel |
| user_password | VARCHAR | Gehashter Passwort-String |

**Table user_roles**

| **user_roles** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| user_email | VARCHAR | Fremdschlüssel, der auf `users.user_email` verweist |
| roles | VARCHAR | Die Rolle des Benutzers (`ADMIN`, `READER`, `MODERATOR`) |

Die Entity-Klasse für die User sieht dabei folgendermaßen aus:

```java
...
@Entity
@Table
public class User {
    @Column(name = "user", nullable = false)
    private String name;
    @Id
    @Column(name = "user_id", unique = true, nullable = false)
    private String email;
    @ElementCollection(fetch = FetchType.EAGER) // Sammlung einfacher Werte speichern ohne zusätzliche Entität, sofortiges laden
    // ähnlich wie Colum aber spezifiert die zustäzliche Tabelle
    @CollectionTable(name = "user_roles", joinColumns = @JoinColumn(name = "user_id")) //user_id Fremschlüssel
    @Enumerated(EnumType.STRING)
    private Set<Role> roles;
    @Column(name = "user_password", nullable = false)
    private String password;
}
```

Folgende Punkte sind dabei wichtig:

- @Column Annotation um die Datenbankspalte zu konfigurieren (Spaltenname, Einzigartigkeit…)
- @Id um die “user_email” als Primary key festzulegen
- “user_roles” wird als eigene Tabelle implementiert —> keine eigene Entität für “user_roles” Attribut notwendig durch Annotation “ElementCollection”
- user_roles ist ein Set an Enums
- @ElementCollection um die Sammlung einfacher Werte als Attribut festzulegen und zu speichern ohne dafür eine eigene Entität anlegen zu müssen
- CollectionTable fungiert ähnlich wie @Column aber nur für die Tabelleneinstellung, darunter dient “joinColumns = @JoinColumn(name = "user_id")”, um das zugewiesene Set an Rollen einem EINZIGEN User zuzuordnen
- Durch die Annotation “@Enumerated”  wird das Set aus Enums “user_roles” in String Form statt in numerischer Form gespeichert

Hier ist die dazugehörige Enum-Klasse welche die Rollen als enum darstellt:

```java
public enum Role {
    ADMIN,
    READER, 
    MODERATOR
}
```

### Promptverzeichnis:

[1] Prompt 1

```sql
Erkläre kurz und knapp:

Welche grundlegenden Elemente müssen bei einer REST Schnittstelle zur Verfügung gestellt werden?
```

Mit ChatGPT Model 4o generiert

[2] Prompt 2

```sql
Erklär die 4 Operation welche auf der RFC 2616 Seite unter diesem Link zu finden sind: https://datatracker.ietf.org/doc/html/rfc2616#page-54

Bitte ausführlich und genau
```

```sql
Welche Datenbasis bietet sich für solchen Use-Case an?
Erkläre kurz und knapp, gehe auf Sicherheit und Simplität ein
```

```sql
Welche Erfordernisse bezüglich der Datenbasis sollten hier bedacht werden?
Erklär kurz und knapp
```

```sql
Welche Eckpunkte müssen bei einer öffentlichen Bereitstellung (Production) von solchen Services beachtet werden? 
Erkäre so knapp wie möglich
```

```bash
    @Bean
    public AuthenticationProvider authenticationProvider() {
        DaoAuthenticationProvider authProvider = new DaoAuthenticationProvider();
        authProvider.setUserDetailsService(userDetailsService());
        authProvider.setPasswordEncoder(passwordEncoder());
        return authProvider;
    }

was genau ist dieser dao authentication provider und wofür ist er da?
```

```bash
damit kann ich dann auch gleich api requests filtern ob es successful war oder nicht oder?
```

Mit ChatGPT Model 4o generiert ( https://chatgpt.com/share/67360d0c-ab94-800f-acd4-0a41d80443b0 )
