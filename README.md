# Designing a social network
Welcome to the repository, where **I will develop the architecture of the social network** — from the collection of requirements to the final technical implementation.

## 🔍 What will happen here?
In this repository, I:

- I will define functional and non-functional requirements
- I will develop architectural solutions
- I will highlight the key components of the system
- I will analyze scalability and fault tolerance
- I will evaluate different approaches to design

## 🎯 Goal
Create an optimal architecture for a social network, taking into account the workload, scalability and ease of development.

## 📌 Note
This repository is not a codebase, but a research project dedicated to design.


## 🎯 Функциональные требования (Functional Requirements)
- publishing travel posts with photos, a short description and a link to a specific place of travel;
- rating and comments of other travelers' posts;
- Subscribe to other travelers to keep track of their activity;
- Search for popular travel destinations and view posts from those locations;
- View other travelers' feeds and user feeds based on subscriptions in reverse chronological order;

## 📊 Нефункциональные требования (Non-Functional Requirements)
- DAU (Daily Active Users): 10 000 000
- Availability target: 99.99% uptime (SLA).
- Disaster tolerance: Data replication in several regions (CIS).
- The average user will make (posts, reactions, comments) -> (1, 10, 5) = 15 total actions
- Location is CIS only
- We always keep it
- One user can have 1,000,000 subscribers
- no more than 1 second for all actions (posts, reactions, comments)


## 📊 Load assessment
- RPS = 10 000 000 * 15 / 86 400 -> (100 000) = 1500
- RPS(posts) = 100
- RPS(reactions) = 1000
- RPS(comments) = 500
### Traffic calculation
- Traffic(posts) = 100 * (txt(20kb) + image(20mb) + meta(1mb) ) = 2.1gb
- Traffic(reactions) = 1000 * (meta(1kb)) = 1mb
- Traffic(comments) = 500 * (text(1kb) + meta(1kb) ) = 1000kb = 1mb
### Calculation of simultaneous connections
Connections = 10 000 000 * 0.1 = 1 000 000