# Intempt Demo - Book a Room

## Setup Intempt

1. Create an account on [Intempt's Console](https://app.intempt.com/)

2. Head to Customer Data Platform -> Sources

3. Create an ```Intempt Web Tracker``` source

4. Copy the tracker snippet and insert it into [index.html](/public/index.html)

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
### Compiles and hot-reloads for development
```
yarn dev
```

## Deployment
### Compiles and minifies for production
```
yarn build
```

### Lints and fixes files
```
yarn lint
```
