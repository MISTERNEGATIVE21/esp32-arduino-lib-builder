if(CONFIG_TINYUSB_ENABLED)

  ### variables ###
  #################

  if(IDF_TARGET STREQUAL "esp32s2")
    set(compile_options
        "-DCFG_TUSB_MCU=OPT_MCU_ESP32S2"
        "-DCFG_TUSB_DEBUG=${CONFIG_TINYUSB_DEBUG_LEVEL}"
        "-Wno-type-limits" # needed for the vanila tinyusb with turned off classes
    )
  elseif(IDF_TARGET STREQUAL "esp32s3")
    set(compile_options
        "-DCFG_TUSB_MCU=OPT_MCU_ESP32S3"
        "-DCFG_TUSB_DEBUG=${CONFIG_TINYUSB_DEBUG_LEVEL}"
        "-Wno-type-limits" # needed for the vanila tinyusb with turned off classes
    )
  elseif(IDF_TARGET STREQUAL "esp32p4")
    set(compile_options
        "-DCFG_TUSB_MCU=OPT_MCU_ESP32P4"
        "-DCFG_TUSB_DEBUG=${CONFIG_TINYUSB_DEBUG_LEVEL}"
        "-Wno-type-limits" # needed for the vanila tinyusb with turned off classes
    )
  endif()

  set(srcs
      # espressif:
      "${COMPONENT_DIR}/src/dcd_dwc2.c"
      # tusb:
      #"${COMPONENT_DIR}/tinyusb/src/portable/synopsys/dwc2/dcd_dwc2.c"
      "${COMPONENT_DIR}/tinyusb/src/portable/synopsys/dwc2/dwc2_common.c"
      "${COMPONENT_DIR}/tinyusb/src/class/cdc/cdc_device.c"
      "${COMPONENT_DIR}/tinyusb/src/class/hid/hid_device.c"
      "${COMPONENT_DIR}/tinyusb/src/class/midi/midi_device.c"
      "${COMPONENT_DIR}/tinyusb/src/class/msc/msc_device.c"
      "${COMPONENT_DIR}/tinyusb/src/class/video/video_device.c"
      "${COMPONENT_DIR}/tinyusb/src/class/dfu/dfu_rt_device.c"
      "${COMPONENT_DIR}/tinyusb/src/class/dfu/dfu_device.c"
      "${COMPONENT_DIR}/tinyusb/src/class/vendor/vendor_device.c"
      "${COMPONENT_DIR}/tinyusb/src/class/net/ncm_device.c"
      "${COMPONENT_DIR}/tinyusb/src/common/tusb_fifo.c"
      "${COMPONENT_DIR}/tinyusb/src/device/usbd_control.c"
      "${COMPONENT_DIR}/tinyusb/src/device/usbd.c"
      "${COMPONENT_DIR}/tinyusb/src/tusb.c")

  set(includes_private
      # tusb:
      "${COMPONENT_DIR}/tinyusb/hw/bsp/"
      "${COMPONENT_DIR}/tinyusb/src/"
      "${COMPONENT_DIR}/tinyusb/src/device"
      "${COMPONENT_DIR}/tinyusb/src/portable/synopsys/dwc2"
      )

  idf_component_get_property(FREERTOS_ORIG_INCLUDE_PATH freertos
                             ORIG_INCLUDE_PATH)
  set(includes_public
      # tusb:
      "${FREERTOS_ORIG_INCLUDE_PATH}"
      "${COMPONENT_DIR}/tinyusb/src/"
      # espressif:
      "${COMPONENT_DIR}/include")

  set(requires esp_rom freertos soc)
  set(priv_requires arduino main)

  idf_component_register(
    INCLUDE_DIRS ${includes_public} 
    PRIV_INCLUDE_DIRS ${includes_private} 
    SRCS ${srcs} 
    REQUIRES ${requires} 
    PRIV_REQUIRES ${priv_requires}
    )
  target_compile_options(${COMPONENT_TARGET} PRIVATE ${compile_options})

else()

  idf_component_register()

endif()
