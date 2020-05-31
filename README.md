# Intempt Demo - Book a Room

## Setup Intempt

1. Create an account on [Intempt's Console](https://app.intempt.com/)

2. Head to Customer Data Platform -> Sources

3. Create an ```Intempt Web Tracker``` source

4. Copy the tracker snippet and insert it into [index.html](/public/index.html) - between `<!--Intempt Tracker Start-->` & `<!--Intempt Tracker End-->`

5. See the results on the Intempt's console

**Bonus**:

* Add additional configurations by reading this guide: [Intempt's JavaScript Tracker](https://github.com/intempt/intempt-intemptjs)

## Setup Dependencies

Install dependencies:
```
yarn install
```

Create `auth_config.json` with the following contents:
```json
{
  "domain": "Your Auth0 Domain",
  "clientId": "Your Auth0 Client ID"
}
```

Create `stripe_config.json` with the following contents:
```json
{
  "ContentType": "application/x-www-form-urlencoded",
  "Authorization": "Bearer Your-API-Key"
}
```

## Development

Run it on locally on your device using:
```
yarn dev
```

After the website is online, it will be found using the link below:

```
http://localhost:8080/
```

## Deployment

Build for release using this:

```
yarn build
```

After the process is complete, your ready-for-release files will be found in the newly-created dist folder
