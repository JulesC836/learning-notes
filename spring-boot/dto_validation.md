Voici un fichier Markdown complet et structuré que tu peux copier-coller. Il résume les options de validation essentielles avec **Jakarta Validation**.

---

```markdown
# Guide de Validation des DTO avec Jakarta Validation

La validation des **DTO (Data Transfer Objects)** est la première ligne de défense de votre application. Elle permet de s'assurer que les données entrantes respectent le format attendu avant même d'atteindre votre logique métier.

---

## 🚀 Installation

Si vous utilisez **Spring Boot**, la dépendance suivante inclut l'implémentation de référence (**Hibernate Validator**) :

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>

```

---

## 🛠 Les Annotations Principales

### 1. Validation de Présence

| Annotation | Description |
| --- | --- |
| `@NotNull` | Le champ ne peut pas être `null`. |
| `@NotEmpty` | Ni `null`, ni vide (taille > 0). Utilisé pour les Strings et Collections. |
| `@NotBlank` | Ni `null`, ni uniquement composé d'espaces. Réservé aux Strings. |

### 2. Validation Numérique

| Annotation | Description |
| --- | --- |
| `@Min(value)` / `@Max` | Définit une valeur numérique plancher ou plafond. |
| `@Positive` / `@Negative` | Vérifie le signe du nombre (strict). |
| `@PositiveOrZero` | Autorise zéro et les nombres positifs. |
| `@DecimalMin` / `@DecimalMax` | Pour les nombres décimaux (accepte une chaîne pour la précision). |

### 3. Validation de Format & Taille

| Annotation | Description |
| --- | --- |
| `@Size(min, max)` | Contrôle la longueur d'une String ou d'une Collection. |
| `@Email` | Vérifie que le format correspond à une adresse email valide. |
| `@Pattern(regexp)` | Valide la donnée selon une expression régulière (Regex). |

### 4. Validation de Dates

| Annotation | Description |
| --- | --- |
| `@Past` / `@PastOrPresent` | La date doit être dans le passé. |
| `@Future` / `@FutureOrPresent` | La date doit être dans le futur. |

---

## 📝 Exemple Pratique : `UserRequestDTO`

```java
import jakarta.validation.constraints.*;
import java.time.LocalDate;

public class UserRequestDTO {

    @NotBlank(message = "Le pseudo est obligatoire")
    private String username;

    @Email(message = "L'email doit être valide")
    private String email;

    @Size(min = 8, message = "Le mot de passe doit contenir au moins 8 caractères")
    private String password;

    @Min(value = 18, message = "L'âge minimum est de 18 ans")
    private int age;

    @Past(message = "La date de naissance doit être passée")
    private LocalDate birthDate;
}

```

---

## ⚡️ Activation de la Validation

Pour que ces annotations soient traitées, vous devez annoter l'objet dans votre Controller avec `@Valid` :

```java
@PostMapping("/users")
public ResponseEntity<String> createUser(@Valid @RequestBody UserRequestDTO userDto) {
    return ResponseEntity.ok("Utilisateur valide !");
}

```

---

## 💡 Astuces Avancées

* **Validation Cascade** : Si votre DTO contient un autre objet (ex: `AddressDTO`), utilisez `@Valid` sur le champ pour déclencher la validation récursive.
* **Messages personnalisés** : Vous pouvez créer un fichier `src/main/resources/ValidationMessages.properties` pour gérer les messages d'erreur de façon centralisée.
* **Groupes** : Utilisez l'attribut `groups` pour appliquer des règles différentes selon le contexte (ex: `@NotNull(groups = OnUpdate.class)`).

```

---

Souhaitez-vous que je génère également le code pour un **GlobalExceptionHandler** afin de capturer ces erreurs et les renvoyer proprement au format JSON ?

```