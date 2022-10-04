# My PREPT stack

Here is my PREPT (Parcel/React/Express/PostgreSQL/Typescript) stack example application:

1. Parcel for the front-end bundler.
1. React front-end.
1. Express back-end.
1. PostgreSQL.
1. Typescript.

**_Major Caveat A_**: It is convenient to have one repo but the front-end and back-end have different requirements (see below), so for bigger project I would recommend splitting them apart.

**_Major Caveat B_**: I have no testing framework included yet.

## Components

### Parcel

Homepage: [https://parceljs.org](Parcel)

I haven't been using Parcel for long but prefer it over Webpack. The documentation is great and things work well out-of-the-box. Parcel has a built-in proxy to use with an Express back-end during development, support for SASS, Images etc.

I find that Webpack can be a major pain to configure. Create-React-App improves the situation but creates a host of new issues.

**_Caveat_**: Because the output is ES5 and modules are not CommonJS, you can't use the JS version of the proxy config, which means you can't use the PORT environment variable for the proxy (so there is duplication in `.proxyrc.json`) See the issue here:

https://github.com/parcel-bundler/parcel/issues/7330

### React

The front-end is React. This example app includes the correct route setup when using react-router-dom, i.e. a page /foo is served correctly (which need the back-end to serve index.html).

### Express

Because the Typescript config extends create-react-app, the compile target is es5:

tsconfig.json:

```
  "extends": "@tsconfig/create-react-app/tsconfig.json",
```

@tsconfig/create-react-app/tsconfig.json:

```
  "target": "es5",
```

Which means that the TS compiler will output "`import`" statements and not "`require()`" statements. Which means you have to specify `"type": "module"` in the `package.json`.

### PostgreSQL

You have to create the database (`$ createdb foo`) and `people` table manually, you can copy & paste the contents of `create.sql` into a `psql` session. Also set the `DATABASE_URL` during development.

### Typescript

I'm still getting to know Typescript so will improve my usage in later versions. See above regarding the different target requirements for Express and Parcel/React. When running the `tsc` command-line compiler it will also compile the unnecessary front-end `index.ts` file, so this is deleted during `npm build`.

## Environment Setup

The app requires some environment variables:

```
PORT=<Express port>
PARCEL_PORT=<Parcel port during development>
LOG_LEVEL=debug|info|warn|error
DATABASE_URL=postgresql://...
```

I take the approach that no environment vars should have defaults to be defensive - I have been caught out be undefined production environment variable too often and having them all explicitly in development reduces errors.

## Running in dev mode

```
npm run dev
```

Will run 3 processes:

1. Parcel bundler (with hot reloading).
1. Typescript compiler in watch mode for Express.
1. Express server (using nodemon).

## Deployment on Heroku

This app is deployed on Heroku and deploys as-is with `git push heroku main`. You only have to do 3 things:

1. Add a PostgreSQL DB resource for your app (Heroku defines `DATABASE_URL` automatically).
1. Create the required table in the Postgres Database (see above). You can access the Heroku DB using the command `heroku pg:psql`.
1. Create a LOG_LEVEL environment variable.

The app is deployed on Heroku at the moment (Oct 2022):

1. Web app: [https://typescript-website-boilerplate.herokuapp.com](https://typescript-website-boilerplate.herokuapp.com)
1. API: [https://typescript-website-boilerplate.herokuapp.com/api/people](https://typescript-website-boilerplate.herokuapp.com/api/people)
1. Working React-router link: [https://typescript-website-boilerplate.herokuapp.com/foo](https://typescript-website-boilerplate.herokuapp.com/foo)
