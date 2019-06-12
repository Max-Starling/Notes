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
