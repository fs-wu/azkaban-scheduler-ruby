# AzkabanScheduler

Azkaban client that can update the schedule.

Azkaban scheduler was designed to make it easy to maintain both the
job definitions and the schedule for the jobs from within a projects
code. This allows you to easily keep the jobs in azkaban in sync
with your code.

## Installation

Add this line to your application's Gemfile:

    gem 'azkaban_scheduler'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install azkaban_scheduler

## Usage

```ruby
require 'azkaban_scheduler'

client = AzkabanScheduler::Client.new('https://localhost:8443')
session = AzkabanScheduler::Session.start(client, 'admin', ENV['AZKABAN_PASSWORD'])

project = AzkabanScheduler::Project.new('Demo', 'A simple project to get you started')
flow_name = 'hello-world'
project.add_job(flow_name, AzkabanScheduler::Job.new(type: 'command', command: 'echo "hello world"'))
begin
  session.create_project(project)
rescue AzkabanScheduler::ProjectNotFoundError
end
session.upload_project(project)

session.remove_all_schedules(project.name)
start_time = Time.now + 60
session.post_schedule(project.id, project.name, flow_name, start_time, period: '12h')
```
or 

```ruby
require 'azkaban_scheduler'

client = AzkabanScheduler::Client.new('https://localhost:8443')
session = AzkabanScheduler::Session.start(client, 'admin', ENV['AZKABAN_PASSWORD'])
project_name = 'Demo'
session.create(project_name,'A simple project to get you started')
session.upload(project_name, '/tmp/file.zip')

session.remove_all_schedules()
start_time = Time.now + 60
project_id = session.get_project_id(project_name)
flow_name = session.fetch_project_flows(project_name)["flows"][0]["flowId"]
option = {period: '30m',success_emails:'a@a.com',failure_emails:'a@a.com'}
session.post_schedule(project_id, project_name, flow_name, start_time, option)

p session.fetch_project_flows(project_name)["flows"][0]["flowId"]
p session.list_schedules
flow_id = session.list_flow_ids(project_name)[0]
session.execute_flow(project_name, flow_id)
exs =  session.fetch_flow_executions(project_name, flow_id)
execid = exs["executions"][0]["execId"]
p session.fetch_exec_flow(execid)
jobid = "my_job_id"
result = session.fetch_exec_job_logs(execid, jobid)
result["data"].split("\n").each do |r|
    p r
end
```

## Contributing

1. Fork it ( https://github.com/Shopify/azkaban-scheduler-ruby/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request
