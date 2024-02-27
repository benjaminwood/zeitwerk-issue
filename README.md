# Zeitwerk issue

This repository reproduces an issue with Zeitwerk in Rails 6.1 when files have been autoloaded during initialization.

A real-world scenario where I've seen this occur is in a Rails app that used devise. The devise initializer loaded the `ApplicationController`, which loaded helpers, which had require explicit statements like the ones in this reproduction example.

Just run `bundle exec rake zeitwerk:check --trace` and you'll see output like this:

```
> bundle exec rake zeitwerk:check --trace
** Invoke zeitwerk:check (first_time)
** Invoke environment (first_time)
** Execute environment
Autoloaded A
Requiring a in app/models/b.rb
Autoloaded B
** Execute zeitwerk:check
Hold on, I am eager loading the application.
rake aborted!
NameError: uninitialized constant A

    parent.const_get(cname, false)
          ^^^^^^^^^^
/usr/local/lib/ruby/gems/3.1.0/gems/zeitwerk-2.6.8/lib/zeitwerk/loader/helpers.rb:135:in `const_get'
/usr/local/lib/ruby/gems/3.1.0/gems/zeitwerk-2.6.8/lib/zeitwerk/loader/helpers.rb:135:in `cget'
/usr/local/lib/ruby/gems/3.1.0/gems/zeitwerk-2.6.8/lib/zeitwerk/loader/eager_load.rb:169:in `block in actual_eager_load_dir'
/usr/local/lib/ruby/gems/3.1.0/gems/zeitwerk-2.6.8/lib/zeitwerk/loader/helpers.rb:40:in `block in ls'
/usr/local/lib/ruby/gems/3.1.0/gems/zeitwerk-2.6.8/lib/zeitwerk/loader/helpers.rb:25:in `each'
/usr/local/lib/ruby/gems/3.1.0/gems/zeitwerk-2.6.8/lib/zeitwerk/loader/helpers.rb:25:in `ls'
...more backtrace
```