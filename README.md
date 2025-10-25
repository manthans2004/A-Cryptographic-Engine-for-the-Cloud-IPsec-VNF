
# 🛡️ VNF-IPSec: Containerized IPsec VPN for Secure Network Tunneling

This repository provides a **containerized IPsec (StrongSwan-based) VPN solution** using the `openvnf/vnf-ipsec` image.  
It supports both **environment-based configuration** and **manual configuration** for advanced network security research and demonstrations.

---

## 🚀 Quick Start

```bash
docker run --privileged \
    -v /lib/modules:/lib/modules \
    --env-file environment.sh \
    openvnf/vnf-ipsec
````

---

## ⚙️ Configuration

Configuration can be managed via **environment variables** or a separate file referenced by `$ENVFILE`.

### Example

```bash
# Source configuration file before startup
ENVFILE=environment.sh
```

---

## 🌐 Core Environment Variables

| Variable                  | Description                                                                           |
| ------------------------- | ------------------------------------------------------------------------------------- |
| `SET_ROUTE_DEFAULT_TABLE` | Add route for tunnel in default routing table (useful with Calico or host networking) |
| `IPSEC_LOCALIP`           | Local IP address (`%any` if dynamic)                                                  |
| `IPSEC_LOCALID`           | Local identifier (must match `IPSEC_REMOTEID` on peer)                                |
| `IPSEC_LOCALNET`          | Local subnet to share over VPN                                                        |
| `IPSEC_REMOTEIP`          | Public IP of remote peer                                                              |
| `IPSEC_REMOTENET`         | Remote subnet to share                                                                |
| `IPSEC_REMOTEID`          | Remote identifier for connection                                                      |
| `IPSEC_PSK`               | Pre-shared key (use a long random string)                                             |
| `IPSEC_KEYEXCHANGE`       | Key exchange protocol (`ike`, `ikev1`, `ikev2`)                                       |
| `IPSEC_ESPCIPHER`         | ESP encryption/authentication algorithms                                              |
| `IPSEC_IKECIPHER`         | IKE/ISAKMP algorithms                                                                 |
| `IPSEC_LIFETIME`          | Connection lifetime (e.g., `1h`, `3h`, `24h`)                                         |
| `IPSEC_IKELIFETIME`       | IKE SA lifetime before rekey (default: 3h)                                            |
| `IPSEC_FORCEUDP`          | Force UDP encapsulation even without NAT (`yes` / `no`)                               |
| `IPSEC_IKEREAUTH`         | Reauthenticate on IKE_SA rekey (`yes` / `no`)                                         |
| `IPSEC_INTERFACES`        | Interfaces to bind Charon to (comma-separated)                                        |
| `DEBUG`                   | Enable debug output (`yes`)                                                           |

> 🔒 **Note:**
> To use keys and certificates instead of PSK, repository code must be extended to include certificate-based authentication.

---

## 🧩 Manual Configuration

You can bypass environment variables and mount a **custom StrongSwan configuration**.

Enable manual mode:

```bash
IPSEC_USE_MANUAL_CONFIG=TRUE
```

### Required Files

| File            | Destination                                             |
| --------------- | ------------------------------------------------------- |
| `ipsec.conf`    | `/etc/ipsec.config.d/ipsec.<connection-name>.conf`      |
| `ipsec.secrets` | `/etc/ipsec.secrets.de/ipsec.<connection-name>.secrets` |

### Optional Overrides

You may also provide:

```bash
/etc/ipsec.conf
/etc/ipsec.secrets
```

By default, **Charon** generates configurations dynamically.

To provide a custom Charon configuration:

```bash
IPSEC_MANUAL_CHARON=True
```

---

## 🧠 Enabling or Disabling Additional Modules

StrongSwan’s **Charon daemon** supports multiple plugins.
You can enable or disable modules by mounting configuration files under `/etc/strongswan.d/charon/`.

### Example: Disable FARP Module

Default `farp.conf`:

```bash
farp {
    load = no
}
```

To customize, mount your own configuration at:

```bash
/etc/strongswan.d/charon/farp.conf
```

---

## 🔁 Virtual Tunnel Interface (VTI) Support

The container supports **Virtual Tunnel Interfaces (VTI)** for routing IPsec traffic.

### Related Environment Variables

| Variable                 | Description                   |
| ------------------------ | ----------------------------- |
| `IPSEC_LOCALIP`          | Local IP bound to tunnel      |
| `IPSEC_VTI_KEY`          | Tunnel key (integer)          |
| `IPSEC_VTI_STATICROUTES` | Comma-separated static routes |
| `IPSEC_VTI_IPADDR_LOCAL` | Local VTI address             |
| `IPSEC_VTI_IPADDR_PEER`  | Peer VTI address              |

### Usage Example

Run the container with VTI initialization:

```bash
docker run --privileged \
    -v /lib/modules:/lib/modules \
    -e IPSEC_VTI_KEY=10 \
    -e IPSEC_VTI_IPADDR_LOCAL=192.168.100.1/30 \
    -e IPSEC_VTI_IPADDR_PEER=192.168.100.2/30 \
    openvnf/vnf-ipsec
```

Add static routes (optional):

```bash
IPSEC_VTI_STATICROUTES=192.168.1.0/24,10.10.0.0/16
```

---

## 🧪 Development Notes

### 🔐 Freezing Versions

To upgrade or lock package versions:

```bash
apk update && apk upgrade
freeze_apk_versions
```

Copy the generated `/root/MANIFEST` file to the repository and commit it.

---

## 📜 License

This project is licensed for **academic and research purposes only**.
Unauthorized commercial usage is strictly prohibited.

© **2025 Manthan S & Likhith U**
*Department of Computer Science and Engineering*
*The Oxford College of Engineering, VTU, Bangalore, India*

All rights reserved.

```

---

Would you like me to also include a **sample `environment.sh` file** (ready to run two peers like “Site-A” and “Site-B” for your demo)? That’ll let you launch your IPsec tunnel in seconds.
```
