# Intempt Demo - Book a Room

## Setup

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
