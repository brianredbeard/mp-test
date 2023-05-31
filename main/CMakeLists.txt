set(IDF_COMPONENTS
    app_update
    bootloader_support
    bt
    driver
    esp_common
    esp_eth
    esp_event
    esp_ringbuf
    esp_rom
    esp_wifi
    freertos
    heap
    log
    lwip
    mbedtls
    mdns
    micropython
    newlib
    nvs_flash
    sdmmc
    soc
    spi_flash
    tcpip_adapter
    ulp
    vfs
    xtensa
    fatfs
    wear_levelling
    spiffs
    driver_sdcard
    buses
    wpa_supplicant
)

idf_component_register(
                    REQUIRES micropython
                    INCLUDE_DIRS ".")

