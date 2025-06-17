### Node-cron
Helps to run at specific times or interval.
Don't use node-cron is distributed systems. It has not concurrency control and might perform overlapping tasks. Also no provided error handling mechanism also.

Cron jobs don't remember anything between cycles. They just work on repeating some task that is it. 

Job is just a unit of work that I would want to do outside request/response flow.
###### Plus points
- for running some tasks every fixed intervals
- running your health checks every some minutes
- sending summary email for some events daily
- used in clean up expired token
- monitor events, daily reports.
###### Bad points
- can't set up one time usage tasks in cron-jobs unless some hacky thing is implemented. 
- cron-job is fixed based schedule. not dynamic one. meaning it will run in fixed interval
- Execution is in-memory. that means if the server isn't running then the scheduled task which is to be run will not run at all. 
- If job fails, then no retries.
- There is no monitoring too. Like did job failed, succeed or what ? 

I had one tasks to send the email to all participants before 30 minutes of starting and I solved the problem with cron-jobs with doing scheduled task every minutes and quering the database to check if it is 30 minutes to start the event. This is extremely inefficient however it does have some plus points. Like we don't need Redis or queues. No external services. It is called ==Polling with cron==. 

In other things, there is no job id, job name, just run callback every time .
### Agenda

### BullMq