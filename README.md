# Todolist – Webapplikasjon med Flask og MariaDB

## Tittel og hensikt

**Todolist** er en nettbasert oppgavebehandler (todo-applikasjon) som lar brukere registrere seg, logge inn og administrere egne oppgaver via en enkel og ryddig webside.

Løsningen løser problemet med å holde oversikt over gjøremål på en sikker og strukturert måte – med innlogging, passordhashing og databaselagring.

---

## Målgruppe

Studenter og andre som ønsker en enkel digital oppgaveliste med innlogging og personlig oversikt.

---

## Hva gjør systemet?

- Registrering og innlogging med SHA-256-hashet passord
- Legge til, fullføre og slette oppgaver
- FAQ-side med mulighet for å legge til og slette spørsmål/svar
- Tilgangskontroll: kun innloggede brukere får tilgang
- Session-basert innlogging med Flask sessions

---

## Teknologi

| Teknologi | Bruk |
|-----------|------|
| Python | Programmeringsspråk |
| Flask | Web-rammeverk (backend) |
| MariaDB | Database |
| mysql-connector-python | Kobling mellom Flask og MariaDB |
| hashlib (SHA-256) | Passordhashing |
| HTML / CSS | Frontend |
| Raspberry Pi OS (Linux) | Operativsystem på server |
| Waitress | Produksjonswebserver |

---

## Databasemodell

### Tabell: `users`

| Kolonne | Type | Beskrivelse |
|---------|------|-------------|
| id | INT (PK) | Primærnøkkel, auto-increment |
| username | VARCHAR | Brukernavn (unikt) |
| password | VARCHAR | SHA-256-hashet passord |

### Tabell: `tasks`

| Kolonne | Type | Beskrivelse |
|---------|------|-------------|
| id | INT (PK) | Primærnøkkel, auto-increment |
| title | VARCHAR | Tittel på oppgaven |
| description | TEXT | Beskrivelse av oppgaven |
| is_done | BOOLEAN | Om oppgaven er fullført |
| created_at | DATETIME | Tidspunkt oppgaven ble opprettet |

### Tabell: `faq`

| Kolonne | Type | Beskrivelse |
|---------|------|-------------|
| id | INT (PK) | Primærnøkkel, auto-increment |
| question | VARCHAR | Spørsmål |
| answer | TEXT | Svar |
| created_at | DATETIME | Tidspunkt FAQ ble opprettet |

---

## SQL – Opprette tabeller

```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(100) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL
);

CREATE TABLE tasks (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    is_done BOOLEAN DEFAULT FALSE,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE faq (
    id INT AUTO_INCREMENT PRIMARY KEY,
    question VARCHAR(255) NOT NULL,
    answer TEXT NOT NULL,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

---

## Routes (Flask)

| Metode | URL | Beskrivelse |
|--------|-----|-------------|
| GET/POST | `/register` | Registrering av bruker |
| GET/POST | `/login` | Innlogging |
| GET | `/logout` | Logger ut og tømmer session |
| GET | `/` | Forsiden med alle oppgaver |
| POST | `/add` | Legger til ny oppgave |
| GET | `/done/<id>` | Markerer oppgave som fullført |
| GET | `/delete/<id>` | Sletter oppgave |
| GET | `/faq` | Viser FAQ-siden |
| POST | `/faq/add` | Legger til nytt FAQ-innslag |
| GET | `/faq/delete/<id>` | Sletter FAQ-innslag |

---

## Sikkerhet

- **Passordhashing:** Passord lagres aldri i klartekst. SHA-256 brukes via `hashlib`.
- **Parametriserte SQL-spørringer:** Beskytter mot SQL Injection.
- **Session-basert tilgangskontroll:** Kun innloggede brukere får tilgang til beskyttede sider.
- **UFW-brannmur:** Kun nødvendige porter er åpne på serveren.
- **.gitignore:** Sensitiv informasjon lastes ikke opp til GitHub.

---

## Versjonskontroll

Prosjektet bruker Git og GitHub for versjonskontroll.

```bash
git add .
git commit -m "Beskrivelse av endring"
git push
```

På Raspberry Pi hentes siste versjon med:

```bash
git pull
```

---

## Deployment – Raspberry Pi

### Logg inn med SSH

```bash
ssh brukernavn@ip-adresse
```

### Start applikasjonen

```bash
sudo systemctl start minapp
sudo systemctl status minapp
```

### Vis at MariaDB kjører

```bash
sudo systemctl status mariadb
```

### Finn IP-adresse

```bash
ip a
```

---

## Backup av database

```bash
mysqldump -u brukernavn -p todolist_db > backup.sql
```

### Gjenoppretting

```bash
mariadb -u brukernavn -p todolist_db < backup.sql
```

---

## GDPR og personvern

Systemet lagrer brukernavn og hashede passord. Brukere har rett til innsyn, retting og sletting av egne data i henhold til GDPR. Passord lagres aldri i klartekst.

---

## Kanban / Arbeidslogg

Prosjektet bruker GitHub Projects som Kanban-tavle for å planlegge og følge opp arbeidsoppgaver.

---

## Hvordan kjøre lokalt (utvikling)

```bash
pip install flask mysql-connector-python
python app.py
```

Åpne nettleseren og gå til: `http://localhost:5000`
