### Node-cron
Helps to run at specific times or interval.
Don't use node-cron is distributed systems. It has not concurrency control and might perform overlapping tasks. Also no provided error handling mechanism also.

Cron jobs don't remember anything between cycles. They just work on repeating some task that is it. 
###### Plus points
- for running some tasks every fixed intervals
- running your health checks every some minutes
- sending summary email for some events daily
###### Bad points
- can't set up one time usage tasks in cron-jobs
- cron-job is fixed based schedule. not dynamic one. 
I had one tasks to send the email to all participants before 30 minutes of starting and I solved the problem with cron-jobs with doing scheduled task every minutes and quering the database to check if it is 30 minutes to start the event. This is extremely inefficient however it does have some plus points. Like we don't need Redis or queues. No external services. It is called ==Polling with cron==. 


### Agentic

### BullMQ