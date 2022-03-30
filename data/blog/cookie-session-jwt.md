---
title: Cookie, Session, Token
date: '2022-03-29'
tags: ['web']
draft: false
summary: 'What is the difference between Cookie, Session, JWT at the front-end?'
---

This post is just a script of this video below from Nomad coder Nicolas.
[![Nomad coder Nicolas Cookie vs Session vs JWT video](https://img.youtube.com/vi/tosLBcAX1vk/0.jpg)](https://www.youtube.com/watch?v=tosLBcAX1vk)

&nbsp;

`Authentication` is how your application **`knows who` your users** are.

## What are Cookies?

`Cookies` are how a server can **put data on your browser** to remember something about you.

- When you go to a website, your **Browser** would make a **`request` to a server**. Their server will reply with a `response`.
- the response would have all the data and the code required to show you the page you are looking for.

also **on that `response`** the server can give to the browsers **some `cookies`** to save.

&nbsp;

### Cookie's features

1. After you get the cookies in your browser, `Every time` that you go to the website, `the cookies` are going to also **be sent on that `request`**.

2. `Cookies` are **limited by `domain`**. For example, all the cookies that _youtube.com_ will give you are only gonna be sent to _youtube.com_.

3. `Cookies` also have an `expiration date`, some of them can expire in a day, week, or whenever the server wants to.

4. `Cookies` can be used to remember **all sorts of things**. They are **not actually only related to `authentification`**.

   - for example, if you **`change` the language** of your website, the server can give you a `cookie` that will **remember what language** you chose.
   - so the next time you visit that website again, the `cookie` is going to **go with a request**, so the server will be able to give the page in **the language that you want**.

&nbsp;

## Why we need sessions, tokens?

we **need to remember** that `http`, the protocol that we used to go to a website, that protocol is a `Stateless`.

`Stateless` means that **every request that goes to the server** is treated `independently` from the previous one.

- There is **no link** between requests. There is no memory.
- After the `request` **is finished**, the `server` will **forget** about who you are.

Because the server forget everything about who we are once request is finished, we have to **remind to the `server`** who we are with **every `request` we make**.

&nbsp;

## What is Session

One way to do this is by using `Sessions`.

1. If we have a username called **GN**, and we want to login, we are going to `send` the **username** and the **password** to the `server`.
2. If the password is correct, the `server` will **create a `record`** in a **session database** for the **GN user**.
3. That `session` will have a **unique ID**. That `session ID` will **be sent back** to our `browser` using `cookies` as a transportation method, and there it will **be saved**.

   - `Cookies` are sent automatically, the `server` is going to look at the cookies are coming in and **will see** that there is an **incoming cookie with** a `session ID`.
   - still, at this point, the server doesn't know who we are.

4. with that `session ID`, the `server` will go and **look on** the `session DB`, there the server will **find that this ID** belongs to GN user. and only then, the server will know who we are.

The important thing to remember here is that all the `information` **about the user** is on the `server-side`, is on the `database`. The **user only has** a `session ID`.

&nbsp;

### Cookie vs Session

The `cookie` is just a **transportation method** that you used to send and receive the `session ID` that is **on our** `DB`.

We can **still use `sessions`** when making iOS, Android apps but we **cannot use `cookies`**. because `cookie` only **exists on browser**, not on native apps.

&nbsp;

## What is Token

In this context, people will refer to this as a `token`.

We will be sending a `token` to the `server`, but the token is just a `string`.

- It's just a string that looks **really weird**.

then we have to send that to the server, and the server is going to **take that token** and then is going to **look in** the `session DB` for the person that **has that token**.

&nbsp;

### Session's feature

The most important thing to remember about `session` is that the `server` **has to use `DB resources`** to keep track of **all the people** that are **logged in**.

That also means that with **every request** that comes in, the server has to **take the `cookie`**, has to **search the `session ID`** for the user.

That means are **the more users** are logged in, **the more database** resources we need.

&nbsp;

### What is JWT

This is when we introduce `JWT`. JWT is a **token format**.

If you **use JWT** to authenticate your users, there is `no need` to have a `session DB` and the server doesn't have to work that hard to authenticate a user.

&nbsp;

Let's see the difference between **JWT** and **Sessions** by looking at the log in example.

## Login process with JWT

1. If the username and pw are correct, the `server` **won't create anything** on the `DB`.
2. Instead, the `server` will **take the ID** of the user for example, and it will **sign in** using a `signature algorithm`.
3. Then the `server` will send this the `signed information` as a **string**.

   - It is very long, **longer than** an average **session ID** that we use when we send cookies.
   - This is because cookies have a space limit, `JWT` **doesn't have** a `space limit`. That can be very big.

4. If we want to **make a request** to the server, just like when we were using **session IDs**, we have to send that `token` or `signed information` to the server.
5. When the **server receives** our `token`, the server is going to **check if** the `signature is valid`, which basically means that **nobody modifies that token**.

   - and if it is, then the server will **trust the token** and it will **see who we are**.

&nbsp;

### JWT's features

The `JWT` is not encryted. It is `signed`, but **not `encryted`**.

- Encryted means that **nobody can see the content** or **understand** what's going on.

In the case of JWT, anybody **could** `open up` the JWT and `look at` **the content** it has inside. so you **`shouldn't put` any secret** information in the JWT.

The point is that **not keep** the information **secret**, just to `sign the token` and `verify` that **nobody has modified** it.

&nbsp;

### Pros of using Session

When we use `sessions`, the server `keeps track` of **all people** that are logged in on this **session DB**.

**Having the sessions** in our session DB allows us to `build features` that app need.

- You can do what instagram does, instagram `show` you a **list of all the `device`** that you have **logged in from**, so you can delete.
- Netflix that `limits` the **amount of people** that can share an account. We also `knows` **how many people** are currently logged in and watching. ex) redis

&nbsp;

with `JWT`, the server doesn't keep track of the token that they has created by default. **All the server knows** is if the `token is valid or not`.

This is actually JWT encoded QR code.
