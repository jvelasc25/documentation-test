GMSL HDL Reference Design
=========================

A brief introduction of the acronyms that are going to be used:

- MIPI- Mobile Industry Processor Interface
- MIPI CSI-2 - MIPI’s camera serial interface specification
- GMSL - Gigabit Multimedia Serial Link
- VC - Virtual channel

Introduction
------------

Devices Used
~~~~~~~~~~~~~~

- :adi:`MAX96724` GMSL2/1 to CSI-2 Deserializer
- :adi:`AD-GMSLCAMRPI-ADP#` GMSL EV Kit Adapter Board

Supported FPGA Carrier
~~~~~~~~~~~~~~~~~~~~~~

- `AMD/XILINX KV260 <https://www.xilinx.com/products/som/kria/kv260-vision-starter-kit.html>`__ Vision AI Development Platform

Reference HDL Design
--------------------

The design is built upon ADI’s generic HDL reference design framework. Thus,
this reference design is used to interface the GMSL technology by using the MIPI
CSI-2 `specification <https://www.mipi.org/specifications/csi-2>`__ for
high-speed data transmission, and I2C standard for control. An in-depth
presentation and instructions about the HDL design framework in general, can be
found in the :dokuwiki:`ADI Reference Designs HDL User Guide <resources/fpga/docs/hdl>`.

Block Design
~~~~~~~~~~~~~

.. figure:: gmsl_hdl_block_design1.png

   GMSL HDL Architecture

The architecture of this reference design is composed of multiple Xilinx’s
multimedia IPs that have the following roles:

- AMD-Xilinx’s MIPI CSI-2 Rx Subsystem IP (receive video data from the GMSL
  deserializer’s PHY using the 2-lane 15-pin connector);
- AMD-Xilinx’s AXIS Switch IP (handle MIPI’s virtual-channel option by
  redirecting each virtual channel to corresponding Video Framebuffer Write
  instance);
- AMD-Xilinx’s Video Framebuffer Write IP (video-specific DMA engine);

Using the HDL Reference Design
------------------------------

In order to build the HDL design, the user has to go through the following steps:

#. Confirm that you have the right tools (see `Release notes <https://github.com/analogdevicesinc/hdl/releases>`__)
#. Clone the HDL GitHub repository (see https://wiki.analog.com/resources/fpga/docs/git)
#. Choose the required interface (see caption **Switching between interface types**)
#. Build the project (see https://wiki.analog.com/resources/fpga/docs/build)

As regards the design’s IPs, this GMSL-based reference design contains multiple
AMD-Xilinx-related multimedia IPs such as MIPI CSI-2 Rx Subsystem, Axi-Stream
Switch, Axi-Stream Subset Converter and Video Framebuffer Write, all these ones
being available free of charge using standard AMD-Xilinx’s Vivado version.

AXI4-LITE Control Interfaces
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

====================== ===========
Instance               Address
====================== ===========
mipi_csi2_rx_subsyst_0 0x84A0 0000
axi_iic_mipi           0x84A2 0000
v_frmbuf_wr_0          0x84A4 0000
v_frmbuf_wr_1          0x84A6 0000
v_frmbuf_wr_2          0x84A8 0000
v_frmbuf_wr_3          0x84AA 0000
====================== ===========

PL Interrupts
~~~~~~~~~~~~~

====================================== ============= ===================
Instance                               HDL interrupt Linux PsU interrupt
====================================== ============= ===================
—                                      0             89
—                                      1             90
—                                      2             91
—                                      3             92
—                                      4             93
—                                      5             94
—                                      6             95
—                                      7             96
v_frmbuf_wr_3/interrupt                8             104
v_frmbuf_wr_2/interrupt                9             105
v_frmbuf_wr_1/interrupt                10            106
v_frmbuf_wr_0/interrupt                11            107
axi_iic_mipi/iic2intc_irpt             12            108
mipi_csi2_rx_subsyst_0/csirxss_csi_irq 13            109
—                                      14            110
—                                      15            111
====================================== ============= ===================

GPIO Signals
~~~~~~~~~~~~

Ps8 EMIO offset = 78

================ ==== ==============
GPIO Signal      GPIO HDL GPIO EMIOn
================ ==== ==============
fan_en_b         78   0
csirxss_rstn     79   1
ap_rstn_frmbuf_0 80   2
ap_rstn_frmbuf_1 90   3
ap_rstn_frmbuf_2 91   4
ap_rstn_frmbuf_3 92   5
================ ==== ==============

HDL Project
-----------

- `MAX96724 HDL Project <https://github.com/analogdevicesinc/hdl/tree/main/projects/max96724/kv260>`_

Project's User Guide
---------------------
- :dokuwiki:`GMSL Kit with AMD Kria User Guide <resources/eval/user-guides/ad-gmslcamrpi-adp/ug_amd_kria>`

Support
-------

For questions and more information, please contact us on the **Analog Devices
Engineer Zone**.

- :ez:`EngineerZone Linux Software Drivers <community/linux-software-drivers>`
- :ez:`EngineerZone FPGA Reference Designs <community/fpga>`


---------------------------------------------------------------------------------------------------------------------------------------------
