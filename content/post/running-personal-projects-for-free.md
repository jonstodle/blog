---
title: Running Personal Projects for Free
tags:
  - Cloud
  - Tip
date: 2020-01-31T16:07:14.849Z
---
I have some personal projects, which consists of some simple web apps and some data they need to store, and I've always wanted to run them as cheap as possible. My current solution has them running at no cost at all.

## Requirements

It's worth mentioning that these projects don't have very high requirements. They're mostly used by me and some of my family, which doesn't amount to much traffic or storage at all.

## Frontend

I have a couple of sites which are just statically generated web pages ([like my blog](https://blog.jonstodle.com)), and a few web apps written with [Svelte](https://svelte.dev/). All of this I host on [Netlify](https://www.netlify.com/).

Netlify is easy to set up and get started with, but they have a lot of nice advanced features too. You can get started for free and host as many sites as you like.

They have built-in support for automatic deploys from Github. It's easy to set up and they even build your frontend project for you.

## Backend

My backend is split in two: the logic and the database.

### The logic

I have some code that I want to run on a server. It's basically a few REST APIs fetching and formatting some data. All of this logic I'm running as serverless functions through [Netlify Functions](https://www.netlify.com/products/functions/).

They support writing the functions in Javascript or Go. And you get 125,000 free requests to the functions each month with each site you host on Netlify. I'm not going to hit that limit for a while yet.

#### Netlify Dev

Netlify recently launched a new feature with their Netlify CLI tool called [Netlify Dev](https://www.netlify.com/products/dev/). Netlify Dev let's you spin up their server backend locally, making it very easy to develop your site with Netlify in mind.

Netlify Dev hosts your web app and any associated serverless functions in your project, locally. It even fetches any secret/environment variables you have stored for the site from Netlify and exposes them to the local environment.

I use this all the time when working on my web apps.

I originally intended to write the serverless functions in Go, but Netlify Dev only support Javascript functions, and [will do so for the forseeable future](https://community.netlify.com/t/support-for-go-lambdas-in-netlify-dev/6751/2) 😔.

### The database

And, finally, the database. I've only recently found a proper (in my mind) solution for this.

I started out using [Airtable](https://airtable.com/) for storage. They have a nice mobile app which made it easy for my significant other to enter new data, and they expose a pretty decent API for getting the records out of the database.

My problem with Airtable, however, have always been their unwieldy query syntax and the fact that users of a table can change the names of the fields the JSON object you get from the REST API. If my significant other changes the name of a column from `Description` to `description` my frontend code breaks...

So, for a while I've been wanting to move over to a "proper" database, and I recently made the move to MongoDB. They have a, relatively, new offering called [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) which let's you run managed MongoDB servers on any of the three big cloud providers (Azure, AWS, Google Cloud).

When I first heard about it I didn't even consider it an option, until I realised they have a free tier! Up to 512MB of storage. Which, again, is more than enough for me. At the moment I'm consuming something akin to 1MB.

MongoDB Atlas is super easy to set up too. You specify you want the free tier, which provider you want to use and which region to run the server in. After that, you're up and running in a couple of minutes.

MongoDB also has a nice data explorer called [Compass](https://www.mongodb.com/products/compass) which let's you view and edit your database structure and documents.

---

My current running cost for my 10 web apps/sites is the total of NOK 0. I'm pretty happy with that, I have to say 😁
