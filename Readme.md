# Physical Design using OpenLANE SKY130 
# Installating OpenLane v1.0
To install the necessary tools, please refer to the link below. Follow the instructions provided and run the example given to verify that the OpenLANE tools are installed correctly. Ensure that all commands are executed as the `root` user.

| OpenROAD and some supporting tools. | [OpenLANE Ubuntu Installation](https://openlane.readthedocs.io/en/latest/getting_started/installation/installation_ubuntu.html) |
| ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |

During the initial `make smoke` test, some users might encounter the error: 
```
qt.qpa.plugin: Could not load the Qt platform plugin "xcb" in "" even though it was found
```
This error indicates a problem with loading the Qt platform plugin "xcb," which is necessary for opening the GUI.

This issue can be resolved by running the command `xhost +local:*` to allow local connections. After completing your work and before closing the terminal, remember to revert the setting by running `xhost -local:*`. This creates a workspace directory in your home directory if it doesn't already exist. This permits the root user on the local machine to connect to GUI apps.

# Example
Before delving into the design flow. Installation of a text editor is required. The user can use any text editor. Here, `vim` is used. vim` can be installed by running the following command:
```sh
sudo apt-get install vim
```
