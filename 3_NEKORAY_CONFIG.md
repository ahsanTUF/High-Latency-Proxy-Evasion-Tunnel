# Network Encapsulation (Nekoray)

Configure the local client to ingest global system traffic and route it through the established SSH port.

## 1. Profile Configuration
* **Type:** VLESS
* **Address:** `127.0.0.1`
* **Port:** `4444`
* **Flow:** `xtls-rprx-vision`
* **Multiplex (Mux):** `Keep Default` (Do not enable Mux; it conflicts with the Vision protocol).

## 2. Routing Rules
Navigate to **Preferences** > **Routing Setting** > **Simple Route**.
Input the local restrictive proxy IP into the **Direct IP** field to prevent infinite TUN loops:
* **Direct IP:** `172.30.10.11`

## 3. TUN Configuration
Navigate to **Preferences** > **TUN Setting**.
* Enable **Fake DNS**. (Intercepts local UDP DNS queries and forces remote TCP resolution).

## 4. Activation
1. Start the configured profile.
2. Check the **TUN Mode** box on the main interface.
