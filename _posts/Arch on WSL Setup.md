# Arch on WSL setup
## References:
  1. https://learn.microsoft.com/en-us/windows/wsl/use-custom-distro
  2. https://github.com/yuk7/ArchWSL/issues/121
## Prerequiste 
  1. Docker Installed, refer to docker documentation

### Step 1: Export Arch Image from Docker
  1. ``` docker run -t archlinux bash ls / ```
  2. ``` docker ps -a  # Get the container Id, in my case 14999590eaf0 ```
  3. ``` docker export 14999590eaf0 > /mnt/d/temp/arch.tar  # replace 14999590eaf0 with container id from previous step ```

### Step 2: Import that image using WSL import

  1. Run the below command in CMD or Powershell
  2. ``` wsl --import Arch d:\Antany\wsl d:\temp\arch.tar ```
  3. ``` wsl --set-default Arch ```
  4. ``` wsl -d Arch  or wsl  # open as root ```
### Step 3: Post Installation Steps
  1. By default it will login as root, create some useraccount and add that to default user
  2. Set the time zone. ``` ln -sf /usr/share/zoneinfo/_Region_/_City_ /etc/localtime ```
  3. Set Locale
       ```
       sed -i -e "s/#en_US.UTF-8/en_US.UTF-8/" /etc/locale.gen
       locale-gen  
       echo "LANG=en_US.UTF-8" > /etc/locale.conf
       ```

  4. Complete the pacman initialization. ``` pacman-key --init && pacman-key --populate archlinux ```
  5. Install other required packages.
  6. ``` pacman -Syu _mypackagename_ ```
  7. Install base-devel package group if you want to install AUR packages too.
  8. Install a text editor such as nano, vim, neovim.
  9. Create a normal user account.
      ``` useradd -m -G wheel _myusername_ ```
 10. Set passwords for both root and myusername. ```passwd and  passwd username ```
 11. Edit the /etc/sudoers file and uncomment the following line. ``` %wheel ALL=(ALL) ALL ```
 12. Edit /etc/pacman.conf ``` IgnorePkg   = fakeroot ```
 13. ``` echo -e "[user]\ndefault=username" >> /etc/wsl.conf ``` to add default user
  
  
