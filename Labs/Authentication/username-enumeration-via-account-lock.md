---
title: Username enumeration via account lock
category: Authentication
difficulty: Practitioner
tags: []
---
> [!danger] Attention
> Work in progress. Initial observations only.


We need to find the correct username and password, but there is an account locking mechanism in place. It is said to contain some kind of a flaw that we might use to our advantage.

Quick manual testing for a random account shows that as long as the account does not exist, the error message probably is just `Invalid username or password.` even after many (10+) tries.
Therefore, we will look for a different error message after some tries - we will check each account with some requests (start with 10, we'll see if we get any hits) and see which account gets locked.
