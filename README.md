# MongoDB Integration mit React/TypeScript and Java/Spring Boot

# Installation und Einrichtung von MongoDB

### 1. Installation von MongoDB mit HomeBrew (macOS):

```bash
brew tap mongodb/brew
brew install mongodb-community
```

Nach der Installation kann der MongoDB-Dienst gestartet werden:

```bash
brew services start mongodb/brew/mongodb-community
```

### 2. Alternative: MongoDB Atlas (Cloud-Instanz):

- Besuche [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) und erstelle ein Konto.
- Richte eine kostenlose Cluster-Instanz ein.
- Erstelle eine Datenbank und notiere dir die Verbindungs-URL (z. B. `mongodb+srv://<user>:<password>@cluster0.mongodb.net/<dbname>`).

### 3. Test der Verbindung:

- Über die Shell:
  ```bash
  mongo
  ```
- Oder mit GUI-Tools wie [MongoDB Compass](https://www.mongodb.com/try/download/compass).

---

# Umgebungskonfiguration: Nutzung von `.env` Dateien

## Installation und Einrichtung von `.env`

1. Erstelle eine Datei namens `.env` im Root-Verzeichnis deines Projekts:

   ```env
   MONGO_URI=mongodb+srv://<user>:<password>@cluster0.mongodb.net/<dbname>
   SECRET_KEY=supersecrettoken123
   ```

2. Stelle sicher, dass die `.env` Datei in deiner `.gitignore` steht, damit sie nicht in ein Versionskontrollsystem hochgeladen wird:

   ```bash
   echo ".env" >> .gitignore
   ```

3. Konfiguriere deine Entwicklungsumgebung, um die `.env` Datei zu nutzen:
   - **IntelliJ IDEA/PhpStorm:**
     1. Gehe zu den "Run/Debug Configurations".
     2. Wähle deine Konfiguration aus und klicke auf "Edit Configurations".
     3. Im Abschnitt "Environment variables" klicke auf das Ordner-Icon.
     4. Wähle die `.env` Datei aus, oder gib die Variablen manuell ein.

## Nutzung der `.env` Datei

Nutze die Umgebungsvariablen in deinem Code (zum Beispiel in Node.js):

```javascript
const mongoURI = process.env.MONGO_URI;
const secretKey = process.env.SECRET_KEY;

console.log('Mongo URI:', mongoURI);
console.log('Secret Key:', secretKey);
```

Durch die Nutzung von `.env` Dateien werden sensible Daten sicher verwaltet und der Code bleibt flexibel für unterschiedliche Umgebungen wie Entwicklung, Test und Produktion.

---

# CRUD-Operationen mit React/TypeScript und Java/Spring Boot

CRUD steht für die grundlegenden Funktionen einer Datenbankanwendung: **Create (Erstellen)**, **Read (Lesen)**, **Update (Aktualisieren)** und **Delete (Löschen)**. Diese Methoden bilden den Kern jeder Anwendung, die mit Daten arbeitet.


## Backend: Java/Spring Boot

### 1. Create (Erstellen)
Beim Erstellen von Daten (POST-Anfragen) wird ein neuer Datensatz in die Datenbank eingefügt.

**Spring Boot Controller:**
```java
@PostMapping
public Item createItem(@RequestBody Item item) {
    return repository.save(item);
}
```
Hier nimmt die Methode `createItem` ein Objekt vom Typ `Item` aus dem Request-Body entgegen und speichert es mit der `save`-Methode in der MongoDB-Datenbank.

### 2. Read (Lesen)
Lesen von Daten (GET-Anfragen) ermöglicht das Abrufen von Einträgen, entweder als Liste oder spezifisch nach ID.

**Spring Boot Controller:**
```java
@GetMapping
public List<Item> getAllItems() {
    return repository.findAll();
}
```
Diese Methode ruft alle Einträge aus der Datenbank ab und gibt sie als Liste zurück.

### 3. Update (Aktualisieren)
Aktualisieren von Daten (PUT-Anfragen) erlaubt es, bestehende Datensätze zu verändern.

**Spring Boot Controller:**
```java
@PutMapping("/{id}")
public Item updateItem(@PathVariable String id, @RequestBody Item item) {
    item.setId(id); // ID setzen, um sicherzustellen, dass der Eintrag aktualisiert wird
    return repository.save(item);
}
```
Hier wird der Eintrag durch seine ID identifiziert und anschließend aktualisiert.

### 4. Delete (Löschen)
Löschen von Daten (DELETE-Anfragen) entfernt Datensätze aus der Datenbank.

**Spring Boot Controller:**
```java
@DeleteMapping("/{id}")
public void deleteItem(@PathVariable String id) {
    repository.deleteById(id);
}
```
Diese Methode verwendet die `deleteById`-Methode des Repositories, um einen Datensatz anhand seiner ID zu löschen.

---

## Frontend: React/TypeScript

Auf der Frontend-Seite ist die Kommunikation mit dem Backend über eine API der Schlüssel. Hier werden die CRUD-Operationen mit **Axios** durchgeführt.

### 1. Create (Erstellen)
Das Hinzufügen neuer Daten erfolgt mit einer POST-Anfrage.

```typescript
export const createItem = async (item: { name: string; description: string }) => {
    const response = await axios.post(API_URL, item);
    return response.data;
};
```
Diese Funktion sendet ein neues `Item`-Objekt an die API und gibt die Antwort zurück, in der Regel das erstellte Objekt mit einer generierten ID.

### 2. Read (Lesen)
Das Abrufen von Daten erfolgt mit einer GET-Anfrage.

```typescript
export const getItems = async () => {
    const response = await axios.get(API_URL);
    return response.data;
};
```
Diese Funktion ruft alle verfügbaren Daten von der API ab.

### 3. Update (Aktualisieren)
Das Aktualisieren von Daten erfolgt mit einer PUT-Anfrage.

```typescript
export const updateItem = async (id: string, item: { name: string; description: string }) => {
    const response = await axios.put(`${API_URL}/${id}`, item);
    return response.data;
};
```
Die Funktion sendet das aktualisierte `Item` an die API, wobei die ID in der URL angegeben wird.

### 4. Delete (Löschen)
Das Löschen von Daten erfolgt mit einer DELETE-Anfrage.

```typescript
export const deleteItem = async (id: string) => {
    await axios.delete(`${API_URL}/${id}`);
};
```
Diese Funktion entfernt einen Eintrag, der durch die ID identifiziert wird.

---

## Vorteile und Anwendung von CRUD

- **Modularität:** CRUD-Operationen schaffen eine klare Struktur in Backend und Frontend, die leicht erweitert und gepflegt werden kann.
- **Flexibilität:** CRUD erlaubt das dynamische Verwalten von Daten, unabhängig von der Anwendung (z. B. Web, Mobile).
- **Skalierbarkeit:** Die Trennung von CRUD-Operationen ermöglicht eine bessere Skalierbarkeit und Wiederverwendbarkeit des Codes.

---

## Zusammenarbeit von Backend und Frontend

1. **API-Endpoints:** Das Backend stellt die Endpunkte bereit (`/api/items`), die das Frontend für CRUD-Operationen nutzt.
2. **JSON als Datenformat:** Daten werden zwischen Frontend und Backend im JSON-Format ausgetauscht.
3. **Fehlerbehandlung:** Sowohl im Frontend als auch im Backend können Fehler, z. B. nicht gefundene Ressourcen (404), elegant behandelt werden.

---





3. **Integration in React-Komponenten:**
   ```typescript
   import React, { useEffect, useState } from "react";
   import { getItems, createItem } from "./api";

   const App: React.FC = () => {
       const [items, setItems] = useState([]);

       useEffect(() => {
           getItems().then(setItems);
       }, []);

       const handleCreate = async () => {
           const newItem = { name: "Test", description: "Description" };
           await createItem(newItem);
           const updatedItems = await getItems();
           setItems(updatedItems);
       };

       return (
           <div>
               <button onClick={handleCreate}>Create Item</button>
               <ul>
                   {items.map((item: any) => (
                       <li key={item.id}>{item.name}</li>
                   ))}
               </ul>
           </div>
       );
   };

   export default App;
   ```

---

## Fehlerbehandlung

### Backend:
- Verwende `@ControllerAdvice` für globale Fehlerbehandlung:
  ```java
  import org.springframework.http.ResponseEntity;
  import org.springframework.web.bind.annotation.ExceptionHandler;
  import org.springframework.web.bind.annotation.RestControllerAdvice;

  @RestControllerAdvice
  public class GlobalExceptionHandler {
      @ExceptionHandler(Exception.class)
      public ResponseEntity<String> handleException(Exception e) {
          return ResponseEntity.status(500).body(e.getMessage());
      }
  }
  ```

### Frontend:
- Nutze Axios-Interceptors:
  ```typescript
  axios.interceptors.response.use(
      response => response,
      error => {
          alert(error.response?.data || "An error occurred");
          return Promise.reject(error);
      }
  );
  ```

---

## Deployment

### Docker-Konfiguration:

1. **Dockerfile für Spring Boot:**
   ```dockerfile
   FROM openjdk:17-jdk-slim
   COPY target/app.jar app.jar
   ENTRYPOINT ["java", "-jar", "app.jar"]
   ```

2. **Dockerfile für React:**
   ```dockerfile
   FROM node:18-alpine
   WORKDIR /app
   COPY package*.json ./
   RUN npm install
   COPY . .
   RUN npm run build
   CMD ["npx", "serve", "-s", "build"]
   ```

3. **Docker-Compose:**
   ```yaml
   version: '3.8'
   services:
     backend:
       build: ./backend
       ports:
         - "8080:8080"
     frontend:
       build: ./frontend
       ports:
         - "3000:3000"
     mongodb:
       image: mongo
       ports:
         - "27017:27017"
       volumes:
         - mongodb-data:/data/db
   volumes:
     mongodb-data:
   ```

---

## Testing

1. **Backend-Unit-Tests (JUnit & Mockito):**
   ```java
   @SpringBootTest
   public class ItemServiceTest {
       @Mock
       private ItemRepository repository;

       @InjectMocks
       private ItemService service;

       @Test
       public void testCreateItem() {
           Item item = new Item();
           item.setName("Test Item");
           when(repository.save(any(Item.class))).thenReturn(item);

           Item result = service.createItem(item);
           assertEquals("Test Item", result.getName());
       }
   }
   ```

2. **Frontend End-to-End Tests (Cypress):**
   ```javascript
   describe('CRUD Tests', () => {
       it('Creates a new item', () => {
           cy.visit('/');
           cy.get('button').contains('Create Item').click();
           cy.get('ul').should('contain', 'Test');
       });
   });
   ```

---

## Sensible Daten

1. **Umgebungskonfiguration:**
   - Nutze `.env` Dateien, um sensible Daten / Keys zu speichern.

---

# Erweiterte Funktionen

## Suchfunktionen mit MongoDB-Indexes

### Index erstellen
Um die Performance von Suchanfragen zu verbessern, können Indizes auf bestimmten Feldern erstellt werden:

```javascript
// Index auf das Feld "name" in aufsteigender Reihenfolge erstellen
db.items.createIndex({ name: 1 });
```

### Volltextsuche
MongoDB unterstützt Volltextindizes, die es ermöglichen, nach Wörtern oder Phrasen zu suchen:

```javascript
// Volltextindex auf das Feld "description" erstellen
db.items.createIndex({ description: "text" });

// Beispiel für eine Suche
const results = db.items.find({ $text: { $search: "keyword" } });
```

### Backend-Integration
Im Spring Boot Backend können MongoDB-Indizes verwendet werden, um reguläre Suchmuster zu implementieren:

```java
@Query("{ 'name': { $regex: ?0, $options: 'i' } }")
List<Item> findByNameRegex(String name);
```

---

## Detaillierte Erklärung zu JSON

### Definition
JSON (JavaScript Object Notation) ist ein weit verbreitetes Format für den Datenaustausch. Es ist leichtgewichtig und leicht zu lesen.

### Beispielstruktur

```json
{
    "id": "1",
    "name": "Test Item",
    "attributes": {
        "color": "red",
        "size": "medium"
    },
    "tags": ["sale", "popular"]
}
```

### Datentypen
- **String**: `"value"`
- **Number**: `123`
- **Boolean**: `true` oder `false`
- **Array**: `["value1", "value2"]`
- **Objekt**: `{ "key": "value" }`

### Backend-Parsing
Mit Spring Boot erfolgt das Mapping von JSON-Daten automatisch mithilfe von `@RequestBody`:

```java
@PostMapping
public Item createItem(@RequestBody Item item) {
    return repository.save(item);
}
```

### Frontend-Nutzung
JSON kann mit JavaScript leicht serialisiert und geparst werden:

```javascript
// JSON in einen String serialisieren
const jsonString = JSON.stringify({ name: "Test Item" });

// JSON-String in ein Objekt parsen
const jsonObject = JSON.parse(jsonString);
```

