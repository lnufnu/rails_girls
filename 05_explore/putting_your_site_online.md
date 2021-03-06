# Putting Your App Online With Heroku

*Modified only slightly, from an original by Terence Lee, [@hone02](https://twitter.com/hone02)*

### Get Heroku

Follow steps 1 through 3 of the [Heroku quickstart guide](https://devcenter.heroku.com/articles/quickstart) to sign up, install the toolbelt, and login.

### Preparing your app

#### Version Control Systems

We need to add our code to version control. If you haven't already done so please visit the guide on Git and Github. You can do this by running the following in the terminal:

#### Updating our database

First, we need to get our database to work on Heroku, which uses a different database. Please change the following in the Gemfile:

```ruby
gem 'sqlite3'
```

to

```ruby
group :development do
  gem 'sqlite3'
end
group :production do
  gem 'pg'
end
```

Run `bundle install --without production` to setup your dependencies.

__COACH__: You can talk about RDBMS and the different ones out there, plus include some details on Heroku's dependency on PostgreSQL.


#### Adding rails_12factor

Next, we need to add `rails_12factor` entry into our Gemfile to make our app available on Heroku.

This gem modifies the way Rails works to suit Heroku, for example Logging is updated and the configuration for static assets (your images, stylesheets and javascript files) is tweaked to work properly within Heroku's systems.

Please change the following in the Gemfile:

```ruby
group :production do
  gem 'pg'
end
```

to

```ruby
group :production do
  gem 'pg'
  gem 'rails_12factor'
end
```

After this run `bundle`, then commit the changes to Gemfile.lock to your repository:

```bash
git commit -a -m "Added rails_12factor gem and updated Gemfile.lock"
```


### Deploying your app

#### App creation

We need to create our Heroku app by typing `heroku create` in the terminal and see something like this:

```bash
Creating sheltered-refuge-6377... done, stack is cedar
http://sheltered-refuge-6377.herokuapp.com/ | git@heroku.com:sheltered-refuge-6377.git
Git remote heroku added
```

In this case "sheltered-refuge-6377" is your app name.

#### Pushing the code

Next we need to push our code to heroku by typing `git push heroku master`. You'll see push output like the following:

```bash
Initializing repository, done.
Counting objects: 101, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (91/91), done.
Writing objects: 100% (101/101), 22.68 KiB | 0 bytes/s, done.
Total 101 (delta 6), reused 0 (delta 0)

-----> Ruby app detected
-----> Compiling Ruby/Rails
-----> Using Ruby version: ruby-2.0.0
-----> Installing dependencies using 1.6.3
       Running: bundle install --without development:test --path vendor/bundle --binstubs vendor/bundle/bin -j4 --deployment
       Fetching gem metadata from https://rubygems.org/..........
...
-----> Launching... done, v6
       http://sheltered-refuge-6377.herokuapp.com/ deployed to Heroku
```

You'll know the app is done being pushed, when you see the "Launching..." text like above.

#### Migrate database

Next we need to migrate our database like we did on your own machine a little earlier. This is done by writing `heroku run` before the usual `rake db:migrate` command:

```bash
$ heroku run rake db:migrate
```

When that command is finished being run, you can visit the app based on the url. For this example app, you can go to <http://sheltered-refuge-6377.herokuapp.com/>. You can also type `heroku open` in the terminal to visit the page.


#### Closing notes

Heroku's platform is not without its quirks. Applications run on Heroku live within an ephermeral environment — this means that (except for information stored in your database) any files created by your application will disappear if it restarts (for example, when you push a new version).

###### [Ephemeral filesystem](https://devcenter.heroku.com/articles/dynos#ephemeral-filesystem)
> Each dyno gets its own ephemeral filesystem, with a fresh copy of the most recently deployed code. During the dyno’s lifetime its running processes can use the filesystem as a temporary scratchpad, but no files that are written are visible to processes in any other dyno and any files written will be discarded the moment the dyno is stopped or restarted.

In the exploration where we add an images to Chirps, we place new files in `public/uploads` folder, which will not work on Heroku. Images may in fact show up for a while. However, any time you update your code, Heroku will restart the "dyno" that runs your app. This will toss out all the uploaded files!

##### Working around Ephemeral Storage

Obviously this doesn't seem to be useful if you were running a real life application, but there are ways to work around this which is commonly used by a lot of popular websites.

The most common method is to use an external asset host such as Amazon S3 (Simple Storage Service) or Rackspace CloudFiles. These services provide (for a low cost - usually less then $0.10 per GB) storage 'in the cloud' (meaning the files could potentially be hosted anywhere) which your application can use as persistent storage.

While this functionality is a bit out of scope for this tutorial there are some resources available which you can use to find your way:

* [Amazon S3 - The Beginner' Guide](http://www.hongkiat.com/blog/amazon-s3-the-beginners-guide/)
* [Making Paperclip work on Heroku](https://devcenter.heroku.com/articles/paperclip-s3)

As always if you require any more information or assistance your coaches will be able to assist.
