This guide will help you to add long-running tasks, which may run on different machines or in the background. This document is part of the [[Hydroshare Developers' Guide]].

# Introduction

We are using Celery to add and manage tasks. According to its website, [Celery](http://www.celeryproject.org/) is "an asynchronous task queue/job queue based on distributed message passing. It is focused on real-time operation, but supports scheduling as well. The execution units, called tasks, are executed concurrently on a single or more worker servers using multiprocessing, Eventlet, or gevent. Tasks can execute asynchronously (in the background) or synchronously (wait until ready)."

According to its documentation on [ReadTheDocs](http://celery.readthedocs.org/en/latest/index.html), Celery is "a simple, flexible and reliable distributed system to process vast amounts of messages, while providing operations with the tools required to maintain such a system. It’s a task queue with focus on real-time processing, while also supporting task scheduling."

Celery is open source and [available for download](http://www.celeryproject.org/install/) on its website.

# Tasks

Documentation on tasks within Celery can be found within its [user guide](http://celery.readthedocs.org/en/latest/userguide/tasks.html). There you'll also find instructions for creating and using tasks.

From the user guide: "Tasks are the building blocks of Celery applications. A task is a class that can be created out of any callable. It performs dual roles in that it defines both what happens when a task is called (sends a message), and what happens when a worker receives that message. Every task class has a unique name, and this name is referenced in messages so that the worker can find the right function to execute. A task message does not disappear until the message has been acknowledged by a worker. A worker can reserve many messages in advance and even if the worker is killed – caused by power failure or otherwise – the message will be redelivered to another worker."

## Shared tasks

When writing tasks in a reusable app, you will need to use the @shared_task decorator to create tasks without having a concrete app instance. See the 
[shared tasks](http://celery.readthedocs.org/en/latest/django/first-steps-with-django.html?#using-the-shared-task-decorator) section of the documentation for more information.

# More information, tutorials, and documentation

Celery 3.1 supports Django out of the box, so you can get started by following the general [First Steps with Celery](http://celery.readthedocs.org/en/latest/getting-started/first-steps-with-celery.html) guide before the [First Steps with Django](http://celery.readthedocs.org/en/latest/django/first-steps-with-django.html) guide.

Once you have a working example, you can move on to the [Next Steps with Celery](http://celery.readthedocs.org/en/latest/getting-started/next-steps.html) guide. An example of using Celery with Django can be found [on Github](https://github.com/celery/celery/tree/3.1/examples/django/).

If you need more information about using Celery, or if you still have questions, you can check the [user guide](http://celery.readthedocs.org/en/latest/userguide/index.html) or the [FAQ](http://celery.readthedocs.org/en/latest/faq.html#faq). 