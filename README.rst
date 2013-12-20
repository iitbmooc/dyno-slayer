Part of `edX code`__.

__ http://code.edx.org/


Overview
------------------------

This is a tool to identify laggard dynos within a formation, and kill them.

It was created for mitigating slowdowns and timeouts in heroku apps with data-intensive workloads.  While the
acknowledged best practice is to keep web applications servicing small units of work, deferring long-running tasks
to background jobs, sometimes reality is more complicated and the distinction between the two is blurred.

On Heroku, one's app dynos can be cycled at any time into a virtual environment whose performance or resource
availability are limited (the "noisy neighbor" problem).  This is an inherent feature of elastic computing, but
in use cases where minimizing response time is critical, it can have a major impact.

This script works by measuring the response time of each dyno in an app's formation, computing an overall mean
response time, and then looking for individual dynos whose timings are more than a certain number of standard
deviations higher than that.  If any are found, one of them is randomly selected and stopped.  In our experience, we
find that Heroku's dyno manager will automatically start the dyno soon afterward, and this almost always results
in improved response times.

Because this tool was purpose-crafted to solve a specific problem with a specific app, absolutely no assumptions
should be made about its suitability for use in your system.  Before you deploy this code, please ensure you fully
understand what it does and the potential consequences of running it, especially on a production system!


Getting Started
-------------------------------

While the Heroku python library is a requirement, and the codebase is set up for deployment on Heroku, it can be
run in any Python 2.7 environment.  Do ``pip install -r requirements.txt`` first.

To invoke the tool, do ``python scripts/slayer.py``.  Two environment variables must be set: HEROKU_APP_NAME and 
HEROKU_API_KEY.  Consult `Heroku's documentation`__ if you need to set up an api key.

__ https://devcenter.heroku.com/articles/platform-api-quickstart

Many settings are configurable as script options; read the source or do ``python scripts/slayer.py -h`` for the details.

The intended usage is to run slayer.py on a regular schedule.  Every 5-10 minutes is the general ballpark.  Keep in
mind that by default the script will take a few minutes to run, and you probably don't want multiple runs to overlap.

When lagging dynos are detected, the script will drop log lines with a level of WARN.  This is meant to be useful for
triggering alerts from a log processing system, in the event you'd like to be informed when this tool has decided
to stop one of your dynos.  If the script encounters conditions that make it unable to complete its task, it will log
at level ERROR.


License
-------

The code in this repository is licensed under version 3 of the AGPL unless
otherwise noted.

Please see ``LICENSE.txt`` for details.

How to Contribute
-----------------

Contributions are very welcome.

Please read `How To Contribute <https://github.com/edx/edx-platform/wiki/How-To-Contribute>`_ for details.

Reporting Security Issues
-------------------------

Please do not report security issues in public. Please email security@edx.org

Mailing List and IRC Channel
----------------------------

You can discuss this code on the `edx-code Google Group`__ or in the
``edx-code`` IRC channel on Freenode.

__ https://groups.google.com/forum/#!forum/edx-code
