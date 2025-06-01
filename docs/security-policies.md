
# Vaultwarden Security Policies & Best Practices

Using a password manager is a significant step towards better digital security. However, the security of your password manager itself and how you use it are equally important. This document outlines recommended security policies and best practices for users of this self-hosted Vaultwarden instance.

## 1. Master Password

* **Complexity:** Your Master Password is the key to your entire vault. It must be:
    * **Long:** Aim for at least 16-20 characters, ideally more. Passphrases (multiple words strung together) are easier to remember and can be very strong.
    * **Unique:** This password must *not* be used anywhere else.
    * **Complex:** Include a mix of uppercase letters, lowercase letters, numbers, and symbols.
* **Memorization:** Memorize your Master Password. Do not write it down insecurely. If you must write it down as a last resort, store it in an extremely secure physical location (e.g., a safe, or split into parts stored in different locations). Consider trusted family members for recovery if appropriate.
* **Never Share:** Do not share your Master Password with anyone.
* **Regular Changes (Optional but Recommended):** Consider changing your Master Password periodically (e.g., every 6-12 months), especially if you suspect it might have been compromised.

## 2. Two-Factor Authentication (2FA)

* **Enable 2FA:** All users **must** enable Two-Factor Authentication for their Vaultwarden account. This adds a critical layer of security.
    * Preferred methods: TOTP (Time-based One-Time Password) apps like Authy, Google Authenticator, Aegis Authenticator (Android), or YubiKey (if enabled on the server).
* **Backup Codes:** When you enable 2FA, you will be given backup codes. Store these codes securely in a separate, safe location (e.g., printed and stored in a safe). They are essential if you lose access to your 2FA device.
* **Secure 2FA Devices:** Ensure the device used for 2FA (e.g., your smartphone) is itself secured with a strong PIN, password, or biometric lock.

## 3. Account Security Settings (Inside Vaultwarden)

* **Vault Timeout:** Configure your Vaultwarden clients to automatically lock after a short period of inactivity (e.g., 5-15 minutes).
* **Clipboard Clearing:** Configure clients to clear the clipboard automatically after a short period (e.g., 30-60 seconds) when passwords are copied.
* **Regularly Review Account Activity:** (If available) Check login history for any suspicious activity.

## 4. Password Generation & Usage

* **Strong, Unique Passwords:** Use Vaultwarden to generate strong, unique passwords for *every* online account. Avoid reusing passwords.
* **Password Length:** Generate passwords that are as long as the service allows (e.g., 16-25+ characters).
* **Avoid Predictable Patterns:** Let the generator create random passwords.

## 5. Backups (Server-Side)

While this section primarily targets users, it's important to be aware of server-side practices.
* **Responsibility:** The administrator of this Vaultwarden instance is responsible for ensuring regular, automated backups of the Vaultwarden data volume (`vw-data` or the configured `DATA_FOLDER`).
* **Backup Strategy:**
    * Backups should be performed at least daily.
    * Backups must be stored securely.
    * Ideally, backups should be encrypted.
    * At least one copy of the backups should be stored off-site (e.g., different physical location or secure cloud storage).
* **Restore Testing:** The administrator should periodically test the backup restoration process to ensure data can be recovered successfully.

## 6. Software Updates

* **Client Applications:** Keep your Vaultwarden browser extensions and mobile apps updated to the latest versions.
* **Server Software:** The administrator is responsible for keeping the Vaultwarden Docker image and the host system updated.

## 7. Phishing and Social Engineering Awareness

* Be cautious of phishing emails or messages asking for your Master Password or 2FA codes. Vaultwarden (or the administrator) will never ask you for your Master Password.
* Access Vaultwarden only through the official URL or trusted client applications.

## 8. Device Security

* Ensure the devices (computers, smartphones, tablets) you use to access Vaultwarden are secure:
    * Keep operating systems and software updated.
    * Use reputable antivirus/antimalware software.
    * Use a firewall.
    * Secure devices with strong passwords, PINs, or biometric authentication.
    * Be cautious about using Vaultwarden on public or untrusted computers. If you must, use a private Browse window and ensure you log out completely.

## 9. Reporting Security Concerns

If you suspect your account has been compromised or notice any suspicious activity related to this Vaultwarden instance, immediately:
1.  Attempt to change your Master Password if you still have access.
2.  Revoke any active sessions.
3.  Notify the administrator of this Vaultwarden instance.

By following these policies, you can significantly enhance the security of your digital life.