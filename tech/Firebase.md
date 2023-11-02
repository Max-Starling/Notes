# Firebase Deployment

## Get Started
* Install Firebase tools:
```yarn
npm i -g firebase-tools
```
* Init hosting project:
```yarn
firebase init hosting
```
* Select a default Firebase project for the directory.

## Basic commands
* Show all your firebase projects:
```js
  firebase list
```
* Serve locally
```js
  firebase serve --only functions, hosting
```
* Deploy
```js
  firebase deploy
```
* Deploy by using token
```yml
firebase deploy --token "$FIREBASE_TOKEN"
```

## Deploy React App (frontend)

* Create a new project with the [Firebase console](https://console.firebase.google.com).
* Choose a project name.
* Click "Add Firebase into web app"
* Choose an app name.
* Select the "Set up  Firebase Hosting" option and go next.
* Open web app settings -> Configuration and copy object like this:
```js
const firebaseConfig = {
  apiKey: "...",
  authDomain: "...",
  databaseURL: "...",
  projectId: "...",
  storageBucket: "...",
  messagingSenderId: "...",
  appId: "..."
};
```
* Now open index.html file and post this two scripts at the end of the body tag:
```HTML
<body>
  <!-- /* ... */ -->
  <!-- The core Firebase JS SDK is always required and must be listed first -->
  <script src="https://www.gstatic.com/firebasejs/6.0.4/firebase-app.js"></script>
  <script>
    // Your web app's Firebase configuration
    const firebaseConfig = { /* your config here */ };
    // Initialize Firebase
    firebase.initializeApp(firebaseConfig);
  </script>
 </body>
```
* Create firebase.json in the root project folder and set up Firebase hosting:  
  * public - path to the build folder (the only one required attribure for hosting)
  * ignore - files to ignore on deploy
  * rewrites - serve index.html for requests to files or directories that do not exist
```json
{
  "hosting": {
    "public": "dist",
    "ignore": [
      "firebase.json",
      "**/.*",
      "**/node_modules/**"
    ],
    "rewrites": [
      {
        "source": "**",
        "destination": "/index.html"
      }
    ]
  }
}
```
* (optional) Create .firebaserc in the root project folder and set up default:
```json
{
  "projects": {
    "default": "your_project_name"
  }
}
```
* Create a build:
```NPM
npm run build
```
* Deploy your app:
```npm
firebase deploy
```

## Deploy Node.js App (backend)

## Bitbucket Integration
* Get deployment token:
```yml
firebase login:ci
```
* Visit shown URL to confirm your request.
* Copy generated token.
* Create *bitbucket-pipelines.yml* file in the root directory:
```yml
pipelines:
  default:
    - step:
        name: Client: install modules and run build
        image: node:10.15.3
        deployment: test
        script:
          - cd ./client
          - npm install
          - npm run build
        artifacts: # cache for next steps
          - client/dist/**
    - step:
        name: Client: Deploy to firebase
        script:
          - pipe: atlassian/firebase-deploy:0.2.4
            variables:
              FIREBASE_TOKEN: "FIREBASE_TOKEN" # paste generated token
              PROJECT_ID: "PROJECT_ID" # you can find projectId in firebase config
              DEBUG: 'true'
```


# Set up `node-gyp` for Windows (to make `grpc` work)

Firebase may require GRPC module and GRPC requires [node-gyp](https://github.com/nodejs/node-gyp).
There is a guide how I managed to install it after reading tons of stackoverflows resolving issues one after another.

Download and open `Visual Studio Installer` (https://visualstudio.microsoft.com/downloads/).
Intall `Visual Studio Build Tools 2022`. Select there:
* `Desktop development with C++`

![image](https://github.com/Max-Starling/Notes/assets/22237384/edb4764e-7e59-412d-bce5-477962bbed1c)

* Some `SDK`

![image](https://github.com/Max-Starling/Notes/assets/22237384/f2e17f8b-72e3-48bf-a9de-456db9d52b01)

* `VC++`

![image](https://github.com/Max-Starling/Notes/assets/22237384/f1dc472f-6870-4300-91de-0d8eae83f0a8)

Then install, restart PC.

```
npm config edit

msbuild_path=C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\MSBuild\Current\Bin\MSBuild.exe
msbuild-path=C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\MSBuild\Current\Bin\MSBuild.exe
msvs_version=2022
msvs-version=2022
python=C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools
```

```
SETX VCINSTALLDIR 'C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\VC'
SETX VSINSTALLDIR 'C:\\Program Files (x86)\\Microsoft Visual Studio\\2022\\BuildTools'
refreshenv
```
![image](https://github.com/Max-Starling/Notes/assets/22237384/5dbe6296-09c7-4cf8-aa03-d514e247201a)
![image](https://github.com/Max-Starling/Notes/assets/22237384/7a892ee6-b4cc-46ea-bd32-bb34dce4c238)

```
npm i -g node-gyp
```

Create `binding.gyp` text tile
```json
{
  "targets": [
    {
      "target_name": "binding",
      "sources": [ "./binding.cc" ]
    }
  ]
}
```
Create empty `binding.cc` text file.

```
node-gyp configure
node-gyp build
node-gyp rebuild
```
now you can do `npm install` without `node-gyp` errors

