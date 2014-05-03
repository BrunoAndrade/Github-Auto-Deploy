!http://logsol.github.com/Github-Auto-Deploy/images/Github-Auto-Deploy.png!

# What is it?

This is a small HTTP server written in python. 
It allows you to have a version of your project installed, that will be updated automatically on each Github push.

To set it up, do the following:
* Install python
	`sudo apt-get install python`
* Copy the GitAutoDeploy.conf.json.example to GitAutoDeploy.conf.json. This file will be gitignored and can be environment specific.
* Update the config file with your required fields (See below)
* Start the server by typing "python GitAutoDeploy.py" 
* To run it as a daemon add ==--daemon-mode==
* On the Github page go to a repository, then "Settings", "Webhooks & Services", 
"Add webhook"
	* Add the url of your machine + port (e.g. http://example.com:8001).
	* Set the "Payload Version" to "application/vnd.github.v3+form"

Once you add the webhook, the first message will fail (at the moment the project looks for a valid push request and fails otherwise).

## How this works

When someone pushes changes into Github, it sends a json file to the service hook url. 
It contains information about the repository that was updated.

All it really does is match the repository urls to your local repository paths in the config file, 
move there and run "git pull".


Additionally it runs a deploy bash command that you can add to the config file optionally.
Make sure that you start the server as the user that is allowed to pull from the github repository.

# Config File

The config file has the following fields:

## port

This has to be the port number you want the python server to listen on and must be included in the "Payload URL" you set on Github

## respositories

This is an array of repositories you want to accept webhooks for (You can have one server listen for request for multiple repositories).

### url

This is the URL for the github repository

### path

The path is simply where the project lives on the filesystem and where the ptyhon script will run 'git pull'

### ref

You can use the ref to specify the branch you want to listen for. An example if you may want to run a staging script when the master branch is pushed to, but run a production script when a release branch is pushed to

### deploy

Deploy will run the string as a bash script, allowing you to run an external script.

	{
		"port": <Port>, 
		"repositories": 
		[{
			"url": "https://github.com/PaulKinlan/Github-Auto-Deploy",
			"path": "/home/user/somedirectory/Github-Auto-Deploy/",
			"ref": "refs/heads/master",
			"deploy": "echo Deploying Github-Auto-Deploy"
		}]
	}