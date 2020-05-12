# Heroku Deployment

## Get Started
* Install Heroku CLI:
```yarn
npm i -g heroku
```
* Login with:
```yarn
heroku login
```
* Create a new empty heroku app:
```yarn
heroku create
```
* Copy *app_id* and set up git remote with it:
```yarn
heroku git:remote -a <app_id>
```
* Make changes and commit them:
```git
  git add -A
  git commit -m "<commit_message>"
```
* Now you can deploy the app via:
```git
git push heroku master
```

## Basic commands
* Command list:
```js
  heroku help
```
* Open app in a browser:
```js
  heroku open
```
