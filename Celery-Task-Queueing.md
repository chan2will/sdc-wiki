Celery tasks are used to create asynchronous (or synchronous) tasks to be queued.  They use distributed message passing via execution units called “tasks”, which can be executed concurrently.

### Sample periodic task ###
 
    class MyTask(PeriodicTask):

        run_every = crontab(minute=‘*/10’)

        def run(self, *args, **kwargs):
		try:
		    logger.info(‘running my test task’)
		except Exception as err:
		    logger.info(‘error running MyTask’)
		    self.retry(err=err)

Currently, Celery queries the Postgres tesseract database, in the **operations_task_control** table, for a list of tasks to queue.  For a task to run periodically, insert the task class name such as 'workflow.tasks.MyTask' and set enabled = TRUE.

Settings/celery.py contains a call to app.autodiscovery_tasks() which will automatically load tasks with the **@app.task** decorator.

### Sample non-periodic task ###
     
    @app.task
    def do_my_task():
        try:
            logger.info('running a one-off task')
        except Exception as err:
	    logger.info(‘error running MyTask’)
	    self.retry(err=err)`

Using a non-periodic task within another function:

`do_my_task.apply_async(countdown=60, queue='priority_high')`

The above call to do_my_task() includes a delay, or countdown of 60 seconds before running, and is added to a Celery queue named 'priority_high'.  To view the Celery queue set up, see **settings/base.py**.

### Testing Celery Tasks ###

Redis is our Celery "broker".  Be sure to start Redis (use the following command) 

`redis-server`

To start the celery daemon locally, for tasks:

`celery -A settings.celery worker --loglevel=info`
 
To start the celery daemon locally for periodic tasks:

`celery beat -A settings.celery --loglevel=info`
 
To run a single task (for testing):

`python ./manage.py shell_plus`

`from common.tasks import MyTask`

`MyTask.delay()`