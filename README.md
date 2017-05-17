# Redmine Recurring Tasks

[![Build Status](https://travis-ci.org/centosadmin/redmine_recurring_tasks.svg?branch=master)](https://travis-ci.org/centosadmin/redmine_recurring_tasks)

Plugin for creating scheduled tasks from templates.

# Installation

* Ruby 2.2+
* Redmine 3.3+
* Standard install plugin:

```
cd {REDMINE_ROOT}
git clone https://github.com/centosadmin/redmine_recurring_tasks.git plugins/redmine_recurring_tasks
bundle install
bundle exec rake redmine:plugins:migrate RAILS_ENV=production
```

# Run

To run copying issues task you should run rake task

```
cd {REDMINE_ROOT}
bundle exec rake redmine_recurring_tasks:exec
```

And to do it periodically you may use cron or another external scheduler.

## Schedulers

### Sidekiq-cron


```
class WeeklyScheduleWorker
  include Sidekiq::Worker

  def perform
    checker = RedmineRecurringTasks::IssueChecker.new(Setting.plugin_redmine_recurring_tasks)
    checker.call
  end
end

cron_job_array = [
  {
    'name'  => 'Weekly schedule worker',
    'class' => 'WeeklyScheduleWorker',
    'cron'  => '*/5 * * * *'
  }
]

Sidekiq::Cron::Job.load_from_array cron_job_array
```

### Whenever

```
cd {REDMINE_ROOT}
whenever --update-crontab --load-file plugins/redmine_recurring_tasks/config/schedule.rb
```

### Cron manual

```
$ crontab -e
```

And add cron job line

```
*/5 * * * * /bin/bash -l -c 'cd /home/redmine && RAILS_ENV=production bundle exec rake redmine_recurring_tasks:exec
```

# Settings

If you have any plugins, which for some reason doesn't copying in spawned issues, you can set specific issue associations fields in plugin settings. But be careful — this option can break work plugin scheduler.

For example, if you using plugin *Redmine checklists*, you can check "checklists" in RedmineRecurringTasks settings. 

# License

[MIT](https://github.com/centosadmin/redmine_recurring_tasks/blob/master/LICENSE)

# Author of the Plugin

The plugin is designed by [Southbridge](https://southbridge.io).
