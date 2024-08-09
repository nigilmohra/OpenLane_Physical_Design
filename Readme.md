# Physical Design using OpenLANE SKY130 
# 1. Installating OpenLane v1.0
To install the necessary tools, please refer to the link below. Follow the instructions provided and run the example given to verify that the OpenLANE tools are installed correctly. Ensure that all commands are executed as the `root` user.

| OpenROAD and some supporting tools. | [OpenLANE Ubuntu Installation](https://openlane.readthedocs.io/en/latest/getting_started/installation/installation_ubuntu.html) |
| ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |

During the initial `make smoke` test, some users might encounter the error: 

```
qt.qpa.plugin: Could not load the Qt platform plugin "xcb" in "" even though it was found
```

This error indicates a problem with loading the Qt platform plugin "xcb," which is necessary for opening the GUI.

This issue can be resolved by running the command `xhost +local:*` to allow local connections. After completing your work and before closing the terminal, remember to revert the setting by running `xhost -local:*`. This creates a workspace directory in your home directory if it doesn't already exist. This permits the root user on the local machine to connect to GUI apps.

# 2. Example
Before delving into the design flow. Installation of a text editor is required. The user can use any text editor. Here, `vim` is used. `Vim` can be installed by running the following command:

```sh
sudo apt-get install vim
```

## 2.1. Full Adder Design
All design files with the `.v` extension are stored in the `src` folder within a directory named after the design inside the `designs` folder of the OpenLANE directory. For instance, the one-bit full adder, implemented in Verilog, will have its files in this structure, and the GDSII file is generated using the OpenLANE ASIC flow.

*Note: There are several methods to run the design using OpenLANE.*

Create a folder named `fa` inside the `designs` folder using the following command:

```sh
sudo mkdir fa
```
To create the RTL file, either copy and paste the file directly into this folder or follow the alternative approach outlined below for a bit of extra fun!

Inside the `OpenLANE/designs/fa/src` folder, open the terminal and type the following command to create a file for the one-bit full adder:

```sh
cat > fa.v
```

After typing this command, enter the RTL code for the one-bit full adder. To finish and exit the terminal-based text editor, press `Ctrl + D`.

Next, open the RTL file in `Vim` with the following command:

```sh
vim fa.v
```

Add or modify the RTL design for the full adder as needed. To save the changes and exit `Vim`, use the command:

```sh
:wq
```

