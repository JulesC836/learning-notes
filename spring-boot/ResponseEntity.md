# ResponseEntity dans Spring Boot

## Qu'est-ce que ResponseEntity ?

`ResponseEntity` est une classe Spring qui représente la **réponse HTTP complète** retournée par une API REST. Elle encapsule trois éléments clés :
- **Le corps** (body) : les données retournées
- **Les en-têtes** (headers) : les métadonnées HTTP
- **Le code de statut** (status code) : ex. 200, 201, 404, 500...

## Avantages de l'utilisation

✅ **Contrôle total** sur la réponse HTTP
✅ **Codes de statut appropriés** pour chaque situation
✅ **Gestion des erreurs cohérente** dans l'API
✅ **En-têtes personnalisés** (Content-Type, Location, etc.)
✅ **Meilleure communication client-serveur**

## Syntaxe de base

```java
ResponseEntity<T> response = new ResponseEntity<>(body, headers, statusCode);
```

Où :
- `T` : type générique du corps de la réponse
- `body` : les données à retourner
- `headers` : les en-têtes HTTP (optionnel)
- `statusCode` : le code HTTP (HttpStatus)

## Exemples d'utilisation

### 1. Succès simple (200 OK)

```java
@GetMapping("/users/{id}")
public ResponseEntity<User> getUser(@PathVariable Long id) {
    User user = userService.findById(id);
    return ResponseEntity.ok(user);
}
```

### 2. Création de ressource (201 Created)

```java
@PostMapping("/users")
public ResponseEntity<User> createUser(@RequestBody User user) {
    User created = userService.save(user);
    return ResponseEntity
        .status(HttpStatus.CREATED)
        .body(created);
}
```

### 3. Sans contenu (204 No Content)

```java
@DeleteMapping("/users/{id}")
public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
    userService.deleteById(id);
    return ResponseEntity.noContent().build();
}
```

### 4. Ressource non trouvée (404 Not Found)

```java
@GetMapping("/users/{id}")
public ResponseEntity<User> getUser(@PathVariable Long id) {
    return userService.findById(id)
        .map(ResponseEntity::ok)
        .orElse(ResponseEntity.notFound().build());
}
```

### 5. Erreur serveur (500 Internal Server Error)

```java
@GetMapping("/data")
public ResponseEntity<Data> getData() {
    try {
        Data data = fetchData();
        return ResponseEntity.ok(data);
    } catch (Exception e) {
        return ResponseEntity
            .status(HttpStatus.INTERNAL_SERVER_ERROR)
            .build();
    }
}
```

### 6. Avec en-têtes personnalisés

```java
@PostMapping("/users")
public ResponseEntity<User> createUser(@RequestBody User user) {
    User created = userService.save(user);
    HttpHeaders headers = new HttpHeaders();
    headers.setLocation(URI.create("/users/" + created.getId()));
    
    return ResponseEntity
        .status(HttpStatus.CREATED)
        .headers(headers)
        .body(created);
}
```

## Codes de statut HTTP courants

| Code | Constante | Utilisation |
|------|-----------|------------|
| 200 | `HttpStatus.OK` | Requête réussie |
| 201 | `HttpStatus.CREATED` | Ressource créée |
| 204 | `HttpStatus.NO_CONTENT` | Succès sans contenu |
| 304 | `HttpStatus.NOT_MODIFIED` | Ressource non modifiée |
| 400 | `HttpStatus.BAD_REQUEST` | Requête invalide |
| 401 | `HttpStatus.UNAUTHORIZED` | Authentification requise |
| 403 | `HttpStatus.FORBIDDEN` | Accès refusé |
| 404 | `HttpStatus.NOT_FOUND` | Ressource introuvable |
| 500 | `HttpStatus.INTERNAL_SERVER_ERROR` | Erreur serveur |

## Méthodes utiles de ResponseEntity

```java
// Réponse OK
ResponseEntity.ok(body)
ResponseEntity.ok().body(body)

// Réponse créée
ResponseEntity.created(location).body(body)

// Pas de contenu
ResponseEntity.noContent().build()

// Ressource non trouvée
ResponseEntity.notFound().build()

// Avec statut personnalisé
ResponseEntity.status(HttpStatus.CUSTOM).body(body)

// Construction complète
ResponseEntity
    .status(HttpStatus.CREATED)
    .header("X-Custom-Header", "value")
    .body(body)
```

## Bonnes pratiques

1. **Toujours utiliser le bon code de statut** : 200 pour succès, 201 pour création, 404 pour non trouvé...
2. **Retourner une structure d'erreur cohérente** : objet d'erreur standardisé
3. **Documenter les codes retournés** avec Swagger/OpenAPI
4. **Gérer les exceptions** et retourner les bons codes HTTP
5. **Utiliser les génériques** pour typer correctement les réponses

## Exemple complet d'API

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @GetMapping
    public ResponseEntity<List<User>> getAllUsers() {
        return ResponseEntity.ok(userService.findAll());
    }
    
    @GetMapping("/{id}")
    public ResponseEntity<User> getUserById(@PathVariable Long id) {
        return userService.findById(id)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }
    
    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody User user) {
        User created = userService.save(user);
        return ResponseEntity
            .status(HttpStatus.CREATED)
            .body(created);
    }
    
    @PutMapping("/{id}")
    public ResponseEntity<User> updateUser(@PathVariable Long id, @RequestBody User user) {
        return userService.findById(id)
            .map(existing -> {
                existing.setName(user.getName());
                return ResponseEntity.ok(userService.save(existing));
            })
            .orElse(ResponseEntity.notFound().build());
    }
    
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        userService.deleteById(id);
        return ResponseEntity.noContent().build();
    }
}
```

## Conclusion

`ResponseEntity` est un outil fondamental pour développer des APIs REST propres et professionnelles avec Spring Boot. Il permet de contrôler précisément chaque aspect de la réponse HTTP et d'offrir une meilleure expérience aux consommateurs de l'API.
