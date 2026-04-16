#### Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload (Vite dev server + Electron)
npm run dev:vite

# serve with hot reload (Webpack dev server + Electron, legacy)
npm run dev

# build electron application for production
npm run build:vite

# run unit tests (Vitest)
npm run test:vitest:ci

# run unit tests with UI
npm run test:vitest:ui

# lint all JS/Vue/TS files in `src/`
npm run lint
```

---

This project was originally generated with [electron-vue](https://github.com/SimulatedGREG/electron-vue)@[ef811ba](https://github.com/SimulatedGREG/electron-vue/tree/ef811ba974d696ee965da747315f20a034ebc590) using [vue-cli](https://github.com/vuejs/vue-cli). It has since been migrated to Vue 3 Composition API with TypeScript.


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
