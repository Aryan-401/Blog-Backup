---
title: "Scraping Reddit — One Subreddit at a Time"
datePublished: Mon Aug 01 2022 11:30:00 GMT+0000 (Coordinated Universal Time)
cuid: cl6ao663k0f9ty6nv3fn46f9v
slug: scraping-reddit-one-subreddit-at-a-time
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1656943266101/eeQndl1TA.jpg
tags: newbie, python, apis, reddit, python-projects

---

Continuing our tradition of using APIs to solve problems no one ever had, we've come to Reddit. Launched more than 17 years ago, Reddit is where everyone goes to discuss. It's a mega-forum, and today we're going to be getting data just because we can!

We will use PRAW (Python Reddit API Wrapper) to scrape our way through Reddit. Traditionally, we would have used a scraping package like Selenium or BeautifulSoup, but PRAW has simplified getting information from Reddit.

## Step 1: Getting our Credentials

First, we would need credentials to help us access the Reddit API. Log-in to [this](https://www.reddit.com/prefs/apps) link, and at the bottom, you will see a button labeled "create another app", click on it.

![reddit-2.jpg.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1656943894950/OdtgZot1-.png align="left")

Next, you'll have to give your script a name and fill out a description. Once that's done, make sure to select the "script" option and then make sure to put the following into the redirect URI box: http://localhost:8080. This is suggested by the PRAW docs but is necessary because Reddit requires a redirect URI even if our application doesn't use it. Your "personal use script" can be found near the top left corner, directly under where it says "personal use script." The next thing you'll need is the "secret." It should be listed below the "personal use script." With these two seemingly random strings, we can start using PRAW. Please note that PRAW can also be used for posting to Reddit, but I would not be covering that in this article.

## Step 2: Creating a Virtual Environment

Follow the steps given in [this](https://aryan401.hashnode.dev/api-avalanche-all-about-apis) article, and then continue with this tutorial

## Step 3: Time to Code

The first step in any project is to install our dependencies, this time, we're going to be installing PRAW as a package in Python. To install the required package enter the following pip command into the terminal

```bash
pip install praw
```

Next, we will be setting up our .env file, which will house all the credentials for the bot. Ensure its name is `.env` and is in the working directory. It should also have this format.

```python
CLIENT_ID=client id here
CLIENT_SECRET=client secret here
USER_AGENT=<platform>:<app ID>:<version string> (by u/<Reddit username>)
```

You can also do this using a `praw.ini` file, but I prefer to leave everything in my `.env`

Now we've our credentials stored, let's start with our `main.py`

```Python
from os import getenv
import praw  # pip install praw
from dotenv import load_dotenv  # pip install python-dotenv

reddit = praw.Reddit(
    client_id=getenv('CLIENT_ID'),
    client_secret=getenv('CLIENT_SECRET'),
    user_agent=getenv("USER_AGENT"),
)
```

This boilerplate code would be present in every PRAW file for a read-only instance.

Now let's get into the magical aspects of this API Wrapper.

## Step 4: Explore the API

Let's first check if we're in read-only mode, so we don't cause any unwanted errors

```python
reddit.read_only()
#returns True
```

25 "Hottest" Submissions from r/python

```python
for submission in reddit.subreddit("python").hot(limit=25):
    print(submission.title)

# Output: 25 submissions
```

We can also pass a string in the format `subreddit1+subreddit2+subreddit3...` Its syntax would look like this:

```python
subreddit = reddit.subreddit("python+reddit+java+discord")
for submission in subreddit.top(limit=25, time_filter="day"):
    print(submission.title)
# Output: 25 submissions according to score
```

### Working with Comments

Like Submissions are a subclass of a Subreddit, Comments can be considered a subclass of a Submission. To access comments, we have to have a submission in memory.

```python
submission = reddit.submission("vnsq8s")  # Every Reddit Submission has its own ID
submission.comment_sort = "new"
top_level_comments = list(submission.comments)[:25]
for comment in top_level_comments:
    print(f"{comment.author} wrote: {comment.body} at {comment.created_utc}")
```

Like all APIs, you only get better by using it, [here](https://praw.readthedocs.io/en/stable/index.html) is the link to the Documentation, which you can use to refer for more attributes and find out about posting using the wrapper.

Cheers, and keep API-ing!