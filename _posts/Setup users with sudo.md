# Setup users with Sudo
## Step 1
    1. Make sure sudo package was installation
    2. ```visudo```
    3. uncomment ```%sudo ALL=(ALL) ALL``` line and save it
    4. ```groupadd sudo```
    5. ```useradd -m -G sudo -s /bin/bash username```
    6. ```passwd username``` -- to set password for user
    7. Logout and login as user