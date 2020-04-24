+++
title = "Azure subscriptions, app service plan and app terms explained"
date = 2017-08-17T10:00:00.0000000+00:00
[taxonomies]
tags = ["Azure"]
+++
When I first got started with Azure I was confused with all these terms. I just wanted a web app, which let's me put a website on the web, but I needed a subscription and an app service plan first. What?!

![Creating a Web App](/images/Screenshot-170712-092111-1.png)

# Subscription

This is where the money comes from. Part of the confusion is that you can have multiple subscriptions. As you can see from the picture above, the selected subscription is called *Personal*. This subscription is what I use for my personal projects.

My *Personal* subscription is a *Pay-As-You-Go* subscription, meaning I pay for what I use. If I use a service for 1 hour, I pay for 1 hour of use. Every month Azure bills my account for the last month's use.

If you have an MSDN subscription, you get credits to use on Azure every month. These credits are added as a separate subscription. If you want to pay for a service using those credits, you simply select that as the subscription.

If you're a consultant or run you own small company you might have a separate subscription for those expenses to easily keep track of how much your spending on Azure.

I think for most people, a single subscription is what you're going to end up with.

# App Service Plan

![Creating an App Service Plan](/images/Screenshot-170712-095702.png)

The *App Service Plan* is the server you are going to run your apps on. The reason it's called an app service plan is because you're not necessarily getting your own server.

On the *Free* and *Shared* tiers you're actually sharing a server with other customers on Azure. That doesn't mean that you're not going to get what you paid for, it just means it's cheaper for Microsoft (and therefore cheaper for you).

When you create an *App Service Plan* you choose where in the world you want it to be located. This is to enable you to have it close to your users. The closer it is, the faster it's going to respond.

You also pick a pricing tier. I find this a bit misleading. While you are choosing how much you're going to spend, you're also choosing what kind of computer power you'll have at your disposal.

## Scaling up vs scaling out

Just a short note on the scaling alternatives...

When you move past the two cheapest tiers you'll be able to scale out, and as you can see: on the *Basic* tier that means up to 3 instances. But what does that mean?

**Scaling up** is probably the most familiar. This means upgrading to more powerful hardware. Moving from 1 CPU to 2 CPUs. Moving from 1.75 GB of RAM to 3.5 GB of RAM.

**Scaling out** means more of the same. Instead of having 1 server with 1 CPU and 1.75 GB of RAM, you'll have 2 servers with 1 CPU and 1.75 GB of RAM. The server isn't more powerful, but you'll have 2 instead of 1.

While I don't have too much experience with running large applications, my impression of how to use this is this: You scale up until you have a server that can run you application, and then scale out when you might need extra power for a while. This might be a sudden spike in traffic; during a promotion of you app perhaps.

# Web App or Mobile App

Finally we've reached the app itself. This is what you'll be running on the server you just chose in you *App Service Plan*.

One thing to remember is that you can run multiple apps on a single *App Service Plan*. On the *Free* and *Share* tiers you can run 10(!) apps on a single *App Service Plan*. You can probably get away with a single *App Service Plan* for a lot of apps without any problems, as long as their not very heavy to run.

# Conclusion

This is my attempt at explaining how the different terms in Azure couple together. I hope I've managed to clear it up a little for you too.

Happy coding!
