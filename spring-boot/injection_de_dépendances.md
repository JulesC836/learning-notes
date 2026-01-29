# 🔌 Stratégies d’injection de dépendances en Spring Boot

Spring Boot repose sur le principe d’**Inversion de Contrôle (IoC)** :  
les objets ne créent pas leurs dépendances, **le conteneur Spring les fournit**.

---

## 1️⃣ Injection par constructeur (RECOMMANDÉE)

### Principe
Les dépendances sont fournies **au moment de la création du bean**.

```java
@Component
public class PizzaService {

    private final Dough dough;
    private final Sauce sauce;

    public PizzaService(Dough dough, Sauce sauce) {
        this.dough = dough;
        this.sauce = sauce;
    }
}
Avantages
Dépendances obligatoires

Objet toujours valide

Compatible avec final

Facile à tester

Favorise l’immutabilité

Quand l’utiliser
Dépendance essentielle

Le bean ne peut pas fonctionner sans

2️⃣ Injection par setter (dépendance optionnelle)
Principe
Les dépendances sont injectées après la création du bean.

@Component
public class PizzaService {

    private Cheese cheese;

    @Autowired
    public void setCheese(Cheese cheese) {
        this.cheese = cheese;
    }
}
Avantages
Dépendances optionnelles

Flexible

Inconvénients
Bean potentiellement incomplet

Risque de NullPointerException

Quand l’utiliser
La dépendance est facultative

3️⃣ Injection optionnelle avec Optional<T>
Principe
Spring injecte la dépendance si elle existe.

@Component
public class PizzaService {

    private final Optional<Cheese> cheese;

    public PizzaService(Optional<Cheese> cheese) {
        this.cheese = cheese;
    }
}
Quand l’utiliser
Dépendance facultative

Intention explicite (pas de null)

4️⃣ Injection de collection List<T> / Set<T>
Principe
Spring injecte tous les beans du type T.

@Component
public class PaymentService {

    private final List<PaymentStrategy> strategies;

    public PaymentService(List<PaymentStrategy> strategies) {
        this.strategies = strategies;
    }
}
@Component
public class CardPayment implements PaymentStrategy {}

@Component
public class CashPayment implements PaymentStrategy {}
Quand l’utiliser
Plusieurs implémentations

Pattern Strategy

Architecture extensible

5️⃣ Injection par Map<String, T>
Principe
Spring injecte une map nomDuBean → instance.

@Component
public class PaymentService {

    private final Map<String, PaymentStrategy> strategies;

    public PaymentService(Map<String, PaymentStrategy> strategies) {
        this.strategies = strategies;
    }
}
PaymentStrategy strategy = strategies.get("cardPayment");
6️⃣ Injection avec @Qualifier
Principe
Permet de sélectionner une implémentation précise.

@Component
public class PizzaService {

    private final Cheese cheese;

    public PizzaService(@Qualifier("mozarella") Cheese cheese) {
        this.cheese = cheese;
    }
}
@Component("mozarella")
public class Mozarella implements Cheese {}
7️⃣ Injection par champ (DÉCONSEILLÉE)
@Autowired
private Dough dough;
Problèmes
Dépendances cachées

Difficulté de test

Couplage fort

Impossible d’utiliser final

🚫 À éviter en production

8️⃣ Injection avec @Primary
Principe
Définit une implémentation par défaut.

@Primary
@Component
public class Mozarella implements Cheese {}
🧠 Récapitulatif
Stratégie	Dépendance obligatoire	Usage
Constructeur	Oui	⭐⭐⭐⭐⭐
Setter	Non	⭐⭐
Optional	Non	⭐⭐⭐
List / Set	Non	⭐⭐⭐⭐
Map	Non	⭐⭐⭐⭐
Champ	Non	🚫
🎯 Bonnes pratiques
Toujours privilégier l’injection par constructeur

Utiliser setter ou Optional pour l’optionnel

Utiliser List ou Map pour les implémentations multiples

Éviter l’injection par champ

Rendre l’intention explicite dans les signatures

📌 Conclusion
Une bonne stratégie d’injection :

améliore la testabilité

réduit le couplage

clarifie les responsabilités

rend l’architecture plus robuste