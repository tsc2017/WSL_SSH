Forked from the gist [How to automatically start ssh server on boot on Windows Subsystem for Linux](https://gist.github.com/harleyday/76a103a1a0ca97c6f33706e4a8cc3307)

## How to automatically start ssh server on boot on Windows Subsystem for Linux
-------------------------------------------------------------------------------
*This works with Ubuntu 18.04LTS. I make no promises about other distributions, but direct anyone else to an [older Gist](https://gist.github.com/dentechy/de2be62b55cfd234681921d5a8b6be11#file-wsl-ssh-server-md) which apparently works for 16.04LTS.*

Microsoft partnered with Canonical to create Bash on Ubuntu on Windows, running through a technology called the [Windows Subsystem for Linux](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide). Below are instructions on how to set up the ssh server to run automatically at boot.

1. Uninstall and reinstall the ssh server using the following commands:
    1. `sudo apt remove openssh-server` 
    2. `sudo apt install openssh-server` 
2. With this setup, the ssh server must be turned on every time you run Bash on Ubuntu on Windows, as by default it is off. Use this command to turn it on:
    1. `sudo service ssh start`
3. Follow the next steps which will create scripts that start the ssh server automatically:
    1. Create a sshd.bat file and edit it with the following commands:
        - `vi sshd.bat`
        - Add the following code: `C:\Windows\System32\bash.exe -c "sudo /etc/init.d/ssh start"`
        - Save the file and move it to a more accessible location, e.g. `mv sshd.bat /mnt/c/Users/YourUserName/Documents`. Make sure to match your username! Take note of this location for the next step as in Windows language this corresponds to `C:\Users\YourUserName\Documents`
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
        - `%sudo ALL=NOPASSWD: /etc/init.d/ssh`
4. If configured properly, the ssh server should now automatically start in the background when Windows starts.

---

**My thanks go to [dentechy](https://gist.github.com/dentechy) for his [original GitHubGist](https://gist.github.com/dentechy/de2be62b55cfd234681921d5a8b6be11#file-wsl-ssh-server-md) on this subject. I found that the instructions he provides do not work if you've upgraded your Windows Subsystem Linux Ubuntu to 18.04LTS since the UsePrivilegeSeparation option has been depreciated on the ssh server for 18.04LTS. The above is my workaround.**
