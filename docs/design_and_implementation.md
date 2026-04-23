[Click here](../README.md) to view the README.

## Design and implementation

The design of this application is minimalistic to get started with code examples on PSOC&trade; Edge MCU devices. All PSOC&trade; Edge E84 MCU applications have a dual-CPU three-project structure to develop code for the CM33 and CM55 cores. The CM33 core has two separate projects for the secure processing environment (SPE) and non-secure processing environment (NSPE). A project folder consists of various subfolders, each denoting a specific aspect of the project. The three project folders are as follows:

**Table 1. Application projects**

Project | Description
--------|------------------------
*proj_cm33_s* | Project for CM33 secure processing environment (SPE)
*proj_cm33_ns* | Project for CM33 non-secure processing environment (NSPE)
*proj_cm55* | CM55 project

<br>

In this code example, at device reset, the secure boot process starts from the ROM boot with the secure enclave (SE) as the root of trust (RoT). From the secure enclave, the boot flow is passed on to the system CPU subsystem, where the secure CM33 application starts. After all necessary secure configurations, the flow is passed on to the non-secure CM33 application. Resource initialization for this example is performed by this CM33 non-secure project. It configures the system clocks, pins, clock to peripheral connections, and other platform resources. It then enables the CM55 core using the `Cy_SysEnableCM55()` function and the CM55 core is subsequently put into DeepSleep mode.

In the CM33 non-secure application, the clocks and system resources are initialized by the BSP initialization function. The retarget-io middleware is configured to use the debug UART. The debug UART prints a message (as shown in [Terminal output on program startup](../images/terminal-hello-world.png)) on the terminal emulator, the onboard KitProg3 acts the USB-UART bridge to create the virtual COM port. The User LED1 blinks every 1 second. 

The *python-tcp-secure-client* provided in the project root folder contains the keys and certificates for the client and a python implementation of a simple secure TCP server.

In this example, the TCP client establishes a secure connection with the TCP server through an SSL handshake. During the SSL handshake, the client presents its SSL certificate (self-signed) for verification and also verifies the server's identity to which it is connecting. See the [Creating a self-signed certificate](../README.md/#creating-a-self-signed-ssl-certificate) section for more details. Once the SSL handshake completes successfully, the TCP client controls the user LED to turn ON or OFF based on the command received from the TCP server.

Once the SSL handshake completes successfully, the client accepts the supported commands from the TCP server and sets LED ON/OFF accordingly; the client responds by sending an acknowledgement message to the server.

### Switching from MII to RMII interface

To switch from  MII to RMII interface, open the design.modus file and do the following configuration settings in the ModusToolbox™ Device Configurator.

- Switch to the "Peripherals" tab
- Select Communication->"Ethernet 0"
- In "Ethernet - Parameters" pane, go to "Network Interface Settings" section and configure PHY parameters
- Configure "PHY Interconnect" to "MII" from the drop-down
- In "Ethernet - Parameters" pane, go to "Connections" section and configure the pins as mentioned below
- Select pin "ref_clk" from the drop-down
  - Click on "Go to resource" (chain icon) of selected pin
  - Configure "Drive mode" to "Digital HIGH-Z, Input buffer on" from the drop-down
  - Configure "Initial Drive state" to "Low (0)" from the drop-down
- Unassign/deselect these pins - "txd[2]", "txd[3]", "rxd[2]", "rxd[3]", "tx_clk", "tx_er", "rx_clk", and "rx_er"

>**Note** Revert the pin drive mode settings for these pins to match the default BSP settings if you intend to reuse these pins for other purposes.

This example requires the board to be reworked. See the "Rework for Ethernet subsystem" section of the kit user guide to configure the board correctly for RMII interface.
