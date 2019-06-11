## Bitbucket Integration
* Get deployment token:
```yml
firebase login:ci
```
* Visit shown URL to confirm your request.
* Copy generated token.
* Now you can deploy app by using this command:
```yml
firebase deploy --token "$FIREBASE_TOKEN"
```
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
        artifacts:
          - client/dist/**
    - step:
        name: Client: Deploy to firebase
        script:
          - pipe: atlassian/firebase-deploy:0.2.4
            variables:
              FIREBASE_TOKEN: "FIREBASE_TOKEN" # use generated token
              PROJECT_ID: "PROJECT_ID" # you can find projectId in firebase config
              DEBUG: 'true'
```
