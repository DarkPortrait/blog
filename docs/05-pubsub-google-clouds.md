## Why Pub/Sub
Pub/Sub seems to be the zapier of Google Cloud Console. It connects senders of data, **Publishers**, with receivers of data, **Subscribers**. 

The is a fundamental problem to solve in any project involving streaming analytics.

Why not use the zapier, instead?
* Cost savings
* Compatibility with GCP tools

## 
The [quick start guide](https://cloud.google.com/pubsub/docs/quickstart-console) shows how to create a **topic**, **subscriber** and **publisher** under a Google cloud project. This does not mention anything about connecting apps using APIs or webhooks.

Another [guide](https://cloud.google.com/pubsub/docs/building-pubsub-messaging-system) shows how to make a useless app using a number of python scripts and pubsub. 

Downloaded the [Google Cloud SDK](https://cloud.google.com/pubsub/docs/building-pubsub-messaging-system#install_the). After installation, ran the commands:

```
gcloud pubsub topics list
gcloud pubsub subscriptions list
```
[This](https://cloud.google.com/appengine/docs/flexible/python/writing-and-responding-to-pub-sub-messages) document seems to be the most relevant one, but its pre-requisite is to build the [Hello worlld cloud app](https://cloud.google.com/appengine/docs/flexible/python/quickstart).

How to write the webhook / API endpoints to connect the apps?
`https://YOUR_PROJECT_ID.REGION_ID.r.appspot.com/pubsub/push?token=YOUR_TOKEN \`

Dataflow lets you create SQL Jobs and connect them with Pub/Sub topic. This becomes the publisher. NOt sure how to connect the subscriber with a webhook.

Dataflow also has jupyter notebook that can connect with Cloud Storage.

Data Studio BigQuery Table connection is also able to pull accurate order cout and sum of total. However, we need to find out how refunds are coming in Big Query.
 
## Resources
* [YouTube playlist](https://www.youtube.com/watch?v=cvu53CnZmGI)
* [YouTube playlist video 2](https://www.youtube.com/watch?v=MjEam95VLiI)
* [Python library github](https://github.com/googleapis/python-pubsub)
* [Python library documentation](https://googleapis.dev/python/pubsub/latest/index.html)
* [Official documentation](https://cloud.google.com/pubsub/docs/overview)
