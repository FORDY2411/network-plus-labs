# Auditoría de Capa Física (Capa 1) y Cableado de Cobre — CompTIA Network+ (N10-009)

> **Objetivo:** Comprender la arquitectura física del cableado de cobre (par trenzado, coaxial y twinaxial), los estándares IEEE 802.3 y auditar el estado de negociación y errores de enlace mediante consola.

---

## 1. Fundamentos Técnicos de Capa 1

* **Operación de Par Balanceado (Twisted Pair):** Cada par transmite la misma señal en polaridades opuestas (`Transmit+` / `Transmit-` y `Receive+` / `Receive-`). Al estar trenzados, ambos hilos reciben la misma interferencia electromagnética (EMI) externa, permitiendo que el receptor cancele el ruido común y reconstruya la señal original.
* **Tasas de trenzado diferenciadas:** Cada par dentro del cable tiene un número distinto de vueltas por pulgada para minimizar la diafonía (*crosstalk*) entre pares adyacentes.
* **Estándares IEEE 802.3 vs. Categorías:** El cobre no tiene velocidad intrínseca; la tasa de transferencia la determina el estándar Ethernet:
  * **100BASE-TX (100 Mbps):** Utiliza **2 pares** (4 hilos: pines 1, 2, 3 y 6).
  * **1000BASE-T (1 Gbps):** Requiere los **4 pares completos** (8 hilos: pines 1 al 8) sobre un mínimo de **Cat 5**.
* **Medios complementarios:**
  * **Coaxial (RG6):** Conductor central único con blindaje compartiendo el mismo eje; habitual en acometidas de cable módem.
  * **Twinaxial (Twinax / SFP+):** Dos conductores internos para enlaces *full-duplex* a **10 Gbps** con baja latencia en distancias cortas (hasta ~5 metros dentro de racks).
* **Seguridad física y normativa Plenum:** En falsos techos que funcionan como cámaras compartidas de retorno de aire (HVAC), la normativa exige cables con cubierta **Plenum** (FEP o PVC de baja emisión de humo) en lugar de PVC estándar para evitar la propagación de gases tóxicos en caso de incendio.

---

## 2. Perspectiva de Seguridad (Ofensiva y Defensiva)

| Vector / Riesgo | Impacto Técnico | Contramedida / Hardening |
| :--- | :--- | :--- |
| **Sniffing pasivo (*Wiretapping*)** | Captura de tramas en Capa 1 sin generar errores CRC ni alertas en el SO. | Cifrado en Capa 2 (**MACsec / IEEE 802.1AE**) o capas superiores (IPsec / TLS). |
| **Implantes físicos en *Plenum*** | Conexión de *drop boxes* no autorizados en cableado oculto sobre falsos techos. | Autenticación de puerto (**IEEE 802.1X**), *Port Security* y auditoría física. |
| **Interferencia Electromagnética (EMI)** | Corrupción de bits y disparo de errores **CRC**, degradando la disponibilidad. | Separación de líneas eléctricas, canalización y respeto de distancia máxima (100 m). |

---

## 3. Laboratorio Práctico: Inspección de Enlace y Estadísticas de Hardware

### Entorno y Comandos Ejecutados (Windows PowerShell)

```powershell
# 1. Verificación de estado y velocidad física negociada
Get-NetAdapter | Format-Table Name, Status, LinkSpeed -AutoSize

# 2. Auditoría de descartes y errores de trama (sin truncamiento de columnas)
Get-NetAdapterStatistics -Name "Wi-Fi" | Select-Object Name, ReceivedDiscardedPackets, ReceivedPacketErrors, OutboundDiscardedPackets, OutboundPacketErrors | Format-List
