---
title: Several Ways of Configuring Environment Variables in Web FrontEnd Project 
categories: js
tags: 
- env
- dotenv
- cross-env
comments: true
date: 2023-08-13 16:20:40
---

It's quite often that we need to define and use environment variables. May be in building process or runtime. There're many solutions, I'll show some of them.

## Production / CI build phase

**Basically, we don't need to care about them, just follow the rules provided by the platform because the env variables are often set manually in the platform.** For example, in the [vercel](https://vercel.com/)

![](https://github.com/xianshenglu/blog/assets/23273077/8b142955-c0b2-439b-9e47-f1d374b5b153)

So, they will not appear in our projects' code. 

## Development phase

**If the framework you're currently using has provided such configurations, normally we don't need to care also, just follow the rules provided by the platform.**

If not, we can consider some available solutions in the market.

#### [dotnet](https://github.com/motdotla/dotenv)

Just put `.env` file in the root of your project:

```bash
S3_BUCKET="my bucket"
SECRET_KEY="my secret key"
```
Then as early as possible in your application, import and configure `dotenv`:

```ts
require('dotenv').config()
console.log(process.env) // remove this after you've confirmed it is working
```

If you don't want to add the `require` code to the source code, you can 

```bash
node -r dotenv/config your_script.js
```

It works now.

Also, for the reason of security or interruptions between team members' different configurations, I recommend putting `.env` file in the `.gitignore`. 

For better instruction for the new developers, you can add another `.env.example` file not in the `.gitignore` with content like

```bash
# You need to create a .env file in the root with the below env variables to run this project.
S3_BUCKET="your bucket"
SECRET_KEY="your secret key"
```

#### [cross-env](https://github.com/kentcdodds/cross-env)

In the past, we had another popular package `cross-env`. But now, it has been archived.

I think it's really straightforward for new users.

```json
{
  "scripts": {
    "build": "cross-env S3_BUCKET=\"my bucket\" npm run start"
  }
}
```

Just with some issues:

- It'll become unfriendly if I have many env variables.
- I need to remove the variables before committing if I don't want to share them with others.


### Conclusion

For me, I would prefer `dotenv` at this moment.

[**Issue**](https://github.com/xianshenglu/blog/issues/148)

[**Source**](https://github.com/xianshenglu/blog/blob/master/source/_posts/.md)

## Reference
