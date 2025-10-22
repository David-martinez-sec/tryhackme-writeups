# TryHackMe Room: Hashing Basics

## Summary
In this room, I learned how hashing functions are used to protect stored passwords and verify file integrity. Hashing converts data into a fixed-length digest using a one-way mathematical function, making it ideal for verifying authenticity without revealing original content.

## Key Concepts Learned
- **Purpose of Hashing**: Hashes are used to securely store passwords and confirm that files or messages have not been tampered with.
- **Hash Functions**: Functions like MD5, SHA1, and bcrypt take an input and produce a unique hash output. Even a small change in the input drastically changes the hash.
- **Password Protection**: Instead of storing passwords directly, systems store their hashes. When users log in, their entered password is hashed and compared with the stored hash.
- **Integrity Verification**: Hashes can be used to ensure that downloaded files or transmitted data remain unchanged.

## Tools Used
- **Hashcat**: A password recovery tool used to crack hashes using wordlists such as *rockyou.txt*. Learned how to identify hash types and apply rule-based attacks.
- **Online Hash Lookup**: Used online rainbow table services to identify plaintext passwords from known hash values.

## Additional Concepts
- **Encoding**: Converts data into another format for transmission (e.g., Base64). It’s reversible and not secure.
- **Encryption**: Protects data confidentiality using a key for both encryption and decryption.
- **Hashing**: A one-way operation that cannot be reversed; used for verification, not data retrieval.

## Reflection
This room strengthened my understanding of how authentication and data integrity mechanisms work. It also highlighted how important it is to use strong hashing algorithms and salts to protect sensitive information.

