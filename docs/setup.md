#### Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:9080
npm run dev

# build electron application for production
npm run build

# run unit & end-to-end tests
npm test


# lint all JS/Vue component files in `src/`
npm run lint

```

---

## Windows

#### Setup Steps
In order to get the list of $Shares from the fileserver, an administrator should provide id/password in Terminal

```bash
# Type the below command and give ID/PW 
net user \\server\IPC$

# When you get the successful message, 
# the server program is able to get the list of share$ 
# from the samba file server

# You can check it with the below command
net view server
```

This project was generated with [electron-vue](https://github.com/SimulatedGREG/electron-vue)@[ef811ba](https://github.com/SimulatedGREG/electron-vue/tree/ef811ba974d696ee965da747315f20a034ebc590) using [vue-cli](https://github.com/vuejs/vue-cli). Documentation about the original structure can be found [here](https://simulatedgreg.gitbooks.io/electron-vue/content/index.html).


## How to embed code-signing certificate in CI
https://felixrieseberg.com/codesigning-electron-apps-in-ci/

1. run the below code to generate Base64 string of your certificate
   ```powershell
   $file = './cert.p12'
   $b64 = [Convert]::ToBase64String([IO.File]::ReadAllBytes($file))
   Write-Host $b64
   ```
1. copy the output string and paste it into a CI/CD variable
1. make run.ps1
   ```powershell
   if (Test-Path Env:\WINDOWS_CERTIFICATE_P12) {
       # Which directory are we in right now?
       $workingDirectory = Convert-Path (Resolve-Path -path ".")
   
       # Absolute path to the certificate file we'll create
       $filename = "$workingDirectory\cert.p12"
   
       # Turn our base64 environment variable into usable bytes
       $bytes = [Convert]::FromBase64String($env:WINDOWS_CERTIFICATE_P12)
   
       # Then, write them to disk
       [IO.File]::WriteAllBytes($filename, $bytes)
   
       # Finally, set the WINDOWS_CERTIFICATE_FILE environment variable
       $env:WINDOWS_CERTIFICATE_FILE = $filename
   }
   ```

## smbmap for collecting samba shares

https://github.com/ShawnDEvans/smbmap

* smbmap as windows binary: https://github.com/ShawnDEvans/smbmap/issues/7

```bash
sudo pyinstaller smbmap.py -n smbmap --onefile --noconfirm --clean
```
