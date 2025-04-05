# README - OpenBook E-Reader

## Introducere
OpenBook este un e-reader open-source construit în jurul microcontrollerului ESP32-C6, integrând un display e-paper de 7.5 inch, senzori de mediu (BME680), management al bateriei și interfețe precum Wi-Fi și USB-C. Acest document oferă o privire de ansamblu asupra designului hardware, componentelor utilizate și conexiunilor lor.

---

## Diagrama bloc


## Bill of Materials (BOM)
Tabelul de mai jos include componentele principale din BOM-ul furnizat, cu link-uri către Mouser și datasheet-uri unde sunt disponibile.

| **Componentă**         | **Valoare/Part Number**         | **Descriere**                              | **Furnizor (Mouser)**                                                                 | **Datasheet**                                                                                     |
|-------------------------|---------------------------------|--------------------------------------------|--------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|
| U2                     | ESP32-C6-WROOM-1-N8            | Microcontroller Wi-Fi 6, Bluetooth 5       | [Mouser](https://eu.mouser.com/ProductDetail/Espressif-Systems/ESP32-C6-WROOM-1-N8)  | [Datasheet](https://www.espressif.com/sites/default/files/documentation/esp32-c6-wroom-1_datasheet_en.pdf) |
| U1                     | W25Q512JVEIQ                   | 512Mb Flash SPI                            | [Mouser](https://eu.mouser.com/ProductDetail/Winbond/W25Q512JVEIQ)                   | [Datasheet](https://www.winbond.com/resource-files/W25Q512JV%20RevD%2004082020.pdf)              |
| S                      | BME680                         | Senzor temperatură, umiditate, presiune    | [Mouser](https://eu.mouser.com/ProductDetail/Bosch-Sensortec/BME680)                 | [Datasheet](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bme680-ds001.pdf) |
| U3                     | DS3231SN#                      | RTC I2C                                    | [Mouser](https://eu.mouser.com/ProductDetail/Analog-Devices/DS3231SN)                | [Datasheet](https://www.analog.com/media/en/technical-documentation/data-sheets/DS3231.pdf)       |
| U4                     | MAX17048G+T10                  | Fuel Gauge I2C                             | [Mouser](https://eu.mouser.com/ProductDetail/Analog-Devices/MAX17048G+T10)           | [Datasheet](https://www.analog.com/media/en/technical-documentation/data-sheets/MAX17048.pdf)     |
| U5                     | MCP73831T-2ACI/OT              | Încărcător Li-Po USB-C                     | [Mouser](https://eu.mouser.com/ProductDetail/Microchip-Technology/MCP73831T-2ACI-OT) | [Datasheet](https://ww1.microchip.com/downloads/en/DeviceDoc/20001984G.pdf)                     |
| J2                     | USB4110-GF-A                   | Conector USB-C                             | [Mouser](https://eu.mouser.com/ProductDetail/GCT/USB4110-GF-A)                       | [Datasheet](https://www.gct.co/connector/usb4110)                                               |
| BOOT_BUTTON            | EVQPUJ02K                      | Buton tactil                               | [Mouser](https://eu.mouser.com/ProductDetail/Panasonic/EVQ-PUJ02K)                   | [Datasheet](https://www.panasonic.com/global/industrial-components/switches/light-touch/evqpuj02k.pdf) |
| C3                     | 100uF TANT (F910J107MBAAJ6)    | Condensator polarizat                      | [Mouser](https://eu.mouser.com/ProductDetail/KYOCERA-AVX/F910J107MBAAJ6)             | [Datasheet](https://www.kyocera-avx.com/products/tantalum/)                                     |
| IC4                    | XC6220A331MR-G                 | LDO Regulator                              | [Mouser](https://eu.mouser.com/ProductDetail/Torex/XC6220A331MR-G)                   | [Datasheet](https://www.torexsemi.com/file/xc6220/XC6220.pdf)                                   |

---

## Funcționalitate Hardware

### Microcontroller ESP32-C6-WROOM-1-N8
- **Rol**: Procesare centrală, conectivitate Wi-Fi 6 și Bluetooth 5, control al perifericelor.
- **Interfețe**:
  - SPI: Display e-paper, Flash (W25Q512JVEIQ), Micro SD.
  - I2C: BME680, MAX17048, DS3231.
  - GPIO: Butoane, LED, pini de control display.
  - UART: Debugging și extensii.
- **Specificații**: 160MHz, 512KB SRAM, 8MB Flash integrat.

### Display E-Paper
- **Specificații**: 7.5 inch, 800x480, contrast 8:1, SPI 4-wire.
- **Conexiune**: MOSI, MISO, SCK, CS + DC, RST, BUSY.
- **Consum**: <50mA în refresh, aproape 0 în standby.

### Senzor BME680
- **Funcție**: Măsoară temperatura (±1°C), umiditatea (±3%), presiunea (±1 hPa) și calitatea aerului (eCO2, TVOC).
- **Interfață**: I2C la 400kHz.
- **Consum**: <1mA activ, <1µA în sleep.

### Management Baterie
- **Baterie**: 2500mAh Li-Po, 3.7V.
- **Încărcare**: MCP73831 prin USB-C (1A max).
- **Monitorizare**: MAX17048 prin I2C.
- **Consum**: <50µA în deep sleep.

### Butoane
- Trei butoane tactile (Panasonic EVQPUJ02K) pentru navigare, cu rezistențe pull-up de 10K.

### USB-C
- **Rol**: Încărcare (5V/1A) și transfer date (USB 2.0).
- **Protecție**: ESD diodes (USBLC6-2SC6Y).

### Calcul Consum Energie
- **Activ (Wi-Fi + Display Refresh)**: ~150mA.
- **Citire (Display static)**: ~10mA.
- **Deep Sleep**: <50µA.
- **Autonomie estimată**: ~1 săptămână (2500mAh / 10mA medie = ~250 ore).

---

## Pini ESP32-C6 Utilizați

| **Pin ESP32-C6** | **Funcție**         | **Componentă**         | **Motiv**                                      |
|-------------------|---------------------|------------------------|------------------------------------------------|
| GPIO0            | BOOT_BUTTON         | Buton                  | Detectare mod boot la pornire                  |
| GPIO1            | RESET_BUTTON        | Buton                  | Reset manual al dispozitivului                 |
| GPIO2            | CHANGE_BUTTON       | Buton                  | Navigare meniuri                               |
| GPIO3            | EPD_CS              | Display E-Paper        | Chip Select pentru SPI                         |
| GPIO4            | EPD_DC              | Display E-Paper        | Data/Command control                           |
| GPIO5            | EPD_RST             | Display E-Paper        | Reset display                                  |
| GPIO6            | EPD_BUSY            | Display E-Paper        | Detectare stare refresh                        |
| GPIO7            | SPI_MOSI            | Display, Flash, SD     | Master Out Slave In (SPI)                      |
| GPIO8            | SPI_MISO            | Display, Flash, SD     | Master In Slave Out (SPI)                      |
| GPIO9            | SPI_SCK             | Display, Flash, SD     | Clock SPI                                      |
| GPIO10           | FLASH_CS            | W25Q512JVEIQ           | Chip Select pentru Flash                       |
| GPIO11           | SD_CS               | Micro SD               | Chip Select pentru SD                          |
| GPIO12           | I2C_SDA             | BME680, MAX17048, DS3231 | Date I2C                                     |
| GPIO13           | I2C_SCL             | BME680, MAX17048, DS3231 | Clock I2C                                    |
| GPIO14           | CHG_LED             | LED                    | Indicare status încărcare                      |
| GPIO15           | UART_TX             | Debugging              | Transmitere date serial                        |
| GPIO16           | UART_RX             | Debugging              | Recepție date serial                           |
| GPIO17           | USB_D+              | USB-C                  | Linie date USB pozitivă                        |
| GPIO18           | USB_D-              | USB-C                  | Linie date USB negativă                        |

**Notă**: Pinii sunt alocați conform specificațiilor ESP32-C6 și cerințelor ERD/PRD. Unii pini pot fi multifuncționali (ex. GPIO/SPI).

---