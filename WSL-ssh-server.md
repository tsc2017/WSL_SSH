## How to automatically start ssh server on boot on Windows Subsystem for Linux

Microsoft partnered with Canonical to create Bash on Ubuntu on Windows, running through a technology called the [Windows Subsystem for Linux](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide). Below are instructions on how to set up the ssh server to run automatically at boot.

1. Uninstall and reinstall the ssh server using the following commands:
    1. `sudo apt remove openssh-server` 
    2. `sudo apt install openssh-server`
    3. Edit the `/etc/ssh/sshd_config` file by running the command `sudo vi /etc/ssh/sshd_config` and do the following
        - Change `Port` to 2222 (or any other port above 1000)
        - Change `UsePrivilegeSeparation` to no
        - Change `PasswordAuthentication` to yes. This can be changed back to no if ssh keys are setup.
    4. Restart the ssh server:
        - `sudo service ssh --full-restart`  
2. With this setup, the ssh server must be turned on every time you run Bash on Ubuntu on Windows, as by default it is off. Use this command to turn it on:
    1. `sudo service ssh start`
3. Follow the next steps which will create scripts that start the ssh server automatically:
    1. Create a sshd.bat file and edit it with the following commands:
        - `vi sshd.bat`
        - Add the following code: `C:\Windows\System32\bash.exe -c "sudo /usr/sbin/sshd -D"`
        - Save the file and move it to a more accessible location, e.g. `mv ssh.bat /mnt/c/Users/YourUserName/Documents`. Make sure to match your username! Take note of this location for the next step as in Windows language this corresponds to `C:\Users\YourUserName\Documents`
    2. Create a sshd.vbs file and edit it with the following commands:
        - `vi sshd.vbs` 
        - Add the following code, making sure to put in your actual user name: 

        ```
        Set WinScriptHost = CreateObject("WScript.Shell")
        WinScriptHost.Run Chr(34) & "C:\Users\YourUserName\Documents\sshd.bat" & Chr(34), 0
        Set WinScriptHost = Nothing
        ```

        - Save the file and move it to a more accessible location, e.g. `mv sshd.vbs /mnt/c/Users/YourUserName/Documents`.
        - Open start menu, type `run`. Then type `shell:startup`. Copy the vbs file over to the Startup folder
    3. Finally, you will need to configure the ssh server to start without requiring password. Run the command `sudo visudo` and add this line to the end of the file:
        - `%sudo ALL=NOPASSWD: /usr/sbin/sshd`
4. If configured properly, the ssh server should now automatically start in the background when Windows starts.