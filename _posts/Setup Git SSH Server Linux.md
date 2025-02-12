# Setup Git SSH Server

#### Step 1. Create git user account
```useradd -m -s /bin/bash git```

#### Step 2. Create authorization files
```cd ~``` <br>
```mkdir .ssh && chmod 700 .ssh ``` <br>
```touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys``

#### Step 3. Add user public key to auth file
copy the public key to tmp folder <br>
```cat /tmp/id_rsa.pub >> ~/.ssh/authorized_keys```
