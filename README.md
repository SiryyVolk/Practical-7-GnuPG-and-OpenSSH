# Practical 7: GnuPG and OpenSSH

This practical demonstrates public-key encryption and digital signatures with
GnuPG. The repository contains the sample message exchanged between Peter and
Bob, along with the public keys and signed/encrypted responses.

## Files

| File | Purpose |
| --- | --- |
| `message.txt` | Plaintext message to encrypt for Bob |
| `pubkey.bob.porter@initech.com.gpg.asc` | Bob Porter's ASCII-armored public key |
| `pubkey.c00305748@setu.ie.gpg.asc` | Vlad Lysak's ASCII-armored public key |
| `secret-message.txt` | ASCII-armored PGP ciphertext |
| `signed-reply.txt` | Clear-signed reply from Bob |

## Requirements

- GnuPG 2.x (`gpg --version`)
- OpenSSH client tools (`ssh -V`) for the SSH portion of the practical

## GnuPG workflow

Create a separate keyring for this exercise if you do not want to change your
default personal keyring:

```bash
mkdir -p .gnupg-practical
chmod 700 .gnupg-practical
export GNUPGHOME="$PWD/.gnupg-practical"
```

Import the supplied public keys and inspect their identities and fingerprints:

```bash
gpg --import pubkey.bob.porter@initech.com.gpg.asc
gpg --import pubkey.c00305748@setu.ie.gpg.asc
gpg --list-keys --fingerprint
```

Encrypt `message.txt` for Bob. `--armor` produces a text file that can be
shared safely as an email attachment or pasted into a text channel:

```bash
gpg --armor --encrypt \
	--recipient 'bob.porter@initech.com' \
	--output encrypted-message.txt \
	message.txt
```

Decrypt an encrypted message with the matching private key:

```bash
gpg --decrypt --output decrypted-message.txt secret-message.txt
```

The public keys stored in this repository are not sufficient to decrypt
`secret-message.txt`; decryption requires the recipient's corresponding
private key and, if configured, its passphrase.

Verify the clear-signed reply:

```bash
gpg --verify signed-reply.txt
```

For a valid signature, GnuPG reports the signing key and a good signature.
The fingerprint should still be checked against a trusted source; a
cryptographically valid signature alone does not prove that the key belongs to
the claimed person.

## OpenSSH reminders

Generate an Ed25519 SSH key pair when one is not already available:

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

Review the public key before installing it on a server:

```bash
cat ~/.ssh/id_ed25519.pub
ssh-keygen -lf ~/.ssh/id_ed25519.pub
```

Connect to a host using the key:

```bash
ssh -i ~/.ssh/id_ed25519 username@host
```

Never share a private key. Confirm a server's host-key fingerprint through a
trusted channel before accepting it.