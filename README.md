# 📡 CDP DoS Laboratory

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.8+](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Platform: Linux](https://img.shields.io/badge/Platform-Linux-green.svg)](https://www.linux.org/)

## 📋 Descripción

Laboratorio educativo para analizar el funcionamiento del **Protocolo de Descubrimiento de Cisco (CDP)** y evaluar el impacto de un alto volumen de mensajes CDP sobre dispositivos Cisco en un entorno controlado.

Este proyecto genera tramas CDP utilizando **Scapy** y las envía continuamente hacia la dirección multicast utilizada por CDP, simulando múltiples dispositivos de red para estudiar cómo responde la infraestructura ante un volumen elevado de anuncios.

---

## 👤 Autor

| Campo | Valor |
|-------|-------|
| **Nombre** | Luis Hanel Nuñez Perez |
| **Matrícula** | 2024-0784 |
| **Materia** | Seguridad de Redes |
| **Instructor** | Jonathan Rondón |

---

## 🎯 Objetivos

### Objetivo General
Analizar el funcionamiento del protocolo CDP y evaluar el impacto de un alto volumen de mensajes CDP sobre dispositivos Cisco en entornos controlados.

### Objetivos Específicos

- ✅ Comprender el funcionamiento del protocolo CDP
- ✅ Analizar el comportamiento de routers y switches Cisco ante múltiples anuncios CDP
- ✅ Observar el impacto sobre los recursos del dispositivo
- ✅ Identificar medidas de mitigación aplicables en entornos reales

---

## 📌 Objetivo del Script

El script genera tramas CDP utilizando Scapy y las envía de forma continua hacia la dirección multicast (`01:00:0c:cc:cc:cc`) utilizada por Cisco Discovery Protocol.

Durante la ejecución se crean identificadores aleatorios que simulan múltiples dispositivos de red, permitiendo estudiar cómo responde la infraestructura de red ante un volumen elevado de anuncios CDP.

---

## 🛠️ Requisitos

### Hardware

- Router Cisco (cualquier modelo moderno)
- Switch Cisco (cualquier modelo moderno)
- Máquina Kali Linux o similar

### Software

| Software | Versión | Propósito |
|----------|---------|-----------|
| Kali Linux | 2023.x+ | Sistema operativo base |
| Python | 3.8+ | Lenguaje de scripting |
| Scapy | 2.4.4+ | Generación de paquetes |
| GNS3 o EVE-NG | Última | Simulación de red (opcional) |

---

## 📦 Instalación

### 1. Clonar el repositorio

```bash
git clone https://github.com/luis34crak-bot/CDP_DOS_ATTACK.git
cd CDP_DOS_ATTACK
```

### 2. Instalar dependencias

```bash
# Actualizar pip
pip install --upgrade pip

# Instalar Scapy
pip install scapy
```

### 3. Verificar instalación

```bash
python3 -c "import scapy; print(scapy.__version__)"
```

---

## 🚀 Uso

### Ejecución básica

```bash
sudo python3 cdp_dos.py
```

> **⚠️ Nota:** Se requieren permisos de administrador para enviar paquetes raw.

### Con interfaz específica

```bash
sudo python3 cdp_dos.py -i eth0
```

### Con parámetros personalizados

```bash
sudo python3 cdp_dos.py -i eth0 -d 1000  # 1000ms de delay
```

---

## 📊 Parámetros Utilizados

| Parámetro | Descripción | Valor por defecto |
|-----------|-------------|-------------------|
| `CDP_MULTICAST` | Dirección multicast utilizada por CDP | `01:00:0c:cc:cc:cc` |
| `INTERFACE` | Interfaz utilizada para transmitir paquetes | `eth0` |
| `Device ID` | Identificador generado aleatoriamente | Aleatorio |
| `TTL` | Tiempo de vida de los anuncios CDP | 180 segundos |
| `DELAY` | Intervalo entre transmisiones | 1000ms |

---

## 🔧 Funcionamiento del Script

El script ejecuta el siguiente algoritmo:

```
1. Genera un Device-ID aleatorio (para simular múltiples dispositivos)
2. Construye los campos TLV (Type-Length-Value) del protocolo CDP
3. Calcula el checksum correspondiente (HDLC checksum)
4. Encapsula la información utilizando:
   - Capa 2: Ethernet
   - Control lógico de enlace (LLC)
   - Sub-Network Access Protocol (SNAP)
5. Envía los anuncios CDP hacia la dirección multicast 01:00:0c:cc:cc:cc
6. Repite el proceso continuamente con intervalos configurables
```

---

## 🌐 Documentación de la Red

### Topología

```
Kali Linux ─────► Switch Cisco ─────► Router Cisco
   (7.84.0.3)        (VLAN 1)          (7.84.0.1)
```

### Esquema de Direccionamiento IP

| Dispositivo | Interfaz | Dirección IP | Descripción |
|-------------|----------|--------------|-------------|
| Router R1 | e0/0 | 7.84.0.1 | Gateway principal |
| Kali Linux | eth1 | 7.84.0.3 | Máquina atacante |
| Windows | eth1 | 7.84.0.5 | Máquina adicional |

### Configuración VLAN

| VLAN ID | Descripción |
|---------|-------------|
| 1 | Red de laboratorio (VLAN por defecto) |

---

## 📸 Evidencias

### Topología
*Agregar captura de pantalla de la topología utilizada*

### Ejecución del Script
*Agregar captura de pantalla de la ejecución del script*

### Verificación en Cisco

**Comando utilizado:**
```cisco
show cdp neighbors
show cdp neighbors detail
show cdp entry *
```

**Resultado esperado:**
- ✅ Aparición de múltiples vecinos CDP simulados
- ✅ Actualización continua de la tabla CDP
- ✅ Aumento de mensajes CDP en el sniffer
- ✅ Posible impacto en recursos del dispositivo

**Sniffer Wireshark:**
- Filtro: `cdp`
- Observar aumento exponencial de tramas CDP

---

## 🛡️ Contramedidas y Mitigación

### Opción 1: Deshabilitar CDP en una interfaz

```cisco
interface GigabitEthernet0/0
  no cdp enable
```

### Opción 2: Deshabilitar CDP Globalmente

```cisco
configure terminal
no cdp run
exit
```

### Opción 3: Limitar paquetes de control

```cisco
configure terminal
access-list 100 deny udp any any eq 639
access-list 100 permit ip any any
interface GigabitEthernet0/0
  ip access-group 100 in
exit
```

### Otras Medidas Recomendadas

- 🔒 **Segmentación de red** - Implementar VLANs y aislamiento de control plane
- 📊 **Monitoreo proactivo** - Vigilar el plano de control con SPAN/RSPAN
- 🔐 **Control de acceso L2** - Implementar Port Security y DHCP Snooping
- 🔄 **Actualizaciones** - Mantener firmware de equipos Cisco actualizado
- ⏱️ **Rate limiting** - Configurar límites de tasa en protocolos de descubrimiento
- 📝 **Auditoría** - Registrar y analizar mensajes CDP sospechosos

---

## 📚 Conceptos Clave

### ¿Qué es CDP?

Cisco Discovery Protocol (CDP) es un protocolo propietario de capa 2 que permite a dispositivos Cisco descubrirse automáticamente. Se usa para:

- Descubrimiento de dispositivos vecinos
- Obtener información del sistema
- Intercambiar capacidades de dispositivos

### Vulnerabilidad Explotada

CDP no implementa validación de origen ni autenticación, lo que permite:

- **Suplantación de dispositivos** - Simular falsos vecinos
- **Ataques DoS** - Inundar el control plane con anuncios
- **Información de reconocimiento** - Recopilar datos de la red

---

## ⚠️ Advertencia Legal

⚠️ **Este proyecto es exclusivamente para fines educativos en entornos de laboratorio controlados.**

- Solo se debe utilizar en redes propias o con autorización explícita
- No usar en redes de terceros sin permiso
- El uso no autorizado puede constituir un delito

---

## 📖 Referencias

- [Cisco Discovery Protocol (CDP)](https://www.cisco.com/c/en/us/support/docs/ios-nx-os-software/ios-software/46813-145.html)
- [Scapy Documentation](https://scapy.readthedocs.io/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [RFC 1819 - Internet User Glossary](https://tools.ietf.org/html/rfc1819)

---

## 📝 Licencia

Este proyecto está bajo licencia **MIT**. Ver el archivo [LICENSE](LICENSE) para más detalles.

---

## 🤝 Contribuciones

Las contribuciones son bienvenidas. Por favor:

1. Fork el proyecto
2. Crea una rama para tu feature (`git checkout -b feature/AmazingFeature`)
3. Commit tus cambios (`git commit -m 'Add some AmazingFeature'`)
4. Push a la rama (`git push origin feature/AmazingFeature`)
5. Abre un Pull Request

---

## ❓ Preguntas Frecuentes

**P: ¿Necesito equipos Cisco reales?**  
R: No, puedes usar GNS3 o EVE-NG para simular la topología.

**P: ¿Qué tan rápido debo enviar paquetes?**  
R: Depende de tus objetivos. Para laboratorio, 1000ms es adecuado.

**P: ¿Afecta a otras VLANs?**  
R: CDP usa dirección multicast que puede alcanzar todas las VLANs de capa 2.

---

## 📧 Contacto

- **Autor:** Luis Hanel Nuñez Perez
- **Matrícula:** 2024-0784
- **GitHub:** [@luis34crak-bot](https://github.com/luis34crak-bot)

---

<div align="center">

**[⬆ Volver al inicio](#-cdp-dos-laboratory)**

© 2024 CDP DoS Laboratory. Todos los derechos reservados.

</div>
