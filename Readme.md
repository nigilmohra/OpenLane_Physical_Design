# Physical Design using OpenLANE SKY130 (OpenLane v1.0)
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

## 2.2. Configuration File
### 2.2.1. Creating the Configuration File
To run the one-bit full adder, a configuration file is required. Follow these steps:
1. **Invoke the Docker Container:**
Inside the OpenLANE directory (which contains the `Makefile`), use the following command to invoke the Docker container:
   
 ```sh
 sudo make mount
 ```
Once the Docker container is successfully invoked, you should see a confirmation message.

2. **Enter `tcl` Interactive Mode:**
Run the following command to enter `tcl` interactive mode:

```tcl
./flow.tcl -interactive
```

3. **Create the Configuration File:**
To create a `config.tcl` file in the `designs` folder, use the script below. Replace `<design_name>` with the name of the main module, ensuring that it matches the module name and the file name of the RTL design:

```tcl
./flow.tcl -design <design_name> -init_design_config -add_to_designs -config_file config.tcl
```

### 2.2.2. Modifying the Configuration File
To modify the `config.tcl` file, follow these steps:

1. **Open the File in `Vim` as Root User:**
Use the following command to open the `config.tcl` file in `Vim` with root permissions:

```sh
sudo vim config.tcl
 ```

2. **Enter `INSERT MODE`:**
Press `I` to enter `INSERT MODE` in `Vim`.

3. **Make the Required Changes:**
Update the `config.tcl` file with the following content:

```tcl
   set ::env(DESIGN_NAME) {fa}
   set ::env(VERILOG_FILES) [glob $::env(DESIGN_DIR)/src/*.v]
   set ::env(FP_CORE_UTIL) 0.05
   set ::env(FP_ASPECT_RATIO) 0.5
   set ::env(RUN_CTS) 0
   set ::env(DESIGN_IS_CORE) {1}

   set tech_specific_config "$::env(DESIGN_DIR)/$::env(PDK)_$::env(STD_CELL_LIBRARY)_config.tcl"
   if { [file exists $tech_specific_config] == 1 } {
       source $tech_specific_config
   }
```

4. **Exit `INSERT MODE` and Save Changes:**
Press `Esc` to exit `INSERT MODE`. To save the changes and exit `Vim`, type:

```sh
:wq
```

If an error is encountered while saving, add an exclamation mark to force the save and exit:

```sh
:wq!
```

## 2.3. Running the Physical Design
To streamline the design flow, follow these steps to create and use a `fa_synth.tcl` file:

1. **Create the `fa_synth.tcl` File:**
Use the following command to create the `fa_synth.tcl` file:

```sh
 cat > fa_synth.tcl
```

After entering the command, press `Ctrl + D` to exit.

2. **Edit the File with `Vim`:**
Open the `fa_synth.tcl` file in `Vim`:

```sh
sudo vim fa_synth.tcl
```

Enter the following content into the file:

```tcl
   package require openlane
   prep -design fa -tag run1 -overwrite
   run_synthesis
   run_floorplan
   run_placement
   run_routing
   run_magic
```

Save the changes and exit `Vim` by pressing `Esc` and then typing:

```sh
:wq
```

If an error occurs while saving, use:

```sh
:wq!
```

3. **Run the Design Flow:**
Enter `tcl` interactive mode again using the previously described steps. Then, execute the following command to run the entire design flow:

```tcl
source fa_synth.tcl
```

If all steps are followed correctly, the **complete design flow is done**.

## 3. Results
To inspect the `Floorplan`, `Routing`, `Placement`, and complete `GDS` file, the `klayout` tool can be used. Here’s how to set it up:

1. **Install `klayout`:**

   Use the following command to install the `klayout` tool:

   ```sh
   sudo apt-get install klayout
   ```

2. **Open `klayout` and Set Up Process Technology:**

   Launch `klayout`. The first step is to select the process technology `Sky130nm`.

3. **Download the `lyt` File:**

   To set up the process technology, follow these steps:

   - Open a terminal in the `OpenLANE` folder.
   - Switch to the root user by running:

     ```sh
     sudo su
     ```

   - Use the following commands to navigate and download the `lyt` file. Use `ls` to list directories and ensure accurate navigation. Note that commands to navigate and list directories are not explicitly shown here; verify directory names as needed.

   | Command       | Purpose                                                                                                                    |
   |---------------|----------------------------------------------------------------------------------------------------------------------------|
   | `cd`          | Change directory.                                                                                                          |
   | `pwd`         | Display the full pathname of the current directory.                                                                        |
   | `ls -a`       | List all items, including hidden files, in the current directory.                                                          |
   | `cd .volare`  | Navigate to the `.volare` directory.                                                                                       |
   | `cd sky130A`  | Navigate to the `sky130A` directory.                                                                                       |
   | `cd libs.tech`| Navigate to the `libs.tech` directory.                                                                                     |
   | `cd klayout`  | Navigate to the `klayout` directory.                                                                                       |
   | `cd tech`     | Navigate to the `tech` directory.                                                                                          |

   In the `tech` directory, three `.lyt` files will be present. Copy the `sky130A.lyt` file to the design folder using:

   ```sh
   cp sky130A.lyt /home/nigil/OpenLane/designs/fa/runs/
   ```
