
## Dash Deployment
### Brief description

Dash apps can be deployed using two different methods, Plotly’s recommended paid service, Dash Enterprise, and the free-to-use Heroku. Both methods use git-based deployment and so by learning one it is easy to switch to the other. Dash Enterprise provides a suite of development tools and deployment support that allows for easy deployment onto local Linux servers or virtual private cloud services. Dash Enterprise handlrs all of the dependencies of your app by running it through a secure and isolated container, and because of this, deployment is extremely simple and only requires committing changes to git and then running `git push plotly master`. Python, R, and system-level dependencies are also automatically installed by Dash Enterprise, and any future updates to the app can be deployed through committing them and pushing to git. Deployment onto Heroku is essentially the same process since it is also git-based but it doesn’t automatically handle dependencies and so a couple more steps are required, such as setting up a virtual environment, creating a requirements.txt file, and a Procfile to run the app.

### Long description

Before deployment, Dash apps are run through localhost and can only be viewed on your machine. To allow others to access your app you must deploy it to a server. There are two Plotly supported methods of deploying Dash apps. The first method, which is recommended by Plotly, is Dash Enterprise.

Dash Enterprise is a paid service provided by Plotly that allows for rapid Dash app deployment.  Dash Enterprise provides a variety of services that aid in Dash app development (which is outside of the scope of this document) and deployment onto your local Linux servers or virtual private cloud services such as AWS, Google Cloud, or Azure.

Dash Enterprise uses a git-based deployment that automatically installs your app’s Python, R, and system-level dependencies. Conflicting dependencies are not a problem with Dash Enterprise because every Dash app is run from a secure and isolated container. Using the following three easy steps you can deploy a Dash app from the command line.

Initialize your Dash project folder with git:
```
cd <your-folder-name>
git init
```

Select SSH or HTTPS deployment method:
* For HTTPS:

```git remote add plotly https://<your-dash-enterprise-platform>```

where \<your-dash-enterprise-platform\> is replaced with the hostname of your licensed Dash Enterprise in your virtual private cloud service.

* For SSH:

```git remote add plotly <user-name>@<your-dash-enterprise-platform>```

Deploy your Dash app to Dash Enterprise:

```
git add .
git commit -m "<deployment commit message>"
git push plotly master
```

Following these steps, the Dash app is ready for use and can be visited at your Dash Enterprise platform address. Any subsequent updates to your app can be easily made with a `git push`.

The second method that you can use to deploy your Dash app is Heroku, which is free to use. Fortunately, Heroku also uses a git-based deployment method so not only is it still simple but one can switch between Dash Enterprise and Heroku fairly easily. Other than all of the development tools and support that Dash Enterprise provides you, the main difference between the two services are that Heroku doesn’t automatically take care of your app’s dependencies, and so you have to create a virtual environment and manage them yourself.

To deploy a python Dash app using Heroku, first you need a free Heroku account and Python version 3.10. You must download the Heroku installer for your computer’s platform, which will provide you with the ability to use the `heroku` command from the command line. Use the `heroku login` command to login to Heroku, which is required for heroku and git commands to work correctly.

After creating a folder for your project you must initialize it with git and create a virtual environment:

```
git init
virtualenv venv
source venv/bin/activate
```

Since you are using a virtual environment you must install your app’s dependencies, including Dash and Plotly:

```
pip install dash
pip install plotly
```

Additionally you will need the dependency gunicorn, to deploy your app:

```pip install gunicorn```

In your project folder, you must create the app (app.py), requirements.txt, and a Procfile.

The requirements.txt file lists your app’s dependencies and is used by Pip to install them when deploying Heroku. This file can be filled automatically when in your virtual environment with:

```pip freeze > requirements.txt```

The final file that is required for deployment in this method is the Procfile. The Procfile explicitly declares what command is executed to start your app. Its contents should look something like this:

```web: gunicorn app:server```

Running this declares the `web` process type which means the app will be attached to the HTTP routing Heroku stack and receive web traffic after deployment. `app` refers to the filename (`app.py`) and `server` refers to the `server` variable in the app.

Once you have all of these files, you can deploy to Heroku using the following commands:

```heroku create <dash-app-name>
git add .
git commit -m “<deployment commit message>”
git push heroku master
heroku ps:scale web=1
```

You can now view your app at `https://<dash-app-name>.herokuapp.com`.  You can also use `heroku open` from the command line to open the website. If you update your app you can commit your changes to git and deploy to Heroku again with:

```git push heroku master```

The line `heroku ps:scale web=1` specifies that the app is running on a single web dyno. A dyno is a lightweight container that runs the command in the Procfile, and the more dynos you have running the more horizontally scaled out your app will be which means it can handle higher volumes of traffic. You can check how many dynos are running with `heroku ps`.  Your app is by default deployed on a free dyno, which will sleep after a half-hour of not receiving traffic. What this means is that the first bout of traffic it receives after falling asleep will cause a delay of a few seconds before the app loads. Every Heroku account gets a quota of free dyno hours (550 hours a month, 1000 a month with credit card verification), after which the apps will no longer run. You can upgrade to a hobby or professional dyno type to avoid dyno sleeping.

### Links
* https://dash.plotly.com/deployment
* https://plotly.com/dash/app-manager/?tab=deployment
* https://devcenter.heroku.com/articles/getting-started-with-python?singlepage=true
