# Subdir PATH buildpack

This buildpack takes existing PATH entries, finds every `app` occurence (which
in heroku environment is the root directory of your repository), and replaces
it so that it points to a selected subdirectory.

## Example

Let's assume you've got a following directory structure in your repo (i.e.
monorepo):

```
/app
|-- api
|-- frontend
|-- backend
..
```

Inside `/app/api` directory, there's your Rails API app; if you want to
run `bundle exec rake db:migrate` for instance, you will get:

```
bash: bundle: command not found
```

because most buildpacks assume the root directory is where every packages and
binaries get installed. Though, if you're using buildpacks like [multi procfile buildpack](https://github.com/heroku/heroku-buildpack-multi-procfile.git) or [subdir buildpack](https://github.com/negativetwelve/heroku-buildpack-subdir), the `PATH` env variable will not by default contain correct paths.

It'll be something like:

```
/app/bin:/app/vendor/bundle/bin:/app/vendor/bundle/ruby/2.3.0/bin
```

Using this buildpack, set `APP_SUBDIR_PATH` env variable to something, e.g.
`api`, and the buildpack will append the `PATH` to reflect that (original PATH
contents will stay in place), like:

```
/app/api/bin:/app/api/vendor/bundle/bin:/app/api/vendor/bundle/ruby/2.3.0/bin:/app/bin:/app/vendor/bundle/bin:/app/vendor/bundle/ruby/2.3.0/bin
```

## Usage

Set environment variable `APP_SUBDIR_PATH` to path to the app you want to deploy
inside your repository directory structure.

    $ heroku buildpacks:set <buildpack_url>

Or add it to the .buildpacks file if using [heroku-buildpack-multi](https://github.com/ddollar/heroku-buildpack-multi):

    $ cat .buildpacks
    ...
    <buildpack_url>
