# Enabling Two-Factor Authentication (2FA) in Vaultwarden

Two-Factor Authentication (2FA) adds an extra layer of security to your Vaultwarden account. Even if someone discovers your Master Password, they won't be able to log in without also having access to your second factor (e.g., a code from an authenticator app or a security key).

**It is strongly recommended that all users enable 2FA.**

## Supported 2FA Methods

Vaultwarden supports several 2FA methods:

* **Authenticator App (TOTP/HOTP):** Uses apps like Authy, Google Authenticator, Microsoft Authenticator, Aegis (Android), etc., to generate time-based one-time passwords. This is the most common and widely supported method.
* **YubiKey OTP Security Key:** Uses a YubiKey in OTP (One-Time Password) mode. (Requires server-side configuration by the admin: `YUBICO_CLIENT_ID` and `YUBICO_SECRET_KEY`).
* **FIDO2 WebAuthn / Security Keys:** Uses hardware security keys like YubiKey (in FIDO2 mode), SoloKeys, etc., or platform authenticators (like Windows Hello, macOS Touch ID). This is a very secure method. (Requires `DOMAIN` to be correctly set to an HTTPS URL in the server configuration).
* **Duo Security:** Uses Duo Push, SMS, or phone call for verification. (Requires server-side configuration by the admin).
* **Email:** Sends a code to your email address. This is generally considered less secure than other methods due to the risk of email account compromise, but better than no 2FA.

This guide will primarily focus on setting up **Authenticator App (TOTP)**, as it's the most accessible for most users.

## Setting up 2FA with an Authenticator App (TOTP)

1.  **Install an Authenticator App:**
    If you don't already have one, install an authenticator app on your smartphone. Popular choices include:
    * Authy (supports multi-device and encrypted backups)
    * Google Authenticator
    * Microsoft Authenticator
    * Aegis Authenticator (Android, Open Source)
    * andOTP (Android, Open Source)

2.  **Log in to Your Vaultwarden Account:**
    Open your Vaultwarden web vault (e.g., `https://vault.yourdomain.com`) or one of the client applications (desktop, browser extension).

3.  **Navigate to 2FA Settings:**
    * Go to **Settings**.
    * In the security section, find and click on **Two-step Login** (or similar wording).

4.  **Choose "Authenticator App":**
    * You will see a list of available 2FA providers. Click on **Manage** or **Setup** next to "Authenticator App".

5.  **Scan the QR Code or Enter Key Manually:**
    * Vaultwarden will display a QR code.
    * Open your authenticator app on your smartphone and choose the option to add a new account (usually a "+" icon).
    * Scan the QR code displayed by Vaultwarden using your phone's camera.
    * Alternatively, you can manually enter the secret key provided by Vaultwarden into your authenticator app.

6.  **Enter the Verification Code:**
    * Once your authenticator app has added the account, it will start generating 6-digit time-based codes that change every 30-60 seconds.
    * Enter the current code from your authenticator app into the verification field in Vaultwarden.
    * Click **Enable**.

7.  **Save Your Recovery Codes!**
    * **CRITICAL STEP:** Vaultwarden will provide you with a set of recovery codes. These codes can be used to access your account if you lose access to your authenticator app or device.
    * **Copy these codes and store them in a very safe, secure, and offline location.** Do NOT store them only in your Vaultwarden vault (as you'd need them to get in if 2FA fails).
    * Examples of safe storage:
        * Printed and stored in a physical safe.
        * Saved in an encrypted file on a USB drive stored securely.
        * Given to a trusted person for safekeeping.
    * Confirm that you have saved the recovery codes.

8.  **2FA is Now Enabled!**
    The next time you log in to Vaultwarden from a new device or after your session expires, you will be prompted to enter your Master Password and then a code from your authenticator app.

## Using Other 2FA Methods

* **FIDO2 WebAuthn / Security Keys:** If you have a FIDO2 compatible security key:
    1.  In the "Two-step Login" settings, select "FIDO2 WebAuthn Security Key".
    2.  Follow the prompts to register your key. You will typically need to insert the key and touch its button.
    3.  Give the key a recognizable name.
* **YubiKey OTP:** If enabled by the admin, select YubiKey and follow the prompts. You'll need to insert your YubiKey and touch it to input an OTP.

## Important Considerations

* **Multiple 2FA Methods:** You can often enable more than one 2FA method for added flexibility (e.g., an authenticator app and a FIDO2 key).
* **Device Security:** The security of your 2FA method also depends on the security of the device it's on (e.g., your smartphone). Ensure your phone has a strong lock screen (PIN, pattern, password, or biometrics).
* **"Remember Me":** Some clients offer a "Remember Me" option for 2FA for a certain period. Use this cautiously, especially on shared or public devices.

If you encounter any issues setting up 2FA, consult the official Vaultwarden documentation or contact the administrator of your instance.