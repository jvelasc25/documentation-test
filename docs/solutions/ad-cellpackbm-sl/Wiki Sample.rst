.. Wiki Page Migration Trial documentation master file, created by
   sphinx-quickstart on Tue Oct 15 19:54:57 2024.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

.. toctree::index.rst
   :maxdepth: 2
   :caption:
   


AD5570R no-OS Driver User Guide
===============================

Overview
------------

The **AD5770R no-OS Driver** interfaces with the AD5770R multi-channel digital-to-analog converter (DAC). It handles SPI communication and device initialization, enabling channel configuration, output mode adjustment, and reference voltage management. Source code supports single and multiple byte operations for register communication. Dependencies: SPI protocol and GPIO for control signals. Primary API functions in `ad5770r.h` and `ad5770r.c` cover register read/write, configuration, and error management. The driver includes diagnostics for device health monitoring.


Key Features of AD5770R
^^^^^^^^^^^^^^^^^^^^^^^

- **Multi-Channel Capabilities:** Six channels; Channel 0 supports up to 300 mA sourcing and sinking.
- **14-Bit Resolution** for precise current control.
- **On-Chip Reference:** 1.25 V reference for accuracy and stability.
- **Reference Options:** Internal or external selections.
- **Operating Voltage:** 2.9 V to 5.5 V.
- **Thermal Features:** Includes temperature monitoring and shutdown.
- **Performance:** Low noise and glitch operations ensure signal integrity.

Applications
^^^^^^^^^^^^^^^^^^^^^^^

- **Photonics Control:** Precise current modulation for laser currents and photonic sensors.
- **LED Driving:** Programmable current source for LED consistency and longevity.
- **Current Mode Biasing:** Supports bias currents for transistor arrays or RF amplifiers.

Supported DAC Model
^^^^^^^^^^^^^^^^^^^^^^^

**AD5770R:** 6-channel, 14-bit current output DAC suitable for photonics control, LED driving, and current mode biasing.

Configuration Requirements
^^^^^^^^^^^^^^^^^^^^^^^^^^^

- **Power Supply:** 2.9 V to 5.5 V AVDD supply; manage for thermal safety.
- **SPI Interface:** 4-wire connection; specific register configuration needed.
- **Reference Voltage:** Internal 1.25 V or configure for external reference.

Known Limitations
^^^^^^^^^^^^^^^^^^^^^^^

- **Temperature:** Maximum junction temperature: 150°C; requires thermal management.
- **Channel Limits:** Channel 0 can sink 60 mA, source 300 mA; other channels vary.
- **Output Compliance:** Check voltage compliance when combining channels.


Driver Initialization
------------------------------

To initialize the AD5770R driver, configure the SPI communication, set up channels, and select the reference voltage as outlined in "ad5770r.c".

The first API function to be called for interfacing with the AD5770R DAC is ``ad5770r_init``.

This function initializes the device, configures SPI communication, sets up optional GPIOs for alarms and resets, verifies device presence by reading the product ID, and configures device settings based on provided initialization parameters.


**SPI Configuration**

SPI Initialization: Initialize the SPI descriptor with no_os_spi_init using parameters from spi_init in ``ad5770r_init_param``

Device SPI Settings: Use ``ad5770r_set_device_spi`` to configure address ascension, single instruction, and stream mode.


**Channel Setup**

Channel Configuration: Enable channels using ``ad5770r_channel_config``

Output Mode and Filter: Set each channel's output mode and filters using ``ad5770r_set_output_mode`` and ``ad5770r_set_output_filter``

Output Mode and Filter: Set each channel's output mode and filters using ``ad5770r_set_output_mode`` and ``ad5770r_set_output_filter``



**Reference Voltage Selection**

Set the reference voltage with ``ad5770r_set_reference``


**Additional Configuration**

Alarm Setup: Configure alarms using ``ad5770r_set_alarm``

LDAC and Monitors: Set hardware LDAC with ``ad5770r_set_hw_ldac`` and monitor setup via ``ad5770r_set_monitor_setup``


AD5770R Driver Function Descriptions
-------------------------------------

**SPI Register Read Functions**

``ad5770r_spi_reg_read``: Reads a hardware register via SPI by sending the register address with a read marker, then captures the data. Essential for monitoring the DAC's status.

``ad5770r_spi_reg_read_multiple``: Reads multiple contiguous registers in one operation, improving efficiency for bulk data retrieval, useful for diagnostics and status updates.


**SPI Register Write Functions**

``ad5770r_spi_reg_write``: Directly writes data to a specified DAC register, allowing configuration settings like output modes or reference voltages.

``ad5770r_spi_reg_write_multiple``: Writes to multiple consecutive registers, enhancing performance for configuration or calibration.


**Device Configuration Functions**

``ad5770r_set_device_spi``: Adjusts SPI settings specific to the device, optimizing data transactions, including streaming mode and address ascension.

``ad5770r_channel_config``: Configures DAC channel settings such as enabling/disabling outputs, optimizing multi-output channel use.


**Error Handling and Diagnostics**

``ad5770r_get_status``: Retrieves current status to indicate any fault conditions like over-temperature for preventive measures.

``ad5770r_set_alarm``: Sets alarm settings to trigger on fault conditions, enabling immediate diagnostic actions based on warnings such as temperature thresholds.


These driver functions manage SPI communication, device initialization, configuration settings, and error management for the AD5770R DAC, ensuring precise operation and real-time monitoring.


Types Declarations
------------------

**SPI Transaction Types**
::

     struct ad5770r_device_spi_settings {
       bool addr_ascension;
       bool single_instruction;
       uint8_t stream_mode_length;
    };


Configures SPI transaction types, enabling address ascending, single-instruction for multibyte transfers, and defining stream mode length for communication efficiency.


**Channel Configuration Types**
::
   
    struct ad5770r_channel_switches {
       bool en0, en1, en2, en3, en4, en5, sink0;
    };

    struct ad5770r_output_range {
       uint8_t output_scale;
       uint8_t output_range_mode;
    };


``ad5770r_channel_switches`` enables individual channels and channel 0's sink current. ``ad5770r_output_range`` specifies the output scaling and range modes.

**Error or Status Code Enumerations**
::

    struct ad5770r_alarm_cfg {
       bool open_drain_en;
       bool thermal_shutdown_en;
       bool background_crc_en;
       bool temp_warning_msk;
       bool over_temp_msk;
       bool neg_ch0_msk;
       bool iref_fault_msk;
       bool background_crc_msk;
    };

    enum ad5770r_status {
       AD5770R_STATUS_BACKGROUND_CRC_STATUS_MSK = NO_OS_BIT(7),
       AD5770R_STATUS_IREF_FAULT_MSK = NO_OS_BIT(3),
       AD5770R_STATUS_NEGATIVE_CHANNEL0_MSK = NO_OS_BIT(2),
       AD5770R_STATUS_OVER_TEMP_MSK = NO_OS_BIT(1),
       AD5770R_STATUS_TEMP_WARNING_MSK = NO_OS_BIT(0)
    };


``ad5770r_alarm_cfg`` configures alarms for fault conditions. ad5770r_status enumerates status and error flags for real-time error monitoring.





About the no-OS Framework
--------------------------

Analog Devices’ no-OS framework enables embedded systems development without a traditional OS. It simplifies low-level operations for peripherals like ADCs and DACs, ideal for resource-constrained environments.


