#TogglPy
TogglPy is a python library for interacting with the [Toggl API](https://github.com/toggl/toggl_api_docs).

#Features
* Make requests against any (Toggl) API endpoint with request data as a dictionary
* Generate and save PDFs of summary, weekly, or detailed reports
* Fetch reports as JSON
* Get all workspaces or all clients
* Get a specific workspace or client, by id or name
* Query projects, by client, or by a single name
* Add custom time entries

#Setup
1. Download the project, or download **TogglPy.py** for local usage
2. Import the content: 
```
&nbsp;&nbsp;&nbsp;&nbsp;from TogglPy import Toggl
```
3. Create a Toggl object: 
```
&nbsp;&nbsp;&nbsp;&nbsp;toggl = Toggl()
```
4. Authenticate either by Toggl credentials:
``` 
&nbsp;&nbsp;&nbsp;&nbsp;toggl.setAuthCredentials('<EMAIL>', '<PASSWORD>') 
```
   OR using [your personal API token](https://toggl.com/app/profile):
``` 
&nbsp;&nbsp;&nbsp;&nbsp;toggl.setAPIKey('<API-TOKEN>') 
```


#I learn best by **examples**
###Manual requests against any Toggl endpoint:
```
from TogglPy import Toggl

# create a Toggl object and set our API key 
toggl = Toggl()
toggl.setAPIKey("mytogglapikey")

response = toggl.request("https://www.toggl.com/api/v8/clients")

# print the client name and id for each client in the response
# list of returned values can be found in the Toggl docs (https://github.com/toggl/toggl_api_docs/blob/master/chapters/clients.md)
for client in reponse:
    print "Client name: %s  Client id: %s" % (client['name'], client['id'])
```
Or, if you want to add some data to your request:
```
data = {
    'id': 42,
    'some_key': 'some_value',
    'user_agent': 'TogglPy_test',
}   
response = toggl.request("https://www.toggl.com/api/v8/some/endpoint", parameters=data)
```

###Generating PDF reports:
```
# specify that we want reports from this week
data = {
    'workspace_id': 0000, # see the next example for getting a workspace id
    'since': '2015-04-27',
    'until': '2015-05-03',
}

# download one of each type of report for this time period
toggl.getWeeklyReportPDF(data, "weekly-report.pdf")
toggl.getDetailedReportPDF(data, "detailed-report.pdf")
toggl.getSummaryReportPDF(data, "summary-report.pdf")
```

###Finding workspaces and clients
This will print some raw data that will give you all the info you need to identify clients and workspaces quickly:
```
print toggl.getWorkspaces()
print toggl.getClients()
```
If you want to clean it up a little replace those print statements with
```
for workspace in toggl.getWorkspaces():
    print "Workspace name: %s\tWorkspace id:%s" % (workspace['name'], workspace['id'])
for client in toggl.getClients():
    print "Client name: %s\tClient id:%s" % (client['name'], client['id'])
```
If you want to find a specific client or workspace:
```
john_doe = toggl.getClient(name="John Doe")
personal = toggl.getWorkspace(name="Personal")

print "John's client id is %s" % john_doe['id']
print "The workspace id for 'Personal' is %s" % personal['id']
```
The reverse can also be done; use `.getClient(id=0000)` or `.getWorkspace(id=000)` to find items by id.

### Starting New Timer

```
# You can get your project pid in toggl.com->Projects->(select your project) and copying the last number of the url
myprojectpid = 10959693
toggl.startTimeEntry("my description", myprojectpid)
```

### Stopping Current Timer

```
currentTimer = currentRunningTimeEntry()
stopTimeEntry(currentTimer['data']['id'])
```

### Creating a custom time entry

```
# Create a custom entry for today, of a 9 hour duration, starting at 10 AM:
toggl.createTimeEntry(hourduration=9, projectname='GoogleDrive', hour=10)

# Or speed up the query process and provide the clien't name:
toggl.createTimeEntry(hourduration=9, projectname='GoogleDrive', clientname='Google', hour=10)

# Provide *month* and/or *day* too for specific dates:
toggl.createTimeEntry(hourduration=9, projectname='GoogleDrive', clientname='Google', month=1, day=31, hour=10)
```
