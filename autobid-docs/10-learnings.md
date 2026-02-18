## New Class Types

### `record`: Immutable and Safe Data
Records provide immutable data holders in Java. Their fields are final and cannot be reassigned after construction, ensuring safe and predictable data flow. Note that deep immutability depends on the mutability of the objects stored inside the record.

### `sealed class`: Explicit Inheritance Control
Sealed classes allow you to declare exactly which classes can extend them, differing from abstract classes because their inheritance is not free. This prevents unauthorized implementations that could compromise security rules.

### `final class` and `final methods`
Declaring a class or method as final prevents it from being extended or overridden, preserving the integrity of critical security logic.

## Encryption

### AES-GCM
AES-GCM is not suitable for passwords because if an attacker gains access to the database and knows the key, they can decrypt all passwords. Its advantages include:
- Confidentiality and data integrity.
- A random Initialization Vector (IV) for each encryption.
- High performance in backend environments.

### PBKDF2, BCrypt, and SCrypt
These are recommended because each is intentionally slow and features a configurable strength. This means that as computing power increases, the algorithm can be slowed down by changing the inputs.

### References:
- https://medium.com/@dowglasmaia/protecting-sensitive-data-with-java-21-modern-security-best-practices-f61d41a9a5ce
- https://www.baeldung.com/java-password-hashing