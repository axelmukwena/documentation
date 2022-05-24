# PgHero Rails

### How to set up
Reference: https://github.com/ankane/pghero/blob/master/guides/Rails.md

Add gem
```ruby
# Gemfile
gem "pghero"
```

Add login details to environemnt variables

```env
# .env
PGHERO_USERNAME=my_username
PGHERO_PASSWORD=my_password
```

Configure routes with limitations. E.g. limit access to `admin` only

```ruby
# config/routes.rb

authenticate :user, -> (user) { user.admin? } do
  mount PgHero::Engine, at: "pghero"
end
```

Enable Query Stats

Find the `postgresql.conf` file: (`postgres == default postgresql user`):

```shell
$ sudo psql -U postgres

# In psql console
SHOW config_file;
```

The query above should display a path. Example: `/var/lib/pgsql/data/postgresql.conf`

Add the following to your `postgresql.conf`:

```env
shared_preload_libraries = 'pg_stat_statements'
pg_stat_statements.track = all
pg_stat_statements.max = 10000
track_activity_query_size = 2048
```

Then restart PostgreSQL. Run psql as superuser:

```shell
$ sudo psql -U postgres

# In psql console
CREATE extension pg_stat_statements;
```

Create, store and track stats by:

```shell
$ rails generate pghero:query_stats
$ rails db:migrate
```

And schedule the task below to run every 5 minutes.

```
$ rake pghero:capture_query_stats
```

After this, everthing should work.

Go to `http://knorket.lvh.me:3001/pghero` to test

