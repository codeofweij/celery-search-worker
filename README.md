# celery-search-worker
python celery worker/dispatch example

# dependencies

* boto3 (SQS,S3)
* redis
* postgres
* celery
* [scikit-learn](http://scikit-learn.org/stable/)
* openpyxl

# Application Workflow
This application retrieves search requests from SQS. Search request corresponds to a list of search names. A search task is 
issued and the result is then fowarded to an export task which creates an excel file placed into S3 storage.

# Redis Databases
* database 1 - an cache/index store of invididuals in a database
* database 2 - celery messaging
* database 3 - results of the search

# Fuzzy Search
name scoring is done using string comparison algorithms designed for name matching. 

* [jaro-distance](https://pypi.python.org/pypi/jellyfish)
* [scikit-learn](http://scikit-learn.org/stable/)
  
# Export

retrieves results from redis instance and adds further additional information from a postgres database.
The XLS is built from [openpyxl](https://bitbucket.org/openpyxl/openpyxl/src)
 
 Two copies are made. One is sent to S3 storage and the other is a local version.

# commands
    celery -A search worker -n worker1.%h -Q searchRequestQueue -l info -c 2 &
    celery -A search worker -n worker1.%h -l info -Q searchNameQueue -c 10 &
    celery -A search worker -n worker1.%h -Q exportQueue -l info -c 1 &
    python search_displatcher &
